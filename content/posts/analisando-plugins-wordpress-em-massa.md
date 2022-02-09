---
title: "Analisando plugins WordPress em massa"
date: 2021-07-06T23:38:32-03:00
draft: false
tags: ["scraping", "research", "csharp"]
author: "jojo"
---

Acredito que a grande maioria do pessoal que procura vulnerabilidades rentáveis dentro do mundo de aplicações web, em algum momento já passou pela *stack* que o WordPress fornece, seja auditando seu `core`, ou plugins, sendo este último o foco dessa postagem.

Junto a um simples *crawler* escrito em C# 9.0 (só não usei os recursos do 10 pois não havia necessidade de `delegates`), vamos percorrer todos os *plugins* listáveis na *index* do site do projeto, e organiza-lo em uma estrutura contendo o próprio `.zip` com o código do plugin.

> **Nota**: Caso tenha interesse em apenas utilizar o *crawler*, verifique-o no código em *highlight* logo abaixo.

# Top-level program

Por não haver a necessidade de termos uma estrutura de programa complexa, vamos utilizar os recursos que o C# 9.0 tem de melhor, em trazer minimalismo na hora de construir uma aplicação de linha de comando: Com *top-level programs*. Dentro dessa linguagem, essa caracteristica permite que escrevamos código sem o *template* clássico do `static void Main()`.

![Apresentação do site](/static/26f475e9f2a847fe0bbf2eab.png)

Para análisar o HTML da página, e não passar raiva ou perder tempo com expressões regulares ou ficar refém de algo como `LastIndexOf()` ou `IndexOf()`, irei utilizar o `AngleParse` como biblioteca para realizar o *parsing* da sopa de letras que vamos extrair do site exibido acima. Um ponto de atenção na hora de fazer o *crawling*, é justamente se antentar onde será realizado, tendo em vista que a listagem completa dos quase 60 mil plugins não é possível.

> **Nota**: Também vamos fazer tudo isso assíncrono e paralelo, utilizando `async` e `SemaphoreSlim`.

Ah, e seguindo a linha das outras postagens, o código vou escrevendo conforme vou conseguindo tempo no meu dia-a-dia, por tanto essa postagem também se remete a essa condição, e pode ser atualizada constantemente conforme meu humor varie. Caso tenha visto alguma coisa que alterou, todo o blog está disponível em meu GitHub no `/exception`.

Sem mais esperneios, vamos ao código-fonte. Esse que talvez não seja muito *begginers friendly*, tendo em vista a utilização de recursos bem avançados do C#, porém, ainda contendo esse tipo de código, sua legibilidade é realmente simples:

```csharp
using System;
using System.IO;
using System.Net;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Collections.Concurrent;

using AngleSharp.Html.Dom;
using AngleSharp.Html.Parser;

using static System.Console;


var clientHandler = new HttpClientHandler
{
    AllowAutoRedirect = false,
    UseCookies = true,
    CookieContainer = new CookieContainer()
};
var client = new HttpClient(clientHandler);
var parser = new HtmlParser();
var semaphore = new SemaphoreSlim(Environment.ProcessorCount);
var downloadList = new[]
{
    "https://wordpress.org/plugins/browse/popular/",
    "https://wordpress.org/plugins/browse/beta/",
    "https://wordpress.org/plugins/browse/featured/",
    "https://wordpress.org/plugins/browse/blocks/"
};

WriteLine("[+] Crawler started with a possible total of {0} thread(s)", Environment.ProcessorCount);

var pluginsPages = new ConcurrentBag<string>();
async Task GetAllPluginsPagesAsync(string target)
{
    await semaphore.WaitAsync();

    var response = await client.GetAsync(target);
    if (!response.IsSuccessStatusCode) return;

    var counter = 2;
    while (true)
    {
        var content = await response.Content.ReadAsStringAsync();
        var soup = await parser.ParseDocumentAsync(content);
        var navs = soup.All
            .Where(e => e.LocalName == "div" && e.GetAttribute("class") == "nav-links")
            .ToList();

        if (navs.Count == 0)
        {
            pluginsPages.Add(content);
            break;
        }
        
        pluginsPages.Add(content);
        response = await client.GetAsync(target + $"page/{counter}/");
        if (!response.IsSuccessStatusCode) break;

        counter += 1;
    }

    semaphore.Release();
}

var downloadTasks = new List<Task>();
downloadList.ToList().ForEach(d => downloadTasks.Add(GetAllPluginsPagesAsync(d)));
await Task.WhenAll(downloadTasks);

WriteLine("[+] Downloaded {0} page(s) with unique plugins from WordPress", pluginsPages.Count);

var pluginPages = new ConcurrentBag<string>();
async Task GetPluginPageAsync(string page)
{
    await semaphore.WaitAsync();

    var soup = await parser.ParseDocumentAsync(page);
    var headers = soup.All
        .Where(e => e.LocalName == "h3" && e.GetAttribute("class") == "entry-title")
        .Select(e => e.InnerHtml)
        .ToList();
    
    if (headers.Count <= 0) return;
    headers.ForEach(async c =>
    {
        var anchor = await parser.ParseDocumentAsync(c);
        if (anchor.All.FirstOrDefault(e => e.LocalName == "a") is not IHtmlAnchorElement href) return;
        pluginPages.Add(href.Href);
    });

    semaphore.Release();
}

var parserTasks = new List<Task>();
pluginsPages.ToList().ForEach(p => parserTasks.Add(GetPluginPageAsync(p)));
await Task.WhenAny(parserTasks);

var pluginsPagesUniq = pluginPages.Distinct().ToList();
WriteLine("[+] Downloaded {0} plugin page(s)", pluginsPagesUniq.Count);

if (!Directory.Exists("output")) Directory.CreateDirectory("output");

async Task DownloadPluginAsync(string href)
{
    await semaphore.WaitAsync();

    var response = await client.GetAsync(href);
    var content = await response.Content.ReadAsStringAsync();
    var soup = await parser.ParseDocumentAsync(content);

   
    if (soup.All.FirstOrDefault(e => e.LocalName == "a" && 
                                     e.GetAttribute("class") == 
                                     "plugin-download button download-button button-large") 
        is not IHtmlAnchorElement downloadLink) return;

    response = await client.GetAsync(downloadLink.Href);
    var streamContent = await response.Content.ReadAsStreamAsync();

    var pluginFileName = downloadLink.Href.Split("/").Last();
    WriteLine("[+] Downloading file {0}...", pluginFileName);
    
    var path = Path.Combine("output", pluginFileName);
    await using var stream = new FileStream(path, FileMode.CreateNew);
    streamContent.Seek(0, SeekOrigin.Begin);
    streamContent.CopyTo(stream);
    
    semaphore.Release();
}

var downloadPluginTasks = new List<Task>();
pluginsPagesUniq.ForEach(l => downloadPluginTasks.Add(DownloadPluginAsync(l)));
await Task.WhenAll(downloadPluginTasks);

```

Basicamente, na primeira etapa do código, inicializamos uma lista contendo as URLs de todos os alvos que vamos utilizar para popular a segunda lista concorrente, que é representada pela própria pagína do plugin.

Na sequência, pegamos essa mesma listagem de páginas, e extraimos somente as URLs para download dos plugins. Por fim, temos individualmente todas as páginas dos plugins para download, e então, realizam-se o tal ato.

Caso você for realizar o *debugging*, em especial na altura da lógica correspondente a lista `pluginsPagesUniq`, você pode se perguntar a necessidade de realizar um `Distinct()` do conteúdo da lista concorrente, bom, isso acontece pois um plugin pode estar listado em mais de uma página, dai não seria legal ter entidades repetidas no nosso conjunto.

> **Nota**: Existem formas muito mais fáceis e inteligentes de se fazer isso, porém, pegar todos os plugins mais famosos dessa forma, é muito mais *style* e divertido.

# Rodando o código

Ao executar o código acima (instalando a única depêndencia, que é o `AngleSharp`) você terá em menos de dois minutos (caso tiver um processador com no minímo 8 *threads*) mais de 1400 plugins baixados no diretório `output` à partir do diretório que foi utilizado para compilar e executar o código. Algo nessa vibe:

```
[+] Crawler started with a possible total of 20 thread(s)
[+] Downloaded 76 page(s) with unique plugins from WordPress
[+] Downloaded 1395 plugin page(s)
[+] Downloading file wp-lazy-loading.zip...
[+] Downloading file background-image-cropper.zip...
[+] Downloading file akismet.4.1.10.zip...
[+] Downloading file classic-editor.1.6.1.zip...
[+] Downloading file rollback-update-failure.0.5.3.zip...
[+] Downloading file health-check.1.4.5.zip...
[+] Downloading file wp-jquery-update-test.2.0.0.zip...
...
```

A sazonalidade da quantidade de plugins disponíveis para download pode variar bastante dependendo de como os usuários do WordPress utilizam os pluginss, mas a aplicação tende a funcionar até que aconteça alguma grande mudança nas telas do próprio site do WordPress.

# E sobre análise?

Caso você só esteja buscando uma CVE para chamar de sua, é bem mais fácil buscar nos plugins com poucos downloads ou mais obscuros na comunidade. Agora, se acredita ter o suficiente para contornar a análise que já é realizada pelo WordPress, tanto automaticamente, quanto manualmente, dependendo da popularidade do plugin, sem contar a análise que a própria cena de segurança da informação faz, esse aqui é o caminho.

> Todavia, o `semgrep` cantará de qualquer forma.

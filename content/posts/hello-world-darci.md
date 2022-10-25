+++
title = "Então nasce Darci"
date = "2022-10-24T23:46:28-03:00"
author = "Jojo"
authorTwitter = "6a6f6a6f"
cover = ""
tags = ["research", "development"]
keywords = ["darci"]
description = "Se existe o Hugo, agora temos Darci, um gerador de sites mais minimalista, e totalmente orientado a objetos, sem todos aqueles recursos, e escrito em uma linguagem que não é rápida igual Go, mas vai ser legal (eu acho)."
showFullContent = false
+++

## Isso é inventar a roda?

Acho seriamente que quando essa moda pegar vai ser o novo "*olha fiz a interface do NuBank em `$STACK_NAME`*", mas nesse caso, acredito que não vai ser mais do mesmo, tendo em vista que vou passar por uma dor de cabeça mais *hardcore*.

Meu objetivo, além de claro, criar Darci, também é desenvolver um parser de Markdown para HTML bem minimalista, rápido, e potencialmente seguro — sim, *hackers love parsers!*, e tenho certeza de alguém vai conseguir achar uma vulnerabilidade no meu *parser*, vou ficar muito feliz em corrigi-la!

Além disso, tenho alguns objetivos em mente na hora de confeccionar Darci, sendo esses os que mais considero importantes:

- Tudo sem classe no lado do front-end;
  - Quero fazer ou utilizar algo como o [`tbolt/boltcss`](https://github.com/tbolt/boltcss).
- Não utilizar bibliotecas de nenhum tipo, apenas o que há de existente no dotnet (nem te contei né? Pois é, vai ser em C#);
- Vai ter que ser em dotnet 7 por conta do compilador AOT, que finalmente não é um recurso exclusivo apenas do Blazor;
- Quero tentar abstrair tudo em uma estrutura orientada a objetos, afinal de contas, não há motivo para não o fazer;
- A interface vai ser extremamente minimalista, digna do console dos carros da Tesla;
  - O mesmo vale para tipografia, algo monoespaçado e simples.
- *Syntax highlighting* é algo que não considero crucial, tendo em vista que posso utilizar o Gist por enquanto;
- No demais, ser bem fácil de migrar para quem gosta do Hugo, e só quer fazer um blog igual esse;
- Ter o próprio *parser* de Markdown, bem como assegurar que o código-fonte do site sempre tenha o Markdown bem formatado;
- **Ser simples.**

> **Disclaimer**: Esse projeto nunca vai ser um substituto para o Hugo, nem coisa de qualquer outro gerador de sites.

Com certeza novas ideias vão surgir na minha cabeça enquanto vou escrevendo código e pensando melhor nos requerimentos para escrever um ~~bom~~ software, por tanto não se prenda nesse texto, você pode verificar se houve muitas alterações desse esboço lá no repositório do blog no meu GitHub.

## Como imagino a CLI?

A única interface com Darci vai ser a linha de comando, e nesse quesito é sempre bom fazermos algo de qualidade e de boa usabilidade. A minha biblioteca favorita para escrever ferramentas de linha de comando é o [`spectreconsole/spectre.console`](https://github.com/spectreconsole/spectre.console) do meu grande amigo [@firstdrafthell](https://twitter.com/firstdrafthell), que realmente considero melhor que a oficial do dotnet, ainda que não siga muita coisa de como deve ser feita CLIs, mas sim pelas capacidades maravilhosas de simplesmente escrever bonito na tela.

Bom, esse problema é sério, e não consigo contorna-lo por conta de um dos meus requerimentos para tornar Darci uma realidade, não utilizar nada além do `std` do dotnet, e infelizmente, isso inclue todas as bibliotecas que fazem a minha vida mais fácil. E por esse motivo, quero tentar projetar um *parser* de linha de comando bem simples, porém eficaz, que preencha as necessidades que tenho em mente para fazer Darci dar seus primeiros passos:

- `new` ou `n` seguido de um argumento positicional opcional `<name>`: Criar uma nova estrutura de site para Darci;
  - `post <!title|name>`: Um post dentro de um determinado site;
  - `page <!title|name>`: Uma página dentro de um determinado site.
- `build`, `b` ou um *prompt* vazio: Realizar o *release* do site.
  - `--watch` ou `-w`: Ficar de olho no *filesystem* e ir gerando de acordo.

Alguns exemplos seria:

- Criar um novo blog: `darci new "Protozoários Canibais"`;
- Criar uma nova página: `darci new page "Fundadores da Nova Ordem"`;
- Criar um novo blog: `darci new blog "Como criar uma larva explosiva?"`.

Novamente, quero fazer algo muito mais simples que o Hugo, e por consequência, também não vai ser tão poderoso. Mas particularmente acho isso positivo para meu cenário de uso, também por ser um humano dotado de TDAH somado a uma dose de Aspenger.

## Vai ser extensível?

Olha, essa é uma boa pergunta, mas a resposta é provavelmente não. Meu objetivo é fazer algo realmente simples, que tem apenas um propósito também de mesma simplicidade: Gerar blogs para programadores, pesquisadores ou qualquer outra pessoa que goste de escrever coisas e utilizar outra coisa para fazer isso virar um grande blob de HTML bem simples e subir para seu respectivo servidor web ou ter seu *deploy* realizado por um arquivo de configuração.

Tornar muita coisa extensível pode causar problemas em manter a simplicidade, e acaba que esse tudo o que mais tenho interesse, é tornar a ferramenta simples, e sem outras resposabildiades (como ter seu próprio servidor web imbutido ou um complexo sistema de temas).

## Enfim, como vai acontecer o projeto?

Por enquanto vou soltando as novidades por aqui, o repositório em meu GitHub permanece privado até ter de fato uma primeira *release* estável da ideia. Agora, se quiser ajudar a colaborar, me envie uma mensagem lá no Keybase que compartilho um *invite* para ajudar no desenvolvimento de Darci.

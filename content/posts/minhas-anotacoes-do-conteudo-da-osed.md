---
title: "Minhas anotações (comentários e opinião) da OSED"
date: 2021-12-28T13:23:09-03:00
draft: true
tags: ["studing", "research", "certifications"]
author: "jojo"
---

Sou uma pessoa com certa aversão a certificações que efetivamente certificarem que determinada pessoa tem domínio de certas bases de conhecimento. Se você busca conhecimento, sou sincero em dizer que certificações talvez sejam um dos piores caminhos para se seguir, um exemplo prático disso é para o caso da OSED, a quantidade de conteúdo nacional com conteúdo mais desenvolvido, e com suporte nativo ao nosso idioma.

Todavia, se caso seu objetivo for o mesmo que o meu - puramente ter uma muleta para ajudar na carreira e futuras contratações - veio ao lugar certo. Vou deixar aqui todas as minhas anotações de todos os conteúdos da OSED - e sim, irei utilizar essas mesmas anotações durante a certificação, então caso eu não passe, acredito que você não deva se basear aqui para estudar para a prova de aproximadamente 45 horas de duração.

> **Nota**: Essas anotações estão na forma que anotei, por tanto devem haver termos e/ou constatações incorretas, sua atenção é bem vinda na leitura. Caso encontre estes erros, por favor me avise ou faça uma *pull request* no GitHub com a correção, valeu!

# O Começo, o Fim e o Meio.

Ainda que com esse título, não estou ouvindo Raul Seixas, no momento que escrevo esse parágrafo dedico meus momentos de criatividade e capacidade de abstração do material inteiramente a *Straberry Fields Forever*, da banda que não gosto, mas ainda sim, o som me apetece a estudar, *The Beatles*. Trazendo um pouco de contexto pelo meu objetivo de realizar a OSED, digo que é puramente pelo peso da certificação, e um pouco para exercitar atividades que estão fora do meu dia-a-dia em minha *role* atual, que é justamente o oposto disso, tendo em vista que atuo com segurança defensiva.

Quando analisei o conteúdo do curso, tive uma surpresa muito agradável, os vídeos são gravados com uma adorável voz feminina, e não mais aquela voz mecanizada de um senhor que fumou pelo menos desde os 3 anos de idade. Essa nova faze da *Offensive Security* está trazendo bons resultados no que se diz respeito a acessibilidade da plataforma, agora com legendas em todos os vídeos, bem como *timestamps* e descrição do conteúdo (os cursos além de poderem ser baixados, dependendo da modalidade de compra, também podem ser consumidos na plataforma online) quanto na experiência de consumo de conteúdo. Esse estigma de que "coisa de hacker" deve ser difícil é ultrapassada, então considero essa nova cara da empresa, muito positiva.

Sobre o material do curso, de todos os que realizei até o momento para conseguir a OSCE3, foi o maior, e diferente de todo o resto, totalmente focado em bases de conhecimento, ao invés de trazer conteúdos mais espaçados e com pouca profundidade, como é feito na OSWE. A maioria das pessoas que conversei - infelizmente, todas fora do Brasil, quem eu conhecia com OSED ou OSCE3 não se deram nem ao trabalho de responder minha mensagem mesmo tendo-as visualizado - elencaram dentre as três certificações (OSWE, OSEP e OSED), esta como sendo a segunda mais difícil, dessa forma ficando como OSWE, OSED e OSEP na escala de dificuldade.

Para mim é totalmente o contrário, na escala de dificuldade seria OSEP, OSED e OSWE. Sempre trabalhei com desenvolvimento, ainda nas *roles* de segurança da informação, sempre aparecia alguma integração para fazer ou algum *code review* de aplicações web de missão crítica em diversas linguagens, então a OSWE acabou sendo um *piece of pie*, em especial com as maquinas que caíram na minha prova ano passado (como estou começando a escrever esse artigo no final de 2021, vai haver coisas de 2022 e coisas de 2021, e também eu esquecendo qual ano efetivamente estou).

Já na OSEP a coisa muda de figura, nunca trabalhei defendendo ou atacando ambiente corporativo Microsoft, em especial no que se diz respeito ao *Active Directory*, então para mim foi literalmente aprender do zero para me arriscar no conteúdo da OSEP, que no momento que escrevo essa postagem, também estou no processo de certificação, porém mais (muito) distante, focando apenas em ataque e defesa dessas redes corporativas e fazendo minhas anotações em outro lugar. Mas tenho a intensão de fazer como estou agora para OSED quando começar efetivamente a me arriscar nos *labs*.

Ainda sobre os materiais, estes realizei o download do portal da Offensive Security (caso você for antigo aqui, não existe mais o *link* para download no seu e-mail, rola uma notificação, mas o acesso ao material local, é no novo portal da empresa), vão te acompanhar pelos laboratórios e *extra miles*:

- Um PDF, como de costume, gigante, com um total de 604 páginas;
- Gloriosos 8GB de vídeos, separados em 202 arquivos `.mp4` (eles gostam de fazer vídeos de 12 segundos).

> **Nota**: A Offensive Security insiste em enviar os vídeos compactados em `.rar`, o que me causa um certo desconforto.

Olhando rapidamente o conteúdo no PDF, assim como na OSWE, algumas coisas me impressionaram pela profundidade técnica do conteúdo, enquanto outras me deram um pouco de dúvida, especialmente os códigos em Python, escritos de qualquer maneira, sem haver nenhuma menção ao desenvolvimento adequado de *scripts*, por exemplo, o motivo de mesmo sua PoC, conter ao menos um `if __name__`. Muitas pessoas começarão a programar originando-se das certificações da Offensive Security, e começar sem seguir nenhum padrão de desenvolvimento, julgo ser completamente desnecessário. Num PDF de 600 páginas, umas 40 dedicadas ao Python não quebraria a mão, nem cansaria os olhos de ninguém, o mesmo vale para os vídeos.

Um ponto que muita gente criticou esse curso em especial, é justamente o fato de focar apenas em i386. Particularmente não vejo problema algum, adaptar o conteúdo visto para AMD64 não seria nenhum problema, na maioria das técnicas que passei por cima nessa leitura rápida, só vai mudar os nomes dos registradores, seu propósito e objetivos serão os mesmos, só que maiores.

> **Nota**: O curso é focado apenas em Windows, *userland*, havia esquecido de comentar sobre, bom o nome da certificação é auto-explicativo, mas só para não ficar nenhuma ponta solta.

# Fundamentos: i386

Nessa fase do estudo, o foco é os fundamentos. Caso você já venha de SRE (e não me refiro ao termo vinculado à DevOps) acredito que pode pular com folga esse conteúdo. Todavia, como se trata de um fundamento, e estes são o alicerce de qualquer conhecimento, vou passar meticulosamente por essa etapa. Aqui é apresentado o diagrama clássico de como funciona a memória de um processo.

Lá na parte dos endereços mais baixos, temos a *stack*, o próprio *program image*, *process environemnt block* (PEB), *thread environment block* (TEB) por fim a *heap*, separando-a dos amigos mais elevados, temos o BSS, que faz referência ao espaço não inicializado (unitializeed data). Em direção aos endereços de mais altos, temos todas as referências aos módulos do processo, e bem lá no fim, o endereço de memória do Kernel (*Kernel memory space*):

![Layout de memória no NT](https://static.packt-cdn.com/products/9781785282324/graphics/B04315_08_1.jpg)

Na imagem acima, cortesia de um *dork*, cujos créditos vão totalmente a algum livro da Packt, temos uma boa representação disso, só que ao contrário. Dessa forma temos aquela máxima que sempre escutamos nos treinamentos de exploração de binário, a *stack* cresce para baixo. Em inglês o termo fica mais polido, mas acho que a contradição do português nos ajuda a decorar melhor.

Nesse curso da OSED, os *buffer overflows* que serão explorados adiante, são totalmente focados na *stack*, não temos nem um cheiro de *heap*, acredito que esse conteúdo deve ser revisitado e explicado na OSEE, mas aproveitando esse momento, assumo que senti falta de mais foco em nos *chunks* de memória, lembro de uma palestra que o Feroso fez a muitos anos atrás excelente sobre o tema, talvez isso venha nas novas atualizações do curso, que obviamente, serão pagas, já que a Offensive Security faz o melhor emprego possível do capitalismo em suas certificações.

Para simplificar a explicação que foi apresentada no PDF, resumiria o que efetivamente é a *stack*, como sendo um espaço de memória dedicado a executar código do *program image* ou de diversas DLLs, sendo que cada *thread* tem sua própria pilha, para não ter baguncinha na hora do multi-threading, e também por razões óbvias de segurança. Caso você venha da área de desenvolvimento de software, e nunca havia tido contato efetivo com a parte de mais baixo nível, a *stack* funciona igual aquele algoritmo de pilha (zero surpresas né?), isto é, LIFO (*last-in*, *first-out*), exemplificando, em C#:

```csharp
var stack = new Stack<int>(4);
stack.Push(1);
// stack == 1
stack.Push(3);
// stack == 3, 1
stack.Pop();
// stack == 1
stack.Push(3);
// stack == 3, 1
stack.Push(7);
// stack == 7, 3, 1
```

Os nomes dos métodos `Push(<int>)` e `Pop()` também são implementados no Assembly com `push` e `pop`:

```assembly
push 0x29a  ; envia 666 para stack
pop eax     ; eax agora tem 666 como valor
            ; teóricamente, a stack está vazia agora
```

Outra utilização comum para quem brinca com *game hacking* é preservar o valor de determinado registrador pela necessidade de utiliza-lo para alguma coisa:

```assembly
push eax    ; o valor de eax é enviado para stack
            ; algumas coisas acontecem...
pop eax     ; eax agora tem seu valor original
```

# *Calling Conventions*

As convenções para chamadas de funções, por exemplo, de onde vai vir os parâmetros, onde vão estar armazenados e qual a ordem que deve ser seguida, também existe quem vai armazenar o retorno, que no i386 são um caos, variam completamente das implementações individuais dos compiladores ou otimizações e parametrizações manuais por parte do desenvolvedor. No geral, são bem fáceis de serem entendidas ao olhar o *debugger*, no exemplo, considerando que foi importado corretamente `_MessageBoxA@16` e `_ExitProcess@4` posteriormente, seria algo assim:

```assembly
push 0x4                 ; MB_YESNO (hWnd)
push caption             ; caption db "Pepino", 0 (lpText)
push text                ; text    db "Brocolis", 0 (lpCaption)
push 0                   ; uType
call _MessageBoxA@16     ; o retorno fica em eax
push 0
call _ExitProcess@4
```

Caso ficou muito confuso, da para verificar como é a definida a função:

```c++
int MessageBoxA(
  [in, optional] HWND   hWnd,
  [in, optional] LPCSTR lpText,
  [in, optional] LPCSTR lpCaption,
  [in]           UINT   uType
);
```

Dessa forma parametrizamos a função `MessageBoxA` com os valores da *stack*. Para `ExitProcess`, passamos como parâmetro 0, já que este será efetivamente o `uExitCode` da aplicação, assim feito com `push 0`. 

# Registradores de Propósitos Gerais

Como disse, estou seguindo o que está no guia de estudos, e por mais que já exista alguns exemplos de Assembly ai em cima, vou continuar revisando o que é apresentado no curso, que neste momento, é apresentado os registradores de propósito geral: `eax`, `ebx`, `ecx`, `edx`, `esi` e `edi`, sendo descritos pelo material da seguinte forma:

- `eax` (*accumulator*): Operações lógicas e aritméticas;
- `ebx` (*base*): Ponteiro base para endereços de memória (tradução fica ruim, mas no geral é isso ai mesmo);
- `ecx` (counter): Famoso `i`, só que aplicado para loops, shifts e *rotation*;
- `edx` (*data*): Divisão, multiplicação e endereçamento de portas;
- `esi` (*source index*): Ponteiro para endereçar dados de `string` em operações de copia, para a origem;
- `edi` (*destination index*): Ponteiro para endereçar dados de `string` em operações de copia, para o destino;

> **Nota**: Ficou estranho entender isso para mim, ainda que utilize Assembly no dia-a-dia, senti falta de uma explicação mais profunda dos registradores, e efetivamente apresentar todos eles, incluindo outros *sets* de instrução, provavelmente não vai utilizar nada disso em um *shellcode*, mas com certeza verá muito `xmm` do lado dos SIMD, e consequentemente do SSE2, SSE3 e SSE4 durante o *debugging* e engenharia reversa de alguma aplicação. Outro ponto que me incomoda ao ler, é o nome dos registradores em letra maiúscula.

Falando ainda dos registradores, para não passar batido, uma explicação básicas daqueles que é bom só ver mesmo:

- `esp` (*stack pointer*): Como já bem sabido, a *stack* salva coisas nela, e ela vai crescendo e diminuindo conforme vamos consumindo e descartando dados que ali foram indexados, o registrador `esp` tende a sempre ficar endereçado com o último endereço conhecido da *stack*, isto é, o topo da pilha;
- `ebp` (*base pointer*): Assim como saber onde começa a *stack* é fundamental para um programa, conhecer seus limites também é, e perder o *stack frame* de vista é bem fácil quando se olha apenas para ela como um todo. Esse registrador tem como objetivo, similar ao `esp`, registrar o endereço de memória desse espaço, o que é efetivamente o próprio *stack frame*.

Uma pausa rápida para evitar dor de cabeça, quando é realizado um `call`, isto é, a chamada de uma função, precisamos passar algum parâmetro para seu funcionamento, tipicamente falando, esse espaço de memória onde fica os parâmetros de uma função, é referenciado pelo `ebp`. Atualmente alguns compiladores mais parrudos, descartam essa funcionalidade, e delegam essa responsabilidade para o `esp` também, e o `ebp` acaba se juntando a turma dos registradores de propósito geral, mas isso vai variar de acordo com as opções do desenvolvedor e seus gostos para compilar sua aplicação.

- `eip` (*base pointer*): Esse aqui você deve conhecer né? A maioria dos *stack overflows* (ou extravasamento de pilha, caso você for um professor erudita) mais fáceis, envolvem justamente controlar o endereço da próxima instrução que será executada, e como descrito na frase anterior, o `eip` é responsável justamente por apontar à próxima instrução a ser executada durante o fluxo de execução de um programa.

Sobre registradores, a grosso modo, é isso que foi cobrado durante o material, por tanto se você vem da OSCP na modalidade clássica, com o primeiro laboratório de *buffer overflow*, não deve ter notado grandes novidades por aqui, na verdade a OSED entende que o aluno que está aplicando para a prova, já é um certificado OSCP, mas ainda sim revisita todos os tópicos necessários para entender o conteúdo, por tanto, não acho que a OSCP seja um passo necessário para quem quer focar em exploração *userland* dentro do Windows (eu não tenho OSCP, nem pretendo tentar um dia, tem coisa muito melhor por preço mais justo por ai, como por exemplo o pessoal da TCM com a PNPT ou até mesmo a eCPPT).

# Windows Debugger (o janela cinza)

Para quem (assim como eu) utiliza a Preview por bastante tempo, vai ter um pouco de dor de cabeça com o curso, tendo em vista que ele utiliza o clássico, aquela versão mais "quadrada" do *debugger*, acho que é para manter a sensação de l33t quando olha para aquela tela feia. Então sim, UX dentro de ferramentas de segurança é algo muito importante, não é legal ser feio ou feito para pessoas com muito treinamento conseguirem usufruir das capacidades de um software, tire de referência o IDA, lindo e intuitivo. (`</rant>`)

No geral escolher o WinDbg é bem óbvio, ele simplesmente é fodão, e uma das poucas formas que existem de debulhar profundamente no Kernel NT, mas infelizmente o foco do curso é totalmente em *userland*, onde o WinDbg também é um monstro. Caso você venha lá do OllyDbg, ou do seu *fork* mais funcional, *Immunity Debugger*, fique tranquilo, a capacidade de extensão do WinDbg é tão boa quanto (na minha opinião, superior, bem superior). Outro ponto que tende a maioria dos pesquisadores utilizarem o *debugger* oficial do SDK de desenvolvimento do Windows, é justamente o fato de ser 0800.

> **Nota**: Caso você nunca tenha fuçado em um *debugger* ou nem conheça o que ele faz - da para imaginar como um Burp Suite da aplicação em direção ao CPU, similar entre a requisição de uma aplicação em direção ao seu backend -, recomendo bastante dar uma olhada na *talk* do Caloni no MbConf, e também no curso de WinDbg da PentesterAcademy (ele é velinho, na época o Windows 10 estava sendo lançado, mas é muito bom para decorar as *hotkeys* do *debugger*, bem como os comandos principais).

Uma observação importante, nessa parte dei uma boa pausa para estudar mais dessa versão do Windows Debugger, tendo em vista que só tinha utilizado ela umas duas vezes na vida, o propósito e os comandos são iguais, porém a localização das coisas que não conhecia os atalhos eram diferentes, e algumas APIs não existiam, após enviar um e-mail para o suporte ao aluno, recebi o famoso "we don't care" da Offensive Security, então é bom utilizar exatamente a pilha de softwares que eles esperam você utilizar. No geral, nada de Preview.

Se você gosta de nostalgia, e da época onde Windows Forms Applications eram realmente boas, o WinDbg nessa versão, é a melhor personificação que você pode imaginar, um exemplo claro disso, é a forma que o Workspace funciona, baseado em mais Forms ligados a um pai (lembra do `formB.MdiParent = formA;`?), o que é um saco para utilizar em mais de um monitor ou em tarefas mais complexas de *debugging*. Eu demorei para acostumar, todavia, caso tiver me acompanhando só pelo texto, a telinha dele seria essa aqui:

![WinDbg em toda sua glória](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/images/debuglab-image-winddbg-hh.png)

Ainda que na foto acima, seja de alguém *debuggando* (vou usar bastante essa palavra que não existe) o Kernel em uma VM, o Workspace do WinDbg é exatamente assim, e recomendo você ter uma ótima familiarização com ele. Na documentação oficial na Microsoft, mostra-se alguns exercícios utilizando o `notepad.exe`, porém utilizando a versão Preview, então para "converter" para essa, vou reproduzir os mesmos passos, só que para essa versão do WinDbg.

Para anexar o processo  em execução do `notepad.exe`, na interface do WinDbg, podemos ir em File, e na sequência em "Attach to a Process", ou sendo prático, apertando <kbd>F6</kbd>:



![WinDbg e `notepad.exe`](C:\Users\jonas\Projects\exception\content\static\image-20211230130101840.png)

Algo que esqueci de comentar, como estou no meu sistema de *host*, e ele é um Windows 10 (sim, não uso Windows 11 ainda) em 64 bits, o Notepad, também o faz por ser da mesma arquitetura nativa, por tanto estou utilizando a versão de 64 bits do WinDbg. Acredito que a primeira coisa que notou ao utilizar o WinDbg, foi que ao anexar ele em um processo em execução, o mesmo é pausado, por tanto o Notepad nesse momento está hibernando (para continuar sua execução, podemos enviar um `g` ali nos comandos).

> **Nota**: Como estamos em ambiente Microsoft, as cosias tendem a ser bem fáceis de serem amistosas, tendo em vista que estamos no mesmo ecossistema, por tanto se precisar limpar a tela do seu *prompt*, é com um saudoso `.cls`.

> **Dica**: Essa versão do WinDbg é um terror para procurar determinado processo em um Windows cheio de coisas rodando, talvez você prefira encontrar o processo que deseja anexar ao *debugger* com ajuda do PowerShell: `(Get-Process notepad).Id`.

Vamos brincar um pouco com os *breakpoints* do WinDbg, uma das APIs chamadas pelo Notepad é a `ZwCreateFile`:

```c++
NTSYSAPI NTSTATUS ZwCreateFile(
  [out]          PHANDLE            FileHandle,
  [in]           ACCESS_MASK        DesiredAccess,
  [in]           POBJECT_ATTRIBUTES ObjectAttributes,
  [out]          PIO_STATUS_BLOCK   IoStatusBlock,
  [in, optional] PLARGE_INTEGER     AllocationSize,
  [in]           ULONG              FileAttributes,
  [in]           ULONG              ShareAccess,
  [in]           ULONG              CreateDisposition,
  [in]           ULONG              CreateOptions,
  [in, optional] PVOID              EaBuffer,
  [in]           ULONG              EaLength
);
```

Que basicamente é uma rotina para criação de um arquivo, ou abertura de um existente, ela tem uma cacetada de parâmetros como podemos ver, e seria legal olhar para todos esses parâmetros lá dentro da *stack*, para fazer isso, o WinDbg fornece uma API muito simples via os comandos que passamos naquela `TextBox`, sendo um dos que mais vão ser utilizados o `bp`, `bu` e `bm`.

> **Nota**: Caso tenha dado um `g`, e não consiga mais inserir nada de comandos, aperta um <kbd>CTRL</kbd>+<kbd>BREAK</kbd> que vai dar tudo certo.

Esses três comandos nos permitem colocar alguns *software brakepoins* (sim, também existem *hardware brakepoints*), caso deseja entender mais profundamente como funcionam, a [MSDN](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/commands?redirectedfrom=MSDN) é o lugar perfeito para isso. Algo que vale a pena citar, é que diversos comandos do WinDbg operam de forma totalmente diferente quando se está em Kernel e *userland*, para nós, nos interessa penas um *breakpoint* na chamada da `ZwCreateFile`, que podemos realizar da seguinte forma:

```bash
bu ntdll!ZwWriteFile
```

Na teoria, esse comando não é para imprimir nada na *console*, se caso der tudo certo, você pode verificar todos os seus *software breakpoints* utilizando `bl`, e tendo a validação de que o *breakpoint* para `ZwWriteFile` está ali na espreita de olho:

```l
0:001> bl
     0 e Disable Clear  00007ffc`9bb8ce60     0001 (0001)  0:**** ntdll!NtWriteFile
```

Para conseguir ver acontecer, vamos usar o `g`, e realizar alguma operação de IO com o Notepad:

```
Breakpoint 0 hit
ntdll!NtWriteFile:
00007ffc`9bb8ce60 4c8bd1          mov     r10,rcx
```

E teremos o *hit* na chamada da função. Para olhar mais nos detalhes, podemos utilizar o `uf`, que traz uma representação em Assembly dessa função na memória:

```
ntdll!NtWriteFile:
00007ffc`9bb8ce60 4c8bd1          mov     r10,rcx
00007ffc`9bb8ce63 b808000000      mov     eax,8
00007ffc`9bb8ce68 f604250803fe7f01 test    byte ptr [SharedUserData+0x308 (00000000`7ffe0308)],1
00007ffc`9bb8ce70 7503            jne     ntdll!NtWriteFile+0x15 (00007ffc`9bb8ce75)  Branch

ntdll!NtWriteFile+0x12:
00007ffc`9bb8ce72 0f05            syscall
00007ffc`9bb8ce74 c3              ret

ntdll!NtWriteFile+0x15:
00007ffc`9bb8ce75 cd2e            int     2Eh
00007ffc`9bb8ce77 c3              ret
```


---
title: "Code injections e uns modding em um TD"
date: 2021-12-18T23:51:11-03:00
draft: false
tags: ["game-pwning", "research", "csharp"]
author: "jojo"
---

Sou um fã de jogos do tipo *tower defense* desde criança, e sempre curti ownar um ou outro para conseguir chegar em *waves* ridiculamente altas, ou só pelo *memento mori* que em determinado ponto, ainda que tenha um modding pesado no game, ainda acabo perdendo para *waves* que literalmente fazem meu computador dizer adeus a toda sua *heap*.

# O *moddado* da vez

Escolher um TD (caso você não conheça, é o que significa *tower defense*, lembra de todos aqueles jogos que terminam em TD e você nunca entendeu o motivo, agora sabe!) de qualidade hoje em dia é muito difícil, para minha tristeza e de diversos jogadores mais saudosistas, esse é um dos gêneros de jogos que estão totalmente sem popularidade, ou na maioria das vezes é encarado somente como um *minigame* chato dentro de algum jogo maior.

Sem sombra de dúvidas o GOAT dos TDs sempre vai ser os primórdios de Warcraft 2 (esse não tinha algo dedicado ao *Element*, mas tinha umas paradas doidas) e Warcraft 3, que efetivamente marcou o nome *Element*, com os mapas montados para serem jogados como TD, perdi facilmente centenas de horas da minha infância e adolescência neles (atualmente não perco mais, considero investimento). Felizmente no mundo estranho em que vivemos atualmente, ainda disponhamos de um bom lançamento para somar na cena de jogos TD: A continuação de *Element TD*, com um título para lá de óbvio, *Element TD 2*.

> **Nota**: O jogo utiliza Unity, e também o famoso (e chato de mais) `il2cpp`, que para um jogo sem o devido cuidado na hora de fazer o código intermediário da CLR virar essa gambiarra grotesca de nativo, é relativamente fácil de ser modificado. Ainda asim, sou a favor de moddings mais amigos do binário, isso é, que efetivamente não o altera, então só vamos fuçar em estruturas de memória e alguns padrões de estruturas.
>
> Utilizar algum framework pronto para modding em Unity é a melhor alternativa sempre, mas isso envolve chamar o BepInEx e alterar algumas configurações gráficas (já que o `
` não tem o mesmo suporte para fazer o *rendering*, por exemplo, de um IMGUI) além da própria estrutura dos arquivos e binários do jogo.

Quem jogou os clássicos mapas dos jogos primordiais, não deve se estranhar em notar uma forte semelhança com o que vemos em *Element TD 2*, lançado no dia 2 de Abril desse ano, ao menos oficialmente, sua versão de *early access* esteve no ar desde o começo do ano passado. Uma observação interessante, caso você conheça as rinhas de galinha japonesas legalizadas, popularizado sobre o nome Pokémon, vai ter uma facilidade para jogar esse game, por conta das fraquezas de certos elementos contra outros de tipos distintos.

# Jogando na moral (ou quase)

Para facilitar um pouco a brincadeira, vou iniciar no modo de sobrevivência na dificuldade normal, para podermos analisar com calma o que nos aguarda dentro do jogo. Não vou fazer nenhuma bizarrice em engenharia reversa, o jogo é *indie*, ainda que tenha uma comunidade fiel e antiga por de trás, então caso queira brincar mais a fundo no que foi implementado dentro da *engine*, compre-o, e ainda por cima ajude a crescer conteúdo por aí.

O mapa é bem genérico para os padrões de TD, com algumas novidades, além de poder *combar* certas skills e torres, o jogo coloca uma dificuldade (que é **muito** alta, porém artificial) elevada ao revelar certos caminhos até à base em *blind spots* no mapa. Ainda assim é bem divertido para quem gosta de uma boa lógica e tem uma mão rápida no teclado, mouse e uma boa memória nas *hotkeys*. Enfim, eis uma breve tela do jogo em ação:

![O jogo em toda sua glória](/static/image-20211219000854923.png)

Existem bons elementos na UI que já trazem uma ideia de como funciona o `Update()` do game, bem como a ordem das cenas ao serem invocadas e manipuladas para o bem maior do jogador. De cara, podemos enumerar uma série de coisas interessantes que podem ser feitas dentro do jogo:

- Patrimônio infinito, ou parametrizável;
- Vidas infinitas;
- Caminho entre os portais;
- Velocidade de locomoção do player;
- *Cooldown* de construção, destruição e evolução das torres; e
- Movimento e vida dos *minions* dentro do mapa.

Devem haver outras centenas de coisas que devem ser triviais para serem feitas, mas vamos nos focar em algumas da lista acima. Para começar, vamos dar uma olhada em como o *gold* (que é diferente do patrimônio) funciona dentro do game. Existem alguns bons *opcodes* que compartilham o acesso a mesma chamada de função dentro do `Update()`, entretanto, podemos utilizar esse comportamento de acesso ao endereço de memória que corresponde ao *gold* para literalmente mapear qualquer outra estrutura nesse caso específico do *Elements TD 2*.

Mas focando apenas no que ocorre quando uma determinada função ocorre, que é exatamente aquela dentro do *loop* do jogo:

```assembly
movss xmm0,[rax+104]
call GameAssembly.dll+2052F0
cvttss2si eax,xmm0
add rsp,28
ret
```

Acho que a única *capetisse* que tem ai no código do *disassembler* é literalmente a instrução `cvttss2si` (otimizações de compiladores modernos geram coisas estranhas, mas que teoricamente são performáticas, nesse caso acaba sendo a única opção), no contexto geral, não há muito interesse nessa função, e mexer nela seria uma péssima ideia, caso você esteja curioso sobre a empregabilidade dela, de uma verificada no seu manual favorito de IA64.

No final das contas, a única coisa que nos interessa é o endereço de memória de `rax`, que pode ser utilizado em seu *base address* como mapa para diversos ponteiros de memórias, e consequentemente diversas estruturas de memórias bem interessantes do jogo. Ainda assim, para deixar as coisas mais simples, tendo em vista que diversos endereços de memória são visitados pela instrução `movss xmm0,[rax+104]`, encontrar efetivamente uma caracteristica em meio aquelas que acessam o endereço que nos interessa, o *gold* propriamente dito possa ser um pé no saco.

Por fim podemos procurar por *opcodes* que definitivamente escrevem no endereço de memória correspondente ao *gold*, dessa forma, a probabilidade daquela função ser reutilizada em outras estruturas da *engine* do jogo é menor - mas perceba que não é nula, mais para frente vamos notar que efetivamente ocorre alguns problemas em outras estruturas de memória quando realizarmos o *patch* na memória do jogo -, e por tanto mais tranquila de se alterar sem ter grandes impactos na estabilidade do processo. Que neste caso seria:

```assembly
; GameAssembly.dll+428264
movss [rcx+104],xmm0
```

Na instrução acima (perceba que por se tratar de um jogo escrito em Unity, raramente vai aparecer algo fora do módulo `GameAssembly.dll`) temos no endereço de memória contido em `rcx` acrescido do *offset* de 0x104 é a quantidade total de *gold* que temos no momento do jogo. O mesmo *offset* se repete na função dentro do `Update()`, só que naquele momento, o endereço de memória está em algum lugar de `rax`.

Pois bem, podemos fazer algumas coisas para conseguir substituir o valor de `xmm0` para algo ridiculamente grande, porém não é possível simplesmente ter um valor com casas flutuantes e fazer uma atribuição direta ao `xmm0` como `movss xmm0,(float)9999`. Primeiro torna-se necessário inicializar o endereço de memória que vai representar o quanto haverá de *gold* em `xmm0` ao ser transportado para o endereço de memória `[rcx+104]`:

```assembly
[ENABLE]

alloc(newmem,2048,"GameAssembly.dll"+428044) 
label(returnhere)
label(originalcode)
label(exit)

alloc(_gold,8,"GameAssembly.dll"+428044)
registersymbol(_gold)

_gold:
  dd (float)1234567890

newmem:
  movss xmm0,[_gold]

originalcode:
  movss [rcx+00000104],xmm0

exit:
  jmp returnhere

"GameAssembly.dll"+428044:
  jmp newmem
  nop 3

returnhere:
 
[DISABLE]

dealloc(newmem)
dealloc(_gold)
unregistersymbol(_gold)

"GameAssembly.dll"+428044:
  movss [rcx+00000104],xmm0
```

Como temos *gold* sendo representado na memória como `float`, precisamos alocar somente o tamanho correspondente em uma nova região de memória próxima da mesma altura da função dentro do módulo `GameAssembly.dll`, por tanto alocamos `_gold` com 8 bytes de tamanho. Após inicializado podemos utilizar o *label* `newmem`, que representa assim como `_base`, um canto na memória que temos todas as permissões possíveis, por tanto podemos sobrescrever o endereço de memória com o novo valor de `xmm0`, que é literalmente 1234567890.

Mas dai começa alguns problemas, quando vendemos uma torre, ou cancelamos o upgrade de uma durante o jogo, é outra função que sobrescreve o endereço de memória, o que implica que o *patch* realizado na injeção de código não mais terá efeito, tendo em vista que este é realizado em uma instrução completamente diferente.

# O começo do meio

Assim acontece o problema, diversas instruções atingem o mesmo endereço de memória, todas seguem o mesmo padrão, isto é, compartilham do mesmo *offset* de 0x104, todavia em registradores distintos. Uma das formas mais efetivas de contornar esse tipo de problema em diversos jogos, é encontrar a hierarquia de chamada das funções que realização operações de escrita de maneira explicita no endereço de memória, ou que realize leitura, mas tenha permissão para tal.

Para isso vamos dar uma olhada no primeiro *opcode* que bate no endereço de memória, na altura de `GameAssembly.dll+8DBFBA`:

```assembly
GameAssembly.dll+8DBFB5 - 48 85 C0              ; test rax,rax
GameAssembly.dll+8DBFB8 - 74 16                 ; je GameAssembly.dll+8DBFD0
GameAssembly.dll+8DBFBA - F3 0F10 80 04010000   ; movss xmm0,[rax+00000104]
GameAssembly.dll+8DBFC2 - E8 299392FF           ; call GameAssembly.dll+2052F0
GameAssembly.dll+8DBFC7 - F3 0F2C C0            ; cvttss2si eax,xmm0
GameAssembly.dll+8DBFCB - 48 83 C4 28           ; add rsp,28 { 40 }
GameAssembly.dll+8DBFCF - C3                    ; ret 
GameAssembly.dll+8DBFD0 - E8 5B0592FF           ; call GameAssembly.dll+1FC530
```

Ainda que o que nos interesse (`movss xmm0,[rax+00000104]`) esteja dentro da condição do resultado de `test rax,rax` (que no geral, basicamente testa se `rax` == 0), podemos nos arriscar - talvez um pouco de forma otimista - sem analisar o que efetivamente significa esse teste. Se fosse para chutar, diria que é para validar se a cena de jogo efetivamente está carregada e disponível, uma *flag* de validação das funções de *runtime* da `Update()` de aplicar certos bônus no caso de jogar com a opção de juros ativa. No geral, vamos ter um CEA para possibilitar o acesso ao endereço de memória antes de todo mundo da seguinte forma:

```assembly
[ENABLE]

alloc(newmem,2048,"GameAssembly.dll"+8DBFBA) 
label(returnhere)
label(originalcode)
label(exit)

globalalloc(_base,8,"GameAssembly.dll"+8DBFBA)

_base:
 dd 0

newmem:
  mov [_base],rax

originalcode:
  movss xmm0,[rax+00000104]

exit:
  jmp returnhere

"GameAssembly.dll"+8DBFBA:
  jmp newmem
  nop 3
returnhere:

[DISABLE]

dealloc(_base)
unregistersymbol(_base)

dealloc(newmem)
"GameAssembly.dll"+8DBFBA:
  movss xmm0,[rax+00000104]
```

Nessa versão, temos finalmente uma instrução que acessa diretamente ao ponteiro de memória (desde que você lembre-se de acrescer o *offset* correto de 0x104), e finalmente possibilitar a edição do valor correspondente ao *gold* sem maiores dores de cabeça, e desde que não haja mudanças nesse módulo em especial (o que é improvável, já que o jogo é escrito juntamente da Unity), a injeção de código nessa altura, sempre vai resultar no endereço de memória cujo ponteiro mais o *offset* é o *gold*.

> **Nota**: Se você dissecar adequadamente essa estrutura de memória que vem do endereço base de `rax`, vai encontrar tudo aquilo que listei no item anterior, de vida infinita, até a velocidade dos *minions*.

--------------------------------------------------------------------------------
# Introdução à Linguagem Assembly
A linguagem Assembly é uma linguagem de programação de baixo nível que atua como uma interface legível para humanos do código de máquina binário compreendido pelo processador. Cada arquitetura de processador (como x86 ou ARM) possui seu próprio conjunto de instruções e mnemônicos específicos.[¹](https://www.uobabylon.edu.iq/eprints/publication_1_26408_35.pdf)
A minha intenção em aprender Assembly, é para entender de maneira mais aprofundada, a maneira de como os computadores "lêem" e interpretam os códigos e scripts, pois isso ajuda em quase todas as áreas de computação.

1. Conceitos Fundamentais
Relação Um-para-Um: Diferente de linguagens de alto nível (C++, Java 😂), onde uma linha de código se expande em várias instruções, no Assembly existe geralmente uma correspondência direta de um-para-um com as instruções de máquina.
Falta de Portabilidade: O código Assembly é acoplado ao hardware. Um programa escrito para x86 não funcionará em um processador ARM sem emulação.
Controle Total: Ela oferece acesso direto ao hardware, memória e registros da CPU, permitindo otimizações extremas de velocidade e tamanho.

2. Elementos da Arquitetura
Para programar em Assembly, é essencial entender os componentes internos da CPU:
Registradores: São pequenas locações de armazenamento de altíssima velocidade dentro da própria CPU. Exemplos em x86 incluem:
EAX/RAX: Acumulador para operações aritméticas e valores de retorno.
EBX/RBX: Registrador base, usado como ponteiro para dados.
ECX/RCX: Frequentemente usado como contador em loops.
ESP/RSP: Ponteiro que indica o topo da pilha (Stack).
Memória e Segmentos: Os programas são divididos em seções lógicas:
.text: Onde reside o código executável.
.data: Para variáveis globais inicializadas.
.bss: Espaço para variáveis não inicializadas.
A Pilha (Stack): Uma região de memória que funciona no sistema LIFO (Last-In, First-Out). É usada para armazenar dados temporários, endereços de retorno e parâmetros de funções.
[²](https://gpfault.net/posts/asm-tut-0.txt.html)[³](https://cs.lmu.edu/~ray/notes/nasmtutorial/)

3. Sintaxe e Instruções
Uma instrução típica segue o formato: [rótulo:] mnemônico [operandos] [;comentário].
Principais Instruções:
MOV: Copia o conteúdo do segundo operando (origem) para o primeiro (destino).
ADD / SUB: Realiza adição ou subtração aritmética entre operandos.
CMP: Compara dois valores subtraindo-os mentalmente e atualizando as flags (indicadores de status) da CPU.
JMP / JZ / JNZ: Instruções de salto que alteram o fluxo do programa (incondicionalmente ou baseadas em condições como "salte se for zero").
CALL / RET: Usadas para chamar sub-rotinas (procedimentos) e retornar delas. [³](https://www.cs.princeton.edu/courses/archive/fall20/cos318/precepts/x86_assembly_tutorial.pdf)[⁴](https://www.uobabylon.edu.iq/eprints/publication_1_26408_35.pdf)
4. O Ciclo de Desenvolvimento
Editor de Texto: Escreve-se o código fonte (ex: programa.asm).
Assembler (Montador): Utilitário (como NASM ou MASM) que traduz o texto para código de máquina em um arquivo objeto.
Linker (Ligador): Combina um ou mais arquivos objeto e bibliotecas em um arquivo executável final.
Debugger (Depurador): Ferramenta (como o GDB) essencial para rastrear a execução linha a linha e examinar o estado dos registradores.[⁵](http://www.nlpir.org/wordpress/wp-content/uploads/2019/03/Assembly.Language.For_.x86.Processors.Kip_.R..Irvine..6ed.Prentice.Hall_.2011www.xuexi111.com_.pdf)
--------------------------------------------------------------------------------
## Conceitos Importantes
A **Pilha (Stack)** é uma região da memória dedicada ao armazenamento de dados temporários, organizada de acordo com o princípio LIFO (Last-In, First-Out — o último a entrar é o primeiro a sair). Para gerenciar variáveis locais, a CPU utiliza uma estrutura chamada Quadro de Pilha (Stack Frame ou Activation Record).
Aqui está o funcionamento detalhado de como as variáveis locais são armazenadas e acessadas:
1. Dinâmica da Pilha e Registradores Chave
Diferente da memória comum, a pilha cresce para baixo (em direção a endereços de memória mais baixos). Dois registradores são fundamentais:
ESP/RSP (Stack Pointer): Aponta sempre para o topo atual da pilha (o endereço mais baixo ocupado).
EBP/RBP (Base Pointer): Atua como um ponto de referência estável dentro de uma função para encontrar parâmetros e variáveis locais.
2. Criação do Quadro de Pilha e Alocação
Quando uma função (sub-rotina) começa a ser executada, ocorre um processo chamado Prólogo, que prepara o espaço para as variáveis locais:
Preservação do Base Pointer: O valor antigo de EBP (da função que chamou) é salvo na pilha (PUSH EBP).
Definição da nova base: O valor atual de ESP é copiado para EBP (MOV EBP, ESP). Agora, EBP aponta para a base do novo quadro.
Reserva de Espaço: Para alocar variáveis locais, o processador simplesmente decrementa o ESP pelo número de bytes necessários. Por exemplo, SUB ESP, 8 reserva espaço para duas variáveis de 4 bytes (32 bits) cada.
3. Acesso às Variáveis
Uma vez que o espaço foi reservado abaixo de EBP, as variáveis locais são acessadas através de deslocamentos (offsets) negativos em relação ao Base Pointer:
A primeira variável local estará em [EBP - 4].
A segunda em [EBP - 8], e assim por diante. (Nota: Parâmetros passados para a função ficam em deslocamentos positivos, como [EBP + 8]).
4. Desalocação e Encerramento
Ao final da função, no chamado Epílogo, o espaço deve ser liberado para evitar corrupção da pilha:
O ESP é restaurado para o valor de EBP (MOV ESP, EBP), o que efetivamente "esquece" as variáveis locais.
O EBP original é recuperado da pilha (POP EBP).
A instrução RET retoma a execução no endereço de retorno salvo anteriormente.
Atalhos e Diretivas
ENTER e LEAVE: São instruções que automatizam o prólogo e o epílogo. ENTER salva EBP e reserva espaço, enquanto LEAVE restaura os ponteiros de pilha e base.
Diretiva LOCAL: Em montadores como o MASM, a diretiva LOCAL permite declarar variáveis locais por nome, cuidando automaticamente dos cálculos de deslocamento na pilha.
--------------------------------------------------------------------------------
# Recomendações:
[Assembly na Prática](https://www.youtube.com/playlist?list=PLxTkH01AauxRm0LFLlOA9RR5O6hBLqBtC) - Excelente didática do Professor Fernando, sem pressa para pular o assunto, ele te faz aprender, não só decorar, videos longos e bem explicativos, excelente para quem quer realmente aprender a lógica em sí.
[x86 Assembly Guide](https://www.cs.virginia.edu/~evans/cs216/guides/x86.html) - Um guia em inglês, porém bem técnico e ao mesmo tempo compreensível, o que é meio difícil de se ver principalmente quando o assunto é Assembly, ajuda bastante na parte de compreensão técnica.

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

Os **registradores** são locais de armazenamento de altíssima velocidade situados diretamente dentro da Unidade Central de Processamento (CPU). Eles funcionam como uma memória de rascunho para o processador, permitindo que os dados sejam acessados quase instantaneamente, de forma muito mais rápida do que na memória RAM, que depende do barramento do sistema para transferir informações.
Abaixo, detalho as principais categorias e funções dos registradores na arquitetura x86 e x86-64:
1. Registradores de Propósito Geral (GPRs)
Originalmente, a arquitetura x86 possuía 8 registradores de 32 bits. Com a evolução para 64 bits (x86-64), esse número dobrou para 16 registradores de 64 bits (RAX, RBX, RCX, RDX, RSI, RDI, RBP, RSP e os novos R8 até R15).
Embora a maioria possa ser usada para diversas tarefas, cada um tem uma finalidade clássica:
RAX (Acumulador): Usado automaticamente em operações aritméticas (como multiplicação e divisão) e para armazenar o valor de retorno de funções.
RBX (Base): Frequentemente utilizado como ponteiro para endereços base na memória RAM.
RCX (Contador): Usado automaticamente pela CPU como contador em loops e operações de repetição de strings.
RDX (Dados): Auxilia em operações complexas de multiplicação e divisão e em operações de entrada/saída (I/O).
RSI / RDI (Índice de Origem/Destino): Utilizados em instruções de movimentação de blocos de dados (strings).
RSP (Stack Pointer): Aponta para o endereço do topo atual da pilha (stack) na memória.
RBP (Base Pointer): Usado para referenciar parâmetros de função e variáveis locais dentro de um quadro de pilha.
2. Hierarquia e Subdivisões
Uma característica fundamental dos registradores x86 é a sua capacidade de serem acessados em fatias menores para manter a compatibilidade retroativa:
RAX é o registrador completo de 64 bits.
EAX representa os 32 bits inferiores de RAX.
AX representa os 16 bits inferiores de EAX.
AH e AL são as partes Alta (High) e Baixa (Low) de 8 bits que compõem o AX.
3. Registradores de Status e Controle
Existem registradores específicos que coordenam a execução do programa:
Instruction Pointer (EIP / RIP): Contém o endereço da próxima instrução a ser executada pelo processador. Ele é atualizado automaticamente conforme as instruções são processadas ou alteradas por saltos (JMP) e chamadas (CALL).
FLAGS (EFLAGS / RFLAGS): É um registrador composto por bits individuais (flags) que indicam o estado resultante de operações anteriores. Por exemplo:
Zero Flag (ZF): Ativada se o resultado da última operação for zero.
Carry Flag (CF): Ativada se houve estouro em uma operação aritmética sem sinal.
Sign Flag (SF): Indica se o resultado de uma operação foi negativo.
4. Registradores de Segmento
Em modos de operação como o Modo Real, esses registradores de 16 bits (CS, DS, SS, ES, FS, GS) são essenciais para calcular endereços físicos de memória, apontando para as bases de diferentes áreas (código, dados e pilha). No modo de 64 bits moderno, o uso de segmentação é amplamente substituído por um modelo de memória "plano".
5. Registradores Especializados
Para tarefas de alto desempenho, existem outros grupos:
FPU (Floating Point Unit): Oito registradores de 80 bits (ST0 a ST7) dedicados a cálculos matemáticos complexos com números decimais.
SIMD (MMX / XMM / YMM): Registradores largos (de 64 até 512 bits) usados para processar múltiplos dados simultaneamente em aplicações multimídia e científicas.

Os **mnemônicos** são a base da linguagem Assembly, servindo como uma representação textual e amigável para os seres humanos das instruções binárias (opcodes) que a CPU executa. Eles funcionam como abreviações que facilitam a memorização e a escrita de programas, transformando sequências complexas de bits em comandos como MOV, ADD e SUB.
Abaixo, detalho esses três mnemônicos fundamentais e como eles se inserem nos conceitos básicos da arquitetura x86:
1. O Mnemônico MOV (Movimentação de Dados)
A instrução MOV é classificada como uma instrução de transferência de dados e é utilizada em praticamente todos os programas para copiar informações de um local para outro.
Funcionamento: Ela copia o conteúdo do segundo operando (origem) para o primeiro operando (destino). Após a execução, o destino é alterado, mas o valor de origem permanece o mesmo.
Sintaxe e Regras: O formato padrão é MOV destino, origem. Uma regra crítica é que ambos os operandos devem ter o mesmo tamanho (por exemplo, mover um byte para um registrador de byte).
Restrições de Memória: Uma limitação severa da arquitetura x86 é que não se pode mover dados diretamente de uma posição de memória para outra em uma única instrução; é necessário usar um registrador como intermediário. Além disso, o registrador de ponteiro de instrução (IP/EIP/RIP) nunca pode ser o destino de um MOV.
2. Os Mnemônicos Aritméticos: ADD e SUB
As instruções ADD (Adição) e SUB (Subtração) são a base para o processamento matemático de inteiros na CPU.
ADD (Adição): Soma o operando de origem ao operando de destino e armazena o resultado no destino. Exemplo: ADD EAX, 10 adiciona 10 ao valor atual de EAX.
SUB (Subtração): Subtrai o operando de origem do destino e armazena o resultado no destino. Exemplo: SUB AL, AH subtrai o valor de AH do valor em AL.
Lógica Interna: Curiosamente, a CPU pode implementar a subtração internamente como uma combinação de negação e adição, utilizando a notação de complemento de dois para números negativos.
3. O Contexto das Flags de Status
Diferente do MOV, que geralmente não altera o estado dos sinalizadores da CPU, as instruções ADD e SUB afetam diretamente o registrador de FLAGS (como Zero, Carry, Sign e Overflow).
Importância: Essas alterações nas flags são cruciais para o controle de fluxo do programa. Por exemplo, uma instrução SUB que resulta em zero ativará a Zero Flag (ZF), o que permite que uma instrução de salto posterior (como JZ - salto se for zero) tome uma decisão lógica.
Estrutura Geral e Ciclo de Execução
No contexto maior dos conceitos básicos, cada uma dessas instruções segue uma sintaxe rígida: mnemônico destino, origem [;comentário]. Para serem executadas, elas passam pelo Ciclo de Instrução da CPU:
Fetch (Busca): A CPU busca a instrução na memória usando o ponteiro de instrução.
Decode (Decodificação): A unidade de controle traduz o mnemônico e identifica a operação e os operandos.
Execute (Execução): A Unidade Lógica e Aritmética (ALU) realiza a tarefa (movimentar, somar ou subtrair) e atualiza as flags de status.
Esses mnemônicos operam sobre três tipos principais de operandos: registradores (memória interna ultra-rápida), memória (endereços RAM) e valores imediatos (constantes numéricas fixas no código).

As **Interrupções de software**, como a 'int 80h', funcionam como chamadas a funções do sistema operacional ou da BIOS, permitindo que um programa solicite serviços de baixo nível de forma controlada. Elas atuam como uma ponte entre o código do usuário e o kernel, suspendendo temporariamente a execução do programa atual para realizar uma tarefa específica.
Aqui está o funcionamento detalhado desse mecanismo:
1. O Mecanismo da Instrução INT
Quando o processador executa a instrução INT <valor>, ele segue uma sequência rígida de passos para garantir que o sistema possa retornar ao estado original após o processamento:
Salvamento de Estado: O processador empilha automaticamente o registrador de flags, o CS (Code Segment) e o IP/EIP (Instruction Pointer) na pilha (stack).
Desativação de Interrupções: A flag de interrupção (IF) é zerada (IF = 0), desativando outras interrupções de hardware momentaneamente para evitar conflitos.
Desvio de Fluxo: O controle é transferido para o procedimento de interrupção (handler) correspondente.
2. A Tabela de Vetores de Interrupção (IVT)
Para saber para onde desviar o fluxo, a CPU consulta a Interrupt Vector Table (IVT), localizada nos primeiros 1024 bytes da memória RAM.
Cada entrada na tabela possui 4 bytes, contendo o endereço (segmento e deslocamento) do manipulador da interrupção.
O processador localiza o endereço correto multiplicando o número da interrupção por 4 (ex: para int 10h, ele busca na posição 10h * 4 = 40h).
3. O Caso Específico da int 80h no Linux
No Linux (especialmente em sistemas de 32 bits), a int 80h é a porta de entrada para as chamadas de sistema (syscalls)
. Diferente de uma interrupção de BIOS simples, ela exige parâmetros específicos nos registradores para identificar qual serviço do kernel deve ser executado:
EAX: Contém o número da syscall. Por exemplo, o valor 1 representa sys_exit e o valor 4 representa sys_write.
EBX, ECX, EDX...: São usados para passar os argumentos da função.
Exemplo: Para escrever um texto (sys_write), EBX recebe o descritor do arquivo (1 para a saída padrão), ECX aponta para a mensagem e EDX contém o tamanho da mensagem.
4. Retorno da Interrupção (IRET)
Após o manipulador de interrupção (ISR - Interrupt Service Routine) concluir sua tarefa, ele termina com a instrução IRET. Esta instrução realiza o processo inverso da INT:
Ela desempilha o IP, o CS e o registrador de flags.
Isso faz com que o processador retome a execução exatamente na instrução seguinte àquela que disparou a interrupção no programa original.
Nota de Compatibilidade: Embora a int 80h seja o padrão histórico no Linux x86, em arquiteturas modernas de 64 bits (x86-64), a instrução SYSCALL é utilizada no lugar da int 80h por ser mais eficiente. Em sistemas Windows modernos, o acesso direto a interrupções de hardware ou BIOS via software é geralmente restrito para garantir a estabilidade do sistema.
--------------------------------------------------------------------------------
#Prompts recomendados:
1. Primeiramente, você deve informar ao NotebookLM, o seu nível de conhecimento em Assembly, e em programação em geral, isso ajuda a IA a se flexibilizar em seus retornos. (Sou iniciante-intermediário em programação, sei alguns conceitos básicos como OOP e APIs, porém quero aprender sobre Assembly, não sei nada sobre essa linguagem, poderia explicar de uma maneira para iniciantes ?)
2. Se você tem conhecimento de outras linguagens de programação, informe à ela, pois fica muito mais fácil para ela usar exemplos e comparações. (Já programei em Python e LUA, e sei alguns conceitos de nível intermedáio em ambos, poderia usar essas linguagem como exemplo em suas explicações ?)
3. Representações visuais ajudam bastante, principalmente na questão de registradores, e do Stack, qua são conteúdos meio difíceis de se engolir quando se aprende pela primeira vez. (Poderia me mostrar uma imagem de como funciona o Stack, e como ele interage com os pointers ?)
4. Recomendo também, usar o Mapa Mental, se aprofundar sobre cada conceito, um por vez, para que você não se perca. (Poderia criar um Mapa Mental sobre os Registradores e suas funções ? Quero poder me localizar entre eles sem me perder.)
--------------------------------------------------------------------------------
# Recomendações:
[Assembly na Prática](https://www.youtube.com/playlist?list=PLxTkH01AauxRm0LFLlOA9RR5O6hBLqBtC) - Excelente didática do Professor Fernando, sem pressa para pular o assunto, ele te faz aprender, não só decorar, videos longos e bem explicativos, excelente para quem quer realmente aprender a lógica em sí.
[x86 Assembly Guide](https://www.cs.virginia.edu/~evans/cs216/guides/x86.html) - Um guia em inglês, porém bem técnico e ao mesmo tempo compreensível, o que é meio difícil de se ver principalmente quando o assunto é Assembly, ajuda bastante na parte de compreensão técnica.
--------------------------------------------------------------------------------
Introdução | Aprendendo Assembly, https://mentebinaria.gitbook.io/assembly
Assembly Programming Tutorial - TutorialsPoint, https://www.tutorialspoint.com/assembly_programming/index.htm
Computer Architecture: Assembly Language | Codecademy, https://www.codecademy.com/learn/computer-architecture-assembly-language
Construindo um web server em Assembly x86, parte IV, um assembly modesto, https://dev.to/leandronsp/construindo-um-web-server-em-assembly-x86-parte-iv-um-assembly-modesto-oif
Introduction to SASM Assembly Language | PDF - Scribd, https://www.scribd.com/document/504915932/Assembly-Language
Assembly Language for x86 Processors 7th Edition.pdf, https://broman.dev/download/Assembly%20Language%20for%20x86%20Processors%207th%20Edition.pdf
Assembly Language for x86 Processors (Sixth edition), http://www.nlpir.org/wordpress/wp-content/uploads/2019/03/Assembly.Language.For_.x86.Processors.Kip_.R..Irvine..6ed.Prentice.Hall_.2011www.xuexi111.com_.pdf
lenguaje ensamblador y programación para pc ibm® y compatibles, https://virushatake.files.wordpress.com/2010/10/peterabel.pdf
Assembly Language Solutions Manual, https://lan-portal.uob.edu.ly/goto/TXT/53GO141735/assembly_language-solutions_manual.pdf
Linux X86 Assembly - How to Build a Hello World Program in NASM - Secure Ideas, https://www.secureideas.com/blog/2021/05/linux-x86-assembly-how-to-build-a-hello-world-program-in-nasm.html
Cursos - Mente Binária, https://www.mentebinaria.com.br/cursos/
x86 Assembly Tutorial - cs.Princeton, https://www.cs.princeton.edu/courses/archive/fall20/cos318/precepts/x86_assembly_tutorial.pdf
Tutorial Emu86 - UTN FRM, http://www1.frm.utn.edu.ar/arquitectura/t86.pdf
8086 assembler tutorial for beginners (part 1) what is assembly language? inside the cpu, https://www.uobabylon.edu.iq/eprints/publication_1_26408_35.pdf
Guide to x86 Assembly - Computer Science, https://www.cs.virginia.edu/~evans/cs216/guides/x86.html
8086 Assembler Tutorial for Beginners 1, https://people.vts.su.ac.rs/~pmiki/ASM_old/ASM%20zbirka/8086%20Assembly.pdf
EMU 8086 Assembler Tutorial for Beginners: Part 1 Guide - Studocu, https://www.studocu.vn/vn/document/truong-dai-hoc-fpt/statistics-probability/emu-8086-tuorial-qwe/31969946
Programación en Ensamblador x86 Curso Gratuito | Certificado en 7 Idiomas - Formencia, https://formencia.com/course/programacion-en-ensamblador-x86
Let's Learn x86-64 Assembly! Part 0 - Setup and First Steps, https://gpfault.net/posts/asm-tut-0.txt.html
Assembly x86 - CIn UFPE, https://www.cin.ufpe.br/~eaa3/Arquivos/Assembly/Assembly%20x86%20NASM.pdf
Instruções do NASM | Aprendendo Assembly - GitBook, https://mentebinaria.gitbook.io/assembly/a-base/instrucoes-do-nasm
Curso Assembly x86 desde cero | Clase 1: Introducción y configuración (MASM32 + VS Code) - YouTube, https://www.youtube.com/watch?v=w3XJbXlv460
Intro to x86 Assembly Language (Part 1) - YouTube, https://www.youtube.com/watch?v=wLXIWKUWpSs
Assembly x86 usando NASM, https://www.lncc.br/~pcslara/courses/sb/Assembly.pdf
NASM Tutorial, https://cs.lmu.edu/~ray/notes/nasmtutorial/
Help for Emu8086, https://www.philadelphia.edu.jo/academics/qhamarsheh/uploads/emu8086.pdf
--------------------------------------------------------------------------------

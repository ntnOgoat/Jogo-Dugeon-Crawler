# 🗡️ Dungeon Crawler: O Herói de Aeldoria

![C](https://img.shields.io/badge/Language-C-blue.svg)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux-lightgrey.svg)
![Status](https://img.shields.io/badge/Status-Conclu%C3%ADdo-brightgreen.svg)

**Dungeon Crawler** é um jogo de aventura de perspectiva *top-down* (visão aérea) totalmente renderizado em arte ASCII para o terminal/console. O projeto foi desenvolvido de forma nativa na linguagem C, com foco em lógica de matrizes, gerenciamento de estados e portabilidade entre sistemas operacionais.

---

## 📜 A História

O reino de Aeldoria foi tomado pelas forças das trevas. Um herói corajoso deve descer às profundezas da dungeon medieval, enfrentar monstros terríveis, resolver quebra-cabeças mecânicos e derrotar o temível Boss Final para restaurar a luz e a paz ao reino.

---

## 🎮 Mecânicas do Jogo

O jogo é dividido em **4 fases progressivas** (Vila + 3 Andares de Dungeon) com elementos clássicos de RPG de ação:

* **Sistema de Combate Direcional:** O herói ataca com base na direção para onde está olhando (`^`, `v`, `<`, `>`).
* **Três Armas Distintas:** Escolha sua estratégia conversando com o NPC na vila:
    * *Espada:* Ataque de área de longo alcance à frente ($3 \times 2$).
    * *Arco:* Flechas em linha reta que perfuram até 4 células de distância.
    * *Cajado:* Ataque elemental mágico que atinge as 8 células ao redor.
* **Inteligência Artificial dos Monstros:** * `X` (Monstro Aleatório): Movimentação errática.
    * `Y` (Monstro Perseguidor): Caça ativamente o jogador calculando a distância absoluta do menor caminho.
    * `Z` (Boss Final): Possui **3 fases de batalha** dinâmicas dependendo da sua vida (Perseguição rápida, Teleporte e Rastro de Espinhos).
* **Progressão e Puzzles:** Coleta de chaves (`@`) para abrir portas (`D`), destruição de caixas (`k`) para abrir caminhos e ativação de botões mecânicos (`O`).
* **Persistência de Estado:** Ao perder uma vida em um espinho (`#`) ou monstro, o andar é reiniciado, mas o jogador preserva seu inventário de armas e vida restante.

---

## 🗺️ Dicionário de Símbolos (ASCII)

| Símbolo | Entidade | Comportamento |
| :---: | :--- | :--- |
| `^ v < >` | **Jogador** | Representa o herói e sua direção de movimento/ataque. |
| `*` | **Parede** | Obstáculo intransponível. |
| `#` | **Espinho** | Armadilha fatal (perde 1 vida ao pisar). |
| `k` | **Caixa** | Objeto destrutível através de ataques. |
| `O` | **Botão** | Gatilho mecânico ativado com interação. |
| `D` | **Porta** | Bloqueio que consome 1 chave para abrir (`=`). |
| `@` | **Chave** | Item coletável necessário para abrir portas. |
| `L` | **Escada** | Passagem para descer ao próximo andar. |
| `N` | **NPC** | Personagem da Vila que fornece o menu de armas. |

---

## 🕹️ Controles

O jogo utiliza leitura de teclado em tempo real por meio da interceptação de buffers (sem necessidade de pressionar Enter).

* `W` `A` `S` `D` : Movimentar o personagem (Cima, Esquerda, Baixo, Direita).
* `I` : Interagir (Conversar com NPC, coletar chaves, abrir portas, puxar botões e descer escadas).
* `O` : Desferir ataque com a arma equipada.
* `Q` : Sair do loop de jogo e voltar ao menu principal.

---

## 🛠️ Portabilidade e Arquitetura técnica

O código foi projetado para ser **multiplataforma**, utilizando diretivas de compilação condicional (`#ifdef _WIN32`). 

* **No Windows:** Utiliza a biblioteca nativa `<conio.h>` para a função `_getch()`.
* **No Linux:** Implementa manualmente a emulação do `getch()` manipulando a estrutura `termios` do POSIX no terminal (`ICANON` e `ECHO` desligados) e adiciona rotinas de tratamento de sinais (`SIGINT`, `SIGTERM`) para garantir que o terminal do usuário não quebre caso ele force a saída com `Ctrl+C`.

---

## 🚀 Como Executar o Projeto

### Pré-requisitos
Você precisará de um compilador de C (como o `gcc` ou `clang`) instalado na sua máquina.

### Passos para Compilação e Execução

1. Faça o clone do repositório ou baixe o arquivo fonte:
   ```bash
   git clone [https://github.com/SEU_USUARIO/NOME_DO_REPOSITORIO.git](https://github.com/SEU_USUARIO/NOME_DO_REPOSITORIO.git)
   cd NOME_DO_REPOSITORIO





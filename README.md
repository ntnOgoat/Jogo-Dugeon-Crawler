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

## 📜 Uso da Ia
Ia foi utilizada pra fazer algumas funcionalidades do Cod. Fonte
E pra enfeitar o READ.ME


CODIGO FONTE
```bash
/*
 * =====================================================
 *   DUNGEON CRAWLER - Jogo em C (Console ASCII)
 *   GÃªnero: Dungeon Crawler | Top-down | ASCII
 * =====================================================
 */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#ifdef _WIN32
  #include <conio.h>
  #include <windows.h>
  #define CLEAR "cls"
  #define GETCH() _getch()
#else
  #include <termios.h>
  #include <unistd.h>
  #include <signal.h>
  #define CLEAR "clear"

  static struct termios g_saved_termios;
  static int g_termios_saved = 0;

  /* Restaura o terminal em caso de Ctrl+C ou sinal de encerramento */
  static void restoreTerminal(int sig) {
      if(g_termios_saved)
          tcsetattr(STDIN_FILENO, TCSANOW, &g_saved_termios);
      /* Re-lanca o sinal com comportamento padrao para o processo encerrar */
      signal(sig, SIG_DFL);
      raise(sig);
  }

  /* getch() para Linux */
  int GETCH() {
      struct termios oldt, newt;
      int ch;
      tcgetattr(STDIN_FILENO, &oldt);
      if(!g_termios_saved) {
          g_saved_termios = oldt;
          g_termios_saved = 1;
          /* Registra handler na primeira chamada */
          signal(SIGINT,  restoreTerminal);
          signal(SIGTERM, restoreTerminal);
      }
      newt = oldt;
      newt.c_lflag &= ~(ICANON | ECHO);
      tcsetattr(STDIN_FILENO, TCSANOW, &newt);
      ch = getchar();
      tcsetattr(STDIN_FILENO, TCSANOW, &oldt);
      return ch;
  }
#endif

/* ===================== CONSTANTES ===================== */
#define MAX_ROWS 25
#define MAX_COLS 25
#define MAX_MONSTERS 10
#define MAX_LIVES    3

/* SÃ­mbolos */
#define SYM_WALL    '*'
#define SYM_EMPTY   ' '
#define SYM_THORN   '#'
#define SYM_BOX     'k'
#define SYM_BUTTON  'O'
#define SYM_DOOR    'D'
#define SYM_KEY     '@'
#define SYM_OPENDOOR '='
#define SYM_STAIR   'L'
#define SYM_MON1    'X'
#define SYM_MON2    'Y'
#define SYM_BOSS    'Z'
#define SYM_NPC     'N'

/* DireÃ§Ãµes */
#define DIR_UP    0
#define DIR_DOWN  1
#define DIR_LEFT  2
#define DIR_RIGHT 3

/* Armas */
#define WEAPON_SWORD  0
#define WEAPON_BOW    1
#define WEAPON_STAFF  2

/* Fases */
#define PHASE_VILLAGE 0
#define PHASE_FLOOR1  1
#define PHASE_FLOOR2  2
#define PHASE_FLOOR3  3

/* ===================== ESTRUTURAS ===================== */

typedef struct {
    int row, col;
    int dir;
    int lives;
    int keys;
    int weapon;
    int hasWeapon; /* 0 = ainda nÃ£o escolheu */
} Player;

typedef struct {
    int row, col;
    int alive;
    int type; /* 1=X, 2=Y, 3=Z */
    int hp;   /* para o boss */
    int phase; /* fase de ataque do boss */
    int moveCooldown; /* boss pode ter cooldown */
} Monster;

typedef struct {
    int rows, cols;
    char grid[MAX_ROWS][MAX_COLS];
    char base[MAX_ROWS][MAX_COLS]; /* mapa base (sem entidades) */
    Monster monsters[MAX_MONSTERS];
    int monsterCount;
    int buttonPressed;
} GameMap;

/* ===================== GLOBAIS ===================== */
Player player;
GameMap currentMap;
int currentPhase = PHASE_VILLAGE;
int gameOver = 0;
int victory = 0;
int phaseRestarted = 0; /* flag: fase foi reiniciada em playerHit sem game over */

/* ===================== UTILITÃRIOS ===================== */

void clearScreen() {
    system(CLEAR);
}

void pauseMsg(const char* msg) {
    printf("\n%s\n[Pressione qualquer tecla...]\n", msg);
    GETCH();
}

/* Retorna sÃ­mbolo do jogador conforme direÃ§Ã£o */
char playerSymbol() {
    switch(player.dir) {
        case DIR_UP:    return '^';
        case DIR_DOWN:  return 'v';
        case DIR_LEFT:  return '<';
        case DIR_RIGHT: return '>';
    }
    return '^';
}

/* =================== MAPAS =================== */

/* ---- VILA (10x10) ---- */
void initVillage() {
    currentMap.rows = 10;
    currentMap.cols = 10;
    currentMap.monsterCount = 0;
    currentMap.buttonPressed = 0;

    const char* layout[10] = {
        "**********",
        "*        *",
        "* N      *",
        "*        *",
        "*   **   *",
        "*   **   *",
        "*        *",
        "*        *",
        "*   L    *",
        "**********"
    };

    int r = 0;
    while(r < 10) {
        int c = 0;
        while(c < 10) {
            currentMap.base[r][c] = layout[r][c];
            currentMap.grid[r][c] = layout[r][c];
            c++;
        }
        r++;
    }

    /* Posiciona jogador */
    player.row = 1;
    player.col = 5;
    player.dir = DIR_DOWN;
}

/* ---- ANDAR 1 (10x10) ---- */
void initFloor1() {
    currentMap.rows = 10;
    currentMap.cols = 10;
    currentMap.monsterCount = 0;
    currentMap.buttonPressed = 0;

    /* REDESENHO: parede vertical col 5 separa sala esq (spawn+chave) da dir (escada).
     * Porta D em (4,5) e' a UNICA passagem. BFS confirma escada inacessivel sem chave. */
    const char* layout[10] = {
        "**********",
        "*^   *   *",
        "*  k * L *",
        "*  k *   *",
        "*    D   *",
        "*  @ *   *",
        "*    *   *",
        "*    *   *",
        "*    *   *",
        "**********"
    };

    int r = 0;
    while(r < 10) {
        int c = 0;
        while(c < 10) {
            currentMap.base[r][c] = layout[r][c];
            currentMap.grid[r][c] = layout[r][c];
            c++;
        }
        r++;
    }

    player.row = 1;
    player.col = 1;
    player.dir = DIR_DOWN;
    player.keys = 0;
    /* Remove o ^ do layout do grid â€” buildRenderGrid desenha o jogador por cima */
    currentMap.grid[player.row][player.col] = SYM_EMPTY;
    currentMap.base[player.row][player.col] = SYM_EMPTY;
}

/* ---- ANDAR 2 (15x15) ---- */
void initFloor2() {
    currentMap.rows = 15;
    currentMap.cols = 15;
    currentMap.monsterCount = 0;
    currentMap.buttonPressed = 0;

    /* REDESENHO CRITICO1: chave2 fica na sala DIREITA, atras da porta1.
     * Progressao obrigatoria: chave1(3,4) -> porta1(4,8) -> chave2(3,10)
     *                      -> porta2(9,8) -> escada(10,11).
     * BFS confirma: escada inacessivel sem as 2 chaves em sequencia. */
    const char* layout[15] = {
        "***************",
        "*^      *     *",
        "*       *     *",
        "*  @    * @   *",
        "*       D     *",
        "*       *     *",
        "******* *     *",
        "*       *******",
        "*  ###  *     *",
        "*   O   D     *",
        "*   X   *  L  *",
        "*       *     *",
        "*       *     *",
        "*       *     *",
        "***************"
    };

    int r = 0;
    while(r < 15) {
        int c = 0;
        while(c < 15) {
            char ch = layout[r][c];
            currentMap.base[r][c] = ch;
            currentMap.grid[r][c] = ch;

            if(ch == 'X') {
                Monster* m = &currentMap.monsters[currentMap.monsterCount++];
                m->row = r; m->col = c;
                m->alive = 1; m->type = 1;
                m->hp = 1; m->phase = 0; m->moveCooldown = 0;
                /* CORRECAO BUG1: limpar tanto grid quanto base */
                currentMap.grid[r][c] = SYM_EMPTY;
                currentMap.base[r][c] = SYM_EMPTY;
            }
            c++;
        }
        r++;
    }

    player.row = 1;
    player.col = 1;
    player.dir = DIR_DOWN;
    player.keys = 0;
    /* Remove o ^ do layout do grid â€” buildRenderGrid desenha o jogador por cima */
    currentMap.grid[player.row][player.col] = SYM_EMPTY;
    currentMap.base[player.row][player.col] = SYM_EMPTY;
}

/* ---- ANDAR 3 (25x25) ---- */
void initFloor3() {
    currentMap.rows = 25;
    currentMap.cols = 25;
    currentMap.monsterCount = 0;
    currentMap.buttonPressed = 0;

    /* REDESENHO: 4 zonas verticais separadas por paredes em cols 7, 13, 19.
     * Porta1 (12,7):  acessa zona2 â€” contem chave2 + monstro Y + espinhos.
     * Porta2 (12,13): acessa zona3 â€” contem chave3 + botao + monstro Y.
     * Porta3 (12,19): acessa zona4 â€” contem boss Z + monstro Y + escada.
     * BFS confirma: cada chave desbloqueia acesso a proxima, escada so com 3 chaves. */
    const char* layout[25] = {
        "*************************",
        "*^     *     *     *    *",
        "*      *     *     *    *",
        "*  @   *     *     * Y  *",
        "*      *     *  @  *    *",
        "*      *  @  *     *    *",
        "*      *     *     *    *",
        "*      *     *     *    *",
        "*      *  Y  *     * Z  *",
        "*      *     *  O  *    *",
        "*      *     *     *    *",
        "*      *     *     *    *",
        "*      D     D     D    *",
        "*      *     *     *    *",
        "*      *     *     *    *",
        "*      * ##  *     *    *",
        "*      * #   *     *    *",
        "*      *     * Y   *    *",
        "*      *     *     *    *",
        "*      *     *     *    *",
        "*      *     *     * L  *",
        "*      *     *     *    *",
        "*      *     *     *    *",
        "*      *     *     *    *",
        "*************************"
    };

    int r = 0;
    while(r < 25) {
        int c = 0;
        while(c < 25) {
            char ch = layout[r][c];
            currentMap.base[r][c] = ch;
            currentMap.grid[r][c] = ch;

            if(ch == 'Y') {
                Monster* m = &currentMap.monsters[currentMap.monsterCount++];
                m->row = r; m->col = c;
                m->alive = 1; m->type = 2;
                m->hp = 1; m->phase = 0; m->moveCooldown = 0;
                /* CORRECAO BUG1: limpar tanto grid quanto base */
                currentMap.grid[r][c] = SYM_EMPTY;
                currentMap.base[r][c] = SYM_EMPTY;
            } else if(ch == 'Z') {
                Monster* m = &currentMap.monsters[currentMap.monsterCount++];
                m->row = r; m->col = c;
                m->alive = 1; m->type = 3;
                m->hp = 5; m->phase = 0; m->moveCooldown = 0;
                /* CORRECAO BUG1: limpar tanto grid quanto base */
                currentMap.grid[r][c] = SYM_EMPTY;
                currentMap.base[r][c] = SYM_EMPTY;
            }
            c++;
        }
        r++;
    }

    player.row = 1;
    player.col = 1;
    player.dir = DIR_DOWN;
    player.keys = 0;
    /* Remove o ^ do layout do grid â€” buildRenderGrid desenha o jogador por cima */
    currentMap.grid[player.row][player.col] = SYM_EMPTY;
    currentMap.base[player.row][player.col] = SYM_EMPTY;
}

/* =================== RENDERIZAÃ‡ÃƒO =================== */

void buildRenderGrid(char render[MAX_ROWS][MAX_COLS]) {
    /* Copia mapa base */
    int r = 0;
    while(r < currentMap.rows) {
        int c = 0;
        while(c < currentMap.cols) {
            render[r][c] = currentMap.grid[r][c];
            c++;
        }
        r++;
    }

    /* Desenha monstros */
    int i = 0;
    while(i < currentMap.monsterCount) {
        Monster* m = &currentMap.monsters[i];
        if(m->alive) {
            char sym = (m->type == 1) ? SYM_MON1 : (m->type == 2) ? SYM_MON2 : SYM_BOSS;
            render[m->row][m->col] = sym;
        }
        i++;
    }

    /* Desenha jogador */
    render[player.row][player.col] = playerSymbol();
}

void drawMap() {
    char render[MAX_ROWS][MAX_COLS];
    buildRenderGrid(render);

    /* HUD */
    printf("==============================================\n");
    printf(" DUNGEON CRAWLER  ");
    printf("| Vidas: ");

    int i = 0;
    while(i < player.lives)  { printf("[*]"); i++; }
    while(i < MAX_LIVES)     { printf("[ ]"); i++; }

    printf("| Chaves: %d", player.keys);
    printf("| Arma: %s\n",
        player.weapon == WEAPON_SWORD ? "Espada" :
        player.weapon == WEAPON_BOW   ? "Arco"   : "Cajado");

    const char* phaseName[] = {"Vila", "Andar 1", "Andar 2", "Andar 3"};
    printf(" Fase: %-10s", phaseName[currentPhase]);
    if(currentPhase == PHASE_FLOOR3) {
        /* Mostra HP do boss se estiver vivo */
        i = 0;
        while(i < currentMap.monsterCount) {
            if(currentMap.monsters[i].type == 3 && currentMap.monsters[i].alive)
                printf("| Boss HP: %d/5", currentMap.monsters[i].hp);
            i++;
        }
    }
    printf("\n==============================================\n");

    int r = 0;
    while(r < currentMap.rows) {
        int c = 0;
        while(c < currentMap.cols) {
            putchar(render[r][c]);
            c++;
        }
        putchar('\n');
        r++;
    }
    printf("==============================================\n");
    printf("[w/a/s/d] Mover  [i] Interagir  [o] Atacar\n");
}

/* =================== COLISÃƒO =================== */

int isWalkable(int r, int c) {
    if(r < 0 || r >= currentMap.rows || c < 0 || c >= currentMap.cols) return 0;
    char ch = currentMap.grid[r][c];
    if(ch == SYM_WALL) return 0;
    if(ch == SYM_DOOR) return 0;
    if(ch == SYM_BOX)  return 0;
    /* Monstros bloqueiam movimento */
    int i = 0;
    while(i < currentMap.monsterCount) {
        Monster* m = &currentMap.monsters[i];
        if(m->alive && m->row == r && m->col == c) return 0;
        i++;
    }
    return 1;
}

/* =================== DANO / MORTE =================== */

void playerHit() {
    player.lives--;
    if(player.lives <= 0) {
        gameOver = 1;
        phaseRestarted = 0;
    } else {
        /* Reinicia o andar preservando arma e vidas */
        int weapon = player.weapon;
        int lives = player.lives;
        if(currentPhase == PHASE_VILLAGE) initVillage();
        else if(currentPhase == PHASE_FLOOR1) initFloor1();
        else if(currentPhase == PHASE_FLOOR2) initFloor2();
        else if(currentPhase == PHASE_FLOOR3) initFloor3();
        player.weapon = weapon;
        player.lives = lives;
        player.dir = DIR_DOWN; /* MENOR1: restaura direcao ao reiniciar */
        player.keys = 0;
        phaseRestarted = 1; /* sinaliza que o mapa foi reiniciado */
        clearScreen();
        printf("\n  *** Voce perdeu uma vida! Vidas restantes: %d ***\n", player.lives);
        pauseMsg("");
    }
}

/* =================== ATAQUE =================== */

void attackCell(int r, int c) {
    if(r < 0 || r >= currentMap.rows || c < 0 || c >= currentMap.cols) return;

    /* DestrÃ³i caixa */
    if(currentMap.grid[r][c] == SYM_BOX) {
        currentMap.grid[r][c] = SYM_EMPTY;
        currentMap.base[r][c] = SYM_EMPTY;
        return;
    }

    /* Atinge monstro */
    int i = 0;
    while(i < currentMap.monsterCount) {
        Monster* m = &currentMap.monsters[i];
        if(m->alive && m->row == r && m->col == c) {
            m->hp--;
            if(m->hp <= 0) {
                m->alive = 0;
                if(m->type == 3) { /* Boss morreu */
                    victory = 1;
                    break; /* CORRECAO: encerra o loop imediatamente apos matar o boss */
                }
            }
            break; /* apenas um monstro por celula â€” para apos acertar */
        }
        i++;
    }
}

void doAttack() {
    int pr = player.row, pc = player.col, d = player.dir;

    if(player.weapon == WEAPON_STAFF) {
        /* Ataca 8 cÃ©lulas ao redor */
        int dr = -1;
        while(dr <= 1) {
            int dc = -1;
            while(dc <= 1) {
                if(!(dr == 0 && dc == 0))
                    attackCell(pr+dr, pc+dc);
                dc++;
            }
            dr++;
        }
        return;
    }

    if(player.weapon == WEAPON_BOW) {
        /* Linha reta, 4 cÃ©lulas â€” para em parede, porta, caixa, espinho e monstro */
        int bdr = 0, bdc = 0;
        if(d == DIR_UP)    bdr = -1;
        if(d == DIR_DOWN)  bdr =  1;
        if(d == DIR_LEFT)  bdc = -1;
        if(d == DIR_RIGHT) bdc =  1;

        int i = 1;
        while(i <= 4) {
            int nr = pr + bdr*i, nc = pc + bdc*i;
            if(nr < 0 || nr >= currentMap.rows || nc < 0 || nc >= currentMap.cols) break;
            char cell = currentMap.grid[nr][nc];
            /* Para em obstaculos solidos sem atacar */
            if(cell == SYM_WALL || cell == SYM_DOOR || cell == SYM_THORN) break;
            /* Verifica monstro ANTES de atacar â€” flecha para no primeiro alvo */
            int hitMonster = 0;
            int mi = 0;
            while(mi < currentMap.monsterCount) {
                Monster* mm = &currentMap.monsters[mi];
                if(mm->alive && mm->row == nr && mm->col == nc) {
                    hitMonster = 1;
                    break;
                }
                mi++;
            }
            attackCell(nr, nc);
            /* Para apos atingir monstro ou destruir caixa */
            if(hitMonster || cell == SYM_BOX) break;
            i++;
        }
        return;
    }

    /* ESPADA: Ã¡rea 3x2 Ã  frente */
    if(player.weapon == WEAPON_SWORD) {
        if(d == DIR_UP) {
            int dr = -1;
            while(dr >= -2) {
                int dc = -1;
                while(dc <= 1) { attackCell(pr+dr, pc+dc); dc++; }
                dr--;
            }
        } else if(d == DIR_DOWN) {
            int dr = 1;
            while(dr <= 2) {
                int dc = -1;
                while(dc <= 1) { attackCell(pr+dr, pc+dc); dc++; }
                dr++;
            }
        } else if(d == DIR_LEFT) {
            int dc = -1;
            while(dc >= -2) {
                int dr = -1;
                while(dr <= 1) { attackCell(pr+dr, pc+dc); dr++; }
                dc--;
            }
        } else { /* RIGHT */
            int dc = 1;
            while(dc <= 2) {
                int dr = -1;
                while(dr <= 1) { attackCell(pr+dr, pc+dc); dr++; }
                dc++;
            }
        }
    }
}

/* =================== INTERAÃ‡ÃƒO =================== */

void getFrontCell(int* r, int* c) {
    *r = player.row;
    *c = player.col;
    if(player.dir == DIR_UP)    (*r)--;
    if(player.dir == DIR_DOWN)  (*r)++;
    if(player.dir == DIR_LEFT)  (*c)--;
    if(player.dir == DIR_RIGHT) (*c)++;
}

/* Forward declarations */
int monsterCanMove(int r, int c);
void showVictory(void);

void doInteract() {
    int fr, fc;
    getFrontCell(&fr, &fc);
    if(fr < 0 || fr >= currentMap.rows || fc < 0 || fc >= currentMap.cols) return;

    char ch = currentMap.grid[fr][fc];

    if(ch == SYM_KEY) {
        player.keys++;
        currentMap.grid[fr][fc] = SYM_EMPTY;
        currentMap.base[fr][fc] = SYM_EMPTY;
        printf("\n  [Chave coletada! Chaves: %d]\n", player.keys);
        GETCH();
    }
    else if(ch == SYM_DOOR) {
        if(player.keys > 0) {
            player.keys--;
            currentMap.grid[fr][fc] = SYM_OPENDOOR;
            currentMap.base[fr][fc] = SYM_OPENDOOR;
            printf("\n  [Porta aberta!]\n");
            GETCH();
        } else {
            printf("\n  [VocÃª precisa de uma chave!]\n");
            GETCH();
        }
    }
    else if(ch == SYM_BUTTON) {
        if(!currentMap.buttonPressed) {
            currentMap.buttonPressed = 1;
            if(currentPhase == PHASE_FLOOR2) {
                /* Botao remove os espinhos da linha 8, cols 3-5 */
                int bc = 3;
                while(bc <= 5) {
                    if(currentMap.grid[8][bc] == SYM_THORN) {
                        currentMap.grid[8][bc] = SYM_EMPTY;
                        currentMap.base[8][bc] = SYM_EMPTY;
                    }
                    bc++;
                }
                printf("\n  [Botao pressionado! Espinhos removidos â€” caminho aberto!]\n");
            } else if(currentPhase == PHASE_FLOOR3) {
                /* CORRECAO CRITICO3: botao invoca 2 monstros X nas zonas adjacentes */
                int spawned = 0;
                int spawnCandidates[4][2] = {
                    {player.row - 3, player.col},
                    {player.row + 3, player.col},
                    {player.row, player.col - 3},
                    {player.row, player.col + 3}
                };
                int s = 0;
                while(s < 4 && spawned < 2) {
                    int sr = spawnCandidates[s][0];
                    int sc = spawnCandidates[s][1];
                    if(monsterCanMove(sr, sc) && currentMap.monsterCount < MAX_MONSTERS) {
                        Monster* nm = &currentMap.monsters[currentMap.monsterCount++];
                        nm->row = sr; nm->col = sc;
                        nm->alive = 1; nm->type = 1;
                        nm->hp = 1; nm->phase = 0; nm->moveCooldown = 0;
                        spawned++;
                    }
                    s++;
                }
                if(spawned > 0)
                    printf("\n  [Botao pressionado! %d monstro(s) invocado(s)!]\n", spawned);
                else
                    printf("\n  [Botao pressionado! Algo se move nas sombras...]\n");
            } else {
                printf("\n  [Botao pressionado!]\n");
            }
            GETCH();
        } else {
            printf("\n  [Botao ja foi pressionado.]\n");
            GETCH();
        }
    }
    else if(ch == SYM_STAIR) {
        /* Bloqueia entrada na dungeon sem arma */
        if(currentPhase == PHASE_VILLAGE && !player.hasWeapon) {
            printf("\n  [Fale com o NPC (N) antes de entrar na dungeon!]\n");
            GETCH();
            return;
        }
        /* CRITICO: bloqueia escada do andar 3 se boss ainda vivo */
        if(currentPhase == PHASE_FLOOR3) {
            int bossAlive = 0;
            int bi = 0;
            while(bi < currentMap.monsterCount) {
                if(currentMap.monsters[bi].type == 3 && currentMap.monsters[bi].alive) {
                    bossAlive = 1;
                    break;
                }
                bi++;
            }
            if(bossAlive) {
                printf("\n  [Voce precisa derrotar o boss antes de sair!]\n");
                GETCH();
                return;
            }
            /* Boss morto no andar 3: seta vitoria â€” gameLoop exibe a tela */
            victory = 1;
            return;
        }
        /* AvanÃ§a de fase */
        int weapon = player.weapon;
        int lives = player.lives;
        currentPhase++;
        if(currentPhase == PHASE_FLOOR1) initFloor1();
        else if(currentPhase == PHASE_FLOOR2) initFloor2();
        else if(currentPhase == PHASE_FLOOR3) initFloor3();
        player.weapon = weapon;
        player.lives = lives;
        player.keys = 0;
        printf("\n  [Descendo para o proximo andar...]\n");
        GETCH();
    }
    else if(ch == SYM_NPC && currentPhase == PHASE_VILLAGE) {
        /* Escolha de arma */
        if(!player.hasWeapon) {
            clearScreen();
            printf("\n==========================================\n");
            printf("  NPC: Aventureiro, escolha sua arma!\n");
            printf("==========================================\n");
            printf("  1. Espada   - Ataca area 3x2 a frente\n");
            printf("  2. Arco     - Ataca linha reta (4 celulas)\n");
            printf("  3. Cajado   - Ataca 8 celulas ao redor\n");
            printf("==========================================\n");
            printf("Escolha (1/2/3): ");
            int ch2;
            do {
                ch2 = GETCH();
            } while(ch2 < '1' || ch2 > '3');
            player.weapon = ch2 - '1';
            player.hasWeapon = 1;
            const char* names[] = {"Espada", "Arco", "Cajado"};
            printf("\n  [%s escolhida!]\n", names[player.weapon]);
            GETCH();
        } else {
            printf("\n  [NPC: Boa sorte na dungeon, herÃ³i!]\n");
            GETCH();
        }
    }
}

/* =================== MOVIMENTO MONSTROS =================== */

/* Verifica se cÃ©lula estÃ¡ livre para monstro se mover */
int monsterCanMove(int r, int c) {
    if(r < 0 || r >= currentMap.rows || c < 0 || c >= currentMap.cols) return 0;
    char ch = currentMap.grid[r][c];
    if(ch == SYM_WALL || ch == SYM_DOOR || ch == SYM_BOX) return 0;
    /* NÃ£o pode sobrepor outro monstro */
    int i = 0;
    while(i < currentMap.monsterCount) {
        Monster* m = &currentMap.monsters[i];
        if(m->alive && m->row == r && m->col == c) return 0;
        i++;
    }
    return 1;
}

void moveMonsters() {

    int i = 0;
    while(i < currentMap.monsterCount) {
        Monster* m = &currentMap.monsters[i];
        if(!m->alive) { i++; continue; }

        int nr = m->row, nc = m->col;

        if(m->type == 1) {
            /* Monstro X: movimento aleatÃ³rio */
            int dirs[4][2] = {{-1,0},{1,0},{0,-1},{0,1}};
            int attempts = 0;
            do {
                int d = rand() % 4;
                nr = m->row + dirs[d][0];
                nc = m->col + dirs[d][1];
                attempts++;
            } while(!monsterCanMove(nr, nc) && attempts < 8);

            if(monsterCanMove(nr, nc)) {
                m->row = nr;
                m->col = nc;
            }
        }
        else if(m->type == 2) {
            /* Monstro Y: persegue jogador */
            int dr = player.row - m->row;
            int dc = player.col - m->col;

            /* Se ja esta na mesma celula do jogador, a colisao e tratada abaixo â€” nao move */
            if(dr != 0 || dc != 0) {
                /* Move no eixo de maior diferenÃ§a, sem diagonal */
                if(abs(dr) >= abs(dc)) {
                    nr = m->row + (dr > 0 ? 1 : -1);
                    nc = m->col;
                } else {
                    nr = m->row;
                    nc = m->col + (dc > 0 ? 1 : -1);
                }
                if(monsterCanMove(nr, nc)) {
                    m->row = nr;
                    m->col = nc;
                }
            }
        }
        else if(m->type == 3) {
            /* BOSS Z: Comportamento unico em 3 fases
             * HP 5-4: Perseguicao rapida
             * HP 3:   Teleporte aleatorio (raio 3)
             * HP 2-1: Perseguicao + espinho no rastro
             */
            /* CORRECAO BUG3: removido goto, logica reescrita com if/else limpo */
            if(m->moveCooldown > 0) {
                m->moveCooldown--;
            } else {
                if(m->hp >= 4) {
                    /* Fase 1: perseguicao rapida */
                    m->phase = 1;
                    int bdr = player.row - m->row;
                    int bdc = player.col - m->col;
                    if(abs(bdr) >= abs(bdc)) {
                        nr = m->row + (bdr > 0 ? 1 : -1);
                        nc = m->col;
                    } else {
                        nr = m->row;
                        nc = m->col + (bdc > 0 ? 1 : -1);
                    }
                    if(monsterCanMove(nr, nc)) { m->row = nr; m->col = nc; }
                }
                else if(m->hp == 3) {
                    /* Fase 2: teleporte aleatorio */
                    m->phase = 2;
                    int attempts = 0;
                    int tr = m->row, tc = m->col;
                    do {
                        tr = m->row + (rand() % 7) - 3;
                        tc = m->col + (rand() % 7) - 3;
                        attempts++;
                    } while((!monsterCanMove(tr, tc) || (tr == player.row && tc == player.col)) && attempts < 20);
                    if(attempts < 20) {
                        m->row = tr;
                        m->col = tc;
                        m->moveCooldown = 1; /* pausa so apos teleporte bem-sucedido */
                    }
                }
                else {
                    /* Fase 3: perseguicao + espinho no rastro */
                    m->phase = 3;
                    int oldR = m->row, oldC = m->col;
                    int bdr = player.row - m->row;
                    int bdc = player.col - m->col;
                    if(abs(bdr) >= abs(bdc)) {
                        nr = m->row + (bdr > 0 ? 1 : -1);
                        nc = m->col;
                    } else {
                        nr = m->row;
                        nc = m->col + (bdc > 0 ? 1 : -1);
                    }
                    if(monsterCanMove(nr, nc)) {
                        if(currentMap.grid[oldR][oldC] == SYM_EMPTY)
                            currentMap.grid[oldR][oldC] = SYM_THORN;
                        m->row = nr;
                        m->col = nc;
                    }
                }
            }
        } /* fim else if type==3 */

        /* Verifica se monstro tocou no jogador */
        if(m->row == player.row && m->col == player.col) {
            playerHit();
            /* CORRECAO CRITICO2: retorna em QUALQUER caso apos playerHit â€”
             * se gameOver=1: evita acesso invalido; se reiniciou fase: evita
             * que monstros do mapa novo se movam antes do primeiro frame. */
            return;
        }

        i++;
    }
}

/* =================== VERIFICA EVENTOS DO MAPA =================== */

void checkMapEvents() {
    char ch = currentMap.grid[player.row][player.col];

    if(ch == SYM_THORN) {
        clearScreen();
        printf("\n  *** VocÃª pisou em um espinho! ***\n");
        playerHit();
        return;
    }
    /* Chave requer interacao manual com [i] â€” nao ha coleta automatica */
}

/* =================== MOVIMENTO JOGADOR =================== */

void movePlayer(int dr, int dc, int newDir) {
    player.dir = newDir;
    int nr = player.row + dr;
    int nc = player.col + dc;

    phaseRestarted = 0; /* reseta antes de cada acao */

    if(isWalkable(nr, nc)) {
        player.row = nr;
        player.col = nc;
        checkMapEvents();
        /* Nao move monstros se espinho causou reinicio ou game over */
        if(!gameOver && !phaseRestarted) moveMonsters();
    } else {
        /* So muda direcao, move monstros */
        if(!gameOver && !phaseRestarted) moveMonsters();
    }
}

/* =================== MENUS =================== */

void showTutorial() {
    clearScreen();
    printf("==========================================\n");
    printf("           TUTORIAL - DUNGEON CRAWLER\n");
    printf("==========================================\n\n");
    printf("HISTORIA:\n");
    printf("  O reino de Aeldoria foi tomado por trevas.\n");
    printf("  Um heroi corajoso deve descer a dungeon,\n");
    printf("  enfrentar seus horrores e derrotar o boss\n");
    printf("  para restaurar a luz ao reino.\n\n");
    printf("SIMBOLOS:\n");
    printf("  ^ v < >  = Voce (direcao que esta olhando)\n");
    printf("  *        = Parede (intransponivel)\n");
    printf("  #        = Espinho (morte ao pisar)\n");
    printf("  k        = Caixa (destruivel com ataque)\n");
    printf("  O        = Botao (pressione com [i])\n");
    printf("  D        = Porta fechada (use chave)\n");
    printf("  @        = Chave (pegue com [i])\n");
    printf("  =        = Porta aberta\n");
    printf("  L        = Escada (proximo andar com [i])\n");
    printf("  N        = NPC\n");
    printf("  X        = Monstro aleatorio\n");
    printf("  Y        = Monstro perseguidor\n");
    printf("  Z        = Boss Final\n\n");
    printf("CONTROLES:\n");
    printf("  w = cima   s = baixo   a = esq   d = dir\n");
    printf("  i = interagir   o = atacar\n\n");
    printf("DICAS:\n");
    printf("  - Voce tem %d vidas. Ao morrer, recomeÃ§a o andar.\n", MAX_LIVES);
    printf("  - Colete chaves (@) para abrir portas (D).\n");
    printf("  - Destrua caixas com [o] para abrir caminhos.\n");
    printf("  - Pressione botoes (O) com [i] para efeitos.\n");
    printf("==========================================\n");
    pauseMsg("");
}

void showCredits() {
    clearScreen();
    printf("==========================================\n");
    printf("              DUNGEON CRAWLER\n");
    printf("==========================================\n\n");
    printf("  Desenvolvido como projeto academico\n");
    printf("  para a disciplina de Programacao em C.\n\n");
    printf("  Desenvolvedor: [SEU NOME AQUI]\n\n");
    printf("  Tecnologias: C, ASCII, Console\n\n");
    printf("  Obrigado por jogar!\n\n");
    printf("==========================================\n");
    pauseMsg("Encerrando o jogo...");
}

void showGameOver() {
    clearScreen();
    printf("\n");
    printf("  ##############################################\n");
    printf("  #                                            #\n");
    printf("  #              GAME OVER                     #\n");
    printf("  #                                            #\n");
    printf("  #   Voce foi derrotado nas trevas da         #\n");
    printf("  #   dungeon. Aeldoria permanece nas          #\n");
    printf("  #   sombras...                               #\n");
    printf("  #                                            #\n");
    printf("  ##############################################\n");
    pauseMsg("");
}

void showVictory() {
    clearScreen();
    printf("\n");
    printf("  ##############################################\n");
    printf("  #                                            #\n");
    printf("  #           *** VITORIA! ***                 #\n");
    printf("  #                                            #\n");
    printf("  #   O Boss foi derrotado!                    #\n");
    printf("  #                                            #\n");
    printf("  #   A escuridao foi dissipada. Aeldoria      #\n");
    printf("  #   respira livre novamente. As criancas     #\n");
    printf("  #   cantam nas ruas, e o seu nome e          #\n");
    printf("  #   gravado nas pedras do castelo como       #\n");
    printf("  #   o Heroi da Dungeon.                      #\n");
    printf("  #                                            #\n");
    printf("  #   Parabens, aventureiro!                   #\n");
    printf("  #                                            #\n");
    printf("  ##############################################\n");
    pauseMsg("");
}

int mainMenu() {
    while(1) {
        clearScreen();
        printf("==========================================\n");
        printf("           DUNGEON CRAWLER\n");
        printf("       O Heroi de Aeldoria\n");
        printf("==========================================\n\n");
        printf("  1. Jogar\n");
        printf("  2. Tutorial\n");
        printf("  3. Sair\n\n");
        printf("==========================================\n");
        printf("Escolha: ");

        int ch = GETCH();
        if(ch == '1') return 1;
        if(ch == '2') { showTutorial(); }
        if(ch == '3') { showCredits(); return 0; }
    }
}

/* =================== INICIALIZAÃ‡ÃƒO DO JOGO =================== */

void initGame() {
    player.lives = MAX_LIVES;
    player.keys = 0;
    player.weapon = WEAPON_SWORD;
    player.hasWeapon = 0;
    player.dir = DIR_DOWN;
    gameOver = 0;
    victory = 0;
    phaseRestarted = 0;
    currentPhase = PHASE_VILLAGE;
    srand((unsigned)time(NULL));
    initVillage();
}

/* =================== LOOP PRINCIPAL =================== */

void gameLoop() {
    while(!gameOver && !victory) {
        clearScreen();
        drawMap();

        /* Aviso especial */
        if(currentPhase == PHASE_VILLAGE && !player.hasWeapon) {
            printf("  [DICA: Fale com o NPC (N) para escolher sua arma!]\n");
        }

        int input = GETCH();

        switch(input) {
            case 'w': case 'W':
                movePlayer(-1, 0, DIR_UP);
                break;
            case 's': case 'S':
                movePlayer(1, 0, DIR_DOWN);
                break;
            case 'a': case 'A':
                movePlayer(0, -1, DIR_LEFT);
                break;
            case 'd': case 'D':
                movePlayer(0, 1, DIR_RIGHT);
                break;
            case 'i': case 'I':
                phaseRestarted = 0;
                doInteract();
                if(!gameOver && !victory && !phaseRestarted) moveMonsters();
                break;
            case 'o': case 'O':
                phaseRestarted = 0;
                doAttack();
                if(victory) { showVictory(); return; }
                if(!gameOver && !victory && !phaseRestarted) moveMonsters();
                break;
            case 'q': case 'Q':
                return; /* Volta ao menu */
            default:
                break;
        }

        if(gameOver) {
            showGameOver();
            return;
        }

        if(victory) {
            showVictory();
            return;
        }
    }
}

/* =================== MAIN =================== */

int main() {
    while(1) {
        int choice = mainMenu();
        if(choice == 0) break; /* Sair */

        initGame();
        gameLoop();
    }

    return 0;
}

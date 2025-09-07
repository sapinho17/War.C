#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define MAX_TERRITORIOS 6
#define MAX_JOGADORES 2

// Estrutura de um território
typedef struct {
    char nome[20];
    int dono;       // id do jogador dono
    int exercitos;  // quantidade de exércitos
} Territorio;

// Estrutura de um jogador
typedef struct {
    char nome[20];
    int id;
} Jogador;

// Função para inicializar territórios
void inicializarTerritorios(Territorio t[]) {
    char nomes[MAX_TERRITORIOS][20] = {
        "Brasil", "Argentina", "Chile",
        "Peru", "Mexico", "Canada"
    };

    for (int i = 0; i < MAX_TERRITORIOS; i++) {
        snprintf(t[i].nome, 20, "%s", nomes[i]);
        t[i].dono = i % MAX_JOGADORES; // alterna jogadores
        t[i].exercitos = 3;            // cada território começa com 3 exércitos
    }
}

// Mostra o estado do tabuleiro
void mostrarMapa(Territorio t[]) {
    printf("\n=== MAPA ===\n");
    for (int i = 0; i < MAX_TERRITORIOS; i++) {
        printf("%d - %-10s | Dono: Jogador %d | Exercitos: %d\n",
               i, t[i].nome, t[i].dono, t[i].exercitos);
    }
}

// Função de ataque
void atacar(Territorio t[], int origem, int destino) {
    if (t[origem].exercitos < 2) {
        printf("Você precisa de pelo menos 2 exércitos para atacar!\n");
        return;
    }
    if (t[origem].dono == t[destino].dono) {
        printf("Não pode atacar seu próprio território!\n");
        return;
    }

    int dadoAtacante = rand() % 6 + 1;
    int dadoDefensor = rand() % 6 + 1;

    printf("Atacante rolou: %d | Defensor rolou: %d\n", dadoAtacante, dadoDefensor);

    if (dadoAtacante > dadoDefensor) {
        t[destino].exercitos--;
        printf("O defensor perdeu 1 exército!\n");
        if (t[destino].exercitos <= 0) {
            t[destino].dono = t[origem].dono;
            t[destino].exercitos = 1;
            t[origem].exercitos--;
            printf("O atacante conquistou %s!\n", t[destino].nome);
        }
    } else {
        t[origem].exercitos--;
        printf("O atacante perdeu 1 exército!\n");
    }
}

// Verifica se algum jogador venceu
int verificarVitoria(Territorio t[]) {
    int dono = t[0].dono;
    for (int i = 1; i < MAX_TERRITORIOS; i++) {
        if (t[i].dono != dono) return -1;
    }
    return dono;
}

int main() {
    srand(time(NULL));

    Jogador jogadores[MAX_JOGADORES] = {{"Alice", 0}, {"Bob", 1}};
    Territorio territorios[MAX_TERRITORIOS];

    inicializarTerritorios(territorios);

    int turno = 0;
    while (1) {
        mostrarMapa(territorios);

        int jogadorAtual = turno % MAX_JOGADORES;
        printf("\n--- Turno do Jogador %d (%s) ---\n", jogadores[jogadorAtual].id, jogadores[jogadorAtual].nome);

        int origem, destino;
        printf("Escolha território de origem (id): ");
        scanf("%d", &origem);
        printf("Escolha território de destino (id): ");
        scanf("%d", &destino);

        atacar(territorios, origem, destino);

        int vencedor = verificarVitoria(territorios);
        if (vencedor != -1) {
            printf("\n=== FIM DE JOGO ===\n");
            printf("Jogador %d (%s) venceu o WAR!\n", jogadores[vencedor].id, jogadores[vencedor].nome);
            break;
        }

        turno++;
    }

    return 0;
}

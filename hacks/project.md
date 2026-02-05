/*
Terminal Dungeon Escape (single-file C game)

  gcc -Wall -Wextra -Werror -O2 dungeon.c -o dungeon

Run:
  ./dungeon

Controls:
  w/a/s/d = move
  q = quit
  r = restart (after win/lose)
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define W 25
#define H 15

#define MAX_HP 30
#define START_HP 20

// Tile symbols
#define TILE_EMPTY '.'
#define TILE_WALL  '#'
#define TILE_PLAYER 'P'
#define TILE_ENEMY  'E'
#define TILE_TRAP   'T'
#define TILE_HEALTH 'H'
#define TILE_EXIT   'X'

typedef struct {
  int x, y;
  int hp;
  int score;
  int turns;
  int health_packs_used;
  int enemies_defeated;
} Player;

typedef struct {
  char grid[H][W];
  Player p;
  int game_over;   // 0 running, 1 win, -1 lose
} Game;

static void clear_screen() {
#ifdef _WIN32
  system("cls");
#else
  system("clear");
#endif
}

static int in_bounds(int x, int y) {
  return x >= 0 && x < W && y >= 0 && y < H;
}

static int rand_int(int lo, int hi) { // inclusive
  return lo + (rand() % (hi - lo + 1));
}

static void fill_grid(Game *g, char c) {
  for (int y = 0; y < H; y++) {
    for (int x = 0; x < W; x++) g->grid[y][x] = c;
  }
}

static void carve_rooms_and_corridors(Game *g) {
  // Start all walls, then carve open area with some noise.
  fill_grid(g, TILE_WALL);

  // Carve a central open region
  for (int y = 1; y < H - 1; y++) {
    for (int x = 1; x < W - 1; x++) {
      g->grid[y][x] = TILE_EMPTY;
    }
  }

  // Add random internal walls
  int wall_count = (W * H) / 6;
  for (int i = 0; i < wall_count; i++) {
    int x = rand_int(1, W - 2);
    int y = rand_int(1, H - 2);
    // Don't block too hard—make walls in small clusters
    g->grid[y][x] = TILE_WALL;
    if (rand() % 2) g->grid[y][x + (rand() % 2 ? 1 : -1)] = TILE_WALL;
    if (rand() % 2) g->grid[y + (rand() % 2 ? 1 : -1)][x] = TILE_WALL;
  }

  // Ensure border walls
  for (int x = 0; x < W; x++) g->grid[0][x] = g->grid[H - 1][x] = TILE_WALL;
  for (int y = 0; y < H; y++) g->grid[y][0] = g->grid[y][W - 1] = TILE_WALL;
}

static int find_empty_spot(Game *g, int *outx, int *outy) {
  // Try random attempts
  for (int tries = 0; tries < 5000; tries++) {
    int x = rand_int(1, W - 2);
    int y = rand_int(1, H - 2);
    if (g->grid[y][x] == TILE_EMPTY) {
      *outx = x; *outy = y;
      return 1;
    }
  }
  // Fallback scan
  for (int y = 1; y < H - 1; y++) {
    for (int x = 1; x < W - 1; x++) {
      if (g->grid[y][x] == TILE_EMPTY) {
        *outx = x; *outy = y;
        return 1;
      }
    }
  }
  return 0;
}

static void place_n(Game *g, char tile, int n) {
  for (int i = 0; i < n; i++) {
    int x, y;
    if (!find_empty_spot(g, &x, &y)) return;
    g->grid[y][x] = tile;
  }
}

static void init_game(Game *g) {
  memset(g, 0, sizeof(*g));
  g->p.hp = START_HP;

  carve_rooms_and_corridors(g);

  // Place exit, enemies, traps, health packs
  place_n(g, TILE_EXIT, 1);
  place_n(g, TILE_ENEMY, 7);
  place_n(g, TILE_TRAP, 6);
  place_n(g, TILE_HEALTH, 5);

  // Place player last on an empty tile
  int px, py;
  if (!find_empty_spot(g, &px, &py)) {
    // extremely unlikely, but just force center
    px = W / 2; py = H / 2;
  }
  g->p.x = px;
  g->p.y = py;
}

static void render(const Game *g) {
  clear_screen();
  printf("Terminal Dungeon Escape\n");
  printf("HP: %d/%d  Score: %d  Turns: %d  Packs used: %d  Enemies defeated: %d\n",
         g->p.hp, MAX_HP, g->p.score, g->p.turns, g->p.health_packs_used, g->p.enemies_defeated);
  printf("Controls: w/a/s/d move | q quit\n");
  printf("Legend: P player, E enemy, T trap, H health, X exit, # wall, . empty\n\n");

  for (int y = 0; y < H; y++) {
    for (int x = 0; x < W; x++) {
      if (x == g->p.x && y == g->p.y) putchar(TILE_PLAYER);
      else putchar(g->grid[y][x]);
    }
    putchar('\n');
  }

  if (g->game_over == 1) {
    printf("\nYOU ESCAPED! 🎉  Final score: %d\n", g->p.score);
    printf("Press r to restart or q to quit.\n");
  } else if (g->game_over == -1) {
    printf("\nYOU DIED. 💀  Final score: %d\n", g->p.score);
    printf("Press r to restart or q to quit.\n");
  }
}

static void clamp_hp(Player *p) {
  if (p->hp > MAX_HP) p->hp = MAX_HP;
  if (p->hp < 0) p->hp = 0;
}

static void apply_tile(Game *g, int nx, int ny) {
  char t = g->grid[ny][nx];

  if (t == TILE_ENEMY) {
    // Simple combat: you always defeat enemy, but take damage
    int dmg = rand_int(3, 7);
    g->p.hp -= dmg;
    g->p.score += 15;
    g->p.enemies_defeated += 1;
    g->grid[ny][nx] = TILE_EMPTY;
    printf("You fought an enemy! Took %d damage.\n", dmg);
  } else if (t == TILE_TRAP) {
    int dmg = rand_int(5, 10);
    g->p.hp -= dmg;
    g->p.score -= 5;
    g->grid[ny][nx] = TILE_EMPTY; // trap triggers once
    printf("You triggered a trap! Took %d damage.\n", dmg);
  } else if (t == TILE_HEALTH) {
    int heal = rand_int(6, 12);
    g->p.hp += heal;
    g->p.score += 5;
    g->p.health_packs_used += 1;
    g->grid[ny][nx] = TILE_EMPTY;
    printf("You found a health pack! Healed %d.\n", heal);
  } else if (t == TILE_EXIT) {
    g->p.score += 50;
    g->game_over = 1;
    printf("You reached the exit!\n");
  } else {
    // empty or wall should not call here
  }

  clamp_hp(&g->p);
  if (g->p.hp <= 0 && g->game_over == 0) g->game_over = -1;
}

static void try_move(Game *g, int dx, int dy) {
  if (g->game_over != 0) return;

  int nx = g->p.x + dx;
  int ny = g->p.y + dy;

  if (!in_bounds(nx, ny)) return;

  char t = g->grid[ny][nx];
  if (t == TILE_WALL) {
    printf("Bumped into a wall.\n");
    return;
  }

  // Move
  g->p.x = nx;
  g->p.y = ny;

  // Step cost
  g->p.turns += 1;
  g->p.score += 1;

  // Apply tile effects
  if (t == TILE_ENEMY || t == TILE_TRAP || t == TILE_HEALTH || t == TILE_EXIT) {
    apply_tile(g, nx, ny);
  }
}

static int read_command(char *buf, size_t n) {
  // Read a full line; return 0 on EOF
  if (!fgets(buf, (int)n, stdin)) return 0;
  // strip newline
  size_t len = strlen(buf);
  if (len && buf[len - 1] == '\n') buf[len - 1] = '\0';
  return 1;
}

static void wait_for_enter_if_message() {
  // If we printed an action line, let user see it briefly without needing complicated input handling
  // But we still accept immediate next move; we just print messages above and redraw.
}

int main() {
  srand((unsigned)time(NULL));

  Game g;
  init_game(&g);

  char line[128];

  while (1) {
    render(&g);

    if (!read_command(line, sizeof(line))) break;
    if (line[0] == '\0') continue;

    char c = line[0];

    if (c == 'q') break;

    if ((g.game_over == 1 || g.game_over == -1) && c == 'r') {
      init_game(&g);
      continue;
    }

    // Movement
    if (c == 'w') try_move(&g, 0, -1);
    else if (c == 's') try_move(&g, 0, 1);
    else if (c == 'a') try_move(&g, -1, 0);
    else if (c == 'd') try_move(&g, 1, 0);
    else {
      // Unknown command
      printf("Unknown command: %c\n", c);
    }
  }

  clear_screen();
  printf("Bye!\n");
  return 0;
}

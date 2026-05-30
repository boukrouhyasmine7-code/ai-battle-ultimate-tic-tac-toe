# Ultimate Tic-Tac-Toe — AI Battle Engine

An advanced, high-performance adversarial AI agent developed for the **ESILV Engineering School Artificial Intelligence Battle**. 

This repository features an optimized game tree search engine written in pure Python that strictly satisfies tournament conditions: no precomputed move dictionaries, depth-limited anytime processing, and strict text-mode external execution relay compatibility.

##  Repository Structure
* **`UltimateTicTacToe_AI_Yasmine.ipynb`**: The core interactive Jupyter Notebook containing the complete game engine, adversarial search algorithms, evaluation metrics, and text-based gameplay interface.

##  Architectural & Performance Optimizations

### 1. In-Place State Mutation & LIFO History Rollback
* **The Problem:** Visiting tens of thousands of board states using standard recursive copying (`deepcopy`) introduces massive computational overhead, bottlenecks memory allocation, and causes execution timeouts in pure Python.
* **The Solution:** Built the `UltimateBoard` engine to mutate a single shared game state instance. Moves are pushed onto a Last-In-First-Out (LIFO) stack history, and the state is perfectly reverted via an optimized `undo_move` configuration wrapped in robust `try...finally` blocks to guarantee flawless state recovery even during unexpected execution timeouts.

### 2. Incremental Zobrist Hashing & Transposition Table
* Utilizes bitwise `XOR` state updates linked to random 64-bit integers initialized at class-load time for every `(board, cell, player)` coordinate.
* Incremental hashing runs in $O(1)$ time during `make_move` and `undo_move`, completely avoiding slow full-board scans.
* Serves as the primary key for a high-efficiency Transposition Table, memoizing exact scores, search depths, bounds flags, and historical move-ordering suggestions to short-circuit redundant node evaluations.

### 3. Tiered Negamax Move-Ordering Pipeline
Alpha-Beta pruning efficiency depends entirely on move ordering. This agent implements a strict priority metric system to maximize alpha-beta cutoffs and dramatically accelerate pruning:
1. **Transposition Table Hit** (Highest weight: `+100,000`)
2. **Killer-Move Heuristic** (Tracks recent cutoff-inducing moves at the same ply: `+5,000` / `+2,500`)
3. **Local Tactical Plays** (Immediate 3-in-a-row wins: `+800` | Critical defensive blocks: `+400`)
4. **Strategic Positioning** (Center/Corner spatial weights)
5. **Send-Rule Penalty** (Penalizes any action that gives the opponent a "free choice" open move: `-25`)

---

##  Domain Heuristic Design

When depth limits are met, the custom evaluation function evaluates non-terminal positions using four weighted components:

* **Meta-Board Threats (Dominant Factor):** Focuses heavily on aligning completed small boards rather than greedy individual cell control. Implements a multi-line scanner (`W_META_LINE = 30`) to heavily reward 2-in-a-rows with open lines.
* **The Blocked-Line Insight:** If a 3-by-3 grid line contains marks from *both* players, its evaluation immediately drops to `0`. This eliminates scattered, greedy "hallucinations" and keeps the AI focused exclusively on valid winning paths.
* **Strategic Ownership:** Multiplies captured small boards by their structural position on the meta-board following a classic **Center > Corners > Edges** weight matrix to optimize long-term positional advantages.

---

##  Game Engine Features & Modes

The interface runs in an efficient, clean text-mode and supports three deployment configurations:
1. **Human vs AI:** Interactive gameplay with real-time diagnostics (nodes evaluated, plies reached, time elapsed) to track search behavior.
2. **AI vs AI:** Autonomous self-play simulation used to tune heuristic weights and measure performance.
3. **AI vs External AI (Relay Mode):** A specialized intermediary mode engineered for live tournament settings, allowing manual entry and synchronization of opposing moves across separate machines.

## 🛠️ Project Stack & Setup
* **Language:** Python 3.x (Built using only the standard library to ensure zero-dependency Google Colab compliance).
* **Core Concepts:** Negamax Search, Alpha-Beta Pruning, Iterative Deepening, Zobrist Hashing, Anytime Algorithm design.

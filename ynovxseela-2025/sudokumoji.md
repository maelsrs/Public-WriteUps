# 🔢 SudokuMoji

### 🛠 Type : `Dev`

## 📝 Description

Afin d'obtenir le flag, vous allez devoir réussir 30 grilles de sudokumoji à la suite. C'est comme un sudoku, mais avec des émojis

Exemple :
```
([👑, 🐽, ↕], [😯, 🔇, 📄], [🎉, 🏟, 🌈])
([🔇, 📄, 🎉], [🏟, 🐽, 🌈], [👑, ↕, 😯])
([🌈, 🏟, 😯], [↕, 👑, 🎉], [📄, 🔇, 🐽])

([↕, 🔇, 🏟], [🌈, 📄, 😯], [🐽, 👑, 🎉])
([🐽, 🌈, 👑], [🎉, 🏟, 🔇], [😯, 📄, ↕])
([😯, 🎉, 📄], [👑, ↕, 🐽], [🔇, 🌈, 🏟])

([🏟, 😯, 🐽], [📄, 🌈, 👑], [↕, 🎉, 🔇])
([📄, ↕, 🔇], [🐽, 🎉, 🏟], [🌈, 😯, 👑])
([🎉, 👑, 🌈], [🔇, 😯, ↕], [🏟, 🐽, 📄])
```

## 🔍 Analyse

Le serveur envoie 30 grilles de SudokuMoji avec certaines cases manquantes (`❌`), qu'il faut remplir correctement avec les bons emojis selon les règles classiques du sudoku :

- Chaque ligne, colonne et carré 3×3 doit contenir **une seule fois chaque symbole**.

La difficulté principale vient du fait que les symboles ne sont pas connus à l’avance : on doit les extraire depuis les premières grilles pour identifier les 9 emojis utilisés.

## 🧠 Résolution

Un solveur a été développé en Python pour :

- **Parser** les grilles envoyées par le serveur
- **Extraire** dynamiquement les emojis utilisés
- Résoudre le sudoku par **backtracking**
- Envoyer les réponses automatiquement au serveur

Le script traite les 30 grilles consécutivement et interrompt la boucle dès que le flag est renvoyé.

## 🤖 Solver

```python
import socket
import re
import numpy as np

def parse_grid(puzzle):
    items = re.findall(r'[^\s\[\],]+', puzzle)
    grid = np.empty((9, 9), dtype=object)
    grid.fill(None)
    
    idx = 0
    for i in range(9):
        for j in range(9):
            if idx < len(items) and items[idx] != '❌':
                grid[i, j] = items[idx]
            idx += 1
    
    return grid

def get_symbols(grid):
    syms = set()
    for i in range(9):
        for j in range(9):
            if grid[i, j] is not None:
                syms.add(grid[i, j])
    
    if len(syms) < 9:
        print(f"Attention: seulement {len(syms)} symboles trouvés")
    
    return list(syms)

def check_valid(grid, r, c, sym):
    for j in range(9):
        if grid[r, j] == sym:
            return False
    
    for i in range(9):
        if grid[i, c] == sym:
            return False
    
    box_r, box_c = 3 * (r // 3), 3 * (c // 3)
    for i in range(box_r, box_r + 3):
        for j in range(box_c, box_c + 3):
            if grid[i, j] == sym:
                return False
    
    return True

def solve(grid, symbols):
    for i in range(9):
        for j in range(9):
            if grid[i, j] is None:
                for sym in symbols:
                    if check_valid(grid, i, j, sym):
                        grid[i, j] = sym
                        
                        if solve(grid, symbols):
                            return True
                        
                        grid[i, j] = None
                
                return False
    
    return True

def format_result(grid):
    result = ""
    for i in range(9):
        for j in range(9):
            result += grid[i, j]
    return result

def solve_puzzle(puzzle):
    grid = parse_grid(puzzle)
    symbols = get_symbols(grid)
    
    if solve(grid, symbols):
        return format_result(grid)
    else:
        return "Échec de résolution"

def launch_solver():
    HOST = '34.242.102.222'
    PORT = 54390
    
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((HOST, PORT))
        
        count = 0
        max_count = 31
        
        while count < max_count:
            data = s.recv(4096).decode('utf-8')
            print(f"Reçu: {data}")
            
            if 'PUZZLE:' in data:
                puzzle_str = data.split('PUZZLE:')[1].strip()
                print(f"Résolution puzzle {count + 1}/{max_count}")
                
                solution = solve_puzzle(puzzle_str)
                print(f"Solution: {solution}")
                
                s.sendall((solution + '\n').encode('utf-8'))
                count += 1
            
            if 'flag' in data.lower() or 'congrat' in data.lower() or "ynov" in data.lower():
                print("Challenge terminé! Flag trouvé.")
                break
            
    print("Fin de connexion")

if __name__ == "__main__":
    launch_solver()
```

## 👨‍💻 Lancement du programme

```
└─[$] <> python3 solve.py 
Reçu: PUZZLE:[❌, ❌, 🎰] [❌, ❌, 🔛] [❌, ❌, 🧌]
[❌, ❌, 🧎] [⬛, 🖲, 🧯] [❌, ❌, 🎰]
[🔛, ❌, ❌] [🧌, ❌, ❌] [✳, ❌, ❌]

[🎰, 🖲, ❌] [❌, ❌, ⬛] [🔛, 🧌, ✳]
[❌, ✳, ⬛] [🎰, 🔛, ❌] [❌, ⛅, 🧎]
[❌, ❌, ❌] [🖲, ✳, ❌] [🎰, ❌, 🧯]

[⬛, 🎰, ❌] [❌, 🧌, ❌] [❌, ❌, 🔛]
[⛅, ❌, ❌] [❌, 🎰, ✳] [🧌, 🖲, ❌]
[🧌, ❌, ✳] [🔛, ⬛, 🖲] [❌, 🎰, ❌]


Résolution puzzle 1/31
Solution: 🖲⛅🎰✳🧎🔛⬛🧯🧌✳🧌🧎⬛🖲🧯⛅🔛🎰🔛⬛🧯🧌⛅🎰✳🧎🖲🎰🖲⛅🧎🧯⬛🔛🧌✳🧯✳⬛🎰🔛🧌🖲⛅🧎🧎🔛🧌🖲✳⛅🎰⬛🧯⬛🎰🖲⛅🧌🧎🧯✳🔛⛅🧎🔛🧯🎰✳🧌🖲⬛🧌🧯✳🔛⬛🖲🧎🎰⛅
Reçu: Correct! 1 puzzles solved. Next puzzle:

[....]

Reçu: PUZZLE:[🎴, ❌, 🍅] [🍒, 🍖, ❌] [👚, 🦍, ❌]
[🥽, 🎒, 🍒] [❌, 🌃, ❌] [🍅, 🎴, 🍖]
[❌, ❌, 🦍] [🎴, 🥽, 🍅] [🎒, ❌, ❌]

[👚, ❌, 🍖] [❌, 🎒, ❌] [❌, 🌃, 🍅]
[🍅, 🥽, 🌃] [🍖, ❌, ❌] [🦍, ❌, ❌]
[🍒, 🦍, 🎒] [🌃, 🍅, 🎴] [🍖, 🥽, ❌]

[🦍, 🍖, 🎴] [❌, 🍒, ❌] [🥽, 👚, 🎒]
[🎒, ❌, ❌] [👚, ❌, 🍖] [❌, 🍅, 🦍]
[🌃, ❌, 👚] [❌, 🦍, 🥽] [❌, 🍖, ❌]


Résolution puzzle 30/31
Solution: 🎴🌃🍅🍒🍖🎒👚🦍🥽🥽🎒🍒🦍🌃👚🍅🎴🍖🍖👚🦍🎴🥽🍅🎒🍒🌃👚🎴🍖🥽🎒🦍🍒🌃🍅🍅🥽🌃🍖👚🍒🦍🎒🎴🍒🦍🎒🌃🍅🎴🍖🥽👚🦍🍖🎴🍅🍒🌃🥽👚🎒🎒🍒🥽👚🎴🍖🌃🍅🦍🌃🍅👚🎒🦍🥽🎴🍖🍒
Reçu: Congratulations! Here's your flag: YNOV{94517eab1b97abc1ebbd1366b0149bf9}
```

## 🏁 Flag

```
YNOV{94517eab1b97abc1ebbd1366b0149bf9}
```

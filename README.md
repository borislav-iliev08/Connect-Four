# Connect-Four

## License & Usage Notice

This repository is provided for **viewing purposes only**.

All rights are reserved by the author.  
No license is granted to use, copy, modify, merge, publish, distribute, sublicense, or sell any part of this code.

You may read the source code, but you are **not permitted to use it** in any project, commercial or non-commercial, without explicit written permission from the author.

Unauthorized use of this code is prohibited.

---

## Source Code

```python
import tkinter as tk
from tkinter import messagebox

def create_matrix(rows, cols):
    return [[0 for _ in range(cols)] for _ in range(rows)]

def update_ui(labels, row, col, player_num):
    color = 'red' if player_num == 1 else 'blue'
    labels[row][col].config(bg=color)

def reset_game(ma, labels):
    for r in range(len(ma)):
        for c in range(len(ma[0])):
            ma[r][c] = 0
            labels[r][c].config(bg='white')

def handle_colulm_click(ma, labels, column_num, p_num, counter, rows, cols, slots):
    try:
        validation_col_coice(column_num, cols)
        row, column_num = place_player_token(ma, column_num, p_num)
        update_ui(labels, row, column_num, p_num)
        counter += 1

        if is_winner(ma, row, column_num, p_num, slots):
            messagebox.showinfo('Game over!', f'The winner is Player {p_num}')
            reset_game(ma, labels)
            return 1, 0

        if counter == rows * cols:
            messagebox.showinfo('Game over!', 'The game is draw!')
            return 1, 0

        return (2 if p_num == 1 else 1), counter

    except FullColumnHere:
        messagebox.showerror('Invalid move!', "Please choose another column")

    return p_num, counter

def validation_col_coice(column, max_idx):
    if not (0 <= column < max_idx):
        raise InvalidColumn()

class InvalidColumn(Exception):
    pass

def place_player_token(ma, col, p_turn):
    for r in range(len(ma) - 1, -1, -1):
        if ma[r][col] == 0:
            ma[r][col] = p_turn
            return r, col
    raise FullColumnHere

class FullColumnHere(Exception):
    pass

def is_winner(ma, r, col, p_num, slots):
    return (
        is_vertical(ma, r, col, p_num, slots) or
        is_horizontal(ma, r, col, p_num, slots) or
        is_p_diagonal(ma, r, col, p_num, slots) or
        is_s_diagonal(ma, r, col, p_num, slots)
    )

def is_player_num(ma, r, col, p_num):
    try:
        return ma[r][col] == p_num
    except IndexError:
        return False

def is_vertical(ma, r, col, p_num, slots):
    return all(is_player_num(ma, r + idx, col, p_num) for idx in range(slots))

def is_horizontal(ma, r, col, p_num, slots):
    filled = 1
    for idx in range(1, slots):
        if is_player_num(ma, r, col + idx, p_num):
            filled += 1
        else:
            break
    for idx in range(1, slots):
        if is_player_num(ma, r, col - idx, p_num):
            filled += 1
        else:
            break
    return filled >= slots

def is_p_diagonal(ma, r, col, p_num, slots):
    filled = 1
    for idx in range(1, slots):
        if is_player_num(ma, r + idx, col + idx, p_num):
            filled += 1
        else:
            break
    for idx in range(1, slots):
        if is_player_num(ma, r - idx, col - idx, p_num):
            filled += 1
        else:
            break
    return filled >= slots

def is_s_diagonal(ma, r, col, p_num, slots):
    filled = 1
    for idx in range(1, slots):
        if is_player_num(ma, r - idx, col + idx, p_num):
            filled += 1
        else:
            break
    for idx in range(1, slots):
        if is_player_num(ma, r + idx, col - idx, p_num):
            filled += 1
        else:
            break
    return filled >= slots

def create_ui(root, rows, cols, slots_to_win):
    matrix = create_matrix(rows, cols)
    labels = [
        [tk.Label(root, width=4, height=2, bg='white', relief='solid')
         for _ in range(cols)]
        for _ in range(rows)
    ]

    for r in range(rows):
        for c in range(cols):
            labels[r][c].grid(row=r + 1, column=c)

    player_state = {'player_num': 1, 'counter': 0}

    def on_click(column_num):
        player_state['player_num'], player_state['counter'] = handle_colulm_click(
            matrix,
            labels,
            column_num,
            player_state['player_num'],
            player_state['counter'],
            rows,
            cols,
            slots_to_win
        )

    for col in range(cols):
        tk.Button(
            root,
            text='^',
            width=4,
            height=2,
            bg='red',
            command=lambda c=col: on_click(c)
        ).grid(row=0, column=col)

def start_game():
    root = tk.Tk()
    root.title('Connect Four')
    create_ui(root, 6, 7, 4)
    root.mainloop()

if __name__ == '__main__':
    start_game()

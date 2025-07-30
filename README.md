import random

class Minesweeper:
    def __init__(self, width, height, mines_coords):
        self.width = width
        self.height = height
        self.board = [[' ' for _ in range(width)] for _ in range(height)]
        self.mines = set()
        self.place_mines(mines_coords)
        self.dug = set()

    def place_mines(self, mines_coords):
        for r, c in mines_coords:
            self.mines.add((r-1, c-1))

    def dig(self, row, col):
        if (row, col) in self.dug:
            return True

        if (row, col) in self.mines:
            return False

        self.dug.add((row, col))

        if self.board[row][col] == 0:
            for r in range(max(0, row-1), min(self.height-1, row+1)+1):
                for c in range(max(0, col-1), min(self.width-1, col+1)+1):
                    if (r, c) in self.mines:
                        continue
                    self.dig(r, c)
        return True

    def __str__(self):
        visible_board = [[' ' for _ in range(self.width)] for _ in range(self.height)]
        for row in range(self.height):
            for col in range(self.width):
                if (row, col) in self.dug:
                    visible_board[row][col] = str(self.get_num_neighboring_mines(row, col))
                else:
                    visible_board[row][col] = ' '

        string_rep = ''
        widths = []
        for idx in range(self.width):
            columns = map(lambda x: x[idx], visible_board)
            widths.append(
                len(
                    max(columns, key = len)
                )
            )

        indices = [str(i) for i in range(self.width)]
        indices_row = '   '
        cells = []
        for idx, col in enumerate(indices):
            format = '%-' + str(widths[idx]) + "s"
            cells.append(format % (col))
        indices_row += '  '.join(cells)
        indices_row += '  \n'

        for i in range(len(visible_board)):
            row = visible_board[i]
            string_rep += f'{i} |'
            cells = []
            for idx, col in enumerate(row):
                format = '%-' + str(widths[idx]) + "s"
                cells.append(format % (col))
            string_rep += ' |'.join(cells)
            string_rep += ' |\n'

        str_len = int(len(string_rep) / self.height)
        string_rep = indices_row + '-'*str_len + '\n' + string_rep + '-'*str_len

        return string_rep

    def get_num_neighboring_mines(self, row, col):
        num_neighboring_mines = 0
        for r in range(max(0, row-1), min(self.height-1, row+1)+1):
            for c in range(max(0, col-1), min(self.width-1, col+1)+1):
                if r == row and c == col:
                    continue
                if (r, c) in self.mines:
                    num_neighboring_mines += 1
        return num_neighboring_mines


def play(width=15, height=5):
    mines_coords = [
        (1, 1), (1, 5), (2, 1), (2, 2), (2, 3), (2, 4), (2, 5),
        (3, 1), (3, 5), (4, 2), (5, 1), (5, 3), (6, 4), (7, 2),
        (7, 5), (8, 4), (9, 1), (9, 3), (10, 2), (11, 1), (12, 2),
        (13, 3), (13, 4), (13, 5), (14, 2), (15, 1)
    ]
    game = Minesweeper(width, height, mines_coords)

    safe = True

    while len(game.dug) < game.width * game.height - len(mines_coords):
        print(game)
        user_input = input("輸入你想揭開的方塊座標 (列,行): ")
        try:
            row, col = map(int, user_input.split(','))
            if not (0 <= row < game.height and 0 <= col < game.width):
                print("座標超出範圍，請重新輸入。")
                continue
        except ValueError:
            print("輸入格式錯誤，請重新輸入。")
            continue


        safe = game.dig(row, col)
        if not safe:
            break

    if safe:
        print("恭喜你！你贏了！")
    else:
        print("遊戲結束！你踩到地雷了！")
        game.dug.update(game.mines)
        print(game)

if __name__ == '__main__':
    play()

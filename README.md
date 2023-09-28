import random

class Ship:
    def __init__(self, coordinates):
        self.coordinates = coordinates

class Board:
    def __init__(self, ships):
        self.ships = ships
        self.board = [['О' for _ in range(6)] for _ in range(6)]
        for ship in self.ships:
            for coordinate in ship.coordinates:
                x, y = coordinate
                self.board[x][y] = '■'
    
    def print_board(self):
        print('  | 1 | 2 | 3 | 4 | 5 | 6 |')
        for i in range(6):
            row = ' | '.join(self.board[i])
            print(f"{i+1} | {row} |")
    
    def is_valid_coordinate(self, x, y):
        if x < 0 or x >= 6 or y < 0 or y >= 6:
            return False
        return True
    
    def is_hit(self, x, y):
        for ship in self.ships:
            if (x, y) in ship.coordinates:
                return True
        return False
    
    def mark_hit(self, x, y):
        self.board[x][y] = 'X'
    
    def mark_miss(self, x, y):
        self.board[x][y] = 'T'
    
    def is_ship_destroyed(self, ship):
        for coordinate in ship.coordinates:
            x, y = coordinate
            if self.board[x][y] != 'X':
                return False
        return True
    
    def is_game_over(self):
        for ship in self.ships:
            if not self.is_ship_destroyed(ship):
                return False
        return True

def player_turn(board):
    while True:
        try:
            x = int(input("Введите номер строки: ")) - 1
            y = int(input("Введите номер столбца: ")) - 1
            if not board.is_valid_coordinate(x, y):
                raise ValueError("Некорректные координаты!")
            if board.board[x][y] == 'X' or board.board[x][y] == 'T':
                raise ValueError("Вы уже стреляли в эту клетку!")
            break
        except ValueError as e:
            print(e)
    return x, y

def computer_turn(board):
    while True:
        x = random.randint(0, 5)
        y = random.randint(0, 5)
        if not board.is_valid_coordinate(x, y):
            continue
        if board.board[x][y] == 'X' or board.board[x][y] == 'T':
            continue
        break
    return x, y

def play_game():
    player_ships = [
        Ship([(0, 0), (0, 1), (0, 2)]),
        Ship([(1, 3), (1, 4)]),
        Ship([(3, 0), (3, 2), (3, 4)]),
        Ship([(4, 4)])
    ]
    computer_ships = [
        Ship([(0, 3), (0, 4), (0, 5)]),
        Ship([(1, 0), (1, 1)]),
        Ship([(3, 2)]),
        Ship([(5, 0), (5, 2), (5, 4)])
    ]
    
    player_board = Board(player_ships)
    computer_board = Board(computer_ships)
    
    while True:
        print("Доска игрока:")
        player_board.print_board()
        print("Доска компьютера:")
        computer_board.print_board()
        
        print("Ход игрока:")
        x, y = player_turn(computer_board)
        if computer_board.is_hit(x, y):
            computer_board.mark_hit(x, y)
            if computer_board.is_ship_destroyed(computer_ships[0]):
                print("Корабль противника подбит!")
        else:
            computer_board.mark_miss(x, y)
        
        if computer_board.is_game_over():
            print("Игрок победил!")
            break
        
        print("Ход компьютера:")
        x, y = computer_turn(player_board)
        if player_board.is_hit(x, y):
            player_board.mark_hit(x, y)
            if player_board.is_ship_destroyed(player_ships[0]):
                print("Ваш корабль подбит!")
        else:
            player_board.mark_miss(x, y)
        
        if player_board.is_game_over():
            print("Компьютер победил!")
            break

play_game()

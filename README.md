# Project
Softare engineering 
import random

class Card:
    def __init__(self, rank, suit):
        self.rank = rank
        self.suit = suit
        if rank in ["Jack", "Queen", "King"]:
            self.value = 10
        elif rank == "Ace":
            self.value = 11
        else:
            self.value = int(rank)

    def __str__(self):
        return f"{self.rank} of {self.suit}"

class Deck:
    def __init__(self):
        suits = ["Hearts", "Diamonds", "Clubs", "Spades"]
        ranks = ["Ace", "2", "3", "4", "5", "6", "7", "8", "9", "10", "Jack", "Queen", "King"]
        self.cards = [Card(rank, suit) for suit in suits for rank in ranks]
        random.shuffle(self.cards)

    def deal_card(self):
        return self.cards.pop()

class Hand:
    def __init__(self):
        self.cards = []
        self.value = 0
        self.aces = 0

    def add_card(self, card):
        self.cards.append(card)
        self.value += card.value
        if card.rank == "Ace":
            self.aces += 1

    def adjust_for_ace(self):
        while self.value > 21 and self.aces > 0:
            self.value -= 10
            self.aces -= 1

class Player:
    def __init__(self, name, chips=100):
        self.name = name
        self.hand = Hand()
        self.chips = chips

    def hit_or_stand(self, deck):
        while True:
            action = input(f"{self.name}, do you want to hit or stand? ")
            if action.lower() == "hit":
                self.hand.add_card(deck.deal_card())
                print(f"{self.name} draws {self.hand.cards[-1]}.")
                self.hand.adjust_for_ace()
                if self.hand.value > 21:
                    print(f"{self.name} busts with a hand of {self.hand.value}.")
                    return "bust"
            elif action.lower() == "stand":
                print(f"{self.name} stands with a hand of {self.hand.value}.")
                return "stand"
            else:
                print("Invalid input. Please enter 'hit' or 'stand'.")

    def make_bet(self):
        while True:
            bet = int(input(f"{self.name}, how many chips would you like to bet? "))
            if bet < 1 or bet > self.chips:
                print(f"Invalid bet. Please enter a bet between 1 and {self.chips}.")
            else:
                self.chips -= bet
                return bet

class Dealer:
    def __init__(self):
        self.name = "Dealer"
        self.hand = Hand()

    def hit_until_stand_or_bust(self, deck):
        while self.hand.value < 17:
            self.hand.add_card(deck.deal_card())
            print(f"Dealer draws {self.hand.cards[-1]}.")
            self.hand.adjust_for_ace()
        if self.hand.value > 21:
            print("Dealer busts!")
            return "bust"
        else:
            print(f"Dealer stands with a hand of {self.hand.value}.")
            return "stand"
class Blackjack:
    def __init__(self):
        self.users = []
        self.dealer = Dealer()
        self.deck = Deck()
    def load_users_from_file(self):
        try:
            with open("users.json", "r") as f:
                self.users = json.load(f)
        except FileNotFoundError:
            pass
    def save_users_to_file(self):
        with open("users.json", "w") as f:
            json.dump(self.users, f)
    def register(self):
        username = input("Enter your username: ")
        password = input("Enter your password: ")
        for user in self.users:
            if user["username"] == username:
                print("Username already exists.")
                return
        self.users.append({"username": username, "password": password, "balance": 100})
        print("Account created successfully.")
        self.save_users_to_file()
    def login(self):
        username = input("Enter your username: ")
        password = input("Enter your password: ")
        for user in self.users:
            if user["username"] == username and user["password"] == password:
                print("Login successful!")
                return user
        print("Invalid username or password.")
        
    while True:
        choice = input("How many players will play? ").lower()
        try:
            if choice >= 1 && choice <=7:
                user = game.login()
            else:
                print("Invalid input. Player(s) must be between 1 to 7")
        except:
            print("Wrong input. Must be a number")   
        
    def play(self):
        print("Starting game of blackjack.")
        for player in self.players:
            print(f"{player.name}, it's your turn.")
            player.make_bet()
            player.hand.add_card(self.deck.deal_card())
            player.hand.add_card(self.deck.deal_card())
            print(f"{player.name} draws {player.hand.cards[0]} and {player.hand.cards[1]}")
        print("Dealer's turn.")
        self.dealer.hand.add_card(self.deck.deal_card())
        self.dealer.hand.add_card(self.deck.deal_card())
        print(f"Dealer's face-up card is {self.dealer.hand.cards[0]}.")
        for player in self.players:
            if player.hit_or_stand(self.deck) == "bust":
                continue
        if self.dealer.hit_until_stand_or_bust(self.deck) == "bust":
            for player in self.players:
                if player.hand.value <= 21:
                    player.chips += 2 * player.make_bet()
                    print(f"{player.name} wins!")
                    print(f"{player.name} now has {player.chips} chips.")
        else:
            dealer_hand_value = self.dealer.hand.value
            for player in self.players:
                if player.hand.value <= 21:
                    if player.hand.value > dealer_hand_value:
                        player.chips += 2 * player.make_bet()
                        print(f"{player.name} wins!")
                        print(f"{player.name} now has {player.chips} chips.")
                    elif player.hand.value == dealer_hand_value:
                        player.chips += player.make_bet()
                        print(f"{player.name} ties with the dealer.")
                        print(f"{player.name} now has {player.chips} chips.")
                    else:
                        print(f"{player.name} loses.")
                        print(f"{player.name} now has {player.chips} chips.")

blackjack_game = Blackjack()
blackjack_game.play()








import openpyxl
import os

def register():
    """Register a new user."""
    username = input("Enter a username: ")
    password = input("Enter a password: ")
    workbook = openpyxl.load_workbook("users.xlsx")
    sheet = workbook.active
    row = sheet.max_row + 1
    sheet.cell(row=row, column=1).value = username
    sheet.cell(row=row, column=2).value = password
    workbook.save("users.xlsx")
    print("Registration successful!\n")

def login():
    """Log in an existing user."""
    username = input("Enter your username: ")
    password = input("Enter your password: ")
    workbook = openpyxl.load_workbook("users.xlsx")
    sheet = workbook.active
    for row in range(1, sheet.max_row + 1):
        if sheet.cell(row=row, column=1).value == username and sheet.cell(row=row, column=2).value == password:
            print("Login successful!\n")
            return
    print("Invalid username or password.\n")

def main():
    """Main function to run the program."""
    if not os.path.exists("users.xlsx"):
        workbook = openpyxl.Workbook()
        sheet = workbook.active
        sheet.cell(row=1, column=1).value = "Username"
        sheet.cell(row=1, column=2).value = "Password"
        workbook.save("users.xlsx")
    while True:
        choice = input("Enter 1 to register, 2 to login, or q to quit: ")
        if choice == "1":
            register()
        elif choice == "2":
            login()
        elif choice.lower() == "q":
            print("Goodbye!")
            break
        else:
            print("Invalid choice. Please try again.\n")

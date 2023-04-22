import random
class Card:
    def __init__(self, rank, suit):
        self.rank = rank
        self.suit = suit
        if rank in ["J", "Q", "K"]:
            self.value = 10
        elif rank == "A":
            self.value = 11
        else:
            self.value = int(rank)

    def __str__(self):
        suits_symbols = {"Hearts": "♥", "Diamonds": "♦", "Clubs": "♣", "Spades": "♠"}
        return f"{self.rank} of {suits_symbols[self.suit]}"

class Deck:
    def __init__(self):
        suits = ["Hearts","Diamonds", "Clubs", "Spades"]
        ranks = ["A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"]
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
        print(f"Card drawn: {card}")
        self.value += card.value
        if card.rank == "A":
            self.aces += 11

    def adjust_for_ace(self):
        while self.value > 21 and self.aces > 0:
            self.value -= 10
            self.aces -= 1

class Player:
    def __init__(self, name, chips=100):
        self.name = name
        self.hand = Hand()
        self.chips = chips
        self.dealer = Dealer()

    def hit_or_stand(self, deck):
        while True:
            print("-----------------------------------------------------------------")
            print(f"Yours cards are: {self.hand.cards[0]} and {self.hand.cards[1]}")
            print(f"The value is {self.hand.value}")
            try:
                action = input(f"{self.name}, do you want to hit or stand? ")
                if action.lower() == "hit":
                    self.hand.add_card(deck.deal_card())
                    self.hand.adjust_for_ace()
                    if self.hand.value > 21:
                        print(f"{self.name} busts with a hand of {self.hand.value}.")
                        return "bust"
                    elif self.hand.value == 21:
                        print(f"{self.name} has a hand of 21")
                        break
                    elif action.lower() == "stand":
                        print(f"{self.name} stands with a hand of {self.hand.value}.")
                        return "stand"
                    else:
                        raise ValueError("Invalid input. Please enter 'hit' or 'stand'.")
            except ValueError as e:
                print("Wrong input. Please enter 'hit' or 'stand'")

    def make_bet(self):
        while True:
            try:
                bet = float(input(f"{self.name}, how many chips would you like to bet? "))
                if bet < 1 or bet > self.chips:
                    raise ValueError(f"Invalid bet. Please enter a bet between 1 and {self.chips}.")
                else:
                    self.chips -= bet
                    return bet
            except ValueError as e:
                print(f"Invalid bet. Please enter a bet between 1 and {self.chips}.")

    def win_bet(self,bet):
        self.chips += 2 * bet

    def tie_bet(self,bet):
        self.chips += bet
        
class Dealer:
    def __init__(self):
        self.name = "Dealer"
        self.hand = Hand()

    def hit_until_stand_or_bust(self, deck):
        print(f"The value is {self.hand.value}")
        while self.hand.value < 17:
            self.hand.add_card(deck.deal_card())
            self.hand.adjust_for_ace()
        if self.hand.value > 21:
            print("Dealer busts!")
            return "bust"
        else:
            print(f"Dealer stands with a hand of {self.hand.value}.")
            return "stand"
class Blackjack:
    def __init__(self):
        self.players = [Player(f"Player {i+1}") for i in range(4)]
        self.dealer = Dealer()
        self.deck = Deck()

    def play(self):
        print("Starting game of blackjack.")
        print("-----------------------------------------------------------------")
        quit = 0
        choice = "0"
        while quit == 0:
            While choice
                print("Login | Register | Quit")
                choice = input("Please select one of the above options: ").strip().lower()
            
            for player in self.players:
                print(f"{player.name}, it's your turn.")
                bet = player.make_bet()
                player.hand.add_card(self.deck.deal_card())
                player.hand.add_card(self.deck.deal_card())
                print("-----------------------------------------------------------------")
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
                        player.win_bet(bet)
                        print(f"{player.name} wins!")
                        print(f"{player.name} now has {player.chips} chips.")
            else:
                dealer_hand_value = self.dealer.hand.value
                for player in self.players:
                    if player.hand.value <= 21:
                        if player.hand.value > dealer_hand_value:
                            player.win_bet(bet)
                            print("-----------------------------------------------------------------")
                            print(f"{player.name} wins!")
                            print(f"{player.name} now has {player.chips} chips.")
                        elif player.hand.value == dealer_hand_value:
                            player.tie_bet(bet)
                            print("-----------------------------------------------------------------")
                            print(f"{player.name} ties with the dealer.")
                            print(f"{player.name} now has {player.chips} chips.")
                        else:
                            print("-----------------------------------------------------------------")
                            print(f"{player.name} loses.")
                            print(f"{player.name} now has {player.chips} chips.")
blackjack_game = Blackjack()
blackjack_game.play()


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------


import openpyxl

# Define the Excel file name and sheet name
filename = "users.xlsx"
sheetname = "users"

# Load the workbook and select the worksheet
workbook = openpyxl.load_workbook(filename)
worksheet = workbook[sheetname]

# Define the column indexes for username and password
username_col = 1
password_col = 2

# Prompt the user to choose login or register
while True:
    choice = input("Do you want to login or register? ").strip().lower()
    if choice == "login" or choice == "register":
        break

# Handle login
if choice == "login":
    # Prompt the user for their username and password
    username = input("Username: ")
    password = input("Password: ")

    # Check if the username and password match a row in the worksheet
    for row in worksheet.iter_rows(min_row=2, values_only=True):
        if row[username_col-1] == username and row[password_col-1] == password:
            print("Login successful!")
            break
    else:
        print("Invalid username or password")

# Handle register
elif choice == "register":
    # Prompt the user for their desired username and password
    username = input("Choose a username: ")
    password = input("Choose a password: ")

    # Check if the username already exists in the worksheet
    for row in worksheet.iter_rows(min_row=2, values_only=True):
        if row[username_col-1] == username:
            print("Username already exists")
            break
    else:
        # Find the next empty row and write the new user's information
        next_row = len(worksheet["A"]) + 1
        worksheet.cell(row=next_row, column=username_col).value = username
        worksheet.cell(row=next_row, column=password_col).value = password
        workbook.save(filename)
        print("Registration successful!")


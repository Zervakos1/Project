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
        self.hidden=True
        
    def add_card(self, card):
        self.cards.append(card)
        if self.hidden==True:
            print(f"Card drawn: {card}")
        self.value += card.value
        if card.rank == "A":
            self.aces += 11

    def adjust_for_ace(self):
        while self.value > 21 and self.aces > 0:
            self.value -= 10
            self.aces -= 1

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
            for card in self.hand.cards:
                print(card)
            print(f"The value is {self.hand.value}")
            try:
                if self.hand.value == 21:
                        print(f"{self.name} has a hand of 21")
                        break
                action = input(f"{self.name}, do you want to hit or stand? ")
                if action.lower() == "hit":
                    self.hand.add_card(deck.deal_card())
                    self.hand.adjust_for_ace()
                    if self.hand.value > 21:
                        print(f"{self.name} busts with a hand of {self.hand.value}.")
                        return "bust"
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
        print("-----------------------------------------------------------------")
        for card in self.hand.cards:
                print(card)
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
        with open("users.txt", "a+") as file:
            # Read the contents of the file
            file.seek(0)
            contents = file.read()
            # Create a dictionary to store usernames and passwords
            users = {}
            # Split the contents of the file by newline and loop through each line
            for line in contents.split("\n"):
                # Split each line into username and password and add to the dictionary
                if line:
                    username, password = line.split(",")
                    users[username] = password
            print("Starting game of blackjack.")
            print("-----------------------------------------------------------------")
            quit = 0
            while quit != 1:
                while choice != "register" and choice != "play" and choice != "quit":
                    choice = ""
                    choice = input("Do you want to:\n1.register \n2.play \n3.quit ").strip().lower()
                    if choice != "register" and choice != "play" and choice != "quit":
                        print("Incorrect input. Please enter one of the choices shown.")
                if choice == "register":
                    # Check if the username already exists
                    while True:
                        username = input("Enter a username: ").strip()
                        if username in users:
                            print("Username already exists. Please choose a different username.")
                        else:
                            break    
                    password = input("Enter a password: ").strip()
                    # Add the new username and password to the file
                    file.write(f"{username},{password}\n")
                    # Create a new player with the given username
                    self.players.append(Player(username))
                    print(f"Welcome, {username}!")
                elif choice == "play":
                    playAgain = 1
                    while playAgain == 1:
                        while numberofplayers < 1 or numberofplayers > 7:
                            numberofplayers = input("Please enter the number of players (1-7): ")
                            if numberofplayers < 1 or numberofplayers > 7:
                                print("Please enter a number from 1 to 7.")
                        for i in range(1, numberofplayers):
                            while True:
                                username = input("Enter your username: ").strip()
                                password = input("Enter your password: ").strip()
                                # Check if the username and password are correct
                                if username in users and users[username] == password:
                                    self.players.append(Player(username))
                                    print(f"Welcome back, {username}!")
                                    break
                                else:
                                    print("Incorrect username or password. Please try again."
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
                  else:
                      quit == 1
                      print "You have successfully quit the program"           
blackjack_game = Blackjack()
blackjack_game.play()


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Open the file for reading and writing
        with open("users.txt", "a+") as file:

            # Read the contents of the file
            file.seek(0)
            contents = file.read()

            # Create a dictionary to store usernames and passwords
            users = {}

            # Split the contents of the file by newline and loop through each line
            for line in contents.split("\n"):

                # Split each line into username and password and add to the dictionary
                if line:
                    username, password = line.split(",")
                    users[username] = password

            # Ask the user to login or register
            choice = ""
            while choice != "login" and choice != "register" and choice != "quit":
                choice = input("Do you want to:\n1.login\n2.register \n3.play \n4.quit ").strip().lower()

            # Handle the user's choice
            if choice == "login":
                username = input("Enter your username: ").strip()
                password = input("Enter your password: ").strip()

                # Check if the username and password are correct
                if username in users and users[username] == password:
                    self.players.append(Player(username))
                    print(f"Welcome back, {username}!")
                else:
                    print("Incorrect username or password. Please try again.")
                    return

            elif choice == "register":

                # Check if the username already exists
                while True:
                    username = input("Enter a username: ").strip()
                    if username in users:
                        print("Username already exists. Please choose a different username.")
                    else:
                        break    
                password = input("Enter a password: ").strip()
                # Add the new username and password to the file
                file.write(f"{username},{password}\n")
                # Create a new player with the given username
                self.players.append(Player(username))
                print(f"Welcome, {username}!")

            elif choice == "quit":
                print("You have successfully quitted the program.")
                exit

        # Print the list of players
        print("Players:")
        for player in self.players:
            print(player.name)


-------------------------------------------------------------------------------------------------------------------------------------------------------
kainourgio konstantinos
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
        self.hidden=True
        
    def add_card(self, card):
        self.cards.append(card)
        if self.hidden==True:
            print(f"Card drawn: {card}")
        self.value += card.value
        if card.rank == "A":
            self.aces += 11

    def adjust_for_ace(self):
        while self.value > 21 and self.aces > 0:
            self.value -= 10
            self.aces -= 1

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
            for card in self.hand.cards:
                print(card)
            print(f"The value is {self.hand.value}")
            try:
                if self.hand.value == 21:
                        print(f"{self.name} has a hand of 21")
                        break
                action = input(f"{self.name}, do you want to hit or stand? ")
                if action.lower() == "hit":
                    self.hand.add_card(deck.deal_card())
                    self.hand.adjust_for_ace()
                    if self.hand.value > 21:
                        print(f"{self.name} busts with a hand of {self.hand.value}.")
                        return "bust"
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
        print("-----------------------------------------------------------------")
        for card in self.hand.cards:
                print(card)
        print(f"The value is {self.hand.value}")
        while self.hand.value < 17:
            self.hand.add_card(deck.deal_card())
            self.hand.adjust_for_ace()
        if self.hand.value > 21:
            print(f"Dealer busts with a hand of {self.hand.value}.")
            return "bust"
        else:
            print(f"Dealer stands with a hand of {self.hand.value}.")
            return "stand"
class Blackjack:
    def __init__(self):
        self.players = [Player(f"Player {i+1}") for i in range(1)]
        self.dealer = Dealer()
        self.deck = Deck()
    # Open the file for reading and writing
        with open("users.txt", "a+") as file:

            # Read the contents of the file
            file.seek(0)
            contents = file.read()

            # Create a dictionary to store usernames and passwords
            users = {}

            # Split the contents of the file by newline and loop through each line
            for line in contents.split("\n"):

                # Split each line into username and password and add to the dictionary
                if line:
                    username, password = line.split(",")
                    users[username] = password

            # Ask the user to login or register
            choice = ""
            while choice != "login" and choice != "register" and choice != "quit":
                choice = input("Do you want to:\n1.login\n2.register \n3.play \n4.quit ").strip().lower()

            # Handle the user's choice
            if choice == "login":
                username = input("Enter your username: ").strip()
                password = input("Enter your password: ").strip()

                # Check if the username and password are correct
                if username in users and users[username] == password:
                    self.players.append(Player(username))
                    print(f"Welcome back, {username}!")
                else:
                    print("Incorrect username or password. Please try again.")
                    return

            elif choice == "register":
                username = input("Enter a username: ").strip()
                password = input("Enter a password: ").strip()

                # Check if the username already exists
                while True:
                    if username in users:
                        print("Username already exists. Please choose a different username.")
                    else:
                        break    

                # Add the new username and password to the file
                file.write(f"{username},{password}\n")

                # Create a new player with the given username
                self.players.append(Player(username))
                print(f"Welcome, {username}!")

            elif choice == "quit":
                print("You have successfully quitted the program.")
                exit()

        # Print the list of players
        print("Players:")
        for player in self.players:
            print(player.name)
            
            

    def play(self):
        print("Starting game of blackjack.")
        print("-----------------------------------------------------------------")
        for player in self.players:
            print(f"{player.name}, it's your turn.")
            bet = player.make_bet()
            player.hand.add_card(self.deck.deal_card())
            player.hand.add_card(self.deck.deal_card())
            print("-----------------------------------------------------------------")
        print("Dealer's turn.")
        
        self.dealer.hand.add_card(self.deck.deal_card())
        print(f"Dealer's face-up card is {self.dealer.hand.cards[0]}.")
        self.dealer.hand.add_card(self.deck.deal_card())
            
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

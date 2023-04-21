# Project
Softare engineering 
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
        self.dealer= Dealer()

    def hit_or_stand(self, deck):
        while True:
            print("-----------------------------------------------------------------")
            print(f"Yours cards are: {self.hand.cards[0]} and {self.hand.cards[1]}")
            print(f"The value is {self.hand.value}")
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
                print("Invalid input. Please enter 'hit' or 'stand'.")

    def make_bet(self):
        while True:
            bet = float(input(f"{self.name}, how many chips would you like to bet? "))
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
            self.hand.adjust_for_ace()
            print(f"The value is {self.hand.value}")
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
        for player in self.players:
            print(f"{player.name}, it's your turn.")
            player.make_bet()
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
                    player.chips += 2 * player.make_bet()
                    print(f"{player.name} wins!")
                    print(f"{player.name} now has {player.chips} chips.")
        else:
            dealer_hand_value = self.dealer.hand.value
            for player in self.players:
                if player.hand.value <= 21:
                    if player.hand.value > dealer_hand_value:
                        player.chips += 2 * player.make_bet()
                        print("-----------------------------------------------------------------")
                        print(f"{player.name} wins!")
                        print(f"{player.name} now has {player.chips} chips.")
                    elif player.hand.value == dealer_hand_value:
                        player.chips += player.make_bet()
                        print("-----------------------------------------------------------------")
                        print(f"{player.name} ties with the dealer.")
                        print(f"{player.name} now has {player.chips} chips.")
                    else:
                        print("-----------------------------------------------------------------")
                        print(f"{player.name} loses.")
                        print(f"{player.name} now has {player.chips} chips.")
blackjack_game = Blackjack()
blackjack_game.play()

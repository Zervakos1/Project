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
    def __init__(self, name):
        self.name = name
        self.hand = Hand()

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
        self.players = [Player(f"Player {i+1}") for i in range(4)]
        self.dealer = Dealer()
        self.deck = Deck()

    def play(self):
        print("Starting game of blackjack.")
        for player in self.players:
            print(f"{player.name}, it's your turn.")
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
                    print(f"{player.name} wins!")
        else:
            dealer_hand_value = self.dealer.hand.value
            for player in self.players:
                if player.hand.value <= 21:
                    if player.hand.value > dealer_hand_value:
                        print(f"{player.name} wins!")
                    elif player.hand.value == dealer_hand_value:
                        print(f"{player.name} ties with the dealer.")
                    else:
                        print(f"{player.name} loses.")
blackjack_game = Blackjack()
blackjack_game.play()

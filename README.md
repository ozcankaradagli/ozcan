import random

class Card:
    def __init__(self, suit, rank):
        self.suit = suit
        self.rank = rank

    def value(self):
        if self.rank in ["J", "Q", "K"]:
            return 10
        elif self.rank == "A":
            return 11  # Ace will be handled in Hand
        else:
            return int(self.rank)

    def __str__(self):
        return f"{self.rank} of {self.suit}"


class Deck:
    def __init__(self):
        suits = ["Hearts", "Diamonds", "Clubs", "Spades"]
        ranks = [str(i) for i in range(2, 11)] + ["J", "Q", "K", "A"]
        self.cards = [Card(s, r) for s in suits for r in ranks]
        random.shuffle(self.cards)

    def deal(self):
        return self.cards.pop()


class Hand:
    def __init__(self):
        self.cards = []

    def add_card(self, card):
        self.cards.append(card)

    def get_value(self):
        total = sum([card.value() for card in self.cards])
        # Adjust for Aces
        aces = len([card for card in self.cards if card.rank == "A"])
        while total > 21 and aces:
            total -= 10
            aces -= 1
        return total

    def __str__(self):
        return ", ".join(str(card) for card in self.cards)


class Player:
    def __init__(self, name):
        self.name = name
        self.hand = Hand()

    def play_turn(self, deck):
        while DecisionAlgorithm.should_hit(self.hand):
            self.hand.add_card(deck.deal())
        return self.hand.get_value()


class DecisionAlgorithm:
    @staticmethod
    def should_hit(hand):
        return hand.get_value() < 17


class Game:
    def __init__(self):
        self.deck = Deck()
        self.player1 = Player("Player 1")
        self.player2 = Player("Player 2")

    def initial_deal(self):
        for _ in range(2):
            self.player1.hand.add_card(self.deck.deal())
            self.player2.hand.add_card(self.deck.deal())

    def play(self):
        self.initial_deal()
        print("Player 1 Hand:", self.player1.hand)
        p1_score = self.player1.play_turn(self.deck)
        print("Player 1 Final Score:", p1_score)

        print("\nPlayer 2 Hand:", self.player2.hand)
        p2_score = self.player2.play_turn(self.deck)
        print("Player 2 Final Score:", p2_score)

        if p1_score > 21 and p2_score > 21:
            print("\nBoth players busted.")
        elif p1_score > 21:
            print("\nPlayer 2 wins!")
        elif p2_score > 21:
            print("\nPlayer 1 wins!")
        elif p1_score > p2_score:
            print("\nPlayer 1 wins!")
        elif p2_score > p1_score:
            print("\nPlayer 2 wins!")
        else:
            print("\nIt's a tie!")


if __name__ == "__main__":
    game = Game()
    game.play()

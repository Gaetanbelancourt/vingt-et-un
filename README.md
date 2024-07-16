# Author: Gaetan Belancourt
# 07/15/2024
# vingtEtUn.py
#
# Program Summary: This program implements the game of Vingt-et-un, a simplified version of Blackjack.
# Players take turns drawing from the card deck to get as close to 21 as possible without going over.
# The program simulates player vs. computer gameplay.

# IPO Chart:
# Input:
#      - Player's name
#      - Player's choices (see rules, play game, quit)
#      - Player's decisions during the game (stay or hit)
# Processing:
#      - Prompt for and store player'sname
#      - Display menu and process user's choice
#      - If choice is 1, display the rules of the game
#      - If choice is 2, play the game:
#            Initialize game variables
#            Loop through rounds of cards giving for both player and house
#            Check for busts and handle game logic
#            Determine winner or tie based on scores
#            Display results after the game ends
#      - If choice is 3, display a goodbye message and quit
#      - Validate all menu choices and user inputs
#      - Functional decomposition into multiple function
#
# Output:
#      - Rules of the game
#      - Prompts for player's actions during the game
#      - Display of card giving and running totals
#      - Result of each game round (winner, loser, tie)
#      - Good-bye message upon quitting


import random
import time
import os

class Deck:
    """Class to represent a deck of cards."""
    def __init__(self):
        """Initialize the deck by creating a list of cards and shuffling them."""
        self.cards = [value for value in range(1, 11)] * 4
        random.shuffle(self.cards)  # Shuffle the deck to randomize the card order

    def draw(self, num_cards):
        """Draw a specified number of cards from the deck.

        Args:
            num_cards (int): Number of cards to draw.

        Returns:
            list: List of drawn card values.
        """
        drawn_cards = [self.cards.pop() for _ in range(num_cards)]  # Remove cards from the deck and return them
        return drawn_cards

    def is_empty(self):
        """Check if the deck is empty.

        Returns:
            bool: True if the deck has no cards left, False otherwise.
        """
        return len(self.cards) == 0

class Player:
    """Class to represent a player in the game."""
    def __init__(self, name):
        """Initialize the player with a name and an empty hand of cards.

        Args:
            name (str): The name of the player.
        """
        self.name = name
        self.cards = []  # List to store the player's cards

    def draw(self, deck, num_cards=1):
        """Draw a specified number of cards from the deck and add them to the player's hand.

        Args:
            deck (Deck): The deck to draw cards from.
            num_cards (int): Number of cards to draw. Defaults to 1.
        """
        self.cards.extend(deck.draw(num_cards))  # Draw cards and add them to the player's hand

    def total(self):
        """Calculate the total value of the player's cards, treating Aces as 1 or 11.

        Returns:
            int: The total value of the player's hand.
        """
        total = sum(self.cards)  # Start by summing all card values
        num_aces = self.cards.count(1)  # Count the number of Aces (value 1)
        while total <= 11 and num_aces > 0:  # If total is 11 or less and there are Aces
            total += 10  # Treat one Ace as 11
            num_aces -= 1  # Decrease the count of Aces
        return total

    def reset(self):
        """Reset the player's hand for a new round."""
        self.cards = []  # Clear the player's cards

    def __str__(self):
        """Return a string representation of the player's hand and total value.

        Returns:
            str: Description of the player's cards and total value.
        """
        return f"{self.name} has cards: {self.cards} with a total of {self.total()}"

def display_rules():
    """Display the rules of the game to the player."""
    rules_text = """
🎲 Vingt-et-un Rules 🎲:
- The goal is to score 21 points or as near as possible without going over.
- Players take turns drawing from the card deck and adding up the numbers.
- A player who totals over 21 loses the game.
- The player closest to 21 without going over wins.
- If both players have the same total, it's a tie.
- Once a player totals 14 or more, only one card is drawn.
- The house must stay at 17 or higher.
🃏 This game is similar to playing Blackjack 21 online versus a computer."""
    print(rules_text)

def clear_console():
    """Clear the console to provide a clean output display."""
    os.system('cls' if os.name == 'nt' else 'clear')  # Use the appropriate command for clearing console based on OS

def welcome_player(player_name):
    """Display a welcome message to the player.

    Args:
        player_name (str): The name of the player.
    """
    clear_console()  # Clear console before displaying the welcome message
    print("\n" + "=" * 60)  # Print a line of equal signs for visual separation
    welcome_text = f"{' ' * 18}🎉 WELCOME TO VINGT-ET-UN! 🎉"
    print(welcome_text)
    
    # Center the player's welcome message
    welcome_message = f"Welcome, {player_name}!"
    centered_message = welcome_message.center(60)  # Center the message in a 60-character width
    print(centered_message)
    
    print("=" * 60 + "\n")  # Print another line of equal signs for visual separation

def play_game(player, house, deck):
    """Play a round of Vingt-et-un.

    Args:
        player (Player): The player object.
        house (Player): The house object.
        deck (Deck): The deck from which cards are drawn.

    Returns:
        str: The result of the game: 'player', 'house', or 'tie'.
    """
    # Draw initial cards for both player and house
    player.draw(deck, 2)
    house.draw(deck, 2)

    print(f"{player}")  # Display player's initial hand
    print(f"House starts with: {house.cards[:1]} and one hidden card.")  # Display only one of the house's initial cards

    while True:
        choice = input("Do you want to (H)it or (S)tay? ").lower()  # Ask player if they want to hit or stay
        if choice[0] == 'h':
            player.draw(deck)  # Draw one card for the player
            print(f"You drew: {player.cards[-1]}. {player}")  # Show the drawn card and updated hand
            if player.total() == 21:
                print(f"🎉 Congratulations! You hit 21! 🎉")  # Player wins by hitting 21
                return 'player'
            elif player.total() > 21:
                print(f"💥 Sorry, you busted with {player.total()}. You lose! 💔")  # Player loses by busting
                return 'house'
        elif choice[0] == 's':
            break  # Exit the loop if player chooses to stay
        else:
            print("Invalid choice. Please choose again.")  # Handle invalid input

    # Reveal house cards and play the house's turn
    print(f"House reveals its cards: {house.cards}. House total is {house.total()}")
    while house.total() < 17:  # House must draw cards until reaching a total of 17 or more
        house.draw(deck)
        print(f"House drew: {house.cards[-1]}. {house}")
        time.sleep(1)  # Pause for suspense

    # Determine the outcome of the game
    if house.total() > 21:
        print(f"House busts! 🎉 Congratulations {player.name}, you win! 🎉")
        return 'player'
    
    if house.total() > player.total():
        print(f"💔 Sorry {player.name}, you lose. House total is {house.total()}. 💔")
        return 'house'
    elif house.total() == player.total():
        print("🤝 It's a tie! 🤝")
        return 'tie'
    else:
        print(f"🎉 You got more points! Congratulations {player.name}, you win! 🎉")
        return 'player'

def main():
    """Main function to drive the game loop and handle user interactions."""
    clear_console()  # Clear the console before starting the game
    print("=" * 60)  # Print a line of equal signs for visual separation
    print(" " * 20 + "🎲 Vingt-et-un Game 🎲")  # Game title
    print("=" * 60)  # Print another line of equal signs for visual separation

    player_name = input("Enter your name: ").strip()  # Get player's name
    player = Player(player_name)  # Create a Player object for the user
    house = Player("House")  # Create a Player object for the house

    while True:
        deck = Deck()  # Create a new Deck object for each round

        # Reset hands for new round
        player.reset()
        house.reset()

        welcome_player(player_name)  # Display a welcome message

        print("\nMenu:")  # Display menu options
        print("1. See Rules")
        print("2. Play Vingt-et-un")
        print("3. Quit")

        choice = input("Enter your choice (1, 2, or 3): ").lower()  # Get user's menu choice
        if choice == '1':
            display_rules()  # Show the rules of the game
        elif choice == '2':
            if deck.is_empty():  # Check if the deck is empty and reinitialize if necessary
                print("The deck is empty. Please restart the game.")
                deck = Deck()  # Reinitialize the deck
            play_game(player, house, deck)  # Play a game round
        elif choice == '3':
            print("Thank you for playing Vingt-et-un! Goodbye! 👋")  # Exit message
            break
        else:
            print("Invalid choice. Please choose again.")  # Handle invalid input

if __name__ == "__main__":
    main()  # Start the game

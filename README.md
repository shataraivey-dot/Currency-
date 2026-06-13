# Currency-echo "# Currency-" >> README.mdrency:
    # ---------------------------------------------------------
    # "The Big 3" 
    # (In Python, this usually refers to initialization and 
    # string representations: __init__, __str__, and __repr__)
    # ---------------------------------------------------------
    def __init__(self, amount=0.0, name="Credits"):
        """Initializes the generic unit of value."""
        self.amount = float(amount)
        self.name = name

    def __str__(self):
        """Returns a user-friendly string representation."""
        return f"{self.amount:,.2f} {self.name}"

    def __repr__(self):
        """Returns a developer-friendly string representation."""
        return f"Currency(amount={self.amount}, name='{self.name}')"

    # ---------------------------------------------------------
    # Required Magic Methods (Overloaded Operators)
    # ---------------------------------------------------------
    def __mul__(self, scalar):
        """Multiplies the currency by a scalar value."""
        if isinstance(scalar, (int, float)):
            return Currency(self.amount * scalar, self.name)
        return NotImplemented

    def __truediv__(self, scalar):
        """Divides the currency by a scalar value."""
        if isinstance(scalar, (int, float)):
            if scalar == 0:
                raise ZeroDivisionError("Cannot divide currency by zero.")
            return Currency(self.amount / scalar, self.name)
        return NotImplemented

    def __iadd__(self, other):
        """In-place addition (+=). Supports adding another Currency or a number."""
        if isinstance(other, Currency):
            if self.name != other.name:
                raise ValueError("Cannot add different types of currencies together.")
            self.amount += other.amount
        elif isinstance(other, (int, float)):
            self.amount += other
        else:
            return NotImplemented
        return self

    def __isub__(self, other):
        """In-place subtraction (-=). Supports subtracting another Currency or a number."""
        if isinstance(other, Currency):
            if self.name != other.name:
                raise ValueError("Cannot subtract different types of currencies together.")
            self.amount -= other.amount
        elif isinstance(other, (int, float)):
            self.amount -= other
        else:
            return NotImplemented
        return self

    def __le__(self, other):
        """Less than or equal to (<=) comparison."""
        if isinstance(other, Currency):
            return self.amount <= other.amount
        elif isinstance(other, (int, float)):
            return self.amount <= other
        return NotImplemented

    def __ge__(self, other):
        """Greater than or equal to (>=) comparison."""
        if isinstance(other, Currency):
            return self.amount >= other.amount
        elif isinstance(other, (int, float)):
            return self.amount >= other
        return NotImplemented

    def __int__(self):
        """Converts the currency amount to an integer."""
        return int(self.amount)

    def __float__(self):
        """Converts the currency amount to a float."""
        return float(self.amount)

    # ---------------------------------------------------------
    # Conversion Method
    # ---------------------------------------------------------
    def convert(self, target_type, exchange_rate):
        """
        Converts between the in-game currency and USD (or vice versa).
        
        Args:
            target_type (str): The name of the currency to convert to (e.g., "USD").
            exchange_rate (float): The multiplier to convert the current amount.
            
        Returns:
            Currency: A new Currency object representing the converted value.
        """
        converted_amount = self.amount * exchange_rate
        return Currency(converted_amount, target_type)

    # ---------------------------------------------------------
    # Additional Non-Special Method
    # ---------------------------------------------------------
    def is_bankrupt(self):
        """Checks if the currency account is depleted (<= 0)."""
        return self.amount <= 0


# ==========================================
# Example Usage / Testing (Optional)
# ==========================================
if __name__ == "__main__":
    # Create some in-game currency
    player_wallet = Currency(500, "Gold Coins")
    print(f"Starting Wallet: {player_wallet}")
    
    # Test __iadd__ (+=)
    player_wallet += 200
    print(f"After finding loot: {player_wallet}")
    
    # Test __isub__ (-=)
    potion_cost = Currency(50, "Gold Coins")
    player_wallet -= potion_cost
    print(f"After buying a potion: {player_wallet}")
    
    # Test __mul__ and __truediv__
    doubled_wallet = player_wallet * 2
    print(f"Doubled: {doubled_wallet}")
    halved_wallet = player_wallet / 2
    print(f"Halved: {halved_wallet}")
    
    # Test Comparisons (__ge__, __le__)
    print(f"Can afford 1000 Gold item? {player_wallet >= 1000}")
    
    # Test Conversion to USD
    # Let's say 1 Gold Coin = $0.05 USD
    usd_wallet = player_wallet.convert("USD", 0.05)
    print(f"Real world value: {usd_wallet}")
    
    # Test Non-Special method
    broke_player = Currency(0, "Chips")
    print(f"Is broke player bankrupt? {broke_player.is_bankrupt()}")

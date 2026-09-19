#!/usr/bin/env python3
"""
Scientific Calculator
======================
A fully functioning command-line scientific calculator supporting
basic arithmetic, trigonometry, logarithms, powers, roots, factorials,
constants, and memory functions.

Run: python scientific_calculator.py
"""

import importlib
import math
import subprocess
import sys

# ---------------------------------------------------------------------------
# Auto-pip: automatically installs any missing third-party dependencies.
# The core calculator only needs the standard library, but this makes the
# script self-healing if optional packages (e.g. 'sympy' for symbolic/
# fraction-precise math) are added later or are missing from the environment.
# ---------------------------------------------------------------------------
REQUIRED_PACKAGES = {
    "sympy": "sympy",  # import name -> pip package name
}


def ensure_packages_installed(packages: dict):
    missing = []
    for import_name in packages:
        try:
            importlib.import_module(import_name)
        except ImportError:
            missing.append(packages[import_name])

    if not missing:
        return

    print(f"Installing missing dependencies: {', '.join(missing)} ...")
    try:
        subprocess.check_call(
            [sys.executable, "-m", "pip", "install", "--quiet", *missing]
        )
        print("Dependencies installed successfully.\n")
    except subprocess.CalledProcessError as e:
        print(
            f"Warning: could not auto-install {missing} ({e}). "
            "Falling back to built-in math only.\n"
        )


ensure_packages_installed(REQUIRED_PACKAGES)

try:
    import sympy
    SYMPY_AVAILABLE = True
except ImportError:
    SYMPY_AVAILABLE = False


class ScientificCalculator:
    def __init__(self):
        self.memory = 0.0
        self.history = []
        self.angle_mode = "deg"  # "deg" or "rad"

    # ---------- Helper conversions ----------
    def to_radians(self, x):
        return math.radians(x) if self.angle_mode == "deg" else x

    def to_output_angle(self, x):
        return math.degrees(x) if self.angle_mode == "deg" else x

    # ---------- Basic operations ----------
    def add(self, a, b):
        return a + b

    def subtract(self, a, b):
        return a - b

    def multiply(self, a, b):
        return a * b

    def divide(self, a, b):
        if b == 0:
            raise ZeroDivisionError("Cannot divide by zero.")
        return a / b

    def power(self, a, b):
        return a ** b

    def nth_root(self, a, n):
        if a < 0 and n % 2 == 0:
            raise ValueError("Cannot take an even root of a negative number.")
        if a < 0:
            return -((-a) ** (1 / n))
        return a ** (1 / n)

    def modulo(self, a, b):
        if b == 0:
            raise ZeroDivisionError("Cannot divide by zero.")
        return a % b

    # ---------- Trigonometric functions ----------
    def sin(self, x):
        return math.sin(self.to_radians(x))

    def cos(self, x):
        return math.cos(self.to_radians(x))

    def tan(self, x):
        return math.tan(self.to_radians(x))

    def asin(self, x):
        return self.to_output_angle(math.asin(x))

    def acos(self, x):
        return self.to_output_angle(math.acos(x))

    def atan(self, x):
        return self.to_output_angle(math.atan(x))

    def sinh(self, x):
        return math.sinh(x)

    def cosh(self, x):
        return math.cosh(x)

    def tanh(self, x):
        return math.tanh(x)

    # ---------- Logarithmic / exponential ----------
    def log10(self, x):
        if x <= 0:
            raise ValueError("Logarithm undefined for non-positive numbers.")
        return math.log10(x)

    def ln(self, x):
        if x <= 0:
            raise ValueError("Natural log undefined for non-positive numbers.")
        return math.log(x)

    def log_base(self, x, base):
        if x <= 0 or base <= 0 or base == 1:
            raise ValueError("Invalid log arguments.")
        return math.log(x, base)

    def exp(self, x):
        return math.exp(x)

    # ---------- Other functions ----------
    def factorial(self, x):
        if x < 0 or not float(x).is_integer():
            raise ValueError("Factorial requires a non-negative integer.")
        return math.factorial(int(x))

    def sqrt(self, x):
        if x < 0:
            raise ValueError("Cannot take square root of a negative number.")
        return math.sqrt(x)

    def reciprocal(self, x):
        if x == 0:
            raise ZeroDivisionError("Cannot divide by zero.")
        return 1 / x

    def percent(self, x):
        return x / 100

    # ---------- Memory functions ----------
    def memory_store(self, x):
        self.memory = x

    def memory_recall(self):
        return self.memory

    def memory_clear(self):
        self.memory = 0.0

    def memory_add(self, x):
        self.memory += x

    def memory_subtract(self, x):
        self.memory -= x

    # ---------- Exact / symbolic evaluation (uses sympy if available) ----------
    def evaluate_exact(self, expr):
        """Return an exact (fraction/symbolic) result using sympy, e.g.
        '1/3 + 1/6' -> '1/2' instead of a float approximation."""
        if not SYMPY_AVAILABLE:
            raise RuntimeError(
                "sympy is not available and could not be auto-installed."
            )
        expr = expr.strip().replace("^", "**")
        try:
            result = sympy.sympify(expr, evaluate=True)
            return sympy.simplify(result)
        except Exception as e:
            raise ValueError(f"Invalid expression: {e}")

    # ---------- Expression evaluator ----------
    def evaluate_expression(self, expr):
        """
        Safely evaluate a free-form mathematical expression string,
        supporting +, -, *, /, ^ (power), %, parentheses, constants
        (pi, e), and function calls like sin(), cos(), sqrt(), log(), ln().
        """
        expr = expr.strip()
        if not expr:
            raise ValueError("Empty expression.")

        # Replace common notations
        expr = expr.replace("^", "**")

        # Whitelisted names available inside eval()
        safe_names = {
            "sin": self.sin, "cos": self.cos, "tan": self.tan,
            "asin": self.asin, "acos": self.acos, "atan": self.atan,
            "sinh": self.sinh, "cosh": self.cosh, "tanh": self.tanh,
            "log": self.log10, "ln": self.ln, "exp": self.exp,
            "sqrt": self.sqrt, "fact": self.factorial,
            "abs": abs, "round": round,
            "pi": math.pi, "e": math.e,
        }

        # Basic character whitelist to block unsafe input
        allowed_chars = set("0123456789.+-*/()%, " + "".join(safe_names.keys()))
        # Quick sanity check (letters must belong to allowed function/const names)
        stripped_expr = expr
        for name in safe_names:
            stripped_expr = stripped_expr.replace(name, "")
        for ch in stripped_expr:
            if ch.isalpha():
                raise ValueError(f"Unknown symbol or function: '{ch}'")

        try:
            result = eval(expr, {"__builtins__": {}}, safe_names)
        except ZeroDivisionError:
            raise ZeroDivisionError("Cannot divide by zero.")
        except Exception as e:
            raise ValueError(f"Invalid expression: {e}")

        return result


def print_menu():
    print("""
========================================
        SCIENTIFIC CALCULATOR
========================================
  Type an expression directly, e.g.:
      2 + 3 * 4
      sin(30) + sqrt(16)
      log(100) - ln(e)
      2 ^ 10

  Commands:
    menu        Show this menu
    mode        Toggle angle mode (deg/rad)
    exact EXPR  Exact/symbolic result, e.g. 'exact 1/3 + 1/6' -> 1/2
    mstore X    Store X in memory (or 'mstore ans')
    mrecall     Recall memory value
    mclear      Clear memory
    madd X      Add X to memory
    msub X      Subtract X from memory
    hist        Show calculation history
    clear       Clear the screen history log
    quit / exit Exit the calculator
========================================
""")


def main():
    calc = ScientificCalculator()
    last_answer = 0.0

    print_menu()
    print(f"Angle mode: {calc.angle_mode.upper()}")

    while True:
        try:
            user_input = input("\n>> ").strip()
        except (EOFError, KeyboardInterrupt):
            print("\nGoodbye!")
            break

        if not user_input:
            continue

        cmd = user_input.lower()

        if cmd in ("quit", "exit"):
            print("Goodbye!")
            break

        elif cmd == "menu":
            print_menu()
            continue

        elif cmd == "mode":
            calc.angle_mode = "rad" if calc.angle_mode == "deg" else "deg"
            print(f"Angle mode set to {calc.angle_mode.upper()}")
            continue

        elif cmd == "mrecall":
            print(f"Memory: {calc.memory_recall()}")
            continue

        elif cmd == "mclear":
            calc.memory_clear()
            print("Memory cleared.")
            continue

        elif cmd == "hist":
            if not calc.history:
                print("No history yet.")
            else:
                for i, (expr, res) in enumerate(calc.history, 1):
                    print(f"{i}. {expr} = {res}")
            continue

        elif cmd == "clear":
            calc.history.clear()
            print("History cleared.")
            continue

        elif cmd.startswith("mstore"):
            arg = user_input[6:].strip()
            try:
                value = last_answer if arg.lower() == "ans" else float(arg)
                calc.memory_store(value)
                print(f"Stored {value} in memory.")
            except ValueError:
                print("Error: provide a number or 'ans' (e.g., 'mstore 5' or 'mstore ans')")
            continue

        elif cmd.startswith("madd"):
            arg = user_input[4:].strip()
            try:
                value = last_answer if arg.lower() == "ans" else float(arg)
                calc.memory_add(value)
                print(f"Memory is now {calc.memory}")
            except ValueError:
                print("Error: provide a valid number.")
            continue

        elif cmd.startswith("msub"):
            arg = user_input[4:].strip()
            try:
                value = last_answer if arg.lower() == "ans" else float(arg)
                calc.memory_subtract(value)
                print(f"Memory is now {calc.memory}")
            except ValueError:
                print("Error: provide a valid number.")
            continue

        elif cmd.startswith("exact "):
            expr_part = user_input[6:].strip()
            expr_part = expr_part.replace("ans", str(last_answer))
            try:
                result = calc.evaluate_exact(expr_part)
                calc.history.append((f"exact {expr_part}", result))
                last_answer = float(result) if result.is_number else last_answer
                print(f"= {result}")
            except Exception as e:
                print(f"Error: {e}")
            continue

        # Otherwise, treat input as a math expression
        expr_to_eval = user_input.replace("ans", str(last_answer))
        try:
            result = calc.evaluate_expression(expr_to_eval)
            calc.history.append((user_input, result))
            last_answer = result
            print(f"= {result}")
        except Exception as e:
            print(f"Error: {e}")


if __name__ == "__main__":
    sys.exit(main())

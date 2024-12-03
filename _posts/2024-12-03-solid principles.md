---
title: "Mastering SOLID Principles: Build Clean and Scalable Code"
date: 2024-12-03
categories: [development]
tags: [development]
---

Hey there! 👋  

Today, I want to share something that transformed the way I approach coding: the **SOLID Principles**. These are five simple rules that can make your code easier to understand, maintain, and scale. If you're into object-oriented programming (OOP), you definitely need these principles in your toolbox.  

Let’s dive in, keeping things practical with correct and incorrect examples.  

---

### **S - Single Responsibility Principle (SRP)**  
**“A class should have only one reason to change.”**  

This means each class should do just one thing. Mixing responsibilities makes your code messy and hard to maintain.  

#### ❌ Incorrect Use:  
```python
class UserManager:
    def save_user(self, user):
        # Save user to database
        pass

    def send_email(self, email):
        # Send welcome email
        pass
```  
Here, the `UserManager` class is handling both user management and email sending. If you change the email logic, you're touching user management code too.  

#### ✅ Correct Use:  
```python
class UserManager:
    def save_user(self, user):
        # Save user to database
        pass

class EmailService:
    def send_email(self, email):
        # Send welcome email
        pass
```  
Now, each class has a single responsibility. Changes to email logic don’t mess with user management.  

---

### **O - Open/Closed Principle (OCP)**  
**“Open for extension, but closed for modification.”**  

You should be able to add new functionality without altering existing code.  

#### ❌ Incorrect Use:  
```python
class Discount:
    def calculate(self, price, customer_type):
        if customer_type == "regular":
            return price * 0.9
        elif customer_type == "vip":
            return price * 0.8
```  
Adding a new customer type means modifying the `calculate` method, which isn’t scalable.  

#### ✅ Correct Use:  
```python
class Discount:
    def calculate(self, price):
        return price

class RegularCustomerDiscount(Discount):
    def calculate(self, price):
        return price * 0.9

class VIPCustomerDiscount(Discount):
    def calculate(self, price):
        return price * 0.8
```  
You can now extend the discount system by adding new classes without touching existing ones.  

---

### **L - Liskov Substitution Principle (LSP)**  
**“Subtypes must be substitutable for their base types.”**  

This ensures that derived classes don’t break the functionality of the base class.  

#### ❌ Incorrect Use:  
```python
class Bird:
    def fly(self):
        print("I can fly!")

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("Penguins can't fly!")
```  
A `Penguin` isn’t behaving like a `Bird`, violating LSP.  

#### ✅ Correct Use:  
```python
class Bird:
    def move(self):
        print("I can move!")

class Penguin(Bird):
    def move(self):
        print("I waddle!")
```  
Here, `Penguin` and other birds follow the base class behavior but adapt it appropriately.  

---

### **I - Interface Segregation Principle (ISP)**  
**“Don’t force classes to implement what they don’t use.”**  

Large interfaces are a nightmare!  

#### ❌ Incorrect Use:  
```python
class Printer:
    def print_document(self):
        pass

    def scan_document(self):
        pass

    def fax_document(self):
        pass
```  
If you only need a printer, why deal with scan and fax methods?  

#### ✅ Correct Use:  
```python
class Printer:
    def print_document(self):
        pass

class Scanner:
    def scan_document(self):
        pass

class Fax:
    def fax_document(self):
        pass
```  
Now, you can implement just what you need without extra baggage.  

---

### **D - Dependency Inversion Principle (DIP)**  
**“Depend on abstractions, not concrete implementations.”**  

High-level modules should not depend on low-level modules.  

#### ❌ Incorrect Use:  
```python
class StripeProcessor:
    def process_payment(self, amount):
        print(f"Processing {amount} using Stripe")

class PaymentService:
    def pay(self, amount):
        processor = StripeProcessor()
        processor.process_payment(amount)
```  
`PaymentService` is tied to `StripeProcessor`. What if you want to switch to PayPal?  

#### ✅ Correct Use:  
```python
class PaymentProcessor:
    def process_payment(self, amount):
        pass

class StripeProcessor(PaymentProcessor):
    def process_payment(self, amount):
        print(f"Processing {amount} using Stripe")

class PaymentService:
    def __init__(self, processor: PaymentProcessor):
        self.processor = processor

    def pay(self, amount):
        self.processor.process_payment(amount)
```  
Now, you can use any payment processor without changing the `PaymentService`.  

---

### **Conclusion**  

The SOLID principles are not just fancy jargon; they make a real difference in code quality. Start applying them one step at a time in your projects. Trust me, your future self (and teammates!) will thank you.
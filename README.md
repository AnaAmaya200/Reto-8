
# Programación Orientada a Objetos - UNAL

# Ana María Amaya Gómez

## Reto 8: Modificación de restaurante con iteradores
implemente una nueva Clase que cree y sea iterable con todos los elementos en un orden, debería permitir bucles y contener todos los atributos de los elementos.

Para esta ocasión traemos de nuevo nuestro código correspondiente al restaurante, la ultima versión del reto 4 con descuentos e implementación de metodos de pago


```python
class MenuItem:
    def __init__(self, name: str, price: float, type: str):
        self._name = name
        self._price = price
        self._type = type

    def get_name(self):
        return self._name

    def set_name(self, name: str):
        self._name = name

    def get_price(self):
        return self._price

    def set_price(self, price: float):
        self._price = price

    def get_type(self):
        return self._type

    def set_type(self, type: str):
        self._type = type

    def total_price(self, quantity) -> float:
        return quantity * self._price

class Appetizer(MenuItem):
    def __init__(self, name: str, price: float, type: str, serving: str):
        super().__init__(name, price, type)
        self._serving = serving

    def get_serving(self):
        return self._serving

    def set_serving(self, serving: str):
        self._serving = serving

class Beverage(MenuItem):
    def __init__(self, name: str, price: float, type: str, size: str):
        super().__init__(name, price, type)
        self._size = size

    def get_size(self):
        return self._size

    def set_size(self, size: str):
        self._size = size

class Dessert(MenuItem):
    def __init__(self, name: str, price: float, type: str, flavor: str):
        super().__init__(name, price, type)
        self._flavor = flavor

    def get_flavor(self):
        return self._flavor

    def set_flavor(self, flavor: str):
        self._flavor = flavor

class MainCourse(MenuItem):
    def __init__(self, name: str, price: float, type: str, protein: str, vegetable: str, starch: str):
        super().__init__(name, price, type)
        self._protein = protein
        self._vegetable = vegetable
        self._starch = starch

    def get_protein(self):
        return self._protein

    def set_protein(self, protein: str):
        self._protein = protein

    def get_vegetable(self):
        return self._vegetable

    def set_vegetable(self, vegetable: str):
        self._vegetable = vegetable

    def get_starch(self):
        return self._starch

    def set_starch(self, starch: str):
        self._starch = starch

class Order:
    def __init__(self):
        self.items = []

    def add_item(self, item: MenuItem, quantity: int = 1):
        self.items.append((item, quantity))

    def calculate_total(self) -> float:
        total = sum(item.total_price(quantity) for item, quantity in self.items)
        return total

    def apply_discount(self, discount: float) -> float:
        total = self.calculate_total()
        return total * (1 - discount)

    def display_order(self):
        for item, quantity in self.items:
            print(f'{quantity} x {item.get_name()} - {item.total_price(quantity):.2f} (unidad: {item.get_price():.2f})')

        total = self.calculate_total()
        print(f'Total: {total:.2f}')

    def pay(self, medio_pago: "MedioPago"):
        total = self.calculate_total()
        medio_pago.pagar(total)


class MedioPago:
    def __init__(self):
        pass

    def pagar(self, monto):
        pass

class Tarjeta(MedioPago):
    def __init__(self, numero, cvv):
        super().__init__()
        self.numero = numero
        self.cvv = cvv

    def pagar(self, monto):
        print(f"Pagando {monto:.2f} con tarjeta {self.numero[-4:]}")

class Efectivo(MedioPago):
    def __init__(self, monto_entregado):
        super().__init__()
        self.monto_entregado = monto_entregado

    def pagar(self, monto):
        if self.monto_entregado >= monto:
            print(f"Pago realizado en efectivo. Cambio: {self.monto_entregado - monto:.2f}")
        else:
            print(f"Fondos insuficientes. Faltan {monto - self.monto_entregado:.2f} para completar el pago.")

bebida1 = Beverage("agua", 2.75, "sin gas", "250 ml")
entrada1 = Appetizer("Mini empanadas", 13.50, "Carne", "Grupal")
plato1 = MainCourse("Plato del día", 24.00, "corriente", "carne", "ensalada", "arroz")

order = Order()
order.add_item(bebida1, 2)
order.add_item(entrada1, 1)
order.add_item(plato1, 1)

print("Orden antes del descuento:")
order.display_order()

descuento = 0.10
total_con_descuento = order.apply_discount(descuento)
print(f"\nTotal con {descuento * 100}% de descuento: {total_con_descuento:.2f}")

print("\nRealizando el pago:")
tarjeta = Tarjeta("1234567890123456", "123")
efectivo = Efectivo(50.00)
order.pay(tarjeta)  
order.pay(efectivo)
```

Partiendo de esta base utilizamos los ejercicios aprendidos en clase para crear una modificación a la clase menu item siendo la raíz del modelo para poder integrar el uso de iteradores.
de estte modo la queremos replantear así 

```python
class MenuItems:
    def __init__(self):
        self.items = []

    def add_item(self, item: MenuItem):
        self.items.append(item)

    def __iter__(self):
        return iter(self.items)

# Ahora añadimos algunos ítems al menú
menu_items = MenuItems()
menu_items.add_item(bebida1)
menu_items.add_item(entrada1)
menu_items.add_item(plato1)


for item in menu_items:
    print(f'Nombre: {item.get_name()}, Precio: {item.get_price():.2f}, Tipo: {item.get_type()}')

for item in menu_items:
    if isinstance(item, Beverage):
        print(f'Tamaño de la bebida: {item.get_size()}')
    elif isinstance(item, Appetizer):
        print(f'Tipo de porción: {item.get_serving()}')
    elif isinstance(item, Dessert):
        print(f'Sabor del postre: {item.get_flavor()}')
    elif isinstance(item, MainCourse):
        print(f'Proteína: {item.get_protein()}, Vegetales: {item.get_vegetable()}, Carbohidrato: {item.get_starch()}')

```

ya una vez prevista la modelación de la nueva clase implementamos en el código base y ejecutamos para prueba

```python
class MenuItem:
    def __init__(self, name: str, price: float, type: str):
        self._name = name
        self._price = price
        self._type = type

    def get_name(self):
        return self._name

    def set_name(self, name: str):
        self._name = name

    def get_price(self):
        return self._price

    def set_price(self, price: float):
        self._price = price

    def get_type(self):
        return self._type

    def set_type(self, type: str):
        self._type = type

    def total_price(self, quantity) -> float:
        return quantity * self._price

class Appetizer(MenuItem):
    def __init__(self, name: str, price: float, type: str, serving: str):
        super().__init__(name, price, type)
        self._serving = serving

    def get_serving(self):
        return self._serving

    def set_serving(self, serving: str):
        self._serving = serving

class Beverage(MenuItem):
    def __init__(self, name: str, price: float, type: str, size: str):
        super().__init__(name, price, type)
        self._size = size

    def get_size(self):
        return self._size

    def set_size(self, size: str):
        self._size = size

class Dessert(MenuItem):
    def __init__(self, name: str, price: float, type: str, flavor: str):
        super().__init__(name, price, type)
        self._flavor = flavor

    def get_flavor(self):
        return self._flavor

    def set_flavor(self, flavor: str):
        self._flavor = flavor

class MainCourse(MenuItem):
    def __init__(self, name: str, price: float, type: str, protein: str, vegetable: str, starch: str):
        super().__init__(name, price, type)
        self._protein = protein
        self._vegetable = vegetable
        self._starch = starch

    def get_protein(self):
        return self._protein

    def set_protein(self, protein: str):
        self._protein = protein

    def get_vegetable(self):
        return self._vegetable

    def set_vegetable(self, vegetable: str):
        self._vegetable = vegetable

    def get_starch(self):
        return self._starch

    def set_starch(self, starch: str):
        self._starch = starch

class Order:
    def __init__(self):
        self.items = []

    def add_item(self, item: MenuItem, quantity: int = 1):
        self.items.append((item, quantity))

    def calculate_total(self) -> float:
        total = sum(item.total_price(quantity) for item, quantity in self.items)
        return total

    def apply_discount(self, discount: float) -> float:
        total = self.calculate_total()
        return total * (1 - discount)

    def display_order(self):
        for item, quantity in self.items:
            print(f'{quantity} x {item.get_name()} - {item.total_price(quantity):.2f} (unidad: {item.get_price():.2f})')

        total = self.calculate_total()
        print(f'Total: {total:.2f}')

    def pay(self, medio_pago: "MedioPago"):
        total = self.calculate_total()
        medio_pago.pagar(total)


class MedioPago:
    def __init__(self):
        pass

    def pagar(self, monto):
        pass

class Tarjeta(MedioPago):
    def __init__(self, numero, cvv):
        super().__init__()
        self.numero = numero
        self.cvv = cvv

    def pagar(self, monto):
        print(f"Pagando {monto:.2f} con tarjeta {self.numero[-4:]}")

class Efectivo(MedioPago):
    def __init__(self, monto_entregado):
        super().__init__()
        self.monto_entregado = monto_entregado

    def pagar(self, monto):
        if self.monto_entregado >= monto:
            print(f"Pago realizado en efectivo. Cambio: {self.monto_entregado - monto:.2f}")
        else:
            print(f"Fondos insuficientes. Faltan {monto - self.monto_entregado:.2f} para completar el pago.")

class MenuItems:
    def __init__(self):
        self.items = []

    def add_item(self, item: MenuItem):
        self.items.append(item)

    def __iter__(self):
        return iter(self.items)


bebida1 = Beverage("agua", 2.75, "sin gas", "250 ml")
entrada1 = Appetizer("Mini empanadas", 13.50, "Carne", "Grupal")
plato1 = MainCourse("Plato del día", 24.00, "corriente", "carne", "ensalada", "arroz")


menu_items = MenuItems()
menu_items.add_item(bebida1)
menu_items.add_item(entrada1)
menu_items.add_item(plato1)

for item in menu_items:
    print(f'Nombre: {item.get_name()}, Precio: {item.get_price():.2f}, Tipo: {item.get_type()}')


for item in menu_items:
    if isinstance(item, Beverage):
        print(f'Tamaño de la bebida: {item.get_size()}')
    elif isinstance(item, Appetizer):
        print(f'Tipo de porción: {item.get_serving()}')
    elif isinstance(item, Dessert):
        print(f'Sabor del postre: {item.get_flavor()}')
    elif isinstance(item, MainCourse):
        print(f'Proteína: {item.get_protein()}, Vegetales: {item.get_vegetable()}, Carbohidrato: {item.get_starch()}')


order = Order()
order.add_item(bebida1, 2)
order.add_item(entrada1, 1)
order.add_item(plato1, 1)

print("\nOrden antes del descuento:")
order.display_order()


descuento = 0.10
total_con_descuento = order.apply_discount(descuento)
print(f"\nTotal con {descuento * 100}% de descuento: {total_con_descuento:.2f}")


print("\nRealizando el pago:")
tarjeta = Tarjeta("1234567890123456", "123")
efectivo = Efectivo(50.00)
order.pay(tarjeta)  
order.pay(efectivo)

```
resultados
```python
Nombre: agua, Precio: 2.75, Tipo: sin gas
Nombre: Mini empanadas, Precio: 13.50, Tipo: Carne
Nombre: Plato del día, Precio: 24.00, Tipo: corriente
Tamaño de la bebida: 250 ml
Tipo de porción: Grupal
Proteína: carne, Vegetales: ensalada, Carbohidrato: arroz

Orden antes del descuento:
2 x agua - 5.50 (unidad: 2.75)
1 x Mini empanadas - 13.50 (unidad: 13.50)
1 x Plato del día - 24.00 (unidad: 24.00)
Total: 43.00

Total con 10.0% de descuento: 38.70

Realizando el pago:
Pagando 43.00 con tarjeta 3456
Pago realizado en efectivo. Cambio: 7.00
```
Como se puede apreciar el proceso de iteración fue exitoso

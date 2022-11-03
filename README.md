# ------------------Patrones de diseño--------------- #

En ingeniería de software, los patrones de diseño son soluciones generales y repetibles para problemas comunes de diseño de software. Un patrón de diseño no es un diseño terminado que pueda traducirse directamente a código. Esta es una explicación o plantilla para resolver un problema que se puede usar en varias situaciones.

# ------------------Singleton--------------- #
# ¿Qué es?
El patrón singleton es un patrón de diseño de creación destinado a garantizar que una clase tenga solo una instancia y proporcione un único punto de acceso a esa instancia. Este patrón se usa normalmente (entre otras cosas) para registrar problemas cuando desea centralizar todo con una sola instancia del registrador.
# Para que sirve
•	Asegúrese de que solo haya una instancia de la clase y proporcione un punto de acceso global a esa instancia.
•	encapsuló "inicialización justo a tiempo" o "inicialización en el primer uso".
# Dificultades
•	Una aplicación debe tener solo una instancia del objeto. También requiere inicialización diferida y acceso global.

# Ejemplo:
def singleton(cls):
    
    instances = dict()

    def wrap(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)

        return instances[cls]
       
    return wrap
@singleton
class User(object):
    def __init__(self,username):
        self.username = username

@singleton
class Admin():
    pass
   
    if __name__ =='__main__':
        
        user1 = User('cody')
        user2 = User('facilito')

    print(user1 is user2)


# ------------------Factory--------------- #
# ¿Que es?
Defina el método utilizado para crear el objeto y no la llamada directa al constructor. Las subclases pueden anular este método para cambiar la clase de los objetos que crean.
# Para que sirve
•	Define una interfaz para crear objetos, pero permite que las subclases decidan qué clase instanciar. Los métodos de fábrica permiten que las clases descarguen la instanciación a las subclases.
•	Definición de un constructor "virtual".
•	El nuevo operador se considera malicioso.
# Dificultades
El marco debe estandarizar el modelo arquitectónico para diferentes aplicaciones, pero permitir que las aplicaciones individuales definan sus propios objetos de dominio y manejen la creación de instancias.
# Ejemplo:
from __future__ import annotations
from abc import ABC, abstractmethod
class Creator(ABC):

    @abstractmethod
    def factory_method(self):
        pass

    def some_operation(self) -> str:

        product = self.factory_method()

        result = f"Creator: The same creator's code has just worked with {product.operation()}"

        return result

class ConcreteCreator1(Creator):

    def factory_method(self) -> Product:
        return ConcreteProduct1()

class ConcreteCreator2(Creator):
    def factory_method(self) -> Product:
        return ConcreteProduct2()

class Product(ABC):

    @abstractmethod
    def operation(self) -> str:
        pass

class ConcreteProduct1(Product):
    def operation(self) -> str:
        return "{Result of the ConcreteProduct1}"

class ConcreteProduct2(Product):
    def operation(self) -> str:
        return "{Result of the ConcreteProduct2}"

def client_code(creator: Creator) -> None:

    print(f"Client: I'm not aware of the creator's class, but it still works.\n"
          f"{creator.some_operation()}", end="")

if __name__ == "__main__":
    print("App: Launched with the ConcreteCreator1.")
    client_code(ConcreteCreator1())
    print("\n")

    print("App: Launched with the ConcreteCreator2.")
    client_code(ConcreteCreator2())

# --------------Abstract factory------------- #
# ¿Que es?
Los métodos de fábrica abstractos son un patrón de diseño creativo que le permite crear una familia de objetos relacionados sin especificar una clase concreta. El uso de métodos de fábrica abstractos proporciona la forma más fácil de producir muchos objetos de tipo similar. 
Proporciona una forma de encapsular grupos de fábricas individuales.
# Para que sirve
•	Proporciona una interfaz para crear una familia de objetos relacionados o dependientes sin especificar una clase concreta.
•	Jerarquía que incluye: construir muchas 'plataformas' y conjuntos de 'productos' posibles.
•	El nuevo operador se considera malicioso.
# Dificultades
Las dependencias de la plataforma se deben encapsular si se desea que la aplicación sea portátil. Estas "plataformas" pueden incluir sistemas de ventanas, sistemas operativos, bases de datos y más. Muy a menudo, esta encapsulación no está prediseñada y muchas declaraciones de casos #ifdef con opciones para todas las plataformas admitidas actualmente comienzan a proliferar como conejos en su código.
# Ejemplo
from __future__ import annotations
from abc import ABC, abstractmethod


class AbstractFactory(ABC):
    @abstractmethod
    def create_product_a(self) -> AbstractProductA:
        pass

    @abstractmethod
    def create_product_b(self) -> AbstractProductB:
        pass


class ConcreteFactory1(AbstractFactory):

    def create_product_a(self) -> AbstractProductA:
        return ConcreteProductA1()

    def create_product_b(self) -> AbstractProductB:
        return ConcreteProductB1()


class ConcreteFactory2(AbstractFactory):

    def create_product_a(self) -> AbstractProductA:
        return ConcreteProductA2()

    def create_product_b(self) -> AbstractProductB:
        return ConcreteProductB2()


class AbstractProductA(ABC):

    @abstractmethod
    def useful_function_a(self) -> str:
        pass

class ConcreteProductA1(AbstractProductA):
    def useful_function_a(self) -> str:
        return "The result of the product A1."


class ConcreteProductA2(AbstractProductA):
    def useful_function_a(self) -> str:
        return "The result of the product A2."


class AbstractProductB(ABC):

    @abstractmethod
    def useful_function_b(self) -> None:
        pass

    @abstractmethod
    def another_useful_function_b(self, collaborator: AbstractProductA) -> None:
        pass

class ConcreteProductB1(AbstractProductB):
    def useful_function_b(self) -> str:
        return "The result of the product B1."

    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        result = collaborator.useful_function_a()
        return f"The result of the B1 collaborating with the ({result})"


class ConcreteProductB2(AbstractProductB):
    def useful_function_b(self) -> str:
        return "The result of the product B2."

    def another_useful_function_b(self, collaborator: AbstractProductA):
    
        result = collaborator.useful_function_a()
        return f"The result of the B2 collaborating with the ({result})"


def client_code(factory: AbstractFactory) -> None:
    
    product_a = factory.create_product_a()
    product_b = factory.create_product_b()

    print(f"{product_b.useful_function_b()}")
    print(f"{product_b.another_useful_function_b(product_a)}", end="")

if __name__ == "__main__":
   
    print("Client: Testing client code with the first factory type:")
    client_code(ConcreteFactory1())

    print("\n")

    print("Client: Testing the same client code with the second factory type:")
    client_code(ConcreteFactory2())


# --------------Prototype------------- #
# ¿Que es?}
Un patrón de diseño de compilación que tiene como objetivo reducir la cantidad de clases utilizadas en una aplicación. Puede copiar objetos existentes independientemente de la implementación concreta de la clase. En general, los objetos aquí se crean copiando la instancia del prototipo en tiempo de ejecución.
# Para que sirve
•	Utilice una instancia de prototipo para especificar el tipo de objeto que desea crear y, a continuación, copie este prototipo para crear un nuevo objeto. 
•	Adopte una instancia de la clase para utilizarla como criador para todas las instancias futuras.
•	El nuevo operador se considera malicioso.
# Dificultades
La aplicación "conecta" la clase de objeto para crear en cada expresión "nueva".
# Ejemplo
import copy


class SelfReferencingEntity:
    def __init__(self):
        self.parent = None

    def set_parent(self, parent):
        self.parent = parent


class SomeComponent:

    def __init__(self, some_int, some_list_of_objects, some_circular_ref):
        self.some_int = some_int
        self.some_list_of_objects = some_list_of_objects
        self.some_circular_ref = some_circular_ref

    def __copy__(self):

        some_list_of_objects = copy.copy(self.some_list_of_objects)
        some_circular_ref = copy.copy(self.some_circular_ref)

        new = self.__class__(
            self.some_int, some_list_of_objects, some_circular_ref
        )
        new.__dict__.update(self.__dict__)

        return new

    def __deepcopy__(self, memo=None):
        if memo is None:
            memo = {}

        some_list_of_objects = copy.deepcopy(self.some_list_of_objects, memo)
        some_circular_ref = copy.deepcopy(self.some_circular_ref, memo)

        new = self.__class__(
            self.some_int, some_list_of_objects, some_circular_ref
        )
        new.__dict__ = copy.deepcopy(self.__dict__, memo)

        return new


if __name__ == "__main__":

    list_of_objects = [1, {1, 2, 3}, [1, 2, 3]]
    circular_ref = SelfReferencingEntity()
    component = SomeComponent(23, list_of_objects, circular_ref)
    circular_ref.set_parent(component)

    shallow_copied_component = copy.copy(component)

    shallow_copied_component.some_list_of_objects.append("another object")
    if component.some_list_of_objects[-1] == "another object":
        print(
            "Adding elements to `shallow_copied_component`'s "
            "some_list_of_objects adds it to `component`'s "
            "some_list_of_objects."
        )
    else:
        print(
            "Adding elements to `shallow_copied_component`'s "
            "some_list_of_objects doesn't add it to `component`'s "
            "some_list_of_objects."
        )
    component.some_list_of_objects[1].add(4)
    if 4 in shallow_copied_component.some_list_of_objects[1]:
        print(
            "Changing objects in the `component`'s some_list_of_objects "
            "changes that object in `shallow_copied_component`'s "
            "some_list_of_objects."
        )
    else:
        print(
            "Changing objects in the `component`'s some_list_of_objects "
            "doesn't change that object in `shallow_copied_component`'s "
            "some_list_of_objects."
        )

    deep_copied_component = copy.deepcopy(component)

    deep_copied_component.some_list_of_objects.append("one more object")
    if component.some_list_of_objects[-1] == "one more object":
        print(
            "Adding elements to `deep_copied_component`'s "
            "some_list_of_objects adds it to `component`'s "
            "some_list_of_objects."
        )
    else:
        print(
            "Adding elements to `deep_copied_component`'s "
            "some_list_of_objects doesn't add it to `component`'s "
            "some_list_of_objects."
        )
    component.some_list_of_objects[1].add(10)
    if 10 in deep_copied_component.some_list_of_objects[1]:
        print(
            "Changing objects in the `component`'s some_list_of_objects "
            "changes that object in `deep_copied_component`'s "
            "some_list_of_objects."
        )
    else:
        print(
            "Changing objects in the `component`'s some_list_of_objects "
            "doesn't change that object in `deep_copied_component`'s "
            "some_list_of_objects."
        )

    print(
        f"id(deep_copied_component.some_circular_ref.parent): "
        f"{id(deep_copied_component.some_circular_ref.parent)}"
    )
    print(
        f"id(deep_copied_component.some_circular_ref.parent.some_circular_ref.parent): "
        f"{id(deep_copied_component.some_circular_ref.parent.some_circular_ref.parent)}"
    )
    print(
        "^^ This shows that deepcopied objects contain same reference, they "
        "are not cloned repeatedly."
    )


# --------------Facade------------- #
# ¿Que es?
El método de fachada es un patrón de diseño estructural que proporciona una interfaz unificada más sencilla a un sistema más complejo. La palabra fachada significa la cara de un edificio o, en particular, una interfaz externa de un sistema complejo formado por varios subsistemas. Es una parte esencial de las normas de diseño de la Banda de los Cuatro. Proporciona una forma más fácil de acceder a los métodos de los sistemas subyacentes al ofrecer un único punto de entrada.
# Para que sirve
•	Proporciona una interfaz uniforme para un conjunto de interfaces dentro de un subsistema. Facade define una interfaz de usuario de nivel superior que simplifica el uso de subsistemas.
•	Envuelve un subsistema complejo con una interfaz más simple.
# Dificultades
Una parte de la comunidad de clientes quiere interfaces simplificadas para funciones comunes de subsistemas complejos.
# Ejemplo
from __future__ import annotations
class Facade:

    def __init__(self, subsystem1: Subsystem1, subsystem2: Subsystem2) -> None:

        self._subsystem1 = subsystem1 or Subsystem1()
        self._subsystem2 = subsystem2 or Subsystem2()

    def operation(self) -> str:

        results = []
        results.append("Facade initializes subsystems:")
        results.append(self._subsystem1.operation1())
        results.append(self._subsystem2.operation1())
        results.append("Facade orders subsystems to perform the action:")
        results.append(self._subsystem1.operation_n())
        results.append(self._subsystem2.operation_z())
        return "\n".join(results)


class Subsystem1:

    def operation1(self) -> str:
        return "Subsystem1: Ready!"


    def operation_n(self) -> str:
        return "Subsystem1: Go!"


class Subsystem2:

    def operation1(self) -> str:
        return "Subsystem2: Get ready!"


    def operation_z(self) -> str:
        return "Subsystem2: Fire!"


def client_code(facade: Facade) -> None:

    print(facade.operation(), end="")


if __name__ == "__main__":
    already created.
    Facade with these
    subsystem1 = Subsystem1()
    subsystem2 = Subsystem2()
    facade = Facade(subsystem1, subsystem2)
    client_code(facade)


# -------------.Decorator------------- #
# ¿Que es?
Un decorador en Python es un objeto que extiende/modifica la funcionalidad de una función (o método) en tiempo de ejecución y conceptualmente está más cerca de la anotación de Java que del decorador de orientación a objetos.
En la práctica, el decorador actúa como un envoltorio de regalo, empaquetando la función sin cambiar su contenido  pero haciéndola más bonita.
# Para que sirve
Asigne dinámicamente responsabilidades adicionales a los objetos. Los decoradores proporcionan una alternativa flexible a la subclasificación para ampliar la funcionalidad.
Embellecimiento envolvente recursivo especificado por el cliente del padre.
Envuelve el regalo, ponlo en la caja y envuelve la caja.
# Dificultades
Quiero agregar comportamiento o estado a objetos individuales en tiempo de ejecución. Dado que es estático y se aplica a toda la clase, no es posible la herencia.
# Ejemplo 
def funcion_externa():
    print("Código de la funcion_externa()")

    def funcion_interna():
        print("Código de la funcion_interna()")

    funcion_interna()
# --------------Proxy------------- #
# ¿Que es?
El proxy es un patrón de diseño estructural que proporciona un objeto que actúa como sustituto de un objeto de servicio real utilizado por un cliente. Un proxy recibe las peticiones del cliente, realiza algún trabajo (control de acceso, almacenamiento en caché, etc.) y luego pasa la petición a un objeto de servicio.
# Para que sirve
•	Proporciona un sustituto o marcador de posición para otro objeto para controlar el acceso a ese objeto.
•	Utilice una capa adicional de direccionamiento indirecto para admitir el acceso distribuido, controlado o inteligente.
•	Agregue envolturas y delegaciones para proteger el componente real de complicaciones excesivas.
# Dificultades
Necesitas soportar objetos que requieran muchos recursos, y no quieres instanciar dichos objetos a menos que sean solicitados por el cliente.
# Ejemplo

from abc import ABC, abstractmethod

class Subject(ABC):

    @abstractmethod
    def request(self) -> None:
        pass


class RealSubject(Subject):

    def request(self) -> None:
        print("RealSubject: Handling request.")


class Proxy(Subject):

    def __init__(self, real_subject: RealSubject) -> None:
        self._real_subject = real_subject

    def request(self) -> None:

        if self.check_access():
            self._real_subject.request()
            self.log_access()

    def check_access(self) -> bool:
        print("Proxy: Checking access prior to firing a real request.")
        return True

    def log_access(self) -> None:
        print("Proxy: Logging the time of request.", end="")


def client_code(subject: Subject) -> None:

    subject.request()

if __name__ == "__main__":
    print("Client: Executing the client code with a real subject:")
    real_subject = RealSubject()
    client_code(real_subject)

    print("")

    print("Client: Executing the same client code with a proxy:")
    proxy = Proxy(real_subject)
    client_code(proxy)


# --------------Command------------- #
# ¿Que es?
Un comando es un patrón de diseño de comportamiento que convierte una solicitud u operación simple en un objeto.
# Para que sirve
•	Encapsule las solicitudes como objetos para parametrizar a los clientes con diferentes solicitudes, solicitudes de cola o de registro y compatibilidad con reversiones.
•	Promueve ``Método de invocación de objeto'' al estado de objeto completo.
•	Devoluciones de llamadas orientadas a objetos.
# Dificultades
Necesidad de emitir peticiones a objetos sin saber nada de la operación que se solicita ni del receptor de la petición.
# Ejemplo
from __future__ import annotations
from abc import ABC, abstractmethod

class Command(ABC):

    @abstractmethod
    def execute(self) -> None:
        pass


class SimpleCommand(Command):

    def __init__(self, payload: str) -> None:
        self._payload = payload

    def execute(self) -> None:
        print(f"SimpleCommand: See, I can do simple things like printing"
              f"({self._payload})")


class ComplexCommand(Command):

    def __init__(self, receiver: Receiver, a: str, b: str) -> None:

        self._receiver = receiver
        self._a = a
        self._b = b

    def execute(self) -> None:

        print("ComplexCommand: Complex stuff should be done by a receiver object", end="")
        self._receiver.do_something(self._a)
        self._receiver.do_something_else(self._b)


class Receiver:
    def do_something(self, a: str) -> None:
        print(f"\nReceiver: Working on ({a}.)", end="")

    def do_something_else(self, b: str) -> None:
        print(f"\nReceiver: Also working on ({b}.)", end="")


class Invoker:
    _on_start = None
    _on_finish = None

    def set_on_start(self, command: Command):
        self._on_start = command

    def set_on_finish(self, command: Command):
        self._on_finish = command

    def do_something_important(self) -> None:

        print("Invoker: Does anybody want something done before I begin?")
        if isinstance(self._on_start, Command):
            self._on_start.execute()

        print("Invoker: ...doing something really important...")

        print("Invoker: Does anybody want something done after I finish?")
        if isinstance(self._on_finish, Command):
            self._on_finish.execute()


if __name__ == "__main__":

    invoker = Invoker()
    invoker.set_on_start(SimpleCommand("Say Hi!"))
    receiver = Receiver()
    invoker.set_on_finish(ComplexCommand(
        receiver, "Send email", "Save report"))

    invoker.do_something_important()

# --------------Memento------------- #
# ¿Que es?
Memento es un patrón de diseño de comportamiento que le permite tomar una instantánea del estado de un objeto y restaurarlo en el futuro.
# Para que sirve
•	Sin violar la encapsulación, captura y externaliza el estado interno de un objeto para que luego pueda ser restaurado a ese estado.
•	Una galleta mágica con capacidad de punto de control.
•	Facilita deshacer o restaurar un objeto a su estado completo.
# Dificultades
Necesidad de restaurar un objeto a su estado anterior (por ejemplo, operaciones de "deshacer" o "retroceder").
# Ejemplo
from __future__ import annotations
from abc import ABC, abstractmethod
from datetime import datetime
from random import sample
from string import ascii_letters, digits

class Originator():

    _state = None

    def __init__(self, state: str) -> None:
        self._state = state
        print(f"Originator: My initial state is: {self._state}")

    def do_something(self) -> None:

        print("Originator: I'm doing something important.")
        self._state = self._generate_random_string(30)
        print(f"Originator: and my state has changed to: {self._state}")

    def _generate_random_string(self, length: int = 10) -> None:
        return "".join(sample(ascii_letters, length))

    def save(self) -> Memento:

        return ConcreteMemento(self._state)

    def restore(self, memento: Memento) -> None:

        self._state = memento.get_state()
        print(f"Originator: My state has changed to: {self._state}")


class Memento(ABC):
    @abstractmethod
    def get_name(self) -> str:
        pass

    @abstractmethod
    def get_date(self) -> str:
        pass


class ConcreteMemento(Memento):
    def __init__(self, state: str) -> None:
        self._state = state
        self._date = str(datetime.now())[:19]

    def get_state(self) -> str:

        return self._state

    def get_name(self) -> str:

        return f"{self._date} / ({self._state[0:9]}...)"

    def get_date(self) -> str:
        return self._date


class Caretaker():

    def __init__(self, originator: Originator) -> None:
        self._mementos = []
        self._originator = originator

    def backup(self) -> None:
        print("\nCaretaker: Saving Originator's state...")
        self._mementos.append(self._originator.save())

    def undo(self) -> None:
        if not len(self._mementos):
            return

        memento = self._mementos.pop()
        print(f"Caretaker: Restoring state to: {memento.get_name()}")
        try:
            self._originator.restore(memento)
        except Exception:
            self.undo()

    def show_history(self) -> None:
        print("Caretaker: Here's the list of mementos:")
        for memento in self._mementos:
            print(memento.get_name())


if __name__ == "__main__":
    originator = Originator("Super-duper-super-puper-super.")
    caretaker = Caretaker(originator)

    caretaker.backup()
    originator.do_something()

    caretaker.backup()
    originator.do_something()

    caretaker.backup()
    originator.do_something()

    print()
    caretaker.show_history()

    print("\nClient: Now, let's rollback!\n")
    caretaker.undo()

    print("\nClient: Once more!\n")
    caretaker.undo()

# --------------Observer------------- #
# ¿Que es?
Los observadores son un patrón de diseño de comportamiento que permite que algunos objetos notifiquen a otros objetos los cambios de estado.
# Para que sirve
•	Defina dependencias de uno a muchos entre objetos para que todos los objetos dependientes sean notificados y actualizados automáticamente cuando cambie el estado de un objeto.
•	La abstracción del sujeto encapsula el componente central y la jerarquía del observador encapsula.
# Dificultades
Un diseño monolítico de gran tamaño no se adapta bien a medida que se imponen nuevos requisitos de gráficos o de supervisión.
# Ejemplo
from __future__ import annotations
from abc import ABC, abstractmethod
from random import randrange
from typing import List


class Subject(ABC):

    @abstractmethod
    def attach(self, observer: Observer) -> None:
        pass

    @abstractmethod
    def detach(self, observer: Observer) -> None:
        pass

    @abstractmethod
    def notify(self) -> None:
        pass


class ConcreteSubject(Subject):

    _state: int = None

    _observers: List[Observer] = []

    def attach(self, observer: Observer) -> None:
        print("Subject: Attached an observer.")
        self._observers.append(observer)

    def detach(self, observer: Observer) -> None:
        self._observers.remove(observer)

    def notify(self) -> None:

        print("Subject: Notifying observers...")
        for observer in self._observers:
            observer.update(self)

    def some_business_logic(self) -> None:

        print("\nSubject: I'm doing something important.")
        self._state = randrange(0, 10)

        print(f"Subject: My state has just changed to: {self._state}")
        self.notify()


class Observer(ABC):

    @abstractmethod
    def update(self, subject: Subject) -> None:
        pass

class ConcreteObserverA(Observer):
    def update(self, subject: Subject) -> None:
        if subject._state < 3:
            print("ConcreteObserverA: Reacted to the event")


class ConcreteObserverB(Observer):
    def update(self, subject: Subject) -> None:
        if subject._state == 0 or subject._state >= 2:
            print("ConcreteObserverB: Reacted to the event")


if __name__ == "__main__":

    subject = ConcreteSubject()

    observer_a = ConcreteObserverA()
    subject.attach(observer_a)

    observer_b = ConcreteObserverB()
    subject.attach(observer_b)

    subject.some_business_logic()
    subject.some_business_logic()

    subject.detach(observer_a)

    subject.some_business_logic()

# --------------Strategy------------- #
# ¿Que es?
Una estrategia es un patrón de diseño de comportamiento que convierte un conjunto de comportamientos en un objeto y los hace intercambiables dentro del objeto de contexto original.
# Para que sirve
•	Definir una familia de algoritmos, encapsulando cada uno y haciéndolos intercambiables. Esta estrategia permite que el algoritmo cambie independientemente de los clientes que lo utilicen.
•	Capture la abstracción en las interfaces y oculte los detalles de implementación en las clases derivadas.
# Dificultades
Una de las estrategias dominantes en el diseño orientado a objetos es el "principio de abrir-cerrar".
El diagrama muestra cómo se logra esto de forma rutinaria: encapsulando los detalles de la interfaz en la clase base e incorporando los detalles de implementación en la clase derivada.
# Ejemplo
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List


class Context():

    def __init__(self, strategy: Strategy) -> None:

        self._strategy = strategy

    @property
    def strategy(self) -> Strategy:

        return self._strategy

    @strategy.setter
    def strategy(self, strategy: Strategy) -> None:

        self._strategy = strategy

    def do_some_business_logic(self) -> None:

        print("Context: Sorting data using the strategy (not sure how it'll do it)")
        result = self._strategy.do_algorithm(["a", "b", "c", "d", "e"])
        print(",".join(result))



class Strategy(ABC):


    @abstractmethod
    def do_algorithm(self, data: List):
        pass


class ConcreteStrategyA(Strategy):
    def do_algorithm(self, data: List) -> List:
        return sorted(data)


class ConcreteStrategyB(Strategy):
    def do_algorithm(self, data: List) -> List:
        return reversed(sorted(data))


if __name__ == "__main__":

    context = Context(ConcreteStrategyA())
    print("Client: Strategy is set to normal sorting.")
    context.do_some_business_logic()
    print()

    print("Client: Strategy is set to reverse sorting.")
    context.strategy = ConcreteStrategyB()
    context.do_some_business_logic()

# --------------Data Access Object------------- #
# ¿Que es?
Casi todas las aplicaciones actuales necesitan acceso a al menos una fuente de datos. Estas fuentes suelen ser bases de datos relacionales, por lo que acceder a los datos no es un problema. La mayor parte del código debe ser refactorizado.
# Para que sirve
Sugiero separar completamente la lógica comercial de la lógica para acceder a los datos. De esta forma, las DAO proporcionan los métodos necesarios para insertar, actualizar, eliminar y recuperar información. La capa empresarial, por otro lado, solo considera la lógica empresarial y utiliza DAO para interactuar con las fuentes de datos.
# Dificultades
Al acceder a los datos, es importante que la implementación y el formato de la información puedan variar de una fuente de datos a otra. Implementación de la lógica de acceso a datos en la lógica empresarial Estos pueden diferir. Es necesario implementar una lógica diferente para acceder a diferentes fuentes de datos.
# Ejemplo
def get_packs_for_anime_group():
    dao = DAO(db.session)
    aid = request.args.get("aid")
    group_name = request.args.get("group_name")
    file_packs = dao.get_packs_for_group_anime(aid, group_name)
    return jsonify(json_list=[e.serialize() for e in file_packs])

# --------------Dependency injection------------- #
# ¿Que es?
La inyección de dependencia es una técnica de desarrollo que le permite escribir código con un alto nivel de cohesión y un bajo nivel de dependencias.

La cohesión no es más que una medida que nos permite ver cómo se relacionan los bloques de código entre sí.
# Para que sirve
Esto garantiza que los clientes no creen ni administren servicios dependientes. Esto proporciona separación de preocupaciones al separar las preocupaciones de los clientes sobre el uso de servicios dependientes de las preocupaciones sobre la creación y gestión de esos servicios.
# Ejemplo:

class Client:
    def __init__(self):
        self.foo = None
        self.bar = None

    def set_foo(self, foo: Foo):
        if foo is None:
            raise ValueError("foo must be provided")
        self.foo = foo

    def set_bar(self, bar: Bar):
        if bar is None:
            raise ValueError("foo must be provided")
        self.bar = bar

    def validate_dependencies(self):
        if self.foo is None:
            raise ValueError("foo has not been set")
        if self.bar is None:
            raise ValueError("bar has not been set")

    def do_something(self):
        self.validate_dependencies()

        self.foo.do_something()
        self.bar.do_something_else()

# --------------Model View Controller------------- #
# ¿Que es?
No es solo un modelo de software para implementar interfaces de usuario, sino también una arquitectura que es fácil de modificar y mantener. El patrón MVC divide la aplicación en tres partes básicas
Modelos, Vistas y Controladores. Estas tres partes están interrelacionadas y ayudan a separar el procesamiento de la información de la presentación de la información.
# Para que sirve
El motivo es que puede separar los componentes de su aplicación en función de sus responsabilidades. En otras palabras, cambiar una parte del código no afecta a otra parte.

# Ejemplo

class Model(object):
    services = {
        'email': {'number': 1000, 'price': 2},
        'sms': {'number': 1000, 'price': 10},
        'voice': {'number': 1000, 'price': 15},
    }
 
 
class View(object):
    def list_services(self, services):
        for svc in services:
            print(svc, " ")
 
    def list_pricing(self, services):
        for svc in services:
            print("For", Model.services[svc]['number'],
                  svc, "message you pay $",
                  Model.services[svc]['price'])
 
 
class Controller(object):
    def __init__(self):
        self.model = Model()
        self.view = View()
 
    def get_services(self):
        services = self.model.services.keys()
        return self.view.list_services(services)
 
    def get_pricing(self):
        services = self.model.services.keys()
        return self.view.list_pricing(services)
 
 
class Client(object):
    con = Controller()
    print("Services Provided:")
    con.get_services()
    print("Pricing for Services:")
    con.get_pricing(# taller-dise-o

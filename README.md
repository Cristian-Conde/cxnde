# cxnde

# Clase base Personaje
class Personaje:
    def __init__(self, nombre, vida, ataque, defensa):
        self.nombre = nombre  # Nombre del personaje
        self.__vida = vida
        self.__ataque = ataque
        self.__defensa = defensa
    
    @property
    def vida(self):
        return self.__vida

    @vida.setter
    def vida(self, valor):
        if 0 <= valor <= 100:
            self.__vida = valor
        else:
            raise ValueError("La vida debe estar entre 0 y 100.")
    
    @property
    def ataque(self):
        return self.__ataque
    
    @property
    def defensa(self):
        return self.__defensa

    def atacar(self, objetivo):
        pass

    def recibir_dano(self, dano):
        self.__vida -= dano
        if self.__vida < 0:
            self.__vida = 0  # Aseguramos que la vida no sea negativa

class Guerrero(Personaje):
    def __init__(self, nombre, vida, ataque, defensa):
        super().__init__(nombre, vida, ataque, defensa)

    def atacar(self, objetivo):
        dano = self.ataque * 1.2
        dano_real = max(dano - objetivo.defensa, 0)
        objetivo.recibir_dano(dano_real)
        print(f"{self.nombre} (Guerrero) ataca a {objetivo.nombre} con {dano_real} de daño. Vida restante de {objetivo.nombre}: {objetivo.vida}")


class Mago(Personaje):
    def __init__(self, nombre, vida, ataque, defensa):
        super().__init__(nombre, vida, ataque, defensa)

    def atacar(self, objetivo):
        dano = self.ataque
        objetivo.recibir_dano(dano)
        print(f"{self.nombre} (Mago) ataca a {objetivo.nombre} con {dano} de daño (sin defensa). Vida restante de {objetivo.nombre}: {objetivo.vida}")


class Arquero(Personaje):
    def __init__(self, nombre, vida, ataque, defensa):
        super().__init__(nombre, vida, ataque, defensa)

    def atacar(self, objetivo):
        if self.ataque > objetivo.defensa:
            dano = (self.ataque - objetivo.defensa) * 2
        else:
            dano = self.ataque - objetivo.defensa
        objetivo.recibir_dano(dano)
        print(f"{self.nombre} (Arquero) ataca a {objetivo.nombre} con {dano} de daño. Vida restante de {objetivo.nombre}: {objetivo.vida}")


def batalla(personaje1, personaje2):
    turno = 0
    while personaje1.vida > 0 and personaje2.vida > 0:
        print(f"\nTurno {turno + 1}:")
        if turno % 2 == 0:
            personaje1.atacar(personaje2)
        else:
            personaje2.atacar(personaje1)
        turno += 1

    if personaje1.vida > 0:
        print(f"\n{personaje1.nombre} (Guerrero) gana la batalla con {personaje1.vida} de vida restante.")
    else:
        print(f"\n{personaje2.nombre} gana la batalla con {personaje2.vida} de vida restante.")

    print("\n¡Batalla finalizada!")


# Función para que cada jugador ingrese su personaje
def elegir_personaje(jugador_nombre):
    print(f"\n{jugador_nombre}, elige tu personaje:")
    print("1. Guerrero")
    print("2. Mago")
    print("3. Arquero")
    eleccion = input("Ingresa el número de tu elección (1, 2 o 3): ")
    
    if eleccion == "1":
        nombre_guerrero = input(f"Ingresa el nombre de tu Guerrero, {jugador_nombre}: ")
        return Guerrero(nombre_guerrero, 100, 30, 20)
    elif eleccion == "2":
        nombre_mago = input(f"Ingresa el nombre de tu Mago, {jugador_nombre}: ")
        return Mago(nombre_mago, 80, 40, 10)
    elif eleccion == "3":
        nombre_arquero = input(f"Ingresa el nombre de tu Arquero, {jugador_nombre}: ")
        return Arquero(nombre_arquero, 90, 25, 15)
    else:
        print("Opción no válida, elige 1, 2 o 3.")
        return elegir_personaje(jugador_nombre)

# Pedir al jugador los nombres
jugador1_nombre = input("Ingresa el nombre del primer jugador: ")
jugador2_nombre = input("Ingresa el nombre del segundo jugador: ")
jugador3_nombre = input("Ingresa el nombre del tercer jugador: ")

# Crear los personajes de los tres jugadores
jugador1 = elegir_personaje(jugador1_nombre)
jugador2 = elegir_personaje(jugador2_nombre)
jugador3 = elegir_personaje(jugador3_nombre)

# Mostrar los personajes creados
print(f"\n{jugador1.nombre} es un {jugador1.__class__.__name__}!")
print(f"{jugador2.nombre} es un {jugador2.__class__.__name__}!")
print(f"{jugador3.nombre} es un {jugador3.__class__.__name__}!")

# Iniciar las batallas entre los tres jugadores
print(f"\n¡La batalla entre {jugador1.nombre}, {jugador2.nombre} y {jugador3.nombre} comenzará!")

# Batalla entre los jugadores
print(f"\nBatalla entre {jugador1.nombre} y {jugador2.nombre}:")
batalla(jugador1, jugador2)

print(f"\nBatalla entre {jugador2.nombre} y {jugador3.nombre}:")
batalla(jugador2, jugador3)

print(f"\nBatalla entre {jugador1.nombre} y {jugador3.nombre}:")
batalla(jugador1, jugador3)

import tkinter as tk
from tkinter import messagebox
import random

# ------------------ Clases de Personajes ------------------

class Personaje:
    def __init__(self, nombre, vida, ataque, defensa):
        self.nombre = nombre
        self.__vida = vida
        self.__ataque = ataque
        self.__defensa = defensa

    @property
    def vida(self):
        return self.__vida

    @vida.setter
    def vida(self, valor):
        self.__vida = max(0, min(valor, 100))

    @property
    def ataque(self):
        return self.__ataque

    @property
    def defensa(self):
        return self.__defensa

    def atacar(self, objetivo):
        dano = self.calcular_dano(objetivo)
        objetivo.recibir_dano(dano)
        return f"{self.nombre} ataca a {objetivo.nombre} con {dano:.2f} de daño. Vida restante: {objetivo.vida:.2f}"

    def calcular_dano(self, objetivo):
        return 0

    def recibir_dano(self, dano):
        self.vida -= dano

    def usar_poder(self):
        return None, 1.0

class Guerrero(Personaje):
    def calcular_dano(self, objetivo):
        base_dano = self.ataque * 1.2
        return max(base_dano - objetivo.defensa, 0)

    def atacar(self, objetivo):
        poder, mult = self.usar_poder()
        dano = self.calcular_dano(objetivo) * mult
        objetivo.recibir_dano(dano)
        texto = f"{self.nombre} (Guerrero) ataca a {objetivo.nombre} con {dano:.2f} de daño."
        if poder:
            texto += f" ¡Usó poder: {poder}!"
        texto += f" Vida restante: {objetivo.vida:.2f}"
        return texto

    def usar_poder(self):
        if random.random() < 0.3:
            poder = random.choice(["Furia", "Golpe Crítico", "Defensa Temporal"])
            if poder == "Furia":
                return poder, 1.5
            elif poder == "Golpe Crítico":
                return poder, 2.0
            elif poder == "Defensa Temporal":
                return poder, 1.3
        return None, 1.0

class Mago(Personaje):
    def calcular_dano(self, objetivo):
        return self.ataque

    def atacar(self, objetivo):
        poder, mult = self.usar_poder()
        dano = self.calcular_dano(objetivo) * mult
        objetivo.recibir_dano(dano)
        texto = f"{self.nombre} (Mago) ataca a {objetivo.nombre} con {dano:.2f} de daño (ignora defensa)."
        if poder:
            texto += f" ¡Usó poder: {poder}!"
        texto += f" Vida restante: {objetivo.vida:.2f}"
        return texto

    def usar_poder(self):
        if random.random() < 0.3:
            poder = random.choice(["Bola de Fuego", "Escudo Mágico", "Curación"])
            if poder == "Bola de Fuego":
                return poder, 1.7
            elif poder == "Escudo Mágico":
                self.vida += 15
                return poder, 1.0
            elif poder == "Curación":
                self.vida += 20
                return poder, 1.0
        return None, 1.0

class Arquero(Personaje):
    def calcular_dano(self, objetivo):
        if self.ataque > objetivo.defensa:
            return (self.ataque - objetivo.defensa) * 2
        else:
            return max(self.ataque - objetivo.defensa, 0)

    def atacar(self, objetivo):
        poder, mult = self.usar_poder()
        dano = self.calcular_dano(objetivo) * mult
        objetivo.recibir_dano(dano)
        texto = f"{self.nombre} (Arquero) ataca a {objetivo.nombre} con {dano:.2f} de daño."
        if poder:
            texto += f" ¡Usó poder: {poder}!"
        texto += f" Vida restante: {objetivo.vida:.2f}"
        return texto

    def usar_poder(self):
        if random.random() < 0.3:
            poder = random.choice(["Disparo Preciso", "Flecha Envenenada", "Evasión"])
            if poder == "Disparo Preciso":
                return poder, 1.8
            elif poder == "Flecha Envenenada":
                return poder, 1.3
            elif poder == "Evasión":
                self.vida += 10
                return poder, 1.0
        return None, 1.0

# ------------------ Interfaz con Tkinter ------------------

class BatallaApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Guardians of the Ancient Kingdom")
        self.root.geometry("650x500")
        self.root.configure(bg="#2D4257")

        titulo = tk.Label(root, text="Guardians of the Ancient Kingdom", font=("Helvetica", 20, "bold"), bg="#34495E", fg="white")
        titulo.pack(fill=tk.X, pady=10)

        frame_jugadores = tk.Frame(root, bg="#2C3E50")
        frame_jugadores.pack(pady=10)

        # Jugador 1
        frame_j1 = tk.LabelFrame(frame_jugadores, text="Jugador 1", font=("Helvetica", 14, "bold"), fg="#ECF0F1", bg="#34495E", padx=10, pady=10)
        frame_j1.grid(row=0, column=0, padx=15)

        tk.Label(frame_j1, text="Nombre:", bg="#34495E", fg="white", font=("Helvetica", 12)).grid(row=0, column=0, sticky="e", pady=5)
        self.j1_nombre = tk.Entry(frame_j1, font=("Helvetica", 12))
        self.j1_nombre.grid(row=0, column=1, pady=5)

        tk.Label(frame_j1, text="Clase:", bg="#34495E", fg="white", font=("Helvetica", 12)).grid(row=1, column=0, sticky="e", pady=5)
        self.j1_clase = tk.StringVar(value="Guerrero")
        clases_menu1 = tk.OptionMenu(frame_j1, self.j1_clase, "Guerrero", "Mago", "Arquero")
        clases_menu1.config(font=("Helvetica", 12), bg="#2980B9", fg="white")
        clases_menu1.grid(row=1, column=1, pady=5)

        # Jugador 2
        frame_j2 = tk.LabelFrame(frame_jugadores, text="Jugador 2", font=("Helvetica", 14, "bold"), fg="#ECF0F1", bg="#34495E", padx=10, pady=10)
        frame_j2.grid(row=0, column=1, padx=15)

        tk.Label(frame_j2, text="Nombre:", bg="#34495E", fg="white", font=("Helvetica", 12)).grid(row=0, column=0, sticky="e", pady=5)
        self.j2_nombre = tk.Entry(frame_j2, font=("Helvetica", 12))
        self.j2_nombre.grid(row=0, column=1, pady=5)

        tk.Label(frame_j2, text="Clase:", bg="#34495E", fg="white", font=("Helvetica", 12)).grid(row=1, column=0, sticky="e", pady=5)
        self.j2_clase = tk.StringVar(value="Guerrero")
        clases_menu2 = tk.OptionMenu(frame_j2, self.j2_clase, "Guerrero", "Mago", "Arquero")
        clases_menu2.config(font=("Helvetica", 12), bg="#2980B9", fg="white")
        clases_menu2.grid(row=1, column=1, pady=5)

        self.boton_batalla = tk.Button(root, text="Iniciar Batalla", font=("Helvetica", 14, "bold"), bg="#27AE60", fg="white", command=self.iniciar_batalla)
        self.boton_batalla.pack(pady=15)

        frame_resultado = tk.Frame(root)
        frame_resultado.pack(padx=20, pady=10, fill=tk.BOTH, expand=True)

        self.resultado_texto = tk.Text(frame_resultado, height=15, width=75, font=("Courier", 12), bg="#1A252F", fg="#ECF0F1", wrap=tk.WORD)
        self.resultado_texto.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)

        scrollbar = tk.Scrollbar(frame_resultado, command=self.resultado_texto.yview)
        scrollbar.pack(side=tk.RIGHT, fill=tk.Y)
        self.resultado_texto.config(yscrollcommand=scrollbar.set)

    def crear_personaje(self, nombre, clase):
        if clase == "Guerrero":
            return Guerrero(nombre, 100, 30, 20)
        elif clase == "Mago":
            return Mago(nombre, 80, 40, 10)
        elif clase == "Arquero":
            return Arquero(nombre, 90, 25, 15)

    def iniciar_batalla(self):
        nombre1 = self.j1_nombre.get()
        clase1 = self.j1_clase.get()
        nombre2 = self.j2_nombre.get()
        clase2 = self.j2_clase.get()

        if not nombre1 or not nombre2:
            messagebox.showwarning("Error", "Por favor ingresa nombres para ambos jugadores.")
            return

        self.jugador1 = self.crear_personaje(nombre1, clase1)
        self.jugador2 = self.crear_personaje(nombre2, clase2)

        self.resultado_texto.delete("1.0", tk.END)
        self.resultado_texto.insert(tk.END, f"Comienza la batalla entre {nombre1} ({clase1}) y {nombre2} ({clase2})\n\n")

        turno = 0
        while self.jugador1.vida > 0 and self.jugador2.vida > 0:
            if turno % 2 == 0:
                resultado = self.jugador1.atacar(self.jugador2)
            else:
                resultado = self.jugador2.atacar(self.jugador1)
            self.resultado_texto.insert(tk.END, resultado + "\n")
            self.resultado_texto.see(tk.END)
            self.root.update()
            turno += 1

        ganador = self.jugador1 if self.jugador1.vida > 0 else self.jugador2
        self.resultado_texto.insert(tk.END, f"\n¡{ganador.nombre} gana la batalla!\n")

# ------------------ Ejecutar la App ------------------
if __name__ == "__main__":
    root = tk.Tk()
    app = BatallaApp(root)
    root.mainloop()












# ------------------ 📘 Documentación del juego: Guardians of the Ancient Kingdom-----------------


1. Información general
Nombre del proyecto: Guardians of the Ancient Kingdom
Lenguaje: Python 3
Interfaz gráfica: Tkinter
Estilo de juego: Juego por turnos (tipo RPG básico)
Objetivo: Simular una batalla entre dos personajes con distintas clases y habilidades especiales.

2. Descripción del juego
Guardians of the Ancient Kingdom es un juego por turnos donde dos jugadores eligen una clase (Guerrero, Mago o Arquero) y compiten en una batalla usando ataques y poderes únicos. Cada clase tiene sus propias estadísticas y habilidades especiales que se activan de forma aleatoria durante la pelea.

3. Clases de personajes
🛡️ Guerrero
Vida: 100

Ataque: 30

Defensa: 20

Estilo: Lucha cuerpo a cuerpo

Poderes posibles:

Furia: Aumenta el daño en 1.5×

Golpe Crítico: Aumenta el daño en 2.0×

Defensa Temporal: Aumenta el daño en 1.3×

🔮 Mago
Vida: 80

Ataque: 40

Defensa: 10

Estilo: Magia a distancia (ignora defensa del oponente)

Poderes posibles:

Bola de Fuego: Aumenta el daño en 1.7×

Escudo Mágico: Recupera 15 de vida

Curación: Recupera 20 de vida

🏹 Arquero
Vida: 90

Ataque: 25

Defensa: 15

Estilo: Combate a distancia con daño doble si supera la defensa

Poderes posibles:

Disparo Preciso: Aumenta el daño en 1.8×

Flecha Envenenada: Aumenta el daño en 1.3×

Evasión: Recupera 10 de vida

4. Mecánica de juego
Ambos jugadores eligen nombre y clase.

Cada turno, un jugador ataca al otro.

Se calcula el daño considerando el ataque, defensa y el uso (o no) de un poder especial.

Los turnos se alternan hasta que uno de los jugadores llega a 0 de vida.

Se muestra el registro de los ataques y el ganador final en un cuadro de texto.

5. Interfaz gráfica
La interfaz se construye con Tkinter e incluye:

Entradas para el nombre de cada jugador.

Menús desplegables para seleccionar la clase.

Botón de iniciar batalla.

Panel de resultados tipo consola para ver los ataques y el resultado de la batalla.

6. Código fuente y estructura
Personaje: Clase base con atributos comunes.

Guerrero, Mago, Arquero: Heredan de Personaje y definen su estilo de combate.

BatallaApp: Clase principal que contiene la interfaz, lógica de creación de personajes y turnos de ataque.

7. Requisitos
Python 3.x instalado

No se requieren librerías externas (Tkinter viene incluido)



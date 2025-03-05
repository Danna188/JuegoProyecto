# JuegoProyecto
import tkinter as tk
import random
from tkinter import messagebox, simpledialog
import pygame 
import os
import TableroJugador1
import TableroJugador2

# Inicializar Pygame
pygame.init()
pygame.mixer.init()

# Directorio de sonidos
sound_dir = os.path.join(os.path.dirname(__file__), 'sonidos')

# Cargar sonidos
sound_disparo = pygame.mixer.Sound(os.path.join(sound_dir, "disparo.wav"))
sound_fallido = pygame.mixer.Sound(os.path.join(sound_dir, "fallido.wav"))
sound_acertado = pygame.mixer.Sound(os.path.join(sound_dir, "acertado.wav"))

# Constantes
COLOR_FONDO = "lightblue"
CANTIDAD_BARCOS_INICIALES = 5
JUGADOR_1 = "Jugador 1"
JUGADOR_2 = "Jugador 2"
DISPARO_FALLADO = "-"
DISPARO_ACERTADO = "*"
DISPAROS_INICIALES = 10



# Configuración de la ventana principal
root = tk.Tk()
root.title("Batalla Naval Pearl Harbor - Jugador 1")

# Configuración de la segunda ventana
root2 = tk.Toplevel(root)
root2.title("Batalla Naval Pearl Harbor - Jugador 2")

# Canvas para la ventana del Jugador 1
canvas = tk.Canvas(root, width=600, height=600, bg=COLOR_FONDO)
canvas.pack()

# Canvas para la ventana del Jugador 2
canvas2 = tk.Canvas(root2, width=600, height=600, bg=COLOR_FONDO)
canvas2.pack()



text_output = tk.Text(root, height=10, width=50)
text_output.pack()

label_disparos = tk.Label(root, text="")
label_disparos.pack()

def manejar_turno(turno_actual, matriz_j1, matriz_j2, x, y, resultado_label):
    if turno_actual == JUGADOR_1:
        if matriz_j2[y][x] != TableroJugador2.MAR and matriz_j2[y][x] not in (DISPARO_FALLADO, DISPARO_ACERTADO):
            matriz_j2[y][x] = DISPARO_ACERTADO
            resultado_label.config(text="¡Impacto!")
            sound_acertado.play()
        elif matriz_j2[y][x] == TableroJugador2.MAR:
            matriz_j2[y][x] = DISPARO_FALLADO
            resultado_label.config(text="Fallido.")
            sound_fallido.play()
        return JUGADOR_2
    else:
        if matriz_j1[y][x] != TableroJugador1.MAR and matriz_j1[y][x] not in (DISPARO_FALLADO, DISPARO_ACERTADO):
            matriz_j1[y][x] = DISPARO_ACERTADO
            resultado_label.config(text="¡Impacto!")
            sound_acertado.play()
        elif matriz_j1[y][x] == TableroJugador1.MAR:
            matriz_j1[y][x] = DISPARO_FALLADO
            resultado_label.config(text="Fallido.")
            sound_fallido.play()
        return JUGADOR_1

def dibujar_juego(canvas, canvas2, matriz_j1, matriz_j2, turno_actual):
    canvas.delete("all")
    TableroJugador1.dibujar_tablero(canvas, matriz_j1, True, 0)
    canvas.create_text(50, 10, text=f"Turno: {turno_actual}", fill="black")

    canvas2.delete("all")
    TableroJugador2.dibujar_tablero(canvas2, matriz_j2, True, 600)
    canvas2.create_text(50, 10, text=f"Turno: {turno_actual}", fill="black")

def on_click(event, turno_actual, matriz_j1, matriz_j2, resultado_label):
    x, y = (event.x % 600) // 60, event.y // 60
    if event.x < 600 and turno_actual == JUGADOR_1:
        turno_actual = manejar_turno(turno_actual, matriz_j1, matriz_j2, x, y, resultado_label)
    elif event.x >= 600 and turno_actual == JUGADOR_2:
        turno_actual = manejar_turno(turno_actual, matriz_j1, matriz_j2, x, y, resultado_label)
    dibujar_juego(canvas, canvas2, matriz_j1, matriz_j2, turno_actual)

def imprimir_disparos_restantes(disparos_restantes, jugador):
    label_disparos.config(text=f"Disparos restantes de {jugador}: {disparos_restantes}")
def imprimir_disparos_restantes(disparos_restantes, jugador):
    label_disparos.config(text=f"Disparos restantes de {jugador}: {disparos_restantes}")


def main():
    matriz_j1 = TableroJugador1.obtener_matriz_inicial()
    matriz_j2 = TableroJugador2.obtener_matriz_inicial()
    matriz_j1 = TableroJugador1.colocar_e_imprimir_barcos(matriz_j1, CANTIDAD_BARCOS_INICIALES)
    matriz_j2 = TableroJugador2.colocar_e_imprimir_barcos(matriz_j2, CANTIDAD_BARCOS_INICIALES)
    turno_actual = JUGADOR_1
    resultado_label = tk.Label(root, text="")
    resultado_label.pack()

    canvas.bind("<Button-1>", lambda event: on_click(event, turno_actual, matriz_j1, matriz_j2, resultado_label))
    canvas2.bind("<Button-1>", lambda event: on_click(event, turno_actual, matriz_j1, matriz_j2, resultado_label))

    dibujar_juego(canvas, canvas2, matriz_j1, matriz_j2, turno_actual)

    root.mainloop()
    root2.mainloop()

if __name__ == "__main__":
    main()


LIBRERIAS TABLERO JUGADOR1 Y TABLERO JUGADOR2
TableroJugador1
import tkinter as tk
import random

# Constantes
COLUMNAS = 10
FILAS = 10
MAR = " "
SUBMARINO = "S"
DESTRUCTOR = "D"
DESTRUCTOR_VERTICAL = "A"

def obtener_matriz_inicial():
    return [[MAR for _ in range(COLUMNAS)] for _ in range(FILAS)]

def colocar_e_imprimir_barcos(matriz, cantidad_barcos):
    barcos_una_celda = cantidad_barcos // 2
    barcos_dos_celdas_verticales = cantidad_barcos // 4
    barcos_dos_celdas_horizontales = cantidad_barcos // 4

    # Colocar barcos de una celda
    for _ in range(barcos_una_celda):
        while True:
            x = random.randint(0, 9)
            y = random.randint(0, 9)
            if matriz[y][x] == MAR:
                matriz[y][x] = SUBMARINO
                break

    # Colocar barcos de dos celdas horizontales
    for _ in range(barcos_dos_celdas_horizontales):
        while True:
            x = random.randint(0, 8)
            y = random.randint(0, 9)
            if matriz[y][x] == MAR and matriz[y][x + 1] == MAR:
                matriz[y][x] = DESTRUCTOR
                matriz[y][x + 1] = DESTRUCTOR
                break

    # Colocar barcos de dos celdas verticales
    for _ in range(barcos_dos_celdas_verticales):
        while True:
            x = random.randint(0, 9)
            y = random.randint(0, 8)
            if matriz[y][x] == MAR and matriz[y + 1][x] == MAR:
                matriz[y][x] = DESTRUCTOR_VERTICAL
                matriz[y + 1][x] = DESTRUCTOR_VERTICAL
                break

    return matriz

def dibujar_tablero(canvas, matriz_juego, mostrar_barcos, offset_x=0, imagen_Barco_destruido= None, imagen_agua_fallida= None):
    for i in range(len(matriz_juego)):
        for j in range(len(matriz_juego[i])):
            x1, y1 = j * 60 + offset_x, i * 60
            x2, y2 = x1 + 60, y1 + 60
            if matriz_juego[i][j] == MAR:
                color = "lightblue"
                canvas.create_rectangle(x1, y1, x2, y2, fill=color)
        
            else:
                color = "gray" if mostrar_barcos and matriz_juego[i][j] != MAR else "lightblue"
                canvas.create_rectangle(x1, y1, x2, y2, fill=color)

TableroJugador2
import tkinter as tk
import random

# Constantes
COLUMNAS = 10
FILAS = 10
MAR = " "
SUBMARINO = "S"
DESTRUCTOR = "D"
DESTRUCTOR_VERTICAL = "A"

def obtener_matriz_inicial():
    return [[MAR for _ in range(COLUMNAS)] for _ in range(FILAS)]

def colocar_e_imprimir_barcos(matriz, cantidad_barcos):
    barcos_una_celda = cantidad_barcos // 2
    barcos_dos_celdas_verticales = cantidad_barcos // 4
    barcos_dos_celdas_horizontales = cantidad_barcos // 4

    # Colocar barcos de una celda
    for _ in range(barcos_una_celda):
        while True:
            x = random.randint(0, 9)
            y = random.randint(0, 9)
            if matriz[y][x] == MAR:
                matriz[y][x] = SUBMARINO
                break

    # Colocar barcos de dos celdas horizontales
    for _ in range(barcos_dos_celdas_horizontales):
        while True:
            x = random.randint(0, 8)
            y = random.randint(0, 9)
            if matriz[y][x] == MAR and matriz[y][x + 1] == MAR:
                matriz[y][x] = DESTRUCTOR
                matriz[y][x + 1] = DESTRUCTOR
                break

    # Colocar barcos de dos celdas verticales
    for _ in range(barcos_dos_celdas_verticales):
        while True:
            x = random.randint(0, 9)
            y = random.randint(0, 8)
            if matriz[y][x] == MAR and matriz[y + 1][x] == MAR:
                matriz[y][x] = DESTRUCTOR_VERTICAL
                matriz[y + 1][x] = DESTRUCTOR_VERTICAL
                break

    return matriz

def dibujar_tablero(canvas, matriz_juego, mostrar_barcos, offset_x=0, imagen_Barco_destruido=None, imagen_agua_fallida=None):
    for i in range(len(matriz_juego)):
        for j in range(len(matriz_juego[i])):
            x1, y1 = j * 60 + offset_x, i * 60
            x2, y2 = x1 + 60, y1 + 60

            # Dibujar la cuadrícula
            canvas.create_rectangle(x1, y1, x2, y2, outline="black")

            # Dibujar el contenido de la casilla
            if matriz_juego[i][j] == MAR:
                color = "lightblue"
                canvas.create_rectangle(x1, y1, x2, y2, fill=color)
            
            else:
                # No mostrar barcos en el tablero del Jugador 2
                color = "lightblue"
                canvas.create_rectangle(x1, y1, x2, y2, fill=color)
   


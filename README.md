🚀 Proyecto Tercer Corte – Ingeniería de Sistemas

Este repositorio contiene el desarrollo completo del proyecto solicitado por La Santoto, compuesto por cuatro grandes módulos:

Web Scraping multihilo con Selenium

Proceso ETL (Extracción, Transformación y Carga)

Clasificación con MediaPipe y análisis de velocidad con OpenCV2

Despliegue en Docker + Streamlit

El trabajo incluye uso obligatorio de hilos, secciones críticas, mutex, semaforización, y documentación detallada del proceso.

🟨 1. Web Scraping con Selenium (Módulo 1)
✔️ Requerimientos del laboratorio

Desarrollo de web scraping usando Selenium.

Implementación con hilos, mutex, zona crítica, semaforización.

Búsqueda de al menos 10 elementos electrónicos.

Obtención mínima de 200 imágenes por cada elemento.

Documentar paso a paso el funcionamiento del algoritmo.

📁 Estructura del módulo
/scraping/
│── scraper.py
│── downloader.py
│── threads.py
│── /data/
│     └── /<producto>/
│             └── imágenes...

🧵 Descripción del Algoritmo Multihilo

El sistema usa:

ThreadPool para ejecutar descargas paralelas

Mutex (Lock) para proteger la sección crítica al escribir archivos

Semáforo para controlar cuántos hilos pueden acceder al navegador

Colas (Queue) para distribuir las tareas

🔧 Código base del Web Scraping (ejemplo)

Reemplaza con tu versión si ya la tienes implementada.

from selenium import webdriver
from selenium.webdriver.common.by import By
import threading
import os
import requests
import time

lock = threading.Lock()
semaforo = threading.Semaphore(3)  # Máx. 3 scrapers simultáneos

def descargar_imagen(url_img, carpeta):
    try:
        img_data = requests.get(url_img).content
        nombre = url_img.split("/")[-1]

        with lock:  # Sección crítica
            with open(f"{carpeta}/{nombre}", "wb") as f:
                f.write(img_data)
    except:
        pass

def scrape_producto(url, carpeta):
    semaforo.acquire()
    options = webdriver.ChromeOptions()
    options.add_argument("--headless")
    driver = webdriver.Chrome(options=options)

    os.makedirs(carpeta, exist_ok=True)
    driver.get(url)
    time.sleep(2)

    imagenes = driver.find_elements(By.TAG_NAME, "img")

    for img in imagenes:
        src = img.get_attribute("src")
        if src:
            threading.Thread(target=descargar_imagen, args=(src, carpeta)).start()

    driver.quit()
    semaforo.release()


🖼️ Evidencias

👉 Aquí agregas las capturas del scraping trabajando, descargas, carpetas creadas, etc.

🟦 2. Proceso ETL (Módulo 2)
✔️ Requerimientos

Preprocesamiento y procesamiento multihilo.

Limpieza de imágenes (detección de archivos dañados).

Transformación de datos y extracción de información relevante.

Documentación del uso de hilos.

🔄 Flujo ETL
E – Extracción

Se cargan las imágenes obtenidas del scraping.

T – Transformación

Eliminación de duplicados

Validación de imágenes dañadas

Conversión de formato

Estandarización de tamaños

Corrección automática de nombres

L – Carga

Organización por carpetas

Opcional: guardar metadatos en CSV/SQLite

🔧 Código Base del Proceso ETL
from PIL import Image
import os
import threading

def validar_imagen(path):
    try:
        img = Image.open(path)
        img.verify()
        return True
    except:
        return False

def procesar_carpeta(carpeta):
    archivos = os.listdir(carpeta)

    for archivo in archivos:
        ruta = f"{carpeta}/{archivo}"
        if not validar_imagen(ruta):
            os.remove(ruta)

threads = []
for folder in os.listdir("data/"):
    t = threading.Thread(target=procesar_carpeta, args=(f"data/{folder}",))
    t.start()
    threads.append(t)

for t in threads:
    t.join()

🖼️ Evidencias

👉 Coloca aquí capturas del proceso ETL y carpetas limpias.

🟩 3. Clasificación con MediaPipe y Velocidad con OpenCV2 (Módulo 3)
✔️ Requerimientos

Clasificación de elementos con MediaPipe.

Detección y cálculo de velocidad de personas con OpenCV2.

Funcionamiento en tiempo real con cámara de PC.

Explicación del manejo de hilos.

🧠 Clasificación con MediaPipe

Ejemplo simple:

import mediapipe as mp
import cv2

mp_obj = mp.solutions.hands

cap = cv2.VideoCapture(0)
with mp_obj.Hands() as hands:
    while True:
        ret, frame = cap.read()
        rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        res = hands.process(rgb)

        if res.multi_hand_landmarks:
            for hand in res.multi_hand_landmarks:
                print("Mano detectada")

        cv2.imshow("Detector", frame)
        if cv2.waitKey(1) & 0xFF == 27:
            break

cap.release()

🏃‍♂️ Detección de velocidad con OpenCV

Se rastrea una persona cuadro a cuadro:

Posición anterior (x1,y1)

Posición nueva (x2,y2)

Distancia recorrida

Tiempo transcurrido

Velocidad = distancia / tiempo

🖼️ Evidencias

👉 Agrega captura del video con recuadros y velocidad.

🟧 4. Despliegue con Docker + Streamlit (Módulo 4)
✔️ Requerimientos

Aplicación empacada en un contenedor Docker.

Despliegue local explicado paso a paso.

Despliegue en Streamlit Cloud (web).

Subir imagen del contenedor a DockerHub.

README bien redactado.

🐳 Dockerfile ejemplo
FROM python:3.10

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

EXPOSE 8501
CMD ["streamlit", "run", "app.py"]

▶️ Correr contenedor localmente
docker build -t proyecto-tercer-corte .
docker run -p 8501:8501 proyecto-tercer-corte

🌐 Despliegue en Streamlit

Subir repo a GitHub

Ir a share.streamlit.io

Conectar repositorio

Seleccionar archivo app.py

🔗 DockerHub
docker tag proyecto-tercer-corte usuario/proyecto-tercer-corte
docker push usuario/proyecto-tercer-corte

🖼️ Evidencias

👉 Coloca captura del Docker ejecutando, Streamlit en web y DockerHub.

📚 Conclusiones

Este proyecto integra:

Scraping profesional con manejo de concurrencia

ETL completo para asegurar calidad de datos

Procesamiento multimedia con IA y visión por computadora

Despliegue moderno con Docker y Streamlit

Es la unión de varias áreas avanzadas de la ingeniería.

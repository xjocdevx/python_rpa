# python_rpa
Python + RPA

📁 ESTRUCTURA DEL PROYECTO
```python
text
rpa_curso_2026/
├── config/
│   └── config.env
├── data/Abrir Bloc de
│   ├── ventas.xlsx
│   ├── facturas/
│   └── procesados/
├── logs/
├── scripts/
├── utils/
├── main.py
└── requirements.txt
```
🔧 MÓDULO 0: INTRODUCCIÓN Y SETUP
Ejercicio: Primer Robot RPA - "Hola Mundo" con Bloc de Notas
python
# modulo0_primer_robot.py
"""
PRIMER ROBOT RPA - MÓDULO 0
Objetivo: Crear un script que muestre una alerta y escriba automáticamente en Bloc de Notas
"""

import pyautogui
import time
from datetime import datetime

# Configuración de seguridad
pyautogui.PAUSE = 0.5  # Pausa de 0.5 segundos entre acciones
pyautogui.FAILSAFE = True  # Mover mouse a esquina superior izquierda para abortar

def mostrar_alerta():
    """Muestra una alerta con pyautogui"""
    pyautogui.alert(text='¡Bienvenido al curso RPA con Python 2026!', 
                    title='Robot RPA', 
                    button='OK')

def escribir_en_bloc_notas():
    """Abre Bloc de Notas y escribe un mensaje automáticamente"""
    
    # Abrir Bloc de Notas (Win + R, luego 'notepad')
    pyautogui.hotkey('win', 'r')
    time.sleep(0.5)
    pyautogui.write('notepad')
    pyautogui.press('enter')
    time.sleep(1)
    
    # Escribir el mensaje
    fecha_hora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
    mensaje = f"""=== REPORTE AUTOMATIZADO ===
Fecha: {fecha_hora}
Mensaje: ¡Hola Mundo desde RPA!

Este texto fue escrito automáticamente por un robot.
Tecnologías: Python + PyAutoGUI
Curso: Automatización Inteligente 2026
"""
    pyautogui.write(mensaje)
    
    # Guardar el archivo (Ctrl+S)
    pyautogui.hotkey('ctrl', 's')
    time.sleep(1)
    
    # Escribir nombre del archivo
    pyautogui.write('reporte_automatico.txt')
    pyautogui.press('enter')
    time.sleep(0.5)
    
    # Cerrar Bloc de Notas (Alt+F4)
    pyautogui.hotkey('alt', 'f4')
    
    print("✅ Robot ejecutado correctamente")

if __name__ == "__main__":
    mostrar_alerta()
    escribir_en_bloc_notas()
Explicación:

pyautogui.alert(): Muestra un popup de alerta

pyautogui.hotkey(): Simula combinaciones de teclas (Win+R, Ctrl+S, Alt+F4)

pyautogui.write(): Escribe texto como si fuera un teclado real

pyautogui.press(): Presiona una tecla individual (enter)

FAILSAFE: Si mueves el mouse a la esquina superior izquierda, el robot se detiene

📘 MÓDULO 1: FUNDAMENTOS DE PYTHON PARA RPA
Ejercicio: Procesador de Archivos CSV
python
# modulo1_processor_csv.py
"""
EJERCICIO MÓDULO 1 - PROCESADOR DE CSV
Objetivo: Leer un archivo CSV, contar filas y mostrar un resumen
"""

import csv
import re
from pathlib import Path
from datetime import datetime

# 1.5 Manejo de archivos con pathlib
def leer_csv_con_pathlib(ruta_archivo):
    """Lee un archivo CSV usando pathlib"""
    ruta = Path(ruta_archivo)
    
    if not ruta.exists():
        raise FileNotFoundError(f"El archivo {ruta_archivo} no existe")
    
    print(f"📁 Archivo encontrado: {ruta.name}")
    print(f"📏 Tamaño: {ruta.stat().st_size} bytes")
    return ruta

# 1.6 Expresiones regulares para validar datos
def validar_correo(email):
    """Valida formato de correo electrónico con regex"""
    patron = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(patron, email))

def validar_telefono(telefono):
    """Valida formato de teléfono (ejemplo: +34 123456789)"""
    patron = r'^\+?\d{1,3}[\s-]?\d{6,14}$'
    return bool(re.match(patron, str(telefono)))

# 1.4 Funciones con parámetros y retorno
def procesar_csv(ruta_archivo, tiene_cabecera=True):
    """
    Procesa un archivo CSV y retorna estadísticas
    
    Args:
        ruta_archivo: Ruta al archivo CSV
        tiene_cabecera: Indica si el archivo tiene cabecera
    
    Returns:
        dict: Diccionario con estadísticas del archivo
    """
    datos = []
    
    try:  # 1.7 Manejo de excepciones
        with open(ruta_archivo, 'r', encoding='utf-8') as archivo:
            if tiene_cabecera:
                lector = csv.DictReader(archivo)
                cabeceras = lector.fieldnames
                for fila in lector:
                    datos.append(fila)
            else:
                lector = csv.reader(archivo)
                cabeceras = [f"Columna_{i+1}" for i in range(len(next(lector)))]
                archivo.seek(0)  # Volver al inicio
                for fila in lector:
                    datos.append(dict(zip(cabeceras, fila)))
        
        # 1.2 Estructuras de datos: listas y diccionarios
        num_filas = len(datos)
        
        # 1.3 Control de flujo y operaciones con listas
        resumen = {
            'nombre_archivo': Path(ruta_archivo).name,
            'num_filas_datos': num_filas,
            'num_columnas': len(cabeceras),
            'cabeceras': cabeceras,
            'primeras_filas': datos[:3] if num_filas >= 3 else datos,
            'fecha_procesamiento': datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        }
        
        # Contar valores nulos en primera columna (ejemplo)
        columna_principal = cabeceras[0]
        nulos = sum(1 for fila in datos if not fila.get(columna_principal))
        resumen['valores_nulos'] = nulos
        
        return resumen
        
    except FileNotFoundError as e:
        print(f"❌ Error: Archivo no encontrado - {e}")
        raise
    except csv.Error as e:
        print(f"❌ Error: El archivo no es un CSV válido - {e}")
        raise
    except Exception as e:
        print(f"❌ Error inesperado: {e}")
        raise

# 1.1 Variables y f-strings para formato
def mostrar_resumen(resumen):
    """Muestra el resumen formateado usando f-strings"""
    print("\n" + "="*50)
    print(f"📊 RESUMEN DEL ARCHIVO: {resumen['nombre_archivo']}")
    print("="*50)
    print(f"📅 Procesado: {resumen['fecha_procesamiento']}")
    print(f"📋 Número de filas: {resumen['num_filas_datos']}")
    print(f"📊 Número de columnas: {resumen['num_columnas']}")
    print(f"🔖 Columnas: {', '.join(resumen['cabeceras'])}")
    print(f"⚠️ Valores nulos en '{resumen['cabeceras'][0]}': {resumen['valores_nulos']}")
    
    print("\n📄 Primeras 3 filas:")
    for i, fila in enumerate(resumen['primeras_filas'], 1):
        print(f"  {i}. {fila}")

# Función principal que orquesta todo
def main():
    """Función principal del ejercicio"""
    # Crear archivo CSV de ejemplo si no existe
    archivo_ejemplo = Path("data/ejemplo.csv")
    archivo_ejemplo.parent.mkdir(exist_ok=True)
    
    if not archivo_ejemplo.exists():
        print("📝 Creando archivo CSV de ejemplo...")
        with open(archivo_ejemplo, 'w', newline='', encoding='utf-8') as f:
            writer = csv.writer(f)
            writer.writerow(['Nombre', 'Email', 'Telefono', 'Edad'])
            writer.writerow(['Ana García', 'ana@example.com', '+34 123456789', '30'])
            writer.writerow(['Juan Pérez', 'juan@example.com', '+34 987654321', '25'])
            writer.writerow(['María López', 'maria@test.com', '654321987', '28'])
            writer.writerow(['Carlos Ruiz', 'carlos.ruiz', '12345', '35'])  # Email inválido para demostrar
    
    # Procesar el archivo
    try:
        resumen = procesar_csv(str(archivo_ejemplo), tiene_cabecera=True)
        mostrar_resumen(resumen)
        
        # Demo de validaciones con regex
        print("\n🔍 Validación de datos de ejemplo:")
        emails = ['ana@example.com', 'carlos.ruiz', 'maria@test.com']
        for email in emails:
            es_valido = validar_correo(email)
            print(f"  Email '{email}': {'✅ Válido' if es_valido else '❌ Inválido'}")
            
    except Exception as e:
        print(f"❌ Error al procesar: {e}")

if __name__ == "__main__":
    main()
Explicación:

Pathlib: Manejo moderno de rutas de archivos

Expresiones Regulares: Validación de emails y teléfonos

Try/Except: Manejo robusto de errores

Estructuras de datos: Listas y diccionarios para organizar información

🖱️ MÓDULO 2: AUTOMATIZACIÓN DE MOUSE Y TECLADO
Mini Proyecto: Reporte Automático en Bloc de Notas
python
# modulo2_reporte_automatico.py
"""
MINI PROYECTO MÓDULO 2
Objetivo: Automatizar apertura de Bloc de Notas, escribir reporte con fecha/hora y guardar
"""

import pyautogui
import time
from datetime import datetime
import os

# Configuración de seguridad
pyautogui.PAUSE = 0.3
pyautogui.FAILSAFE = True

class RobotBlocNotas:
    """Robot para automatizar Bloc de Notas"""
    
    def __init__(self):
        self.titulo_ventana = "Bloc de notas"
        self.reportes_dir = "data/reportes"
        
        # Crear directorio de reportes si no existe
        os.makedirs(self.reportes_dir, exist_ok=True)
    
    def abrir_bloc_notas(self):
        """Abre Bloc de Notas usando teclado"""
        print("📝 Abriendo Bloc de Notas...")
        pyautogui.hotkey('win', 'r')
        time.sleep(0.3)
        pyautogui.write('notepad')
        pyautogui.press('enter')
        time.sleep(1)
    
    def escribir_reporte(self):
        """Escribe el reporte con formato"""
        ahora = datetime.now()
        
        # Datos del reporte usando f-strings
        reporte = f"""
╔══════════════════════════════════════════════════════════╗
║                    REPORTE AUTOMATIZADO                   ║
╠══════════════════════════════════════════════════════════╣
║ Fecha: {ahora.strftime('%d/%m/%Y')}                                      
║ Hora:  {ahora.strftime('%H:%M:%S')}                                      
║ Usuario: {os.getlogin()}                                    
╠══════════════════════════════════════════════════════════╣
║ METRICS                                                  ║
║   • Archivos procesados: 42                             ║
║   • Registros exitosos: 40                              ║
║   • Errores: 2                                           ║
║   • Tasa éxito: 95.2%                                   ║
╠══════════════════════════════════════════════════════════╣
║ MEMORIA DEL SISTEMA                                      ║
║   • Script: modulo2_reporte_automatico.py               ║
║   • Duración: {time.strftime('%H:%M:%S', time.gmtime(time.time()))}                                      
╚══════════════════════════════════════════════════════════╝
"""
        print("✍️ Escribiendo reporte...")
        pyautogui.write(reporte)
    
    def guardar_reporte(self):
        """Guarda el reporte con nombre automático"""
        print("💾 Guardando reporte...")
        pyautogui.hotkey('ctrl', 's')
        time.sleep(1)
        
        # Nombre del archivo con timestamp
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        nombre_archivo = f"reporte_{timestamp}.txt"
        ruta_completa = os.path.join(self.reportes_dir, nombre_archivo)
        
        # Escribir la ruta completa
        pyautogui.write(ruta_completa)
        time.sleep(0.5)
        pyautogui.press('enter')
        time.sleep(0.5)
        
        return ruta_completa
    
    def cerrar_bloc_notas(self):
        """Cierra Bloc de Notas"""
        print("🔒 Cerrando Bloc de Notas...")
        pyautogui.hotkey('alt', 'f4')
        time.sleep(0.3)
        pyautogui.press('n')  # No guardar si pregunta
    
    def ejecutar(self):
        """Ejecuta el flujo completo del robot"""
        try:
            print("🚀 Iniciando Robot RPA...")
            
            self.abrir_bloc_notas()
            self.escribir_reporte()
            ruta = self.guardar_reporte()
            self.cerrar_bloc_notas()
            
            print(f"✅ Reporte guardado exitosamente en: {ruta}")
            
            # Mostrar alerta de éxito
            pyautogui.alert(text=f'Reporte generado correctamente\nUbicación: {ruta}',
                          title='Robot RPA - Éxito',
                          button='OK')
            
        except pyautogui.FailSafeException:
            print("🛑 Robot detenido por FailSafe (mouse en esquina)")
        except Exception as e:
            print(f"❌ Error inesperado: {e}")
            pyautogui.alert(text=f'Error en la ejecución:\n{str(e)}',
                          title='Robot RPA - Error',
                          button='OK')

# Demo de funciones básicas de PyAutoGUI
def demo_pyautogui():
    """Demostración de funciones básicas de PyAutoGUI"""
    print("\n🎮 DEMO DE PYAUTOGUI")
    print("="*40)
    
    # Obtener resolución de pantalla
    ancho, alto = pyautogui.size()
    print(f"📺 Resolución: {ancho}x{alto}")
    
    # Obtener posición actual del mouse
    x, y = pyautogui.position()
    print(f"🖱️ Posición mouse: ({x}, {y})")
    
    # Mover mouse (comentado para no interferir)
    # pyautogui.moveTo(100, 100, duration=0.5)
    # pyautogui.moveRel(50, 0, duration=0.5)
    
    print("\n💡 Consejos:")
    print("   • pyautogui.PAUSE = 0.5 # Pausa entre acciones")
    print("   • pyautogui.FAILSAFE = True # Mouse a esquina para detener")
    print("   • pyautogui.position() # Obtener coordenadas")
    print("   • pyautogui.click() # Hacer clic")

if __name__ == "__main__":
    # Ejecutar demo primero
    demo_pyautogui()
    
    # Preguntar si desea ejecutar el robot
    respuesta = pyautogui.confirm(text='¿Desea ejecutar el robot de Bloc de Notas?',
                                   title='Robot RPA',
                                   buttons=['Sí', 'No'])
    
    if respuesta == 'Sí':
        robot = RobotBlocNotas()
        robot.ejecutar()
Explicación:

Clase RobotBlocNotas: Encapsula toda la funcionalidad

Configuración PAUSE y FAILSAFE: Seguridad para detener el robot

Timestamp: Nombres de archivo únicos para no sobrescribir

🌐 MÓDULO 4: AUTOMATIZACIÓN WEB CON SELENIUM
Mini Proyecto: Login y extracción de datos de dashboard
python
# modulo4_selenium_automation.py
"""
MINI PROYECTO MÓDULO 4 - SELENIUM
Objetivo: Automatizar login, extraer datos de dashboard y guardar en CSV
"""

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException, NoSuchElementException
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service
import csv
import time
from datetime import datetime
import os

class RobotWebAutomation:
    """Robot para automatización web con Selenium"""
    
    def __init__(self, headless=False):
        """Inicializa el driver de Selenium"""
        options = webdriver.ChromeOptions()
        
        if headless:
            options.add_argument('--headless')  # Sin interfaz gráfica
            options.add_argument('--no-sandbox')
            options.add_argument('--disable-dev-shm-usage')
        
        # Bloquear notificaciones
        options.add_argument('--disable-notifications')
        
        # Ventana maximizada
        options.add_argument('--start-maximized')
        
        # Configurar el driver
        service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=service, options=options)
        
        # 4.4 Espera implícita global
        self.driver.implicitly_wait(10)
        self.wait = WebDriverWait(self.driver, 15)
        
        self.datos_extraidos = []
    
    def navegar_a_sitio(self, url):
        """Navega a la URL especificada"""
        print(f"🌐 Navegando a: {url}")
        self.driver.get(url)
        time.sleep(2)
    
    def login(self, username, password, username_selector, password_selector, 
              submit_selector, submit_by=By.CSS_SELECTOR):
        """
        Realiza login en el sitio web
        
        Args:
            username: Nombre de usuario
            password: Contraseña
            username_selector: Selector del campo usuario
            password_selector: Selector del campo contraseña
            submit_selector: Selector del botón submit
            submit_by: Tipo de selector (By.ID, By.CSS_SELECTOR, etc)
        """
        try:
            print("🔐 Realizando login...")
            
            # Esperar y llenar campo usuario
            campo_user = self.wait.until(
                EC.presence_of_element_located((submit_by, username_selector))
            )
            campo_user.clear()
            campo_user.send_keys(username)
            
            # Campo contraseña
            campo_pass = self.driver.find_element(submit_by, password_selector)
            campo_pass.clear()
            campo_pass.send_keys(password)
            
            # Botón submit
            boton_submit = self.driver.find_element(submit_by, submit_selector)
            boton_submit.click()
            
            # Esperar a que cargue después del login
            time.sleep(3)
            print("✅ Login exitoso")
            return True
            
        except TimeoutException:
            print("❌ Timeout: Elementos de login no encontrados")
            return False
        except Exception as e:
            print(f"❌ Error en login: {e}")
            return False
    
    def extraer_datos_tabla(self, tabla_selector, header_fila=True):
        """
        Extrae datos de una tabla HTML
        
        Args:
            tabla_selector: Selector CSS de la tabla
            header_fila: Si la primera fila es encabezado
        """
        try:
            print("📊 Extrayendo datos de tabla...")
            
            # Esperar a que la tabla esté presente
            tabla = self.wait.until(
                EC.presence_of_element_located((By.CSS_SELECTOR, tabla_selector))
            )
            
            # Encontrar todas las filas
            filas = tabla.find_elements(By.TAG_NAME, "tr")
            
            datos = []
            for i, fila in enumerate(filas):
                celdas = fila.find_elements(By.TAG_NAME, "td")
                if not celdas and header_fila and i == 0:
                    # Es la fila de encabezado
                    celdas = fila.find_elements(By.TAG_NAME, "th")
                
                fila_datos = [celda.text.strip() for celda in celdas if celda.text.strip()]
                if fila_datos:
                    datos.append(fila_datos)
            
            print(f"✅ Extraídas {len(datos)} filas")
            return datos
            
        except TimeoutException:
            print("❌ Tabla no encontrada")
            return []
        except Exception as e:
            print(f"❌ Error extrayendo datos: {e}")
            return []
    
    def extraer_datos_por_selector(self, selectores_dict):
        """
        Extrae datos usando selectores CSS específicos
        
        Args:
            selectores_dict: Diccionario {nombre: selector_css}
        """
        datos = {}
        
        for nombre, selector in selectores_dict.items():
            try:
                # 4.5 Extracción de datos
                elemento = self.driver.find_element(By.CSS_SELECTOR, selector)
                datos[nombre] = elemento.text.strip()
            except NoSuchElementException:
                datos[nombre] = "No encontrado"
                print(f"⚠️ Selector no encontrado: {nombre} - {selector}")
        
        return datos
    
    def interactuar_con_formulario(self, campos_dict, boton_selector=None):
        """
        Interactúa con un formulario web
        
        Args:
            campos_dict: Diccionario {selector_css: valor}
            boton_selector: Selector del botón submit
        """
        try:
            for selector, valor in campos_dict.items():
                campo = self.wait.until(
                    EC.element_to_be_clickable((By.CSS_SELECTOR, selector))
                )
                campo.clear()
                campo.send_keys(valor)
                print(f"✏️ Campo '{selector}': {valor}")
            
            if boton_selector:
                boton = self.driver.find_element(By.CSS_SELECTOR, boton_selector)
                boton.click()
                print("✅ Formulario enviado")
                time.sleep(2)
                
        except Exception as e:
            print(f"❌ Error en formulario: {e}")
    
    def guardar_en_csv(self, datos, nombre_archivo):
        """Guarda los datos extraídos en un archivo CSV"""
        os.makedirs("data", exist_ok=True)
        ruta_completa = os.path.join("data", nombre_archivo)
        
        try:
            with open(ruta_completa, 'w', newline='', encoding='utf-8') as csvfile:
                if datos and isinstance(datos[0], list):
                    # Datos en formato tabla
                    writer = csv.writer(csvfile)
                    writer.writerows(datos)
                elif datos and isinstance(datos[0], dict):
                    # Datos en formato diccionario
                    writer = csv.DictWriter(csvfile, fieldnames=datos[0].keys())
                    writer.writeheader()
                    writer.writerows(datos)
                else:
                    writer = csv.writer(csvfile)
                    writer.writerow(datos)
            
            print(f"💾 Datos guardados en: {ruta_completa}")
            return ruta_completa
            
        except Exception as e:
            print(f"❌ Error guardando CSV: {e}")
            return None
    
    def manejar_pestanas(self, abrir_enlace_selector=None):
        """Maneja múltiples pestañas del navegador"""
        ventana_principal = self.driver.current_window_handle
        
        if abrir_enlace_selector:
            # Abrir enlace en nueva pestaña
            enlace = self.driver.find_element(By.CSS_SELECTOR, abrir_enlace_selector)
            self.driver.execute_script("window.open(arguments[0]);", enlace.get_attribute("href"))
        
        # Obtener todas las pestañas
        todas_pestanas = self.driver.window_handles
        
        print(f"📑 Número de pestañas: {len(todas_pestanas)}")
        
        # Cambiar a la última pestaña
        self.driver.switch_to.window(todas_pestanas[-1])
        print(f"🔄 Cambiado a nueva pestaña: {self.driver.title}")
        
        return ventana_principal
    
    def tomar_screenshot(self, nombre="screenshot"):
        """Toma captura de pantalla"""
        os.makedirs("screenshots", exist_ok=True)
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        nombre_archivo = f"screenshots/{nombre}_{timestamp}.png"
        
        self.driver.save_screenshot(nombre_archivo)
        print(f"📸 Screenshot guardado: {nombre_archivo}")
        return nombre_archivo
    
    def cerrar(self):
        """Cierra el navegador"""
        print("🔒 Cerrando navegador...")
        self.driver.quit()

# Sitio de demostración para pruebas
def demo_sitio_automation():
    """Ejemplo con sitio de pruebas público"""
    robot = RobotWebAutomation(headless=False)
    
    try:
        # Usar sitio de pruebas público
        robot.navegar_a_sitio("https://the-internet.herokuapp.com/login")
        
        # Login de demostración
        robot.login(
            username="tomsmith",
            password="SuperSecretPassword!",
            username_selector="#username",
            password_selector="#password",
            submit_selector="button[type='submit']",
            submit_by=By.CSS_SELECTOR
        )
        
        # Verificar login exitoso
        mensaje_exito = robot.driver.find_element(By.CSS_SELECTOR, ".flash.success")
        print(f"✅ Mensaje: {mensaje_exito.text}")
        
        # Tomar screenshot
        robot.tomar_screenshot("login_exitoso")
        
        # Extraer información de la página
        datos = robot.extraer_datos_por_selector({
            'mensaje_flash': ".flash",
            'contenido': "#content",
            'footer': "#page-footer"
        })
        
        print("\n📋 Datos extraídos:")
        for clave, valor in datos.items():
            print(f"  {clave}: {valor[:100]}..." if len(valor) > 100 else f"  {clave}: {valor}")
        
        # Guardar en CSV
        robot.guardar_en_csv([datos], "datos_dashboard.csv")
        
    except Exception as e:
        print(f"❌ Error en demo: {e}")
    finally:
        robot.cerrar()

def demo_extract_table():
    """Demo de extracción de tabla"""
    robot = RobotWebAutomation(headless=True)
    
    try:
        # Sitio con tabla de ejemplo
        robot.navegar_a_sitio("https://the-internet.herokuapp.com/tables")
        
        # Extraer tabla #1
        datos_tabla = robot.extraer_datos_tabla("#table1")
        
        if datos_tabla:
            print("\n📊 Tabla extraída:")
            for i, fila in enumerate(datos_tabla[:5]):  # Mostrar primeras 5 filas
                print(f"  Fila {i}: {fila}")
            
            robot.guardar_en_csv(datos_tabla, "tabla_extraida.csv")
        
    except Exception as e:
        print(f"❌ Error: {e}")
    finally:
        robot.cerrar()

if __name__ == "__main__":
    print("🚀 INICIANDO DEMOS DE SELENIUM")
    print("="*50)
    
    print("\n1️⃣ Demo: Login y extracción de datos")
    demo_sitio_automation()
    
    print("\n2️⃣ Demo: Extracción de tablas")
    demo_extract_table()
Explicación:

WebDriver Manager: No necesita descargar drivers manualmente

Esperas explícitas e implícitas: Robustez ante cargas lentas

Modo headless: Para ejecución en servidores sin GUI

Manejo de múltiples selectores: ID, CSS, XPath

📊 MÓDULO 5: AUTOMATIZACIÓN DE EXCEL Y PDFS
Mini Proyecto: Procesador de Ventas
python
# modulo5_excel_pdf.py
"""
MINI PROYECTO MÓDULO 5 - EXCEL Y PDF
Objetivo: Leer Excel, calcular totales, generar resumen y exportar a PDF
"""

import openpyxl
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side
from openpyxl.chart import BarChart, Reference
import pandas as pd
import PyPDF2
import pdfplumber
from pathlib import Path
import os
from datetime import datetime

class ProcesadorExcel:
    """Procesador avanzado de archivos Excel"""
    
    def __init__(self):
        self.wb = None
        self.hoja = None
    
    def crear_excel_ejemplo(self, ruta_salida="data/ventas_ejemplo.xlsx"):
        """Crea un archivo Excel de ejemplo con datos de ventas"""
        os.makedirs("data", exist_ok=True)
        
        # Datos de ejemplo
        datos = {
            'Fecha': ['2026-01-15', '2026-01-16', '2026-01-17', '2026-01-18', '2026-01-19'],
            'Vendedor': ['Ana García', 'Juan Pérez', 'Ana García', 'María López', 'Juan Pérez'],
            'Producto': ['Laptop', 'Mouse', 'Monitor', 'Teclado', 'Laptop'],
            'Cantidad': [2, 5, 1, 3, 1],
            'Precio_Unitario': [850.00, 25.50, 320.00, 45.00, 850.00]
        }
        
        df = pd.DataFrame(datos)
        df['Total'] = df['Cantidad'] * df['Precio_Unitario']
        
        # Guardar con pandas
        df.to_excel(ruta_salida, index=False)
        print(f"📊 Excel de ejemplo creado: {ruta_salida}")
        return ruta_salida
    
    def cargar_excel(self, ruta_archivo):
        """Carga un archivo Excel con openpyxl"""
        self.wb = openpyxl.load_workbook(ruta_archivo)
        self.hoja = self.wb.active
        print(f"📁 Excel cargado: {ruta_archivo}")
        return self.wb
    
    def leer_datos_pandas(self, ruta_archivo):
        """Lee Excel usando pandas para análisis"""
        df = pd.read_excel(ruta_archivo)
        print(f"📊 Datos cargados: {df.shape[0]} filas, {df.shape[1]} columnas")
        return df
    
    def calcular_resumen_ventas(self, df):
        """Calcula resumen de ventas por vendedor"""
        resumen = df.groupby('Vendedor').agg({
            'Total': 'sum',
            'Cantidad': 'sum'
        }).round(2)
        
        resumen['Ventas_promedio'] = (resumen['Total'] / resumen['Cantidad']).round(2)
        resumen = resumen.sort_values('Total', ascending=False)
        
        print("\n📈 RESUMEN POR VENDEDOR:")
        print(resumen)
        
        return resumen
    
    def formatear_excel(self, titulo="Reporte de Ventas"):
        """Aplica formato profesional al Excel"""
        
        # Definir estilos
        header_font = Font(bold=True, color="FFFFFF", size=11)
        header_fill = PatternFill(start_color="2E75B6", end_color="2E75B6", fill_type="solid")
        center_alignment = Alignment(horizontal="center", vertical="center")
        thin_border = Border(
            left=Side(style='thin'),
            right=Side(style='thin'),
            top=Side(style='thin'),
            bottom=Side(style='thin')
        )
        
        # Formatear cabeceras
        for col in self.hoja.columns:
            celda = col[0]  # Primera fila
            celda.font = header_font
            celda.fill = header_fill
            celda.alignment = center_alignment
            celda.border = thin_border
        
        # Ajustar ancho de columnas
        for col in self.hoja.columns:
            max_length = 0
            col_letter = openpyxl.utils.get_column_letter(col[0].column)
            for celda in col:
                try:
                    if len(str(celda.value)) > max_length:
                        max_length = len(str(celda.value))
                except:
                    pass
            adjusted_width = min(max_length + 2, 30)
            self.hoja.column_dimensions[col_letter].width = adjusted_width
        
        # Agregar título
        self.hoja.insert_rows(1)
        celda_titulo = self.hoja['A1']
        celda_titulo.value = titulo
        celda_titulo.font = Font(bold=True, size=14)
        celda_titulo.alignment = center_alignment
        
        # Agregar fecha de generación
        self.hoja['A2'] = f"Generado: {datetime.now().strftime('%d/%m/%Y %H:%M')}"
        
        print("✅ Formato aplicado al Excel")
    
    def crear_grafico(self, ruta_salida):
        """Crea un gráfico de barras en el Excel"""
        try:
            # Crear gráfico de barras
            chart = BarChart()
            chart.title = "Ventas por Vendedor"
            chart.style = 10
            chart.x_axis.title = "Vendedor"
            chart.y_axis.title = "Total Ventas"
            
            # Encontrar datos para gráfico
            # Asume que los datos están organizados con Vendedor y Total
            data = Reference(self.hoja, min_col=2, min_row=3, max_row=self.hoja.max_row, max_col=2)
            categories = Reference(self.hoja, min_col=1, min_row=3, max_row=self.hoja.max_row)
            
            chart.add_data(data, titles_from_data=True)
            chart.set_categories(categories)
            
            # Posicionar gráfico
            self.hoja.add_chart(chart, "E3")
            print("📊 Gráfico agregado al Excel")
            
        except Exception as e:
            print(f"⚠️ No se pudo crear gráfico: {e}")
    
    def guardar_excel(self, ruta_salida):
        """Guarda el archivo Excel formateado"""
        self.wb.save(ruta_salida)
        print(f"💾 Excel guardado: {ruta_salida}")

class ProcesadorPDF:
    """Procesador de archivos PDF"""
    
    def extraer_texto_pypdf2(self, ruta_pdf):
        """Extrae texto usando PyPDF2"""
        texto_completo = []
        
        with open(ruta_pdf, 'rb') as archivo:
            lector = PyPDF2.PdfReader(archivo)
            num_paginas = len(lector.pages)
            
            print(f"📄 PDF con {num_paginas} páginas")
            
            for i, pagina in enumerate(lector.pages, 1):
                texto = pagina.extract_text()
                if texto.strip():
                    texto_completo.append(f"--- Página {i} ---\n{texto}")
        
        return "\n".join(texto_completo)
    
    def extraer_tablas_pdfplumber(self, ruta_pdf):
        """Extrae tablas usando pdfplumber (mejor para PDFs complejos)"""
        tablas = []
        
        with pdfplumber.open(ruta_pdf) as pdf:
            for i, pagina in enumerate(pdf.pages, 1):
                tablas_pagina = pagina.extract_tables()
                if tablas_pagina:
                    for tabla in tablas_pagina:
                        if tabla and len(tabla) > 0:
                            # Limpiar tabla (eliminar None y vacíos)
                            tabla_limpia = [
                                [celda if celda else "" for celda in fila]
                                for fila in tabla if any(fila)
                            ]
                            if tabla_limpia:
                                tablas.append({
                                    'pagina': i,
                                    'datos': tabla_limpia
                                })
                                print(f"📊 Tabla encontrada en página {i}")
        
        return tablas
    
    def extraer_datos_factura(self, ruta_pdf):
        """Extrae información específica de una factura"""
        datos_factura = {
            'numero_factura': None,
            'fecha': None,
            'proveedor': None,
            'monto_total': None
        }
        
        texto = self.extraer_texto_pypdf2(ruta_pdf)
        
        # Buscar patrones comunes en facturas
        import re
        
        # Número de factura (ej: INV-2026-001)
        patron_numero = r'(?:FACTURA|INVOICE|N[°º]?)\s*[:\-]?\s*([A-Z0-9\-]+)'
        match = re.search(patron_numero, texto, re.IGNORECASE)
        if match:
            datos_factura['numero_factura'] = match.group(1)
        
        # Fecha (ej: 15/01/2026)
        patron_fecha = r'\b(\d{1,2}[/-]\d{1,2}[/-]\d{2,4})\b'
        fechas = re.findall(patron_fecha, texto)
        if fechas:
            datos_factura['fecha'] = fechas[0]
        
        # Monto total (ej: $1,234.56)
        patron_monto = r'(?:TOTAL|SUMA)\s*[:]?\s*[\$]?\s*([\d,]+\.?\d*)'
        match = re.search(patron_monto, texto, re.IGNORECASE)
        if match:
            monto_str = match.group(1).replace(',', '')
            try:
                datos_factura['monto_total'] = float(monto_str)
            except:
                pass
        
        return datos_factura

def generar_reporte_pdf_desde_excel(ruta_excel, ruta_salida_pdf):
    """Genera un reporte PDF a partir de datos de Excel usando pandas + reportlab"""
    try:
        from reportlab.lib.pagesizes import letter
        from reportlab.platypus import SimpleDocTemplate, Table, TableStyle, Paragraph, Spacer
        from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
        from reportlab.lib import colors
        
        # Leer datos
        df = pd.read_excel(ruta_excel)
        
        # Crear PDF
        doc = SimpleDocTemplate(ruta_salida_pdf, pagesize=letter)
        elementos = []
        estilos = getSampleStyleSheet()
        
        # Título
        titulo = Paragraph("Reporte de Ventas", estilos['Title'])
        elementos.append(titulo)
        elementos.append(Spacer(1, 12))
        
        # Fecha
        fecha = Paragraph(f"Generado: {datetime.now().strftime('%d/%m/%Y %H:%M')}", estilos['Normal'])
        elementos.append(fecha)
        elementos.append(Spacer(1, 12))
        
        # Tabla de datos
        tabla_datos = [df.columns.tolist()] + df.values.tolist()
        tabla = Table(tabla_datos)
        tabla.setStyle(TableStyle([
            ('BACKGROUND', (0, 0), (-1, 0), colors.grey),
            ('TEXTCOLOR', (0, 0), (-1, 0), colors.whitesmoke),
            ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
            ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
            ('FONTSIZE', (0, 0), (-1, 0), 10),
            ('BOTTOMPADDING', (0, 0), (-1, 0), 12),
            ('BACKGROUND', (0, 1), (-1, -1), colors.beige),
            ('GRID', (0, 0), (-1, -1), 1, colors.black)
        ]))
        elementos.append(tabla)
        
        # Generar PDF
        doc.build(elementos)
        print(f"📄 PDF generado: {ruta_salida_pdf}")
        
    except ImportError:
        print("⚠️ ReportLab no instalado. Instale con: pip install reportlab")
        print("   Generando PDF alternativo...")
        crear_pdf_simple(ruta_excel, ruta_salida_pdf)

def crear_pdf_simple(ruta_excel, ruta_salida_pdf):
    """Crea un PDF simple como alternativa"""
    import subprocess
    
    # Usar pandas para crear HTML y luego convertir (requiere wkhtmltopdf o similar)
    df = pd.read_excel(ruta_excel)
    html = f"""
    <html>
    <head><title>Reporte de Ventas</title></head>
    <body>
    <h1 style="color: #2E75B6;">Reporte de Ventas</h1>
    <p>Generado: {datetime.now().strftime('%d/%m/%Y %H:%M')}</p>
    {df.to_html()}
    </body>
    </html>
    """
    
    html_path = ruta_salida_pdf.replace('.pdf', '.html')
    with open(html_path, 'w', encoding='utf-8') as f:
        f.write(html)
    
    print(f"📄 Reporte HTML generado: {html_path}")
    print("💡 Use un navegador para imprimir a PDF")

def main():
    """Función principal del módulo"""
    print("🚀 PROCESADOR DE EXCEL Y PDF")
    print("="*50)
    
    # 1. Crear y procesar Excel
    procesador_excel = ProcesadorExcel()
    
    # Crear Excel de ejemplo
    ruta_excel = procesador_excel.crear_excel_ejemplo()
    
    # Leer con pandas
    df = procesador_excel.leer_datos_pandas(ruta_excel)
    
    # Calcular resumen
    resumen = procesador_excel.calcular_resumen_ventas(df)
    
    # Guardar resumen en nuevo Excel
    resumen.to_excel("data/resumen_ventas.xlsx")
    print("\n✅ Resumen guardado en: data/resumen_ventas.xlsx")
    
    # 2. Procesar PDF de ejemplo
    print("\n" + "="*50)
    print("PROCESAMIENTO DE PDF")
    print("="*50)
    
    # Crear un PDF de ejemplo simple
    import subprocess
    pdf_ejemplo = "data/factura_ejemplo.pdf"
    
    if not os.path.exists(pdf_ejemplo):
        print("📝 Creando PDF de ejemplo con texto...")
        with open("data/factura_temp.txt", "w") as f:
            f.write("""FACTURA INV-2026-001
Fecha: 15/01/2026
Proveedor: TechSupplies S.A.
Total: $1,250.00

Detalles:
- Laptop Dell: $850.00
- Mouse Logitech: $25.00
""")
        # Nota: Para crear PDF real, se necesitaría librería adicional
        print("   (PDF de demostración - texto plano)")
    
    # Extraer datos de factura ficticia
    datos_factura = {
        'numero_factura': 'INV-2026-001',
        'fecha': '15/01/2026',
        'proveedor': 'TechSupplies S.A.',
        'monto_total': 1250.00
    }
    
    print("\n📋 Datos extraídos de factura:")
    for clave, valor in datos_factura.items():
        print(f"  {clave}: {valor}")
    
    print("\n✅ Módulo 5 completado exitosamente")

if __name__ == "__main__":
    main()
Explicación:

OpenPyXL: Formato profesional, gráficos, ajuste de columnas

Pandas: Análisis de datos y agregaciones

PyPDF2/pdfplumber: Extracción de texto y tablas de PDFs

ReportLab: Generación de PDFs desde cero

📧 MÓDULO 6: CORREOS E INTEGRACIÓN CON APIS
Mini Proyecto: Consulta de Clima y Envío por Correo
python
# modulo6_correos_api.py
"""
MINI PROYECTO MÓDULO 6 - CORREOS Y APIS
Objetivo: Consultar clima vía API, guardar en Excel y enviar por correo
"""

import smtplib
import imaplib
import email
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email import encoders
import requests
import pandas as pd
import os
from datetime import datetime
import json
from dotenv import load_dotenv
import time

# Cargar variables de entorno
load_dotenv("config/config.env")

class ClimaAPI:
    """Cliente para APIs de clima"""
    
    # URLs de APIs gratuitas
    OPENWEATHER_URL = "https://api.openweathermap.org/data/2.5/weather"
    
    def __init__(self, api_key=None):
        self.api_key = api_key or os.getenv("OPENWEATHER_API_KEY", "demo_key")
        self.resultados = []
    
    def consultar_clima(self, ciudad, pais="MX", unidades="metric"):
        """
        Consulta el clima de una ciudad
        
        Args:
            ciudad: Nombre de la ciudad
            pais: Código de país (2 letras)
            unidades: metric (Celsius) o imperial (Fahrenheit)
        """
        try:
            params = {
                'q': f"{ciudad},{pais}",
                'appid': self.api_key,
                'units': unidades,
                'lang': 'es'
            }
            
            print(f"🌤️ Consultando clima para {ciudad}...")
            
            # Para demo sin API key, usar datos simulados
            if self.api_key == "demo_key":
                return self._simular_clima(ciudad)
            
            response = requests.get(self.OPENWEATHER_URL, params=params, timeout=10)
            
            if response.status_code == 200:
                datos = response.json()
                return self._procesar_respuesta(datos, ciudad)
            else:
                print(f"❌ Error {response.status_code}: {response.text}")
                return self._simular_clima(ciudad)
                
        except requests.exceptions.RequestException as e:
            print(f"❌ Error de conexión: {e}")
            return self._simular_clima(ciudad)
    
    def _procesar_respuesta(self, datos, ciudad):
        """Procesa la respuesta de la API de OpenWeather"""
        clima_info = {
            'ciudad': ciudad,
            'fecha_hora': datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            'temperatura': datos['main']['temp'],
            'sensacion_termica': datos['main']['feels_like'],
            'humedad': datos['main']['humidity'],
            'presion': datos['main']['pressure'],
            'descripcion': datos['weather'][0]['description'],
            'viento_velocidad': datos['wind']['speed'],
            'nubosidad': datos['clouds']['all']
        }
        return clima_info
    
    def _simular_clima(self, ciudad):
        """Simula datos de clima para demostración sin API key"""
        import random
        
        # Datos simulados realistas
        datos = {
            'CDMX': {'temp': 22, 'humedad': 45, 'desc': 'cielo despejado'},
            'Monterrey': {'temp': 28, 'humedad': 55, 'desc': 'soleado'},
            'Guadalajara': {'temp': 24, 'humedad': 50, 'desc': 'parcialmente nublado'},
            'Cancún': {'temp': 30, 'humedad': 75, 'desc': 'nubes dispersas'},
        }
        
        default = {'temp': 25, 'humedad': 60, 'desc': 'cielo despejado'}
        info = datos.get(ciudad, default)
        
        clima_info = {
            'ciudad': ciudad,
            'fecha_hora': datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            'temperatura': info['temp'] + random.uniform(-2, 2),
            'sensacion_termica': info['temp'] + random.uniform(-1, 3),
            'humedad': info['humedad'] + random.uniform(-10, 10),
            'presion': 1013 + random.uniform(-10, 10),
            'descripcion': info['desc'],
            'viento_velocidad': random.uniform(0, 15),
            'nubosidad': random.randint(0, 100)
        }
        return clima_info
    
    def consultar_multiples_ciudades(self, ciudades):
        """Consulta el clima para múltiples ciudades"""
        for ciudad in ciudades:
            clima = self.consultar_clima(ciudad)
            self.resultados.append(clima)
            time.sleep(0.5)  # Pequeña pausa entre consultas
        
        return self.resultados
    
    def guardar_excel(self, ruta_salida="data/reporte_clima.xlsx"):
        """Guarda los resultados en un archivo Excel"""
        if not self.resultados:
            print("⚠️ No hay datos para guardar")
            return None
        
        df = pd.DataFrame(self.resultados)
        
        # Formatear números
        for col in ['temperatura', 'sensacion_termica', 'viento_velocidad']:
            if col in df.columns:
                df[col] = df[col].round(1)
        
        os.makedirs("data", exist_ok=True)
        df.to_excel(ruta_salida, index=False)
        print(f"📊 Datos guardados en: {ruta_salida}")
        
        return ruta_salida

class CorreoSMTP:
    """Cliente para envío de correos SMTP"""
    
    def __init__(self, servidor="smtp.gmail.com", puerto=587):
        self.servidor = servidor
        self.puerto = puerto
        self.correo_origen = os.getenv("CORREO_ORIGEN", "")
        self.password = os.getenv("CORREO_PASSWORD", "")
    
    def enviar_correo(self, destino, asunto, cuerpo_html, adjuntos=None):
        """
        Envía un correo electrónico
        
        Args:
            destino: Correo destino (string o lista)
            asunto: Asunto del correo
            cuerpo_html: Cuerpo del mensaje en HTML
            adjuntos: Lista de rutas de archivos a adjuntar
        """
        if not self.correo_origen or not self.password:
            print("⚠️ Credenciales no configuradas. Guardando correo localmente...")
            return self._guardar_localmente(destino, asunto, cuerpo_html, adjuntos)
        
        try:
            # Crear mensaje
            msg = MIMEMultipart()
            msg['From'] = self.correo_origen
            msg['To'] = ', '.join(destino) if isinstance(destino, list) else destino
            msg['Subject'] = asunto
            
            # Adjuntar cuerpo HTML
            msg.attach(MIMEText(cuerpo_html, 'html'))
            
            # Adjuntar archivos
            if adjuntos:
                for ruta_adjunto in adjuntos:
                    if os.path.exists(ruta_adjunto):
                        with open(ruta_adjunto, 'rb') as adjunto:
                            parte = MIMEBase('application', 'octet-stream')
                            parte.set_payload(adjunto.read())
                            encoders.encode_base64(parte)
                            parte.add_header(
                                'Content-Disposition',
                                f'attachment; filename={os.path.basename(ruta_adjunto)}'
                            )
                            msg.attach(parte)
                            print(f"📎 Adjuntado: {os.path.basename(ruta_adjunto)}")
            
            # Enviar
            with smtplib.SMTP(self.servidor, self.puerto) as servidor_smtp:
                servidor_smtp.starttls()
                servidor_smtp.login(self.correo_origen, self.password)
                servidor_smtp.send_message(msg)
            
            print(f"✅ Correo enviado a: {destino}")
            return True
            
        except Exception as e:
            print(f"❌ Error enviando correo: {e}")
            return self._guardar_localmente(destino, asunto, cuerpo_html, adjuntos)
    
    def _guardar_localmente(self, destino, asunto, cuerpo_html, adjuntos):
        """Guarda el correo localmente cuando no hay credenciales"""
        os.makedirs("data/correos_enviados", exist_ok=True)
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        ruta = f"data/correos_enviados/correo_{timestamp}.html"
        
        with open(ruta, 'w', encoding='utf-8') as f:
            f.write(f"<h2>{asunto}</h2>")
            f.write(f"<p><strong>Destinatario:</strong> {destino}</p>")
            f.write(f"<p><strong>Fecha:</strong> {datetime.now()}</p>")
            f.write("<hr>")
            f.write(cuerpo_html)
        
        print(f"📧 Correo guardado localmente en: {ruta}")
        return True

class LectorCorreoIMAP:
    """Cliente para lectura de correos IMAP"""
    
    def __init__(self, servidor="imap.gmail.com"):
        self.servidor = servidor
        self.correo = os.getenv("CORREO_ORIGEN", "")
        self.password = os.getenv("CORREO_PASSWORD", "")
        self.connection = None
    
    def conectar(self):
        """Conecta al servidor IMAP"""
        if not self.correo or not self.password:
            print("⚠️ Credenciales no configuradas")
            return False
        
        try:
            self.connection = imaplib.IMAP4_SSL(self.servidor)
            self.connection.login(self.correo, self.password)
            self.connection.select('INBOX')
            print("✅ Conectado a correo IMAP")
            return True
        except Exception as e:
            print(f"❌ Error conectando: {e}")
            return False
    
    def buscar_correos(self, remitente=None, asunto_contiene=None, desde_fecha=None):
        """Busca correos por criterios"""
        if not self.connection:
            if not self.conectar():
                return []
        
        criterios = []
        if remitente:
            criterios.append(f'(FROM "{remitente}")')
        if asunto_contiene:
            criterios.append(f'(SUBJECT "{asunto_contiene}")')
        
        query = ' '.join(criterios) if criterios else 'ALL'
        
        try:
            _, ids_mensajes = self.connection.search(None, query)
            ids = ids_mensajes[0].split()
            print(f"📧 Encontrados {len(ids)} correos")
            return ids
        except Exception as e:
            print(f"❌ Error buscando correos: {e}")
            return []
    
    def descargar_adjuntos(self, ids_correos, carpeta_destino="data/adjuntos"):
        """Descarga adjuntos de los correos"""
        os.makedirs(carpeta_destino, exist_ok=True)
        adjuntos_descargados = []
        
        for correo_id in ids_correos[:10]:  # Limitar a primeros 10
            try:
                _, msg_data = self.connection.fetch(correo_id, '(RFC822)')
                msg = email.message_from_bytes(msg_data[0][1])
                
                for part in msg.walk():
                    if part.get_content_maintype() == 'multipart':
                        continue
                    if part.get('Content-Disposition') is None:
                        continue
                    
                    filename = part.get_filename()
                    if filename:
                        ruta_archivo = os.path.join(carpeta_destino, filename)
                        with open(ruta_archivo, 'wb') as f:
                            f.write(part.get_payload(decode=True))
                        adjuntos_descargados.append(ruta_archivo)
                        print(f"📎 Descargado: {filename}")
                        
            except Exception as e:
                print(f"❌ Error descargando adjunto: {e}")
        
        return adjuntos_descargados
    
    def cerrar(self):
        """Cierra la conexión"""
        if self.connection:
            self.connection.close()
            self.connection.logout()

# Integración con Webhooks (Discord/Slack/Telegram)
class WebhookNotificador:
    """Envía notificaciones a Discord, Slack o Telegram"""
    
    def __init__(self, webhook_url=None):
        self.webhook_url = webhook_url or os.getenv("DISCORD_WEBHOOK_URL", "")
    
    def enviar_discord(self, mensaje, titulo=None, color=0x00ff00):
        """Envía mensaje a Discord via webhook"""
        if not self.webhook_url:
            print("⚠️ Webhook URL no configurada")
            return False
        
        data = {
            "content": mensaje if not titulo else None,
            "embeds": [{
                "title": titulo,
                "description": mensaje,
                "color": color,
                "timestamp": datetime.now().isoformat()
            }] if titulo else []
        }
        
        try:
            response = requests.post(self.webhook_url, json=data)
            if response.status_code == 204:
                print("📢 Notificación enviada a Discord")
                return True
            else:
                print(f"❌ Error Discord: {response.status_code}")
                return False
        except Exception as e:
            print(f"❌ Error enviando webhook: {e}")
            return False
    
    def enviar_telegram(self, mensaje, bot_token=None, chat_id=None):
        """Envía mensaje a Telegram"""
        token = bot_token or os.getenv("TELEGRAM_BOT_TOKEN", "")
        chat = chat_id or os.getenv("TELEGRAM_CHAT_ID", "")
        
        if not token or not chat:
            print("⚠️ Credenciales de Telegram no configuradas")
            return False
        
        url = f"https://api.telegram.org/bot{token}/sendMessage"
        data = {"chat_id": chat, "text": mensaje, "parse_mode": "HTML"}
        
        try:
            response = requests.post(url, json=data)
            if response.status_code == 200:
                print("📢 Notificación enviada a Telegram")
                return True
            return False
        except Exception as e:
            print(f"❌ Error Telegram: {e}")
            return False

def generar_reporte_clima_html(datos_clima):
    """Genera un reporte HTML con los datos del clima"""
    html = f"""
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <style>
            body {{ font-family: Arial, sans-serif; background-color: #f0f0f0; }}
            .container {{ max-width: 800px; margin: 0 auto; background: white; padding: 20px; border-radius: 10px; }}
            h1 {{ color: #2E75B6; }}
            .clima-card {{ border: 1px solid #ddd; border-radius: 8px; margin: 15px 0; padding: 15px; }}
            .temperatura {{ font-size: 2em; color: #ff6b35; }}
            .detalle {{ margin: 5px 0; }}
            .footer {{ margin-top: 20px; font-size: 0.8em; color: #666; text-align: center; }}
        </style>
    </head>
    <body>
        <div class="container">
            <h1>🌤️ Reporte de Clima</h1>
            <p>Generado: {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}</p>
    """
    
    for ciudad in datos_clima:
        html += f"""
            <div class="clima-card">
                <h2>📍 {ciudad['ciudad']}</h2>
                <div class="temperatura">{ciudad['temperatura']:.1f}°C</div>
                <div class="detalle">🌡️ Sensación térmica: {ciudad['sensacion_termica']:.1f}°C</div>
                <div class="detalle">💧 Humedad: {ciudad['humedad']:.0f}%</div>
                <div class="detalle">🌬️ Viento: {ciudad['viento_velocidad']:.1f} km/h</div>
                <div class="detalle">📝 {ciudad['descripcion'].capitalize()}</div>
            </div>
        """
    
    html += f"""
            <div class="footer">
                <p>Reporte generado automáticamente por RPA 2026</p>
            </div>
        </div>
    </body>
    </html>
    """
    
    return html

def main():
    """Función principal del módulo"""
    print("🚀 SISTEMA DE CLIMA Y CORREOS")
    print("="*50)
    
    # 1. Consultar clima de múltiples ciudades
    ciudades = ["CDMX", "Monterrey", "Guadalajara", "Cancún"]
    
    clima_api = ClimaAPI()
    datos_clima = clima_api.consultar_multiples_ciudades(ciudades)
    
    print("\n📋 Resultados del clima:")
    for clima in datos_clima:
        print(f"  {clima['ciudad']}: {clima['temperatura']:.1f}°C - {clima['descripcion']}")
    
    # 2. Guardar en Excel
    ruta_excel = clima_api.guardar_excel()
    
    # 3. Generar reporte HTML
    reporte_html = generar_reporte_clima_html(datos_clima)
    
    # 4. Enviar por correo
    correo = CorreoSMTP()
    correo.enviar_correo(
        destino=["reportes@ejemplo.com"],
        asunto=f"Reporte de Clima - {datetime.now().strftime('%d/%m/%Y')}",
        cuerpo_html=reporte_html,
        adjuntos=[ruta_excel] if ruta_excel and os.path.exists(ruta_excel) else None
    )
    
    # 5. Demo de webhook (opcional)
    webhook = WebhookNotificador()
    webhook.enviar_discord(
        mensaje=f"Reporte de clima generado para {len(datos_clima)} ciudades",
        titulo="🌤️ Reporte Completo"
    )
    
    print("\n✅ Proceso completado:")
    print(f"  • Ciudades procesadas: {len(datos_clima)}")
    print(f"  • Excel guardado: {ruta_excel}")
    print("  • Correo enviado/guardado")
    
    return datos_clima

if __name__ == "__main__":
    main()
Explicación:

SMTP: Envío de correos con adjuntos y HTML

IMAP: Lectura de correos y descarga de adjuntos

Requests: Consumo de APIs REST

Webhooks: Notificaciones a Discord/Telegram

Variables de entorno: Seguridad para credenciales

🛡️ MÓDULO 7: BUENAS PRÁCTICAS Y ROBUSTEZA
Mini Proyecto: Robot Robusto con Logging y Manejo de Errores
python
# modulo7_buenas_practicas.py
"""
MÓDULO 7 - BUENAS PRÁCTICAS
Objetivo: Convertir scripts en robots robustos con logging y manejo de errores
"""

import logging
from logging.handlers import RotatingFileHandler
import os
from datetime import datetime
from functools import wraps
import time
import random
from dotenv import load_dotenv
from pathlib import Path

# Cargar variables de entorno
load_dotenv("config/config.env")

# ============================================================
# 7.1 SISTEMA DE LOGGING
# ============================================================

def setup_logging(nombre_robot="rpa_robot", nivel=logging.INFO):
    """
    Configura el sistema de logging con archivos rotativos
    
    Args:
        nombre_robot: Nombre del robot para el archivo de log
        nivel: Nivel de logging (DEBUG, INFO, WARNING, ERROR)
    """
    # Crear directorio de logs
    os.makedirs("logs", exist_ok=True)
    
    # Configurar logger principal
    logger = logging.getLogger(nombre_robot)
    logger.setLevel(nivel)
    
    # Formato del log
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(filename)s:%(lineno)d - %(message)s',
        datefmt='%Y-%m-%d %H:%M:%S'
    )
    
    # Handler para archivo con rotación (10MB, 5 backups)
    file_handler = RotatingFileHandler(
        f"logs/{nombre_robot}.log",
        maxBytes=10*1024*1024,  # 10 MB
        backupCount=5,
        encoding='utf-8'
    )
    file_handler.setLevel(nivel)
    file_handler.setFormatter(formatter)
    logger.addHandler(file_handler)
    
    # Handler para consola
    console_handler = logging.StreamHandler()
    console_handler.setLevel(nivel)
    console_handler.setFormatter(formatter)
    logger.addHandler(console_handler)
    
    return logger

# ============================================================
# 7.2 DECORADOR DE REINTENTOS
# ============================================================

def retry(max_retries=3, delay=1, backoff=2, exceptions=(Exception,)):
    """
    Decorador para reintentar funciones que fallen
    
    Args:
        max_retries: Número máximo de reintentos
        delay: Demora inicial entre reintentos (segundos)
        backoff: Factor de aumento de la demora
        exceptions: Tupla de excepciones a capturar
    """
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            logger = logging.getLogger(__name__)
            current_delay = delay
            
            for attempt in range(max_retries + 1):
                try:
                    return func(*args, **kwargs)
                except exceptions as e:
                    if attempt == max_retries:
                        logger.error(f"❌ Falló después de {max_retries} reintentos: {e}")
                        raise
                    
                    logger.warning(f"⚠️ Intento {attempt + 1} falló: {e}. Reintentando en {current_delay}s...")
                    time.sleep(current_delay)
                    current_delay *= backoff
            
            return None
        return wrapper
    return decorator

# ============================================================
# 7.3 MANEJO DE ERRORES AVANZADO
# ============================================================

class RPAError(Exception):
    """Excepción base para errores RPA"""
    pass

class TimeoutError(RPAError):
    """Error de timeout"""
    pass

class ValidationError(RPAError):
    """Error de validación de datos"""
    pass

class FileNotFoundError(RPAError):
    """Error de archivo no encontrado"""
    pass

class RetryableError(RPAError):
    """Error que puede reintentarse"""
    pass

def timeout(seconds):
    """Decorador para timeout en funciones"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            import signal
            
            def handler(signum, frame):
                raise TimeoutError(f"Función {func.__name__} excedió {seconds} segundos")
            
            # Configurar timeout
            signal.signal(signal.SIGALRM, handler)
            signal.alarm(seconds)
            
            try:
                result = func(*args, **kwargs)
            finally:
                signal.alarm(0)
            
            return result
        return wrapper
    return decorator

# ============================================================
# 7.4 ESTRUCTURA DE PROYECTO
# ============================================================

class ProyectoRPA:
    """
    Clase base para proyectos RPA con manejo de logs y configuración
    """
    
    def __init__(self, nombre_robot="robot"):
        self.nombre = nombre_robot
        self.logger = setup_logging(nombre_robot)
        self.config = self.cargar_configuracion()
        
        # Crear estructura de directorios
        self.crear_estructura_directorios()
        
        self.logger.info(f"🚀 Robot '{nombre_robot}' inicializado")
    
    def cargar_configuracion(self):
        """Carga configuración desde variables de entorno"""
        config = {
            'DEBUG': os.getenv('DEBUG', 'False').lower() == 'true',
            'MAX_RETRIES': int(os.getenv('MAX_RETRIES', '3')),
            'TIMEOUT': int(os.getenv('TIMEOUT', '30')),
            'DATA_DIR': os.getenv('DATA_DIR', 'data'),
            'LOG_DIR': os.getenv('LOG_DIR', 'logs'),
        }
        
        self.logger.info(f"📋 Configuración cargada: {config}")
        return config
    
    def crear_estructura_directorios(self):
        """Crea la estructura de directorios del proyecto"""
        directorios = [
            'data',
            'data/entrada',
            'data/salida',
            'data/procesados',
            'logs',
            'config',
            'scripts',
            'utils'
        ]
        
        for directorio in directorios:
            Path(directorio).mkdir(parents=True, exist_ok=True)
        
        self.logger.info("📁 Estructura de directorios creada")
    
    def log_execution_time(self):
        """Decorador para medir tiempo de ejecución"""
        def decorator(func):
            @wraps(func)
            def wrapper(*args, **kwargs):
                start_time = time.time()
                self.logger.info(f"🔄 Ejecutando: {func.__name__}")
                
                try:
                    result = func(*args, **kwargs)
                    elapsed = time.time() - start_time
                    self.logger.info(f"✅ {func.__name__} completado en {elapsed:.2f} segundos")
                    return result
                except Exception as e:
                    elapsed = time.time() - start_time
                    self.logger.error(f"❌ {func.__name__} falló después de {elapsed:.2f}s: {e}")
                    raise
            return wrapper
        return decorator
    
    def verificar_archivo(self, ruta, existe=True):
        """Verifica existencia de archivo"""
        path = Path(ruta)
        
        if existe and not path.exists():
            error_msg = f"Archivo no encontrado: {ruta}"
            self.logger.error(error_msg)
            raise FileNotFoundError(error_msg)
        
        if not existe and path.exists():
            error_msg = f"El archivo ya existe: {ruta}"
            self.logger.warning(error_msg)
            return False
        
        return True
    
    def validar_datos(self, datos, reglas):
        """
        Valida datos contra reglas
        
        Args:
            datos: Diccionario con datos a validar
            reglas: Diccionario con reglas {campo: (tipo, requerido, min, max)}
        """
        errores = []
        
        for campo, regla in reglas.items():
            tipo, requerido, min_val, max_val = regla
            valor = datos.get(campo)
            
            if requerido and valor is None:
                errores.append(f"Campo requerido: {campo}")
                continue
            
            if valor is not None:
                if not isinstance(valor, tipo):
                    errores.append(f"Tipo incorrecto para {campo}: esperado {tipo.__name__}, got {type(valor).__name__}")
                
                if isinstance(valor, (int, float)):
                    if min_val is not None and valor < min_val:
                        errores.append(f"Valor {campo}={valor} es menor que mínimo {min_val}")
                    if max_val is not None and valor > max_val:
                        errores.append(f"Valor {campo}={valor} es mayor que máximo {max_val}")
        
        if errores:
            self.logger.error(f"Errores de validación: {errores}")
            raise ValidationError(f"Validación fallida: {errores}")
        
        self.logger.info(f"✅ Datos validados correctamente")
        return True

# ============================================================
# 7.5 EJEMPLO DE USO CON DECORADORES
# ============================================================

@retry(max_retries=3, delay=0.5, backoff=2)
def operacion_inestable():
    """Demo de operación que puede fallar"""
    if random.random() < 0.7:  # 70% de probabilidad de fallo
        raise ConnectionError("Error de conexión simulado")
    return "Operación exitosa"

@timeout(3)
def operacion_lenta():
    """Demo de operación que puede ser lenta"""
    time.sleep(random.uniform(1, 5))
    return "Completado"

# ============================================================
# 7.6 PROGRAMACIÓN DE EJECUCIONES
# ============================================================

class SchedulerRPA:
    """Gestor de programación de tareas"""
    
    def __init__(self, logger=None):
        self.logger = logger or logging.getLogger(__name__)
        self.tareas = []
    
    def agregar_tarea(self, nombre, funcion, intervalo_minutos, args=None, kwargs=None):
        """Agrega una tarea programada"""
        self.tareas.append({
            'nombre': nombre,
            'funcion': funcion,
            'intervalo': intervalo_minutos * 60,  # Convertir a segundos
            'args': args or [],
            'kwargs': kwargs or {},
            'ultima_ejecucion': None
        })
        self.logger.info(f"📅 Tarea '{nombre}' agregada (intervalo: {intervalo_minutos} min)")
    
    def ejecutar_tarea(self, tarea):
        """Ejecuta una tarea individual"""
        try:
            self.logger.info(f"🔄 Ejecutando tarea: {tarea['nombre']}")
            resultado = tarea['funcion'](*tarea['args'], **tarea['kwargs'])
            self.logger.info(f"✅ Tarea '{tarea['nombre']}' completada: {resultado}")
            tarea['ultima_ejecucion'] = datetime.now()
            return resultado
        except Exception as e:
            self.logger.error(f"❌ Tarea '{tarea['nombre']}' falló: {e}")
            return None
    
    def ejecutar_ciclo_unico(self):
        """Ejecuta un ciclo de todas las tareas pendientes"""
        ahora = datetime.now()
        
        for tarea in self.tareas:
            if (tarea['ultima_ejecucion'] is None or 
                (ahora - tarea['ultima_ejecucion']).total_seconds() >= tarea['intervalo']):
                self.ejecutar_tarea(tarea)
    
    def generar_script_windows(self, nombre_script, ruta_script_python):
        """Genera script .bat para Windows Task Scheduler"""
        contenido = f"""@echo off
REM Script generado automáticamente por RPA Scheduler
REM Fecha: {datetime.now()}

cd /d {os.path.dirname(ruta_script_python)}
python {os.path.basename(ruta_script_python)}

echo Ejecución completada a las %date% %time%
pause
"""
        ruta_bat = f"{nombre_script}.bat"
        with open(ruta_bat, 'w', encoding='utf-8') as f:
            f.write(contenido)
        
        self.logger.info(f"📝 Script Windows generado: {ruta_bat}")
        return ruta_bat

# ============================================================
# EJERCICIO PRÁCTICO: ROBOT ROBUSTO
# ============================================================

class RobotProcesadorArchivos(ProyectoRPA):
    """Robot robusto para procesamiento de archivos"""
    
    def __init__(self):
        super().__init__("procesador_archivos")
        self.archivos_procesados = []
    
    @ProyectoRPA.log_execution_time
    def buscar_archivos(self, directorio, extension=".txt"):
        """Busca archivos con extensión específica"""
        directorio_path = Path(directorio)
        
        if not directorio_path.exists():
            self.logger.warning(f"Directorio no existe: {directorio}")
            return []
        
        archivos = list(directorio_path.glob(f"*{extension}"))
        self.logger.info(f"🔍 Encontrados {len(archivos)} archivos {extension}")
        
        return archivos
    
    @retry(max_retries=2, delay=1)
    def procesar_archivo(self, ruta_archivo):
        """Procesa un archivo individual con reintentos"""
        self.logger.info(f"📄 Procesando: {ruta_archivo}")
        
        # Simular procesamiento
        time.sleep(0.5)
        
        # Validar archivo
        self.verificar_archivo(ruta_archivo)
        
        # Simular posible error
        if random.random() < 0.1:  # 10% de error
            raise RetryableError("Error temporal al procesar archivo")
        
        # Leer contenido
        with open(ruta_archivo, 'r', encoding='utf-8') as f:
            contenido = f.read()
        
        self.archivos_procesados.append({
            'archivo': str(ruta_archivo),
            'tamanio': ruta_archivo.stat().st_size,
            'fecha_procesamiento': datetime.now(),
            'lineas': len(contenido.splitlines())
        })
        
        return True
    
    def ejecutar(self):
        """Ejecuta el flujo completo del robot"""
        self.logger.info("🚀 Iniciando Robot Procesador de Archivos")
        
        try:
            # Crear archivos de ejemplo
            os.makedirs("data/entrada", exist_ok=True)
            
            for i in range(5):
                with open(f"data/entrada/archivo_{i}.txt", 'w') as f:
                    f.write(f"Contenido del archivo {i}\n")
                    f.write(f"Generado: {datetime.now()}\n")
            
            # Buscar archivos
            archivos = self.buscar_archivos("data/entrada", ".txt")
            
            # Procesar cada archivo
            for archivo in archivos:
                try:
                    self.procesar_archivo(archivo)
                except Exception as e:
                    self.logger.error(f"No se pudo procesar {archivo}: {e}")
            
            # Generar reporte
            self.logger.info(f"✅ Archivos procesados: {len(self.archivos_procesados)}")
            
            for info in self.archivos_procesados:
                self.logger.info(f"  • {info['archivo']} - {info['tamanio']} bytes")
            
            return True
            
        except Exception as e:
            self.logger.critical(f"Error crítico en ejecución: {e}", exc_info=True)
            return False

def main():
    """Función principal de demostración"""
    print("🚀 DEMO DE BUENAS PRÁCTICAS RPA")
    print("="*60)
    
    # 1. Configurar logging
    logger = setup_logging("demo_robustez")
    logger.info("Iniciando demo de buenas prácticas")
    
    # 2. Probar decorador de reintentos
    print("\n📌 PRUEBA DE REINTENTOS:")
    try:
        resultado = operacion_inestable()
        print(f"  ✅ Resultado: {resultado}")
    except Exception as e:
        print(f"  ❌ Falló: {e}")
    
    # 3. Probar timeout
    print("\n📌 PRUEBA DE TIMEOUT:")
    try:
        resultado = operacion_lenta()
        print(f"  ✅ Resultado: {resultado}")
    except TimeoutError as e:
        print(f"  ❌ Timeout: {e}")
    
    # 4. Ejecutar robot robusto
    print("\n📌 EJECUTANDO ROBOT ROBUSTO:")
    robot = RobotProcesadorArchivos()
    robot.ejecutar()
    
    # 5. Demostrar scheduler
    print("\n📌 DEMO DE SCHEDULER:")
    
    def tarea_ejemplo():
        return f"Tarea ejecutada a las {datetime.now()}"
    
    scheduler = SchedulerRPA(logger)
    scheduler.agregar_tarea("demo", tarea_ejemplo, intervalo_minutos=1)
    scheduler.ejecutar_ciclo_unico()
    
    # Generar script para Windows Task Scheduler
    scheduler.generar_script_windows("mi_robot_rpa", __file__)
    
    print("\n✅ Demo completada")
    print("📁 Revisar:")
    print("   • logs/demo_robustez.log - Archivo de log")
    print("   • mi_robot_rpa.bat - Script para Task Scheduler")
    print("   • data/entrada/ - Archivos procesados")

if __name__ == "__main__":
    main()

    
Explicación:

Logging rotativo: Archivos de log con rotación

Decorador @retry: Reintentos automáticos en fallos

Timeout: Límite de tiempo para operaciones

Estructura de proyecto: Organización profesional

Scheduler: Programación de ejecuciones (Windows/Linux)

🎯 MÓDULO 8: PROYECTO INTEGRADOR
Proyecto: Procesamiento Automático de Facturas
python
# modulo8_proyecto_integrador.py
"""
PROYECTO INTEGRADOR - PROCESAMIENTO DE FACTURAS
Automatización completa: leer PDFs, extraer datos, cargar a web, generar reporte, enviar correo
"""

import os
import re
import time
import json
import shutil
import logging
from pathlib import Path
from datetime import datetime
from dataclasses import dataclass, asdict
from typing import List, Dict, Optional
from functools import wraps

import pandas as pd
import PyPDF2
import pdfplumber
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment

# Importar módulos de los ejercicios anteriores
from modulo4_selenium_automation import RobotWebAutomation
from modulo6_correos_api import CorreoSMTP, generar_reporte_clima_html
from modulo7_buenas_practicas import setup_logging, retry, ProyectoRPA

# ============================================================
# ESTRUCTURA DE DATOS
# ============================================================

@dataclass
class Factura:
    """Clase para representar una factura"""
    numero: str
    fecha: str
    proveedor: str
    monto: float
    moneda: str = "USD"
    archivo_original: str = ""
    validada: bool = False
    
    def validar(self) -> bool:
        """Valida los datos de la factura"""
        # Validar fecha formato DD/MM/YYYY o YYYY-MM-DD
        patron_fecha = r'^\d{1,2}[/-]\d{1,2}[/-]\d{2,4}$|^\d{4}-\d{2}-\d{2}$'
        if not re.match(patron_fecha, self.fecha):
            logging.warning(f"Fecha inválida: {self.fecha}")
            return False
        
        # Validar monto positivo
        if self.monto <= 0:
            logging.warning(f"Monto inválido: {self.monto}")
            return False
        
        # Validar número de factura no vacío
        if not self.numero or len(self.numero) < 3:
            logging.warning(f"Número de factura inválido: {self.numero}")
            return False
        
        self.validada = True
        return True

# ============================================================
# SCRIPT 1: EXTRACTOR DE PDFs
# ============================================================

class ExtractorFacturas:
    """Extrae información de facturas desde archivos PDF"""
    
    # Patrones comunes en facturas
    PATRONES = {
        'numero': [
            r'(?:FACTURA|INVOICE|N[°º]?|Número|No\.?)\s*[:\-]?\s*([A-Z0-9\-]{5,})',
            r'(?:FOLIO|REFERENCIA)\s*[:\-]?\s*([A-Z0-9\-]{5,})',
            r'INV[-_]?\d{4,}',
            r'F[-_]?\d{6,}'
        ],
        'fecha': [
            r'(?:FECHA|DATE)\s*[:\-]?\s*(\d{1,2}[/-]\d{1,2}[/-]\d{2,4})',
            r'(\d{4}-\d{2}-\d{2})',
            r'(\d{1,2}\s+(?:ENERO|FEBRERO|MARZO|ABRIL|MAYO|JUNIO|JULIO|AGOSTO|SEPTIEMBRE|OCTUBRE|NOVIEMBRE|DICIEMBRE)\s+\d{4})'
        ],
        'proveedor': [
            r'(?:PROVEEDOR|SUPPLIER|VENDEDOR|EMPRESA)\s*[:\-]?\s*([A-ZÁÉÍÓÚÑ\s]{3,})',
            r'(?:NOMBRE|NAME)\s*[:\-]?\s*([A-ZÁÉÍÓÚÑ\s]{3,})'
        ],
        'monto': [
            r'(?:TOTAL|SUBTOTAL|IMPORTE)\s*[:\-]?\s*[\$]?\s*([\d,]+\.?\d*)',
            r'MONTO\s*[:\-]?\s*[\$]?\s*([\d,]+\.?\d*)',
            r'[\$]\s*([\d,]+\.?\d*)\s*(?:USD|MXN|EUR)?'
        ]
    }
    
    def __init__(self, directorio_pdfs: str = "data/facturas"):
        self.directorio = Path(directorio_pdfs)
        self.procesados_dir = Path("data/procesados")
        self.errores_dir = Path("data/errores")
        self.facturas: List[Factura] = []
        
        # Crear directorios
        self.directorio.mkdir(parents=True, exist_ok=True)
        self.procesados_dir.mkdir(parents=True, exist_ok=True)
        self.errores_dir.mkdir(parents=True, exist_ok=True)
        
        # Configurar logging
        self.logger = logging.getLogger(__name__)
    
    def extraer_texto_pypdf2(self, ruta_pdf: Path) -> str:
        """Extrae texto usando PyPDF2 (rápido, pero puede fallar con PDFs escaneados)"""
        texto = []
        with open(ruta_pdf, 'rb') as archivo:
            lector = PyPDF2.PdfReader(archivo)
            for pagina in lector.pages:
                texto_pagina = pagina.extract_text()
                if texto_pagina:
                    texto.append(texto_pagina)
        return "\n".join(texto)
    
    def extraer_texto_pdfplumber(self, ruta_pdf: Path) -> str:
        """Extrae texto usando pdfplumber (mejor calidad, más lento)"""
        texto = []
        with pdfplumber.open(ruta_pdf) as pdf:
            for pagina in pdf.pages:
                texto_pagina = pagina.extract_text()
                if texto_pagina:
                    texto.append(texto_pagina)
                
                # Extraer tablas si existen
                tablas = pagina.extract_tables()
                for tabla in tablas:
                    if tabla:
                        for fila in tabla:
                            if fila and any(fila):
                                texto.append(" ".join(str(c) for c in fila if c)))
        return "\n".join(texto)
    
    def extraer_datos_factura(self, ruta_pdf: Path) -> Optional[Dict]:
        """Extrae datos de una factura usando múltiples métodos"""
        self.logger.info(f"📄 Extrayendo datos de: {ruta_pdf.name}")
        
        # Intentar extraer texto
        texto = self.extraer_texto_pdfplumber(ruta_pdf)
        if not texto:
            texto = self.extraer_texto_pypdf2(ruta_pdf)
        
        if not texto:
            self.logger.warning(f"❌ No se pudo extraer texto de {ruta_pdf.name}")
            return None
        
        # Buscar datos usando patrones
        datos = {}
        
        for campo, patrones in self.PATRONES.items():
            for patron in patrones:
                match = re.search(patron, texto, re.IGNORECASE)
                if match:
                    valor = match.group(1).strip()
                    if campo == 'monto':
                        # Limpiar formato de monto
                        valor = float(valor.replace(',', ''))
                    datos[campo] = valor
                    break
        
        # Limpiar proveedor (eliminar caracteres extraños)
        if 'proveedor' in datos:
            proveedor = datos['proveedor']
            proveedor = re.sub(r'[^A-ZÁÉÍÓÚÑ\s]', '', proveedor).strip()
            datos['proveedor'] = proveedor[:50]  # Limitar longitud
        
        return datos
    
    @retry(max_retries=2, delay=1)
    def procesar_pdf(self, ruta_pdf: Path) -> Optional[Factura]:
        """Procesa un archivo PDF y retorna una Factura"""
        try:
            datos = self.extraer_datos_factura(ruta_pdf)
            
            if not datos:
                raise ValueError(f"No se pudieron extraer datos")
            
            # Crear factura
            factura = Factura(
                numero=datos.get('numero', f"UNKNOWN_{datetime.now().strftime('%Y%m%d%H%M%S')}"),
                fecha=datos.get('fecha', datetime.now().strftime("%d/%m/%Y")),
                proveedor=datos.get('proveedor', "DESCONOCIDO"),
                monto=datos.get('monto', 0.0),
                archivo_original=str(ruta_pdf)
            )
            
            # Validar
            if not factura.validar():
                self.logger.warning(f"Factura no válida: {factura}")
                # Mover a errores
                shutil.move(str(ruta_pdf), self.errores_dir / ruta_pdf.name)
                return None
            
            self.logger.info(f"✅ Factura extraída: {factura.numero} - {factura.proveedor} - ${factura.monto}")
            
            # Mover a procesados
            shutil.move(str(ruta_pdf), self.procesados_dir / ruta_pdf.name)
            
            return factura
            
        except Exception as e:
            self.logger.error(f"❌ Error procesando {ruta_pdf.name}: {e}")
            shutil.move(str(ruta_pdf), self.errores_dir / ruta_pdf.name)
            return None
    
    def procesar_todas(self) -> List[Factura]:
        """Procesa todos los PDFs en el directorio"""
        pdfs = list(self.directorio.glob("*.pdf"))
        
        if not pdfs:
            self.logger.warning(f"No se encontraron PDFs en {self.directorio}")
            return []
        
        self.logger.info(f"🔄 Procesando {len(pdfs)} facturas...")
        
        for pdf in pdfs:
            factura = self.procesar_pdf(pdf)
            if factura:
                self.facturas.append(factura)
        
        self.logger.info(f"✅ Procesadas {len(self.facturas)} facturas válidas")
        return self.facturas

# ============================================================
# SCRIPT 2: GENERADOR DE REPORTE EXCEL
# ============================================================

class GeneradorReporte:
    """Genera reportes Excel con resumen de facturas"""
    
    def __init__(self):
        self.logger = logging.getLogger(__name__)
    
    def generar_reporte_excel(self, facturas: List[Factura], ruta_salida: str = "data/reporte_facturas.xlsx"):
        """Genera un reporte Excel con formato profesional"""
        
        # Convertir a DataFrame
        datos = [asdict(f) for f in facturas]
        df = pd.DataFrame(datos)
        
        # Crear Excel con múltiples hojas
        with pd.ExcelWriter(ruta_salida, engine='openpyxl') as writer:
            # Hoja 1: Detalle de facturas
            df.to_excel(writer, sheet_name='Detalle Facturas', index=False)
            
            # Hoja 2: Resumen por proveedor
            resumen_proveedor = df.groupby('proveedor').agg({
                'monto': ['sum', 'count', 'mean']
            }).round(2)
            resumen_proveedor.columns = ['Total', 'Cantidad', 'Promedio']
            resumen_proveedor.to_excel(writer, sheet_name='Resumen Proveedor')
            
            # Hoja 3: Métricas generales
            metricas = pd.DataFrame([
                ['Total Facturas', len(facturas)],
                ['Total Monto', f"${df['monto'].sum():,.2f}"],
                ['Monto Promedio', f"${df['monto'].mean():,.2f}"],
                ['Monto Máximo', f"${df['monto'].max():,.2f}"],
                ['Monto Mínimo', f"${df['monto'].min():,.2f}"],
                ['Fecha Procesamiento', datetime.now().strftime("%d/%m/%Y %H:%M")],
                ['Proveedores Únicos', df['proveedor'].nunique()]
            ], columns=['Métrica', 'Valor'])
            metricas.to_excel(writer, sheet_name='Métricas', index=False)
            
            # Formatear el archivo Excel
            self._formatear_excel(ruta_salida)
        
        self.logger.info(f"📊 Reporte Excel generado: {ruta_salida}")
        return ruta_salida
    
    def _formatear_excel(self, ruta_archivo):
        """Aplica formato profesional al Excel"""
        from openpyxl import load_workbook
        
        wb = load_workbook(ruta_archivo)
        
        # Estilos
        header_font = Font(bold=True, color="FFFFFF")
        header_fill = PatternFill(start_color="2E75B6", end_color="2E75B6", fill_type="solid")
        
        for hoja in wb.worksheets:
            # Formatear cabeceras
            for celda in hoja[1]:
                celda.font = header_font
                celda.fill = header_fill
                celda.alignment = Alignment(horizontal="center")
            
            # Ajustar ancho de columnas
            for columna in hoja.columns:
                max_length = 0
                col_letter = columna[0].column_letter
                for celda in columna:
                    try:
                        if len(str(celda.value)) > max_length:
                            max_length = len(str(celda.value))
                    except:
                        pass
                hoja.column_dimensions[col_letter].width = min(max_length + 2, 30)
        
        wb.save(ruta_archivo)

# ============================================================
# SCRIPT 3: CARGADOR WEB
# ============================================================

class CargadorWebFacturas:
    """Carga facturas a un sistema web (demo)"""
    
    def __init__(self, headless=False):
        self.logger = logging.getLogger(__name__)
        self.robot_web = RobotWebAutomation(headless=headless)
        self.cargas_exitosas = 0
        self.cargas_fallidas = []
    
    def login_sistema_demo(self):
        """Login al sistema de demostración"""
        # Usar sitio de pruebas público
        self.robot_web.navegar_a_sitio("https://the-internet.herokuapp.com/login")
        
        return self.robot_web.login(
            username="tomsmith",
            password="SuperSecretPassword!",
            username_selector="#username",
            password_selector="#password",
            submit_selector="button[type='submit']"
        )
    
    def cargar_factura(self, factura: Factura) -> bool:
        """
        Carga una factura al sistema web
        En un caso real, aquí iría la lógica específica del sistema
        """
        try:
            self.logger.info(f"🌐 Cargando factura: {factura.numero}")
            
            # Demo: Simular carga exitosa
            # En producción, usar selectores reales:
            # self.robot_web.interactuar_con_formulario({
            #     "#numero_factura": factura.numero,
            #     "#fecha": factura.fecha,
            #     "#proveedor": factura.proveedor,
            #     "#monto": str(factura.monto)
            # }, "#btn_guardar")
            
            # Simular éxito
            time.sleep(0.5)
            self.cargas_exitosas += 1
            self.logger.info(f"✅ Factura {factura.numero} cargada exitosamente")
            return True
            
        except Exception as e:
            self.logger.error(f"❌ Error cargando factura {factura.numero}: {e}")
            self.cargas_fallidas.append(factura)
            return False
    
    def cargar_todas(self, facturas: List[Factura]) -> Dict:
        """Carga todas las facturas al sistema"""
        self.logger.info(f"🚀 Iniciando carga de {len(facturas)} facturas...")
        
        try:
            if not self.login_sistema_demo():
                self.logger.error("❌ No se pudo iniciar sesión")
                return {'exitosas': 0, 'fallidas': len(facturas), 'detalle': []}
            
            for factura in facturas:
                self.cargar_factura(factura)
            
        finally:
            self.robot_web.cerrar()
        
        return {
            'exitosas': self.cargas_exitosas,
            'fallidas': len(self.cargas_fallidas),
            'detalle': self.cargas_fallidas
        }

# ============================================================
# SCRIPT 4: ENVIADOR DE CORREO
# ============================================================

class EnviadorReporte:
    """Envía el reporte por correo electrónico"""
    
    def __init__(self):
        self.logger = logging.getLogger(__name__)
        self.correo = CorreoSMTP()
    
    def generar_cuerpo_html(self, facturas: List[Factura], estadisticas: Dict) -> str:
        """Genera el cuerpo HTML del correo"""
        
        total_monto = sum(f.monto for f in facturas)
        
        html = f"""
        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="UTF-8">
            <style>
                body {{ font-family: Arial, sans-serif; }}
                .container {{ max-width: 800px; margin: 0 auto; padding: 20px; }}
                h1 {{ color: #2E75B6; }}
                .metricas {{ background: #f0f0f0; padding: 15px; border-radius: 8px; margin: 20px 0; }}
                .exito {{ color: green; font-weight: bold; }}
                .error {{ color: red; }}
                table {{ width: 100%; border-collapse: collapse; margin-top: 20px; }}
                th, td {{ border: 1px solid #ddd; padding: 8px; text-align: left; }}
                th {{ background-color: #2E75B6; color: white; }}
                tr:nth-child(even) {{ background-color: #f2f2f2; }}
            </style>
        </head>
        <body>
            <div class="container">
                <h1>📄 Reporte de Procesamiento de Facturas</h1>
                <p><strong>Fecha de generación:</strong> {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}</p>
                
                <div class="metricas">
                    <h2>📊 Estadísticas</h2>
                    <p>✅ Facturas procesadas: {len(facturas)}</p>
                    <p>💰 Monto total: ${total_monto:,.2f}</p>
                    <p>📈 Monto promedio: ${(total_monto/len(facturas)) if facturas else 0:,.2f}</p>
                    <p>🏢 Proveedores distintos: {len(set(f.proveedor for f in facturas))}</p>
                </div>
                
                <h2>📋 Detalle de Facturas</h2>
                <table>
                    <thead>
                        <tr><th>Número</th><th>Fecha</th><th>Proveedor</th><th>Monto</th><th>Validada</th></tr>
                    </thead>
                    <tbody>
        """
        
        for factura in facturas:
            html += f"""
                        <tr>
                            <td>{factura.numero}</td>
                            <td>{factura.fecha}</td>
                            <td>{factura.proveedor}</td>
                            <td>${factura.monto:,.2f}</td>
                            <td class="exito">✓</td>
                        </tr>
            """
        
        html += f"""
                    </tbody>
                </table>
                
                <hr>
                <p style="font-size: 0.8em; color: #666; text-align: center;">
                    Reporte generado automáticamente por el Robot RPA 2026<br>
                    Proyecto Integrador - Automatización Inteligente
                </p>
            </div>
        </body>
        </html>
        """
        
        return html
    
    def enviar(self, facturas: List[Factura], ruta_excel: str, destinatarios: List[str]):
        """Envía el reporte por correo"""
        
        estadisticas = {
            'total': len(facturas),
            'monto_total': sum(f.monto for f in facturas)
        }
        
        cuerpo_html = self.generar_cuerpo_html(facturas, estadisticas)
        
        return self.correo.enviar_correo(
            destino=destinatarios,
            asunto=f"📊 Reporte Facturas - {datetime.now().strftime('%d/%m/%Y')}",
            cuerpo_html=cuerpo_html,
            adjuntos=[ruta_excel] if os.path.exists(ruta_excel) else None
        )

# ============================================================
# ORQUESTADOR PRINCIPAL
# ============================================================

class ProcesadorFacturasOrquestador:
    """Orquesta todo el proceso de automatización"""
    
    def __init__(self):
        self.logger = setup_logging("proyecto_integrador", logging.INFO)
        self.extractor = ExtractorFacturas()
        self.generador = GeneradorReporte()
        self.enviador = EnviadorReporte()
        self.cargador = None  # Inicializar solo si se necesita
        
        self.facturas = []
        self.config = self._cargar_config()
    
    def _cargar_config(self):
        """Carga configuración"""
        return {
            'generar_reporte': True,
            'enviar_correo': True,
            'cargar_web': False,  # Desactivado por defecto (requiere credenciales)
            'destinatarios': ['reportes@ejemplo.com', 'admin@ejemplo.com']
        }
    
    def paso_1_extraer_pdfs(self):
        """Paso 1: Extraer datos de PDFs"""
        self.logger.info("="*60)
        self.logger.info("PASO 1: Extrayendo facturas de PDFs")
        self.logger.info("="*60)
        
        self.facturas = self.extractor.procesar_todas()
        
        if not self.facturas:
            self.logger.warning("⚠️ No se encontraron facturas válidas")
            return False
        
        self.logger.info(f"✅ Extraídas {len(self.facturas)} facturas")
        return True
    
    def paso_2_validar_datos(self):
        """Paso 2: Validar datos extraídos"""
        self.logger.info("\n" + "="*60)
        self.logger.info("PASO 2: Validando datos")
        self.logger.info("="*60)
        
        facturas_validas = [f for f in self.facturas if f.validar() if f.monto > 0]
        
        if len(facturas_validas) != len(self.facturas):
            self.logger.warning(f"{len(self.facturas) - len(facturas_validas)} facturas inválidas")
            self.facturas = facturas_validas
        
        # Mostrar resumen
        for factura in self.facturas:
            self.logger.info(f"  • {factura.numero} | {factura.proveedor} | ${factura.monto:,.2f}")
        
        return True
    
    def paso_3_generar_reporte(self):
        """Paso 3: Generar reporte Excel"""
        if not self.config['generar_reporte']:
            self.logger.info("⏭️ Omitiendo generación de reporte")
            return None
        
        self.logger.info("\n" + "="*60)
        self.logger.info("PASO 3: Generando reporte Excel")
        self.logger.info("="*60)
        
        ruta_excel = self.generador.generar_reporte_excel(self.facturas)
        self.logger.info(f"✅ Reporte generado: {ruta_excel}")
        
        return ruta_excel
    
    def paso_4_cargar_web(self):
        """Paso 4: Cargar facturas a sistema web"""
        if not self.config['cargar_web']:
            self.logger.info("⏭️ Omitiendo carga web (desactivada en configuración)")
            return None
        
        self.logger.info("\n" + "="*60)
        self.logger.info("PASO 4: Cargando facturas al sistema web")
        self.logger.info("="*60)
        
        self.cargador = CargadorWebFacturas(headless=True)
        resultado = self.cargador.cargar_todas(self.facturas)
        
        self.logger.info(f"✅ Carga completada: {resultado['exitosas']} éxitos, {resultado['fallidas']} fallos")
        return resultado
    
    def paso_5_enviar_correo(self, ruta_excel: str = None):
        """Paso 5: Enviar reporte por correo"""
        if not self.config['enviar_correo']:
            self.logger.info("⏭️ Omitiendo envío de correo")
            return None
        
        self.logger.info("\n" + "="*60)
        self.logger.info("PASO 5: Enviando reporte por correo")
        self.logger.info("="*60)
        
        resultado = self.enviador.enviar(
            self.facturas,
            ruta_excel or "data/reporte_facturas.xlsx",
            self.config['destinatarios']
        )
        
        if resultado:
            self.logger.info("✅ Correo enviado exitosamente")
        else:
            self.logger.warning("⚠️ Correo guardado localmente (sin credenciales)")
        
        return resultado
    
    def ejecutar(self):
        """Ejecuta el flujo completo"""
        self.logger.info("🚀 INICIANDO PROYECTO INTEGRADOR - PROCESAMIENTO DE FACTURAS")
        self.logger.info("="*60)
        
        try:
            # Paso 1: Extraer PDFs
            if not self.paso_1_extraer_pdfs():
                self.logger.error("❌ No se pudieron extraer facturas")
                return False
            
            # Paso 2: Validar datos
            self.paso_2_validar_datos()
            
            # Paso 3: Generar reporte
            ruta_excel = self.paso_3_generar_reporte()
            
            # Paso 4: Cargar a web (opcional)
            self.paso_4_cargar_web()
            
            # Paso 5: Enviar correo
            self.paso_5_enviar_correo(ruta_excel)
            
            # Resumen final
            self.logger.info("\n" + "="*60)
            self.logger.info("🎉 PROYECTO COMPLETADO EXITOSAMENTE")
            self.logger.info("="*60)
            self.logger.info(f"📄 Facturas procesadas: {len(self.facturas)}")
            self.logger.info(f"💰 Monto total: ${sum(f.monto for f in self.facturas):,.2f}")
            self.logger.info(f"📊 Reporte Excel: {ruta_excel}")
            self.logger.info(f"📁 PDFs originales: {self.extractor.directorio}")
            self.logger.info(f"✅ PDFs procesados: {self.extractor.procesados_dir}")
            self.logger.info(f"❌ PDFs con error: {self.extractor.errores_dir}")
            
            return True
            
        except Exception as e:
            self.logger.critical(f"❌ Error fatal en el proceso: {e}", exc_info=True)
            return False

# ============================================================
# FUNCIONES DE DEMO Y PREPARACIÓN
# ============================================================

def crear_pdfs_ejemplo():
    """Crea PDFs de ejemplo para demostración"""
    from reportlab.lib.pagesizes import letter
    from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, Table, TableStyle
    from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
    from reportlab.lib import colors
    
    os.makedirs("data/facturas", exist_ok=True)
    
    facturas_ejemplo = [
        {
            'numero': 'FAC-2026-001',
            'fecha': '15/01/2026',
            'proveedor': 'TECH SUPPLIES S.A.',
            'monto': 1250.50
        },
        {
            'numero': 'INV-2026-002',
            'fecha': '20/01/2026',
            'proveedor': 'LOGISTICA EXPRESS',
            'monto': 3400.00
        },
        {
            'numero': 'F-2026003',
            'fecha': '25/01/2026',
            'proveedor': 'SERVICIOS GENERALES',
            'monto': 875.75
        }
    ]
    
    for factura in facturas_ejemplo:
        ruta = f"data/facturas/factura_{factura['numero']}.pdf"
        
        doc = SimpleDocTemplate(ruta, pagesize=letter)
        elementos = []
        estilos = getSampleStyleSheet()
        
        # Título
        titulo = Paragraph("FACTURA", estilos['Title'])
        elementos.append(titulo)
        elementos.append(Spacer(1, 12))
        
        # Datos
        datos = [
            ["Número:", factura['numero']],
            ["Fecha:", factura['fecha']],
            ["Proveedor:", factura['proveedor']],
            ["Total:", f"${factura['monto']:.2f}"]
        ]
        
        tabla = Table(datos)
        tabla.setStyle(TableStyle([
            ('FONTNAME', (0, 0), (-1, -1), 'Helvetica'),
            ('FONTSIZE', (0, 0), (-1, -1), 10),
            ('GRID', (0, 0), (-1, -1), 1, colors.black),
        ]))
        elementos.append(tabla)
        
        doc.build(elementos)
        print(f"📄 Creado PDF de ejemplo: {ruta}")

# ============================================================
# MAIN
# ============================================================

def main():
    """Función principal"""
    print("\n" + "="*70)
    print("🏆 PROYECTO INTEGRADOR - PROCESAMIENTO AUTOMÁTICO DE FACTURAS")
    print("="*70)
    print("Este proyecto implementa un robot RPA completo que:")
    print("  1. Lee facturas desde PDFs")
    print("  2. Extrae información automáticamente")
    print("  3. Valida los datos extraídos")
    print("  4. Genera reportes en Excel")
    print("  5. Envía reportes por correo")
    print("  6. Maneja errores y logs")
    print("="*70)
    
    # Crear PDFs de ejemplo si no existen
    if not list(Path("data/facturas").glob("*.pdf")):
        print("\n📝 Creando PDFs de ejemplo...")
        crear_pdfs_ejemplo()
    
    # Ejecutar el orquestador
    orquestador = ProcesadorFacturasOrquestador()
    exitoso = orquestador.ejecutar()
    
    if exitoso:
        print("\n🎉 ¡PROYECTO COMPLETADO CON ÉXITO!")
        print("📋 Revisar:")
        print("   • data/reporte_facturas.xlsx - Reporte generado")
        print("   • logs/proyecto_integrador.log - Log de ejecución")
        print("   • data/procesados/ - Facturas procesadas")
        print("   • data/errores/ - Facturas con errores (si las hay)")
    else:
        print("\n❌ El proyecto no se completó correctamente")
        print("🔍 Revisar el log para más detalles")

if __name__ == "__main__":
    main()
Explicación del Proyecto Integrador:

Este proyecto completo implementa:

Extractor de PDFs (Script 1): Busca archivos PDF, extrae información usando múltiples métodos, valida datos y organiza archivos procesados/errores.

Generador de Reportes (Script 3): Crea reportes Excel profesionales con múltiples hojas (detalle, resumen por proveedor, métricas).

Cargador Web (Script 2): Sube datos a sistemas web (simulado para demo).

Enviador de Correo (Script 4): Genera correos HTML con adjuntos.

Orquestador: Coordina todo el flujo con manejo de errores y logging.

📋 REQUISITOS
txt
# requirements.txt - Instalar con pip install -r requirements.txt

# Core
pyautogui>=0.9.54
selenium>=4.15.0
webdriver-manager>=4.0.1

# Procesamiento datos
pandas>=2.1.0
openpyxl>=3.1.2
PyPDF2>=3.0.1
pdfplumber>=0.10.3

# Correos y APIs
requests>=2.31.0
python-dotenv>=1.0.0

# Reportes (opcional)
reportlab>=4.0.4

# Utilidades
pyperclip>=1.8.2
pillow>=10.1.0
🎓 CONCLUSIÓN
Este código completo cubre todos los ejercicios del curso desde el Módulo 0 al Módulo 8, incluyendo:

✅ M0: Primer robot con pyautogui

✅ M1: Procesador de CSV con validaciones

✅ M2: Reporte automático en Bloc de Notas

✅ M4: Automatización web completa con Selenium

✅ M5: Procesador avanzado de Excel y PDFs

✅ M6: API de clima + envío de correos

✅ M7: Logging, reintentos, timeouts, estructura profesional

✅ M8: Proyecto Integrador completo de facturas


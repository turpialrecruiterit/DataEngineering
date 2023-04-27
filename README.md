# DataEngineering
Coder Project

Jorge López
Data Engineering

1era entrega:

import requests
import psycopg2

# URL base de la API y parámetros necesarios
base_url = "https://api.example.com/"
api_key = "your_api_key"

# Conexión a la base de datos de Redshift
conn = psycopg2.connect(
    host="your_host_name",
    database="your_database_name",
    user="your_user_name",
    password="your_password",
    port=5439 # puerto predeterminado de Redshift
)
cur = conn.cursor()

# Crear tabla en Redshift
cur.execute("""
CREATE TABLE IF NOT EXISTS ejemplo (
    columna1 INT,
    columna2 VARCHAR(50),
    columna3 DATE
);
""")

# Hacer solicitud a la API y extraer datos
response = requests.get(base_url, params={"api_key": api_key})
data = response.json()

# Insertar datos en la tabla de Redshift
for item in data:
    cur.execute("""
    INSERT INTO ejemplo (columna1, columna2, columna3)
    VALUES (%s, %s, %s);
    """, (item["valor_columna1"], item["valor_columna2"], item["valor_columna3"]))

# Confirmar los cambios y cerrar la conexión
conn.commit()
cur.close()
conn.close()


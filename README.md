# Lab_2
Lab_2_BDA_NO_SQL_REDIS_&amp;_NEO4J

# Proyecto: Lab 2 - Base de Datos en Redis y Neo4j

Descripción:
Este proyecto implementa un sistema CRUD en Redis y Neo4j utilizando Python. 


Requisitos
Se necesita de tener instalado los siguientes componentes:

- [Python 3.8+](https://www.python.org/downloads/)
- [Neo4j](https://neo4j.com/)
- `pip` para instalar dependencias de Python.
- Crear la conexión entre Redis, Neo4j con Python.
- [Redis](https://redis.io/es/)
- Extrae los archivos descargados en una carpeta de tu elección, por ejemplo, C:\Redis.
- Abre una terminal y navega al directorio donde extrajiste Redis:
  bash
  cd C:\Redis  
- Inicia el servidor Redis ejecutando:
  bash
  redis-server    
- Esto iniciará Redis en el puerto predeterminado 6379.
- En otra terminal, escribe:
  bash
  redis-cli ping
-Si Redis está funcionando correctamente, debería responder:
  bash
  PONG


Script de pruebas de Neo4j con Python.

Antes de iniciar las pruebas con, se debe realizar el archivo docker para una instalación e inicio de sesión de una forma más sencilla.

    version: '3.8'
    services:
      neo4j:
        image: neo4j:latest
        container_name: neo4j
        ports:
          - "7474:7474"  # Para la interfaz web
          - "7687:7687"  # Para el protocolo Bolt
        environment:
          - NEO4J_AUTH=neo4j/password  # Usuario: "Elija_usuario", Contraseña: "Elija_contraseña"
    from neo4j import GraphDatabase
    import time
Luego de esto, se debe ejecutar en el símbolo del sistema con cd y la ruta del archivo.

    class Neo4jCRUD:
    def __init__(self, uri, user, password):
        self.driver = GraphDatabase.driver(uri, auth=(user, password))

    def close(self):
        self.driver.close()

    def create_data(self):
        start_time = time.time()
        with self.driver.session() as session:
            for i in range(1, 10001):
                session.run(
                    "CREATE (n:User {id: $id, name: $name, age: $age, city: $city})",
                    id=i, name=f"User{i}", age=20 + (i % 30), city=f"City{i % 100}"
                )
        return time.time() - start_time

    def read_data(self):
        start_time = time.time()
        with self.driver.session() as session:
            for i in range(1, 10001):
                session.run("MATCH (n:User {id: $id}) RETURN n", id=i)
        return time.time() - start_time

    def update_data(self):
        start_time = time.time()
        with self.driver.session() as session:
            for i in range(1, 10001):
                session.run(
                    "MATCH (n:User {id: $id}) SET n.age = $new_age",
                    id=i, new_age=25
                )
        return time.time() - start_time

    def delete_data(self):
        start_time = time.time()
        with self.driver.session() as session:
            session.run("MATCH (n:User) DELETE n")
        return time.time() - start_time


    if __name__ == "__main__":
    uri = "bolt://localhost:7687"
    user = "neo4j"
    password = "password"

    db = Neo4jCRUD(uri, user, password)

    print("Running Neo4j CRUD operations...")

    # Create
    create_time = db.create_data()
    print(f"Create time: {create_time:.2f} seconds")

    # Read
    read_time = db.read_data()
    print(f"Read time: {read_time:.2f} seconds")

    # Update
    update_time = db.update_data()
    print(f"Update time: {update_time:.2f} seconds")

    # Delete
    delete_time = db.delete_data()
    print(f"Delete time: {delete_time:.2f} seconds")

    db.close()

Script de pruebas de Redis con Python

    import redis

    def insertar_datos():
    r = redis.Redis(host='localhost', port=6379)

    for i in range(10000):
        clave = f"usuario:{i}"
        valor = {"nombre": f"Usuario{i}", "edad": i % 100, "ciudad": f"Ciudad{i % 50}"}
        r.set(clave, str(valor))

    print("Se han insertado 10,000 registros en Redis.")

    if __name__ == "__main__":
    insertar_datos()

    import redis

    def insertar_datos():
    r = redis.Redis(host='localhost', port=6379)

    for i in range(10000):
        clave = f"usuario:{i}"
        valor = {"nombre": f"Usuario{i}", "edad": i % 100, "ciudad": f"Ciudad{i % 50}"}
        r.set(clave, str(valor))

    print("Se han insertado 10,000 registros en Redis.")

    if __name__ == "__main__":
    insertar_datos()

    import time
    import redis

    def medir_tiempo(func):
    """
    Decorador para medir el tiempo de ejecución y calcular latencia promedio y rendimiento.
    """
    def wrapper(*args, **kwargs):
        inicio = time.time()
        resultado = func(*args, **kwargs)
        fin = time.time()
        total_operaciones = kwargs.get("total", 10000)  # Número total de operaciones por defecto
        tiempo_total = fin - inicio
        latencia_promedio = tiempo_total / total_operaciones
        rendimiento = total_operaciones / tiempo_total
        print(f"{func._name_} tomó {tiempo_total:.4f} segundos")
        print(f"Latencia promedio: {latencia_promedio:.6f} segundos/operación")
        print(f"Rendimiento: {rendimiento:.2f} operaciones/segundo")
        return resultado
    return wrapper

    @medir_tiempo
    def prueba_crear(r, total=10000):
    """
    Inserta total registros en Redis.
    """
    for i in range(total):
        clave = f"usuario:{i}"
        valor = {"nombre": f"Usuario{i}", "edad": i % 100, "ciudad": f"Ciudad{i % 50}"}
        r.set(clave, str(valor))

    @medir_tiempo
    def prueba_leer(r, total=10000):
    """
    Lee total registros de Redis y verifica consistencia.
    """
    consistentes = True
    for i in range(total):
        clave = f"usuario:{i}"
        valor = r.get(clave)
        esperado = str({"nombre": f"Usuario{i}", "edad": i % 100, "ciudad": f"Ciudad{i % 50}"})
        if valor is None or valor.decode() != esperado:
            consistentes = False
    print(f"Consistencia de lectura: {'Correcta' if consistentes else 'Fallida'}")

    @medir_tiempo
    def prueba_actualizar(r, total=10000):
    """
    Actualiza total registros en Redis.
    """
    for i in range(total):
        clave = f"usuario:{i}"
        valor = {"nombre": f"UsuarioActualizado{i}", "edad": (i % 100) + 1, "ciudad": f"CiudadActualizada{i % 50}"}
        r.set(clave, str(valor))

    @medir_tiempo
    def prueba_eliminar(r, total=10000):
    """
    Elimina total registros de Redis.
    """
    for i in range(total):
        clave = f"usuario:{i}"
        r.delete(clave)

    if _name_ == "_main_":
    r = redis.Redis(host='localhost', port=6379)

    print("Ejecutando pruebas de rendimiento...")
    prueba_crear(r, total=10000)   # Inserta 10,000 registros
    prueba_leer(r, total=10000)    # Lee 1,000 registros
    prueba_actualizar(r, total=10000)  # Actualiza 5,000 registros
    prueba_eliminar(r, total=10000)    # Elimina 5,000 registros



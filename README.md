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

Script de pruebas de Neo4j con Python

[from neo4j import GraphDatabase
import time

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




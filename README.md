# Graph-Databases-and-graph-Algorithms
#neo4j Algorithm
from neo4j import GraphDatabase
class GraphDatabaseHandler:
    def __init__(self, uri, user, password):
        self.driver = GraphDatabase.driver(uri, auth=(user, password))
    def close(self):
        self.driver.close()
    def create_node(self, label, properties):
        with self.driver.session() as session:
            session.write_transaction(self._create_and_return_node, label, properties)
    @staticmethod
    def _create_and_return_node(tx, label, properties):
        query = (
            f"CREATE (n:{label} {{"
            + ", ".join([f"{key}: ${key}" for key in properties.keys()])
            + "}) RETURN n"
        )
        result = tx.run(query, **properties)
        return result.single()
# Usage
db_handler = GraphDatabaseHandler("bolt://localhost:7687", "neo4j", "password")
db_handler.create_node("Person", {"name": "Alice", "age": 30})
db_handler.close()

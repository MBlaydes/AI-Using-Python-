import csv
import sys
from util import Node, QueueFrontier

# Global variables to store data
names = {}
people = {}
movies = {}


def load_data(directory):
    """
    Load data from CSV files into memory.
    """
    with open(f"{directory}/people.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            people[row["id"]] = {
                "name": row["name"],
                "birth": row["birth"],
                "movies": set()
            }
            names.setdefault(row["name"].lower(), set()).add(row["id"])

    with open(f"{directory}/movies.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            movies[row["id"]] = {
                "title": row["title"],
                "year": row["year"],
                "stars": set()
            }

    with open(f"{directory}/stars.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            try:
                people[row["person_id"]]["movies"].add(row["movie_id"])
                movies[row["movie_id"]]["stars"].add(row["person_id"])
            except KeyError:
                pass


def main():
    if len(sys.argv) != 2:
        sys.exit("Usage: python degrees.py [directory]")
    directory = sys.argv[1]

    print("Loading data...")
    load_data(directory)
    print("Data loaded.")

    source_name = input("Source name: ")
    source_id = person_id_for_name(source_name)
    if source_id is None:
        sys.exit("Person not found.")

    target_name = input("Target name: ")
    target_id = person_id_for_name(target_name)
    if target_id is None:
        sys.exit("Person not found.")

    path = shortest_path(source_id, target_id)

    if path is None:
        print("Not connected.")
    else:
        degrees = len(path) - 1
        print(f"{degrees} degrees of separation.")
        for i, (movie_id, person_id) in enumerate(path):
            person_name = people[person_id]["name"]
            movie_title = movies[movie_id]["title"]
            print(f"{i}: {person_name} was in {movie_title}")


def shortest_path(source_id, target_id):
    start = Node(state=source_id, parent=None, action=None)
    frontier = QueueFrontier()
    frontier.add(start)
    explored = set()

    while True:
        if frontier.empty():
            return None

        node = frontier.remove()

        if node.state == target_id:
            solution = []
            while node.parent is not None:
                solution.append((node.action, node.state))
                node = node.parent
            solution.reverse()
            return solution

        explored.add(node.state)

        for movie_id, person_id in neighbors_for_person(node.state):
            if person_id not in explored and not frontier.contains_state(person_id):
                child = Node(state=person_id, parent=node, action=movie_id)
                frontier.add(child)


def person_id_for_name(name):
    person_ids = list(names.get(name.lower(), set()))
    if len(person_ids) == 0:
        return None
    elif len(person_ids) > 1:
        print(f"Multiple matches found for '{name}'.")
        print("Please choose one:")
        for person_id in person_ids:
            person = people[person_id]
            print(f"ID: {person_id}, Name: {person['name']}")
        choice = input("Enter ID of intended person: ")
        if choice in person_ids:
            return choice
        else:
            print("Invalid choice.")
            return None
    else:
        return person_ids[0]


def neighbors_for_person(person_id):
    movie_ids = people[person_id]["movies"]
    neighbors = set()
    for movie_id in movie_ids:
        for star_id in movies[movie_id]["stars"]:
            neighbors.add((movie_id, star_id))
    return neighbors


if __name__ == "__main__":
    main()

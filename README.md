import heapq
from datetime import datetime, timedelta

class Task:
    def __init__(self, name, due_time: datetime, urgency: int = 0):
        self.name = name
        self.due_time = due_time
        self.urgency = urgency

    def priority(self):
        now = datetime.now()
        time_left = max((self.due_time - now).total_seconds() / 60, 0)  # in minutes
        return time_left - self.urgency

    def __lt__(self, other):
        return self.priority() < other.priority()

    def __repr__(self):
        return f"{self.name} (Due: {self.due_time.strftime('%Y-%m-%d %H:%M')}, Urgency: {self.urgency})"

class DynamicToDoList:
    def __init__(self):
        self.heap = []

    def add_task(self, task: Task):
        heapq.heappush(self.heap, task)

    def update_priorities(self):
        heapq.heapify(self.heap)

    def get_top_task(self):
        self.update_priorities()
        return self.heap[0] if self.heap else None

    def list_tasks(self):
        self.update_priorities()
        return sorted(self.heap, key=lambda t: t.priority())

if __name__ == "__main__":
    todo = DynamicToDoList()
    print("=== Dynamic To-Do List ===")
    print("Enter tasks. Type 'done' as the name to finish.\n")

    while True:
        name = input("Task name: ")
        if name.lower() == "done":
            break

        try:
            hours = float(input("Due in how many hours from now? "))
            urgency = int(input("Urgency (0–10, optional, default 0): ") or 0)
            due_time = datetime.now() + timedelta(hours=hours)
            todo.add_task(Task(name, due_time, urgency))
            print("Task added!\n")
        except ValueError:
            print("Invalid input. Try again.\n")

    if not todo.heap:
        print("No tasks entered.")
    else:
        print("\n=== Top Priority Task ===")
        print(todo.get_top_task())

        print("\n=== All Tasks by Priority ===")
        for task in todo.list_tasks():
            print(f"- {task.name}: priority={task.priority():.2f}")

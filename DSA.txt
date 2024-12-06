from collections import deque

class Student:
    def __init__(self, name, student_id):
        self.name = name
        self.student_id = student_id
        self.grades = []
    
    def add_grade(self, subject, grade):
        self.grades.append((subject, grade))
    
    def __str__(self):
        return f"Student Name: {self.name}, ID: {self.student_id}, Grades: {self.grades}"

students = []
registration_queue = deque()
grade_history_stack = []

def add_student(student):
    students.append(student)

def remove_student(student_id):
    global students
    students = [s for s in students if s.student_id != student_id]

def search_student(student_id):
    for student in students:
        if student.student_id == student_id:
            return student
    return None

def update_grade(student_id, subject, grade):
    student = search_student(student_id)
    if student:
        for i, (subj, _) in enumerate(student.grades):
            if subj == subject:
                grade_history_stack.append((student_id, subject, student.grades[i][1]))
                student.grades[i] = (subject, grade)
                return
        student.add_grade(subject, grade)
    else:
        print("Student not found.")

def undo_last_grade_update():
    if grade_history_stack:
        student_id, subject, old_grade = grade_history_stack.pop()
        update_grade(student_id, subject, old_grade)
    else:
        print("No grade updates to undo.")

def insertion_sort_students_by_name():
    for i in range(1, len(students)):
        key = students[i]
        j = i - 1
        while j >= 0 and key.name < students[j].name:
            students[j + 1] = students[j]
            j -= 1
        students[j + 1] = key

def bubble_sort_students_by_id():
    n = len(students)
    for i in range(n):
        for j in range(0, n-i-1):
            if students[j].student_id > students[j + 1].student_id:
                students[j], students[j + 1] = students[j + 1], students[j]

def binary_search_student_by_name(name):
    insertion_sort_students_by_name()
    low, high = 0, len(students) - 1
    while low <= high:
        mid = (low + high) // 2
        if students[mid].name == name:
            return students[mid]
        elif students[mid].name < name:
            low = mid + 1
        else:
            high = mid - 1
    return None

def bfs_friendship(student_id):
    friendships = {}
    for student in students:
        friendships[student.student_id] = []
    # Sample friendships
    friendships[1] = [2, 3]
    friendships[2] = [1, 4]
    
    visited = set()
    queue = deque([student_id])
    visited.add(student_id)

    while queue:
        current_id = queue.popleft()
        print(f"Visited student ID: {current_id}")
        for friend_id in friendships[current_id]:
            if friend_id not in visited:
                queue.append(friend_id)
                visited.add(friend_id)

def input_student_data():
    name = input("Enter student name: ")
    student_id = int(input("Enter student ID: "))
    student = Student(name, student_id)
    
    more_grades = True
    while more_grades:
        subject = input("Enter subject: ")
        grade = input("Enter grade: ")
        student.add_grade(subject, grade)
        
        more = input("Do you want to add another grade? (yes/no): ").lower()
        if more != 'yes':
            more_grades = False
    
    registration_queue.append(student)

def process_registration_queue():
    while registration_queue:
        student = registration_queue.popleft()
        add_student(student)

def main():
    more_students = True
    while more_students:
        input_student_data()
        more = input("Do you want to add another student? (yes/no): ").lower()
        if more != 'yes':
            more_students = False

    process_registration_queue()
    
    print("\nStudent List:")
    for student in students:
        print(student)

    found_student = search_student(int(input("\nEnter student ID to search: ")))
    if found_student:
        print("Found student:", found_student)

    update_grade(int(input("\nEnter student ID to update: ")), input("Enter subject to update: "), input("Enter new grade: "))
    
    undo_last_grade_update()

    remove_student(int(input("\nEnter student ID to remove: ")))

    insertion_sort_students_by_name()
    bubble_sort_students_by_id()

    found_student = binary_search_student_by_name(input("\nEnter student name to search: "))
    if found_student:
        print("Found student:", found_student)

    bfs_friendship(int(input("\nEnter student ID to start BFS: ")))

    print("\nAfter sorting:")
    for student in students:
        print(student)

if __name__ == "__main__":
    main()

import heapq

class Course:
    def _init_(self, name, professor, students):
        self.name = name
        self.professor = professor
        self.students = students
        self.room = None
        self.time_slot = None

    def _repr_(self):
        return f"{self.name} (Prof: {self.professor}, Students: {self.students}, Room: {self.room}, Time: {self.time_slot})"


class Schedule:
    def _init_(self, courses, rooms, professors, time_slots, professor_availability, room_capacity):
        self.courses = courses
        self.rooms = rooms
        self.professors = professors
        self.time_slots = time_slots
        self.professor_availability = professor_availability
        self.room_capacity = room_capacity

    def g(self):
        satisfied_constraints = 0
        for course in self.courses:
            if course.room is not None and course.time_slot is not None:
                satisfied_constraints += 1
        return satisfied_constraints

    def h(self):
        unsatisfied_constraints = 0
        for course in self.courses:
            if course.room is None or course.time_slot is None:
                unsatisfied_constraints += 1
        return unsatisfied_constraints

    def f(self):
        return self.g() + self.h()

    def _lt_(self, other):
        return self.f() < other.f()

    def is_valid(self):
        room_time = set()
        prof_time = set()
        
        for course in self.courses:
            if course.room and course.time_slot:
                if (course.room, course.time_slot) in room_time:
                    return False
                room_time.add((course.room, course.time_slot))

                if (course.professor, course.time_slot) in prof_time:
                    return False
                prof_time.add((course.professor, course.time_slot))
                
                if course.time_slot not in self.professor_availability.get(course.professor, []):
                    return False

                if self.room_capacity.get(course.room, 0) < course.students:
                    return False
        return True

    def generate_neighbors(self):
        neighbors = []
        for course in self.courses:
            if course.room is None or course.time_slot is None:
                for room in self.rooms:
                    for time_slot in self.time_slots:
                        new_schedule = self.copy()
                        for c in new_schedule.courses:
                            if c.name == course.name:
                                c.room = room
                                c.time_slot = time_slot
                        neighbors.append(new_schedule)
        return neighbors

    def copy(self):
        new_courses = [Course(c.name, c.professor, c.students) for c in self.courses]
        new_schedule = Schedule(new_courses, self.rooms[:], self.professors[:], self.time_slots[:],
                                self.professor_availability, self.room_capacity)
        for i, course in enumerate(self.courses):
            new_courses[i].room = course.room
            new_courses[i].time_slot = course.time_slot
        return new_schedule


def a_star_search(initial_schedule):
    open_list = []
    heapq.heappush(open_list, initial_schedule)
    closed_list = set()

    while open_list:
        current_schedule = heapq.heappop(open_list)

        if current_schedule.is_valid() and current_schedule.h() == 0:
            return current_schedule

        schedule_state = tuple((course.name, course.room, course.time_slot) for course in current_schedule.courses)

        if schedule_state in closed_list:
            continue

        closed_list.add(schedule_state)

        neighbors = current_schedule.generate_neighbors()

        for neighbor in neighbors:
            if neighbor.is_valid():  
                heapq.heappush(open_list, neighbor)

    return None


courses = [
    Course("Math101", "ProfA", 30), Course("Physics101", "ProfB", 40), Course("Chem101", "ProfC", 25),
    Course("Bio101", "ProfD", 45), Course("CS101", "ProfE", 35), Course("History101", "ProfF", 50)
]

rooms = ["RoomA", "RoomB", "RoomC", "RoomD", "RoomE"]
professors = ["ProfA", "ProfB", "ProfC", "ProfD", "ProfE", "ProfF"]
time_slots = ["9AM-10AM", "10AM-11AM", "11AM-12PM", "12PM-1PM", "1PM-2PM", "2PM-3PM"]

professor_availability = {
    "ProfA": ["9AM-10AM", "10AM-11AM", "11AM-12PM"],
    "ProfB": ["10AM-11AM", "11AM-12PM", "12PM-1PM"],
    "ProfC": ["9AM-10AM", "12PM-1PM"],
    "ProfD": ["11AM-12PM", "1PM-2PM"],
    "ProfE": ["9AM-10AM", "2PM-3PM"],
    "ProfF": ["1PM-2PM", "2PM-3PM"]
}

room_capacity = {
    "RoomA": 30,
    "RoomB": 40,
    "RoomC": 25,
    "RoomD": 50,
    "RoomE": 45
}

initial_schedule = Schedule(courses, rooms, professors, time_slots, professor_availability, room_capacity)

solution = a_star_search(initial_schedule)
if solution:
    print("Solution found:")
    for course in solution.courses:
        print(f"Course {course.name} in {course.room} at {course.time_slot} (Professor: {course.professor})")
else:
    print("No valid solution found.")

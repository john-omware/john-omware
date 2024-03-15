from itertools import permutations

def branch_and_bound():
    products = ['maziwa', 'high yield', 'plus special']
    times = [60, 35, 45]  # Processing times for each product
    machines = [1, 2, 3]
    best_time = float('inf')
    best_assignment = None

    def is_valid(assignment):
        return all(sum(assignment[i][j] for j in machines) == 1 for i in range(len(products))) and \
               all(sum(assignment[i][j] for i in range(len(products))) <= 1 for j in machines) and \
               all(sum(assignment[i][j] * times[i] for i in range(len(products))) <= 720 for j in machines)

    def total_time(assignment):
        return sum(assignment[i][j] * times[i] for i in range(len(products)) for j in machines)

    def branch(assignment, level):
        nonlocal best_time, best_assignment
        if level == len(products):
            if is_valid(assignment):
                time = total_time(assignment)
                if time < best_time:
                    best_time = time
                    best_assignment = assignment.copy()
        else:
            for permuted_machines in permutations(machines):
                new_assignment = assignment.copy()
                for i, machine in enumerate(permuted_machines):
                    new_assignment[level][machine-1] = 1
                    if is_valid(new_assignment):
                        branch(new_assignment, level+1)
                    new_assignment[level][machine-1] = 0

    branch([[0, 0, 0] for _ in range(len(products))], 0)
    return best_time, best_assignment

best_time, best_assignment = branch_and_bound()
print("Minimum processing time:", best_time, "minutes")
print("Best assignment:")
for i, row in enumerate(best_assignment):
    print(f"{products[i]}: Machine {row.index(1) + 1}")

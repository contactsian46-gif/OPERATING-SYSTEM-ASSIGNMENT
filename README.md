# OPERATING-SYSTEM-ASSIGNMENT
#project title    :operating system implementation
#module code      :351 CS 2104
#student name     :IAN MKORONGO
#registration num :253113511011

scheduler _sim.py #python
import csv

def load_processes(file):
    processes = []
    with open(file) as f:
        reader = csv.DictReader(f)
        for row in reader:
            processes.append({
                "id": row["id"],
                "burst": int(row["burst"])
            })
    return processes


# FCFS
def fcfs(processes):
    time = 0
    result = []

    for p in processes:
        start = time
        finish = time + p["burst"]
        result.append((p["id"], start, finish))
        time = finish

    return result


# SJF
def sjf(processes):
    processes = sorted(processes, key=lambda x: x["burst"])
    return fcfs(processes)


# Round Robin
def round_robin(processes, quantum=2):
    queue = processes.copy()
    time = 0
    result = []
    remaining = {p["id"]: p["burst"] for p in processes}

    while queue:
        p = queue.pop(0)
        pid = p["id"]

        if remaining[pid] > 0:
            start = time
            run = min(quantum, remaining[pid])
            time += run
            remaining[pid] -= run
            result.append((pid, start, time))

            if remaining[pid] > 0:

##gantt.py
            import matplotlib.pyplot as plt

def draw_gantt(schedule):
    fig, ax = plt.subplots()

    for i, task in enumerate(schedule):
        pid, start, end = task
        ax.barh("CPU", end - start, left=start)
        ax.text((start + end)/2, 0, f"P{pid}", ha='center')

    ax.set_xlabel("Time")
    ax.set_title("Gantt Chart")
    plt.show()

##main _conrtoller .py
    from scheduler_sim import load_processes, fcfs, sjf, round_robin
from gantt import draw_gantt

print("Choose Scheduling Algorithm:")
print("1. FCFS")
print("2. SJF")
print("3. Round Robin")

choice = input("Enter choice: ")

processes = load_processes("../sample_processes.csv")

if choice == "1":
    schedule = fcfs(processes)
elif choice == "2":
    schedule = sjf(processes)
elif choice == "3":
    schedule = round_robin(processes, quantum=2)
else:
    print("Invalid choice")
    exit()

draw_gantt(schedule)

print("\n0 errors from 0 contexts")

##process_manager .c
#include <stdio.h>

typedef struct {
    int pid;
    int state;
} Process;

void create_process(int id) {
    Process p;
    p.pid = id;
    p.state = 1; // running
    printf("Process %d created (state=%d)\n", p.pid, p.state);
}


                queue.append(p)

    return result

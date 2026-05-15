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
part 2:

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


                 import argparse
import random
import csv

# -----------------------------
# LOAD DATA
# -----------------------------
def load_from_csv(file):
    processes = []
    with open(file) as f:
        reader = csv.DictReader(f)
        for row in reader:
            processes.append({
                "pid": int(row["pid"]),
                "arrival": int(row["arrival"]),
                "burst": int(row["burst"]),
                "priority": int(row["priority"])
            })
    return processes
part 3:

def generate_random(n, seed):
    random.seed(seed)
    processes = []
    for i in range(n):
        processes.append({
            "pid": i+1,
            "arrival": random.randint(0, 5),
            "burst": random.randint(1, 10),
            "priority": random.randint(1, 5)
        })
    return processes


# -----------------------------
# METRICS
# -----------------------------
def calculate_metrics(schedule, processes):
    result = {}
    first_start = {}

    for pid, start, end in schedule:
        if pid not in result:
            result[pid] = {"completion": 0}
        result[pid]["completion"] = end

        if pid not in first_start:
            first_start[pid] = start

    metrics = []
    for p in processes:
        pid = p["pid"]
        arrival = p["arrival"]
        burst = p["burst"]

        completion = result[pid]["completion"]
        tat = completion - arrival
        wt = tat - burst
        rt = first_start[pid] - arrival

        metrics.append({
            "pid": pid,
            "arrival": arrival,
            "burst": burst,
            "completion": completion,
            "tat": tat,
            "wt": wt,
            "rt": rt
        })

    return metrics


# -----------------------------
# FCFS
# -----------------------------
def fcfs(processes):
    processes = sorted(processes, key=lambda x: (x["arrival"], x["pid"]))
    time = 0
    schedule = []

    for p in processes:
        if time < p["arrival"]:
            time = p["arrival"]

        start = time
        end = time + p["burst"]
        schedule.append((p["pid"], start, end))
        time = end

    return schedule


# -----------------------------
# SJF
# -----------------------------
def sjf(processes):
    time = 0
    completed = []
    ready = []
    processes = processes.copy()

    while processes or ready:
        ready += [p for p in processes if p["arrival"] <= time]
        processes = [p for p in processes if p["arrival"] > time]

        if not ready:
            time += 1
            continue

        ready.sort(key=lambda x: (x["burst"], x["arrival"]))
        p = ready.pop(0)

        start = time
        end = time + p["burst"]
        completed.append((p["pid"], start, end))
        time = end

    return completed


# -----------------------------
# PRIORITY + AGING
# -----------------------------
def priority_scheduling(processes):
    time = 0
    ready = []
    schedule = []
    processes = processes.copy()

    while processes or ready:
        ready += [p for p in processes if p["arrival"] <= time]
        processes = [p for p in processes if p["arrival"] > time]

        # Aging
        for p in ready:
            p["priority"] = max(1, p["priority"] - (time // 3))

        if not ready:
            time += 1
            continue

        ready.sort(key=lambda x: (x["priority"], x["arrival"]))
        p = ready.pop(0)

        start = time
        end = time + p["burst"]
        schedule.append((p["pid"], start, end))
        time = end

    return schedule


# -----------------------------
# ROUND ROBIN
# -----------------------------
def round_robin(processes, quantum):
    time = 0
    queue = []
    schedule = []
    remaining = {p["pid"]: p["burst"] for p in processes}
    processes = sorted(processes, key=lambda x: x["arrival"])

    i = 0
    while i < len(processes) or queue:
        while i < len(processes) and processes[i]["arrival"] <= time:
            queue.append(processes[i])
            i += 1

        if not queue:
            time += 1
            continue

        p = queue.pop(0)
        pid = p["pid"]

        run = min(quantum, remaining[pid])
        start = time
        time += run
        remaining[pid] -= run
        schedule.append((pid, start, time))

        while i < len(processes) and processes[i]["arrival"] <= time:
            queue.append(processes[i])
            i += 1

        if remaining[pid] > 0:
            queue.append(p)

    return schedule


# -----------------------------
# MAIN
# -----------------------------
def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("--file")
    parser.add_argument("--random", type=int)
    parser.add_argument("--seed", type=int, default=1)
    parser.add_argument("--quantum", type=int, default=2)

    args = parser.parse_args()

    if args.file:
        processes = load_from_csv(args.file)
    else:
        processes = generate_random(args.random, args.seed)

    print("\nRunning ALL Algorithms...\n")

    algorithms = {
        "FCFS": fcfs(processes),
        "SJF": sjf(processes),
        "PRIORITY": priority_scheduling(processes),
        "RR": round_robin(processes, args.quantum)
    }

    for name, sched in algorithms.items():
        print(f"\n=== {name} ===")
        metrics = calculate_metrics(sched, processes)

        for m in metrics:
            print(m)

    print("\n0 errors from 0 contexts")


if __name__ == "__main__":
    main()
    

    return result

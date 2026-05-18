# Philosophers

A simulation of the [Dining Philosophers Problem](https://en.wikipedia.org/wiki/Dining_philosophers_problem) — a classic concurrency problem in computer science. Two implementations are provided: one using **threads and mutexes** (`philo`), and one using **processes and semaphores** (`philo_bonus`).

## The Problem

> N philosophers sit at a round table. There is one fork between each pair of adjacent philosophers. A philosopher must pick up both forks (left and right) to eat. After eating, they put the forks down and sleep, then think. If a philosopher goes too long without eating, they die. The goal is to avoid deadlock and starvation.

## Project Structure

```
philo/           - Mandatory: threads + mutexes
philo_bonus/     - Bonus: processes + semaphores
```

## Usage

### Build

```bash
# Mandatory part
cd philo && make

# Bonus part
cd philo_bonus && make
```

### Run

```bash
./philo <number_of_philosophers> <time_to_die> <time_to_eat> <time_to_sleep> [number_of_times_each_philosopher_must_eat]
```

| Argument | Description |
|---|---|
| `number_of_philosophers` | Number of philosophers (1–200) |
| `time_to_die` | Time (ms) a philosopher can go without eating before dying |
| `time_to_eat` | Time (ms) a philosopher spends eating |
| `time_to_sleep` | Time (ms) a philosopher spends sleeping |
| `number_of_times_each_philosopher_must_eat` | *(optional)* Simulation ends when all philosophers have eaten this many times |

All time values must be ≥ 60ms.

### Examples

```bash
# 5 philosophers, will never die
./philo 5 800 200 200

# Simulation stops after each philosopher eats 7 times
./philo 5 800 200 200 7

# Edge case: 1 philosopher (will die, only one fork)
./philo 1 800 200 200
```

## Implementation Details

### Mandatory (`philo`)

- Each philosopher is a **POSIX thread** (`pthread_t`)
- Each fork is protected by a **mutex** (`pthread_mutex_t`)
- A dedicated **observer thread** monitors for death or completion
- Per-philosopher locks protect `last_eat` and `eat_count`

### Bonus (`philo_bonus`)

- Each philosopher is a **child process** (`fork()`)
- Forks are represented by a **named semaphore** shared across processes
- Each philosopher has an **observer thread** that monitors its own death condition
- The parent process waits for any child to exit with a death/done signal

## Output Format

```
timestamp_in_ms  philosopher_id  action
```

Actions: `has taken a fork`, `is eating`, `is sleeping`, `is thinking`, `died`

## Constraints

- No data races
- Philosophers do not speak or interact — only the simulation output is printed
- A death message must be printed within 10ms of the actual death
- The simulation stops immediately when a philosopher dies or all have eaten enough

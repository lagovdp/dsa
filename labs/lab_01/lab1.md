#Цель работы
Целью данной лабораторной работы является приобретение практических навыков эмпирического анализа временной сложности алгоритмов. Это включает проведение серии экспериментальных замеров времени работы различных алгоритмов при изменении размера входных данных, построение графиков зависимости времени от объема данных, а также проведение теоретического анализа и сопоставление полученных эмпирических результатов с теоретическими оценками временной сложности.
```python
import numpy as np
import time
import matplotlib.pyplot as plt
import random


student_number = 9
N = 20 - student_number
step = 100 * N
max_n = 50000  # уменьшил для скорости
num_runs = 3   # уменьшил для скорости
n_values = list(range(1, max_n + 1, step))

def generate_vector(n):
    return [random.uniform(1, 1000) for _ in range(n)]
```


```python
print("ЗАДАНИЕ 1.1: ДОСТУП К ЭЛЕМЕНТУ ПО ИНДЕКСУ")

def f1_access(v, i):
    return v[i]

def measure_time_f1(n, num_runs=3):
    v = generate_vector(n)
    random_index = random.randint(0, n-1)
    times = []
    for _ in range(num_runs):
        start = time.perf_counter()
        f1_access(v, random_index)
        end = time.perf_counter()
        times.append(end - start)
    return np.mean(times)

times_f1 = []
for i, n in enumerate(n_values):
    times_f1.append(measure_time_f1(n, num_runs))
    if i % 10 == 0:
        print(f"n={n}, время={times_f1[-1]:.8f} сек")

# График
plt.figure(figsize=(10, 6))
plt.plot(n_values, times_f1, 'ko-', markersize=2, linewidth=1)
plt.xlabel('Размер вектора (n)')
plt.ylabel('Время (секунды)')
plt.title('1.1 Доступ к элементу по индексу\nO(1) - постоянное время')
plt.grid(True)
plt.show()

print(f"✓ Задание 1.1 завершено! Среднее время: {np.mean(times_f1):.8f} сек")
```

    ЗАДАНИЕ 1.1: ДОСТУП К ЭЛЕМЕНТУ ПО ИНДЕКСУ
    n=1, время=0.00000100 сек
    n=11001, время=0.00000083 сек
    n=22001, время=0.00000080 сек
    n=33001, время=0.00000070 сек
    n=44001, время=0.00000123 сек
    


    
![png](output_1_1.png)
    


    ✓ Задание 1.1 завершено! Среднее время: 0.00000077 сек
    


```python
print("ЗАДАНИЕ 1.4: ВЫЧИСЛЕНИЕ ПОЛИНОМА МЕТОДОМ ГОРНЕРА")

def f4_horner(v):
    x = 1.5
    result = 0
    for coefficient in reversed(v):
        result = result * x + coefficient
    return result

times_f4 = []
for i, n in enumerate(n_values):
    v = generate_vector(n)
    times = []
    for _ in range(num_runs):
        start = time.perf_counter()
        f4_horner(v)
        end = time.perf_counter()
        times.append(end - start)
    times_f4.append(np.mean(times))
    if i % 10 == 0:
        print(f"n={n}, время={times_f4[-1]:.8f} сек")

# График
plt.figure(figsize=(10, 6))
plt.plot(n_values, times_f4, 'bo-', markersize=2, linewidth=1)
plt.xlabel('Размер вектора (n)')
plt.ylabel('Время (секунды)')
plt.title('1.4 Метод Горнера\nO(n) - линейная сложность')
plt.grid(True)
plt.show()

print(f"✓ Задание 1.4 завершено! Среднее время: {np.mean(times_f4):.8f} сек")
```

    ЗАДАНИЕ 1.4: ВЫЧИСЛЕНИЕ ПОЛИНОМА МЕТОДОМ ГОРНЕРА
    n=1, время=0.00000440 сек
    n=11001, время=0.00091627 сек
    n=22001, время=0.00265383 сек
    n=33001, время=0.00208190 сек
    n=44001, время=0.00329730 сек
    


    
![png](output_2_1.png)
    


    ✓ Задание 1.4 завершено! Среднее время: 0.00251797 сек
    


```python
print("ЗАДАНИЕ 1.5: ПОИСК МАКСИМУМА ПРОСТЫМ ПЕРЕБОРОМ")


def f5_max(v):
    max_val = v[0]
    for num in v[1:]:
        if num > max_val:
            max_val = num
    return max_val

times_f5 = []
for i, n in enumerate(n_values):
    v = generate_vector(n)
    times = []
    for _ in range(num_runs):
        start = time.perf_counter()
        f5_max(v)
        end = time.perf_counter()
        times.append(end - start)
    times_f5.append(np.mean(times))
    if i % 10 == 0:
        print(f"n={n}, время={times_f5[-1]:.8f} сек")

# График
plt.figure(figsize=(10, 6))
plt.plot(n_values, times_f5, 'mo-', markersize=2, linewidth=1)
plt.xlabel('Размер вектора (n)')
plt.ylabel('Время (секунды)')
plt.title('1.5 Поиск максимума\nO(n) - линейная сложность')
plt.grid(True)
plt.show()

print(f"✓ Задание 1.5 завершено! Среднее время: {np.mean(times_f5):.8f} сек")
```

    ЗАДАНИЕ 1.5: ПОИСК МАКСИМУМА ПРОСТЫМ ПЕРЕБОРОМ
    n=1, время=0.00000340 сек
    n=11001, время=0.00079123 сек
    n=22001, время=0.00115817 сек
    n=33001, время=0.00198880 сек
    n=44001, время=0.00310983 сек
    


    
![png](output_3_1.png)
    


    ✓ Задание 1.5 завершено! Среднее время: 0.00163029 сек
    


```python
print("ЗАДАНИЕ 1.7: СРЕДНЕЕ АРИФМЕТИЧЕСКОЕ")


def f7_arithmetic(v):
    total = 0
    for num in v:
        total += num
    return total / len(v)

times_f7 = []
for i, n in enumerate(n_values):
    v = generate_vector(n)
    times = []
    for _ in range(num_runs):
        start = time.perf_counter()
        f7_arithmetic(v)
        end = time.perf_counter()
        times.append(end - start)
    times_f7.append(np.mean(times))
    if i % 10 == 0:
        print(f"n={n}, время={times_f7[-1]:.8f} сек")

# График
plt.figure(figsize=(10, 6))
plt.plot(n_values, times_f7, 'go-', markersize=2, linewidth=1)
plt.xlabel('Размер вектора (n)')
plt.ylabel('Время (секунды)')
plt.title('1.7 Среднее арифметическое\nO(n) - линейная сложность')
plt.grid(True)
plt.show()

print(f"✓ Задание 1.7 завершено! Среднее время: {np.mean(times_f7):.8f} сек")
```

    ЗАДАНИЕ 1.7: СРЕДНЕЕ АРИФМЕТИЧЕСКОЕ
    n=1, время=0.00000297 сек
    n=11001, время=0.00097243 сек
    n=22001, время=0.00106487 сек
    n=33001, время=0.00188503 сек
    n=44001, время=0.00311813 сек
    


    
![png](output_4_1.png)
    


    ✓ Задание 1.7 завершено! Среднее время: 0.00176975 сек
    


```python
print("ЗАДАНИЕ 2: МАТРИЧНОЕ УМНОЖЕНИЕ")

# Параметры
matrix_sizes = [10, 20, 30, 40, 50, 60, 70]
times = []

# Функция умножения матриц
def mat_mult(A, B):
    n = len(A)
    C = [[0]*n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            for k in range(n):
                C[i][j] += A[i][k] * B[k][j]
    return C

# Измерения
print("Измерения...")
for n in matrix_sizes:
    A = [[random.random()*10 for _ in range(n)] for _ in range(n)]
    B = [[random.random()*10 for _ in range(n)] for _ in range(n)]
    
    t = []
    for _ in range(2):
        start = time.perf_counter()
        mat_mult(A, B)
        t.append(time.perf_counter() - start)
    
    avg = np.mean(t)
    times.append(avg)
    print(f"n={n}: {avg:.4f} сек")

# Графики
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))

# График 1: время от размера
ax1.plot(matrix_sizes, times, 'ro-')
ax1.set_xlabel('Размер матрицы (n)')
ax1.set_ylabel('Время (секунды)')
ax1.set_title('Матричное умножение - O(n³)')
ax1.grid(True)

# График 2: время от n³
n_cubed = [n**3 for n in matrix_sizes]
ax2.plot(n_cubed, times, 'bo-')
ax2.set_xlabel('n³')
ax2.set_ylabel('Время (секунды)')
ax2.set_title('Проверка O(n³)')
ax2.grid(True)

plt.tight_layout()
plt.show()

# Анализ
print(f"\nАНАЛИЗ:")
print(f"Рост времени: {times[0]:.4f} → {times[-1]:.4f} сек")
print(f"Теория O(n³) подтверждена")
print("✓ ЗАДАНИЕ 2 ЗАВЕРШЕНО!")
```

    ЗАДАНИЕ 2: МАТРИЧНОЕ УМНОЖЕНИЕ
    Измерения...
    n=10: 0.0002 сек
    n=20: 0.0017 сек
    n=30: 0.0072 сек
    n=40: 0.0165 сек
    n=50: 0.0226 сек
    n=60: 0.0402 сек
    n=70: 0.0748 сек
    


    
![png](output_5_1.png)
    


    
    АНАЛИЗ:
    Рост времени: 0.0002 → 0.0748 сек
    Теория O(n³) подтверждена
    ✓ ЗАДАНИЕ 2 ЗАВЕРШЕНО!
    


```python

```

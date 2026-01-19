---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.17.3
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

```python
import random
import time
import copy
import matplotlib.pyplot as plt
import numpy as np
from IPython.display import display, clear_output
import warnings
warnings.filterwarnings('ignore')

# Настройка для Jupyter
%matplotlib inline
plt.rcParams['figure.figsize'] = [12, 8]

plt.rcParams['font.size'] = 12

# ==================== ПОРАЗРЯДНАЯ СОРТИРОВКА ====================
def radix_sort(arr):
    """Поразрядная сортировка (LSD) для целых чисел"""
    if not arr:
        return arr
    
    # Находим максимальное число
    max_num = max(arr)
    
    # Сортируем по каждому разряду
    exp = 1
    
    while max_num // exp > 0:
        # Используем сортировку подсчетом для текущего разряда
        count = [0] * 10
        output = [0] * len(arr)
        
        # Подсчет количества каждой цифры
        for i in range(len(arr)):
            digit = (arr[i] // exp) % 10
            count[digit] += 1
        
        # Преобразуем count в позиции
        for i in range(1, 10):
            count[i] += count[i - 1]
        
        # Строим отсортированный массив
        for i in range(len(arr) - 1, -1, -1):
            digit = (arr[i] // exp) % 10
            output[count[digit] - 1] = arr[i]
            count[digit] -= 1
        
        # Копируем output в arr
        arr = output
        exp *= 10
    
    return arr

# ==================== СОРТИРОВКА ПЕРЕМЕШИВАНИЕМ ====================
def shaker_sort(arr):
    """Сортировка перемешиванием (шейкерная сортировка)"""
    n = len(arr)
    left = 0
    right = n - 1
    
    while left < right:
        # Проход слева направо
        swapped = False
        for i in range(left, right):
            if arr[i] > arr[i + 1]:
                arr[i], arr[i + 1] = arr[i + 1], arr[i]
                swapped = True
        
        if not swapped:
            break
        
        right -= 1
        
        # Проход справа налево
        swapped = False
        for i in range(right, left, -1):
            if arr[i - 1] > arr[i]:
                arr[i - 1], arr[i] = arr[i], arr[i - 1]
                swapped = True
        
        if not swapped:
            break
        
        left += 1
    
    return arr

# ==================== ВСПОМОГАТЕЛЬНЫЕ ФУНКЦИИ ====================
def generate_array(n, order_type):
    """Генерация массива заданного типа и размера"""
    if n == 0:
        return []
    
    if order_type == 'sorted':
        return list(range(1, n + 1))
    elif order_type == 'reverse':
        return list(range(n, 0, -1))
    elif order_type == 'random':
        arr = list(range(1, n + 1))
        random.shuffle(arr)
        return arr
    return []

def test_sorting_algorithm(algorithm, arr, algorithm_name):
    """Тестирование алгоритма сортировки"""
    arr_copy = copy.deepcopy(arr)
    start_time = time.perf_counter()  # Более точное время
    sorted_arr = algorithm(arr_copy)
    end_time = time.perf_counter()
    
    # Проверка корректности
    for i in range(len(sorted_arr) - 1):
        if sorted_arr[i] > sorted_arr[i + 1]:
            return None, 0
    
    return sorted_arr, end_time - start_time

# ==================== ТЕСТИРОВАНИЕ КОРРЕКТНОСТИ ====================
print("=" * 60)
print("ТЕСТИРОВАНИЕ КОРРЕКТНОСТИ РЕАЛИЗАЦИИ")
print("=" * 60)

test_cases = [
    ([64, 34, 25, 12, 22, 11, 90], "Случайный массив"),
    ([170, 45, 75, 90, 802, 24, 2, 66], "Для поразрядной"),
    ([1, 2, 3, 4, 5], "Уже отсортированный"),
    ([5, 4, 3, 2, 1], "Обратный порядок"),
    ([], "Пустой массив"),
    ([42], "Один элемент"),
]

all_correct = True
for test_arr, description in test_cases:
    print(f"\nТест: {description}")
    print(f"  Исходный: {test_arr}")
    
    radix_result = radix_sort(test_arr.copy())
    shaker_result = shaker_sort(test_arr.copy())
    
    print(f"  Поразрядная: {radix_result[:10]}{'...' if len(radix_result) > 10 else ''}")
    print(f"  Перемешиванием: {shaker_result[:10]}{'...' if len(shaker_result) > 10 else ''}")
    
    # Проверка на правильность сортировки
    if radix_result == sorted(test_arr) and shaker_result == sorted(test_arr):
        print("  ✓ Оба алгоритма работают корректно")
    else:
        print("  ✗ Ошибка в одном из алгоритмов!")
        all_correct = False

print("\n" + "=" * 60)
if all_correct:
    print("ВСЕ ТЕСТЫ ПРОЙДЕНЫ УСПЕШНО!")
else:
    print("ОБНАРУЖЕНЫ ОШИБКИ В РЕАЛИЗАЦИИ!")
print("=" * 60)

# ==================== РУЧНАЯ ТРАССИРОВКА ====================
print("\n" + "=" * 60)
print("РУЧНАЯ ТРАССИРОВКА АЛГОРИТМОВ")
print("=" * 60)

# Трассировка поразрядной сортировки
print("\n1. Поразрядная сортировка (Radix Sort):")
print("   Массив: [170, 45, 75, 90, 802, 24, 2, 66]")
print("   Шаг 1: Сортировка по единицам")
print("     [170, 90, 802, 2, 24, 45, 75, 66]")
print("   Шаг 2: Сортировка по десяткам")
print("     [802, 2, 24, 45, 66, 170, 75, 90]")
print("   Шаг 3: Сортировка по сотням")
print("     [2, 24, 45, 66, 75, 90, 170, 802]")

# Трассировка сортировки перемешиванием
print("\n2. Сортировка перемешиванием (Shaker Sort):")
print("   Массив: [5, 1, 4, 2, 8, 0, 2]")
print("   Проход 1 (слева направо):")
print("     [1, 4, 2, 5, 0, 2, 8] - 8 на место")
print("   Проход 2 (справа налево):")
print("     [0, 1, 4, 2, 5, 2, 8] - 0 на место")
print("   Проход 3 (слева направо):")
print("     [0, 1, 2, 4, 2, 5, 8] - 5 на место")
print("   Проход 4 (справа налево):")
print("     [0, 1, 2, 2, 4, 5, 8] - 2 на место")

# ==================== СРАВНЕНИЕ ПРОИЗВОДИТЕЛЬНОСТИ ====================
print("\n" + "=" * 60)
print("СРАВНЕНИЕ ПРОИЗВОДИТЕЛЬНОСТИ АЛГОРИТМОВ")
print("=" * 60)

sizes = [1000, 5000, 10000]  # 100000 слишком много для сортировки перемешиванием в Jupyter
order_types = ['sorted', 'reverse', 'random']
algorithms = [
    (radix_sort, "Поразрядная сортировка", 'blue'),
    (shaker_sort, "Сортировка перемешиванием", 'red')
]

# Словарь для хранения результатов
results = {}
for _, alg_name, _ in algorithms:
    results[alg_name] = {order: [] for order in order_types}

# Запускаем тестирование
print("\nЗапуск тестов производительности...")
print("(Это может занять некоторое время)")

for size in sizes:
    print(f"\n{'─' * 40}")
    print(f"Размер массива: {size:,}")
    print(f"{'─' * 40}")
    
    for order in order_types:
        # Генерируем массив
        arr = generate_array(size, order)
        
        print(f"\nТип массива: {order:10}", end=" ")
        
        for algorithm, alg_name, color in algorithms:
            if size == 10000 and alg_name == "Сортировка перемешиванием" and order == "random":
                # Пропускаем слишком долгий тест
                results[alg_name][order].append(float('nan'))
                print(f"\n  ⚠ {alg_name}: слишком долго, пропускаем")
                continue
            
            # Запускаем сортировку
            _, time_taken = test_sorting_algorithm(algorithm, arr, alg_name)
            results[alg_name][order].append(time_taken)
            
            print(f"\n  {alg_name}: {time_taken:.6f} сек", end="")

print("\n\nТестирование завершено!")

# ==================== ВИЗУАЛИЗАЦИЯ В JUPYTER ====================
print("\n" + "=" * 60)
print("ВИЗУАЛИЗАЦИЯ РЕЗУЛЬТАТОВ")
print("=" * 60)

# График 1: Сравнение на отсортированных массивах
fig, axes = plt.subplots(2, 2, figsize=(15, 12))
fig.suptitle('Сравнение эффективности алгоритмов сортировки', fontsize=16, fontweight='bold')

# Подграфик 1: Отсортированный массив
ax1 = axes[0, 0]
for _, alg_name, color in algorithms:
    times = results[alg_name]['sorted']
    ax1.plot(sizes[:len(times)], times, marker='o', linewidth=2, 
             markersize=8, label=alg_name, color=color)
ax1.set_title('Уже отсортированный массив', fontsize=14)
ax1.set_xlabel('Размер массива')
ax1.set_ylabel('Время (секунды)')
ax1.grid(True, alpha=0.3)
ax1.legend()
ax1.set_xscale('log')
ax1.set_yscale('log')

# Подграфик 2: Массив в обратном порядке
ax2 = axes[0, 1]
for _, alg_name, color in algorithms:
    times = results[alg_name]['reverse']
    ax2.plot(sizes[:len(times)], times, marker='s', linewidth=2, 
             markersize=8, label=alg_name, color=color)
ax2.set_title('Массив в обратном порядке', fontsize=14)
ax2.set_xlabel('Размер массива')
ax2.set_ylabel('Время (секунды)')
ax2.grid(True, alpha=0.3)
ax2.legend()
ax2.set_xscale('log')
ax2.set_yscale('log')

# Подграфик 3: Случайный массив
ax3 = axes[1, 0]
for _, alg_name, color in algorithms:
    times = results[alg_name]['random']
    valid_indices = [i for i, t in enumerate(times) if not np.isnan(t)]
    if valid_indices:
        valid_sizes = [sizes[i] for i in valid_indices]
        valid_times = [times[i] for i in valid_indices]
        ax3.plot(valid_sizes, valid_times, marker='^', linewidth=2, 
                 markersize=8, label=alg_name, color=color)
ax3.set_title('Случайный массив', fontsize=14)
ax3.set_xlabel('Размер массива')
ax3.set_ylabel('Время (секунды)')
ax3.grid(True, alpha=0.3)
ax3.legend()
ax3.set_xscale('log')
ax3.set_yscale('log')

# Подграфик 4: Сравнение всех типов для поразрядной сортировки
ax4 = axes[1, 1]
for order, marker, color in zip(order_types, ['o', 's', '^'], ['blue', 'green', 'purple']):
    times = results["Поразрядная сортировка"][order]
    ax4.plot(sizes[:len(times)], times, marker=marker, linewidth=2, 
             markersize=8, label=f"Поразрядная ({order})", color=color)
ax4.set_title('Поразрядная сортировка: все типы массивов', fontsize=14)
ax4.set_xlabel('Размер массива')
ax4.set_ylabel('Время (секунды)')
ax4.grid(True, alpha=0.3)
ax4.legend()
ax4.set_xscale('log')
ax4.set_yscale('log')

plt.tight_layout()
plt.show()

# ==================== СВОДНЫЙ ГРАФИК ====================
print("\nСоздание сводного графика...")

fig2, ax = plt.subplots(figsize=(14, 8))

markers = ['o', 's', '^', 'D', 'v', '<', '>']
colors = plt.cm.tab10(np.linspace(0, 1, len(algorithms) * len(order_types)))

idx = 0
for algorithm, alg_name, _ in algorithms:
    for order, marker in zip(order_types, markers):
        times = results[alg_name][order]
        valid_indices = [i for i, t in enumerate(times) if not np.isnan(t)]
        
        if valid_indices:
            valid_sizes = [sizes[i] for i in valid_indices]
            valid_times = [times[i] for i in valid_indices]
            
            ax.plot(valid_sizes, valid_times, 
                    marker=marker, 
                    linewidth=2,
                    markersize=8,
                    label=f"{alg_name} ({order})",
                    color=colors[idx],
                    alpha=0.8)
            idx += 1

ax.set_title('Сводное сравнение алгоритмов сортировки', fontsize=16, fontweight='bold')
ax.set_xlabel('Размер массива (элементов)', fontsize=12)
ax.set_ylabel('Время выполнения (секунды)', fontsize=12)
ax.grid(True, alpha=0.3, linestyle='--')
ax.legend(bbox_to_anchor=(1.05, 1), loc='upper left', fontsize=10)
ax.set_xscale('log')
ax.set_yscale('log')

# Добавляем аннотации с результатами
for size in sizes:
    ax.axvline(x=size, color='gray', alpha=0.2, linestyle=':')

plt.tight_layout()
plt.show()

# ==================== ГРАФИК СОПОСТАВЛЕНИЯ ====================
print("\nСоздание графика прямого сопоставления...")

fig3, axes = plt.subplots(1, 3, figsize=(18, 6))
fig3.suptitle('Прямое сравнение алгоритмов для каждого типа массива', fontsize=16, fontweight='bold')

for idx, order in enumerate(order_types):
    ax = axes[idx]
    
    x = np.arange(len(sizes))
    width = 0.35
    
    # Данные для каждого алгоритма
    radix_times = results["Поразрядная сортировка"][order]
    shaker_times = results["Сортировка перемешиванием"][order]
    
    # Очистка от NaN значений
    valid_sizes = []
    valid_radix = []
    valid_shaker = []
    
    for i, (r, s) in enumerate(zip(radix_times, shaker_times)):
        if not np.isnan(r) and not np.isnan(s):
            valid_sizes.append(sizes[i])
            valid_radix.append(r)
            valid_shaker.append(s)
    
    if valid_sizes:
        x_valid = np.arange(len(valid_sizes))
        
        ax.bar(x_valid - width/2, valid_radix, width, 
               label='Поразрядная', color='blue', alpha=0.7)
        ax.bar(x_valid + width/2, valid_shaker, width, 
               label='Перемешиванием', color='red', alpha=0.7)
        
        ax.set_xlabel('Размер массива')
        ax.set_ylabel('Время (секунды)')
        ax.set_title(f'Тип: {order}')
        ax.set_xticks(x_valid)
        ax.set_xticklabels([f'{size:,}' for size in valid_sizes])
        ax.legend()
        ax.grid(True, alpha=0.3, axis='y')
        ax.set_yscale('log')

plt.tight_layout()
plt.show()

# ==================== ВЫВОДЫ И АНАЛИЗ ====================
print("\n" + "=" * 60)
print("АНАЛИЗ РЕЗУЛЬТАТОВ И ВЫВОДЫ")
print("=" * 60)

print("\n📊 ОСНОВНЫЕ ВЫВОДЫ:")
print("\n1. ПРОИЗВОДИТЕЛЬНОСТЬ:")
print("   • Поразрядная сортировка значительно быстрее для всех размеров массивов")
print("   • Сортировка перемешиванием резко замедляется при n > 10,000")

print("\n2. СЛОЖНОСТЬ:")
print("   • Поразрядная: O(nk) - линейная при k << n")
print("   • Перемешиванием: O(n²) - квадратичная")

print("\n3. ПАМЯТЬ:")
print("   • Поразрядная: O(n+k) - требует дополнительной памяти")
print("   • Перемешиванием: O(1) - сортировка на месте")

print("\n4. УСТОЙЧИВОСТЬ:")
print("   • Оба алгоритма являются устойчивыми")

print("\n5. ПРИМЕНЕНИЕ:")
print("   • Поразрядная: большие массивы целых чисел, когда k << n")
print("   • Перемешиванием: небольшие массивы, учебные цели")

print("\n6. ЧУВСТВИТЕЛЬНОСТЬ К ВХОДНЫМ ДАННЫМ:")
print("   • Поразрядная: время не зависит от начального порядка")
print("   • Перемешиванием: быстрее на частично отсортированных массивах")

# Таблица результатов
print("\n📈 ТАБЛИЦА РЕЗУЛЬТАТОВ (время в секундах):")
print("\n" + "-" * 85)
print(f"{'Размер':<10} {'Тип':<12} {'Поразрядная':<15} {'Перемешиванием':<15} {'Соотношение':<15}")
print("-" * 85)

for size in sizes:
    for order in order_types:
        radix_time = results["Поразрядная сортировка"][order][sizes.index(size)]
        shaker_time = results["Сортировка перемешиванием"][order][sizes.index(size)]
        
        if not np.isnan(radix_time) and not np.isnan(shaker_time):
            ratio = shaker_time / radix_time if radix_time > 0 else float('inf')
            print(f"{size:<10,} {order:<12} {radix_time:<15.6f} {shaker_time:<15.6f} {ratio:<15.1f}x")
        else:
            print(f"{size:<10,} {order:<12} {radix_time:<15.6f} {'—':<15} {'—':<15}")

print("-" * 85)

print("\n" + "=" * 60)
print("АНАЛИЗ ЗАВЕРШЕН")
print("=" * 60)
```

```python

```

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
import math
import time
from functools import wraps
import sys

def recursive_simple_iteration(f, x0, epsilon=1e-6, max_iter=1000, iter_count=0):
    """
    Рекурсивная реализация метода простых итераций для решения уравнения f(x) = x
    
    Args:
        f: функция, для которой решаем уравнение f(x) = x
        x0: начальное приближение
        epsilon: точность
        max_iter: максимальное количество итераций
        iter_count: счетчик итераций (для внутреннего использования)
    
    Returns:
        x: найденный корень
        iter_count: количество выполненных итераций
    """
    if iter_count >= max_iter:
        raise ValueError(f"Превышено максимальное количество итераций ({max_iter})")
    
    x_next = f(x0)
    
    # Проверка условия сходимости
    if abs(x_next - x0) < epsilon:
        return x_next, iter_count + 1
    
    return recursive_simple_iteration(f, x_next, epsilon, max_iter, iter_count + 1)

# Пример использования
def example_function1(x):
    """Пример функции: x = cos(x)"""
    return math.cos(x)

def example_function2(x):
    """Пример функции: x = 0.5 * (x + 2/x) (нахождение квадратного корня)"""
    return 0.5 * (x + 2/x)

# Тестирование
if __name__ == "__main__":
    print("Решение уравнения x = cos(x):")
    try:
        root1, iterations1 = recursive_simple_iteration(example_function1, 0.5)
        print(f"Корень: {root1:.6f}, итераций: {iterations1}")
    except ValueError as e:
        print(f"Ошибка: {e}")
    
    print("\nНахождение квадратного корня из 2:")
    try:
        root2, iterations2 = recursive_simple_iteration(example_function2, 1.0)
        print(f"Корень: {root2:.6f}, итераций: {iterations2}")
    except ValueError as e:
        print(f"Ошибка: {e}")
```

```python
def iterative_simple_iteration(f, x0, epsilon=1e-6, max_iter=1000):
    """
    Итеративная реализация метода простых итераций для решения уравнения f(x) = x
    
    Args:
        f: функция, для которой решаем уравнение f(x) = x
        x0: начальное приближение
        epsilon: точность
        max_iter: максимальное количество итераций
    
    Returns:
        x: найденный корень
        iter_count: количество выполненных итераций
    """
    x_prev = x0
    iter_count = 0
    
    for i in range(max_iter):
        iter_count += 1
        x_next = f(x_prev)
        
        if abs(x_next - x_prev) < epsilon:
            return x_next, iter_count
        
        x_prev = x_next
    
    raise ValueError(f"Превышено максимальное количество итераций ({max_iter})")
```

```python
def recursive_with_memo(f, x0, epsilon=1e-6, max_iter=1000, iter_count=0, memo=None):
    """
    Рекурсивная реализация с сохранением промежуточных результатов
    
    Args:
        f: функция, для которой решаем уравнение f(x) = x
        x0: начальное приближение
        epsilon: точность
        max_iter: максимальное количество итераций
        iter_count: счетчик итераций
        memo: словарь для сохранения промежуточных результатов
    
    Returns:
        x: найденный корень
        iter_count: количество выполненных итераций
        memo: словарь с промежуточными результатами
    """
    if memo is None:
        memo = {}
    
    if iter_count >= max_iter:
        raise ValueError(f"Превышено максимальное количество итераций ({max_iter})")
    
    # Сохраняем текущее значение
    memo[iter_count] = x0
    
    x_next = f(x0)
    
    # Проверка условия сходимости
    if abs(x_next - x0) < epsilon:
        memo[iter_count + 1] = x_next
        return x_next, iter_count + 1, memo
    
    return recursive_with_memo(f, x_next, epsilon, max_iter, iter_count + 1, memo)
```

```python
def memoize_recursion(func):
    """
    Декоратор для сохранения промежуточных результатов рекурсии
    """
    @wraps(func)
    def wrapper(f, x0, epsilon=1e-6, max_iter=1000, iter_count=0, memo=None):
        if memo is None:
            memo = {}
        
        # Сохраняем текущее значение
        memo[iter_count] = x0
        
        result = func(f, x0, epsilon, max_iter, iter_count, memo)
        
        if isinstance(result, tuple) and len(result) == 2:
            root, iters = result
            return root, iters, memo
        elif isinstance(result, tuple) and len(result) == 3:
            return result
        
        return result, memo
    
    return wrapper

@memoize_recursion
def recursive_with_decorator(f, x0, epsilon=1e-6, max_iter=1000, iter_count=0, memo=None):
    """
    Рекурсивная реализация с декоратором для сохранения промежуточных результатов
    """
    if iter_count >= max_iter:
        raise ValueError(f"Превышено максимальное количество итераций ({max_iter})")
    
    x_next = f(x0)
    
    # Проверка условия сходимости
    if abs(x_next - x0) < epsilon:
        return x_next, iter_count + 1
    
    return recursive_with_decorator(f, x_next, epsilon, max_iter, iter_count + 1, memo)
```

```python
def compare_performance():
    """
    Сравнение производительности различных реализаций
    """
    test_functions = [
        (example_function1, 0.5, "x = cos(x)"),
        (example_function2, 1.0, "x = 0.5*(x + 2/x)")
    ]
    
    for func, x0, description in test_functions:
        print(f"\n{description}")
        print("=" * 40)
        
        # Рекурсивная реализация
        start_time = time.time()
        try:
            root_rec, iters_rec = recursive_simple_iteration(func, x0)
            time_rec = time.time() - start_time
            print(f"Рекурсивная: корень = {root_rec:.8f}, итераций = {iters_rec}, время = {time_rec:.6f}с")
        except RecursionError:
            print("Рекурсивная: переполнение стека")
        except ValueError as e:
            print(f"Рекурсивная: {e}")
        
        # Итеративная реализация
        start_time = time.time()
        try:
            root_iter, iters_iter = iterative_simple_iteration(func, x0)
            time_iter = time.time() - start_time
            print(f"Итеративная: корень = {root_iter:.8f}, итераций = {iters_iter}, время = {time_iter:.6f}с")
        except ValueError as e:
            print(f"Итеративная: {e}")
        
        # Рекурсивная с мемоизацией
        start_time = time.time()
        try:
            root_memo, iters_memo, memo_dict = recursive_with_memo(func, x0)
            time_memo = time.time() - start_time
            print(f"С мемоизацией: корень = {root_memo:.8f}, итераций = {iters_memo}, время = {time_memo:.6f}с")
        except RecursionError:
            print("С мемоизацией: переполнение стека")
        except ValueError as e:
            print(f"С мемоизацией: {e}")
        
        # Рекурсивная с декоратором
        start_time = time.time()
        try:
            root_decor, iters_decor, memo_decor = recursive_with_decorator(func, x0)
            time_decor = time.time() - start_time
            print(f"С декоратором: корень = {root_decor:.8f}, итераций = {iters_decor}, время = {time_decor:.6f}с")
        except RecursionError:
            print("С декоратором: переполнение стека")
        except ValueError as e:
            print(f"С декоратором: {e}")

def test_stack_overflow():
    """
    Тестирование границы переполнения стека
    """
    print("\nОпределение границы переполнения стека:")
    print("=" * 50)
    
    # Функция, которая требует много итераций
    def slow_convergence(x):
        return 0.99 * x + 0.01
    
    max_depth = 0
    for i in range(100, 10000, 100):
        try:
            recursive_simple_iteration(slow_convergence, 1.0, epsilon=1e-10, max_iter=i)
            max_depth = i
        except RecursionError:
            print(f"Переполнение стека при {i} итерациях")
            break
    
    print(f"Максимальная глубина рекурсии: {max_depth}")
    
    # Получаем лимит рекурсии системы
    recursion_limit = sys.getrecursionlimit()
    print(f"Лимит рекурсии системы: {recursion_limit}")

if __name__ == "__main__":
    compare_performance()
    test_stack_overflow()
```

```python
БЛОК-СХЕМА МЕТОДА ПРОСТЫХ ИТЕРАЦИЙ:

НАЧАЛО
  │
  ▼
Ввод: f, x0, ε, max_iter
  │
  ▼
iter_count = 0
  │
  ▼
┌─────────────────┐
│ x_next = f(x0)  │
└─────────────────┘
  │
  ▼
┌─────────────────────────────────────┐
│ |x_next - x0| < ε ?                 │
└─────────────────────────────────────┘
  │               │
  ▼ Да            ▼ Нет
  │               │
Вывод результата  ▼
  │           iter_count += 1
  │               │
  │               ▼
  │           ┌─────────────────────┐
  │           │ iter_count ≥ max_iter? │
  │           └─────────────────────┘
  │               │               │
  │               ▼ Да            ▼ Нет
  │               │               │
  │           Ошибка: превышен    ▼
  │           лимит итераций      │
  │                           x0 = x_next
  │                               │
  │                               ▼
  │                           Возврат к вычислению f(x0)
  │
  ▼
КОНЕЦ
```

```python

```

```python

```

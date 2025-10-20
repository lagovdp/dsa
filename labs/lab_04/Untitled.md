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

# Лабораторная работа 4
Цели:

+ Познакомиться со структурами данных стек, очередь и дек, понять, как они устроены и работают.
+ Научиться реализовывать эти структуры на основе массивов и связных списков.
+ Изучить основные операции для каждой структуры (добавление, удаление, просмотр элементов).
+ На практике применить стек для решения задач: проверка правильности скобок и вычисление выражений в обратной польской записи.
+ Освоить перевод математических выражений из обычной записи в постфиксную форму.

```python
from abc import ABC, abstractmethod
from typing import Any, Optional

# Базовые классы
class BaseStack(ABC):
    @abstractmethod
    def push(self, item: Any) -> None:
        pass
    
    @abstractmethod
    def pop(self) -> Any:
        pass
    
    @abstractmethod
    def peek(self) -> Any:
        pass
    
    @abstractmethod
    def is_empty(self) -> bool:
        pass
    
    @abstractmethod
    def size(self) -> int:
        pass

class BaseQueue(ABC):
    @abstractmethod
    def enqueue(self, item: Any) -> None:
        pass
    
    @abstractmethod
    def dequeue(self) -> Any:
        pass
    
    @abstractmethod
    def is_empty(self) -> bool:
        pass
    
    @abstractmethod
    def size(self) -> int:
        pass

class BaseDeque(ABC):
    @abstractmethod
    def add_front(self, item: Any) -> None:
        pass
    
    @abstractmethod
    def add_rear(self, item: Any) -> None:
        pass
    
    @abstractmethod
    def remove_front(self) -> Any:
        pass
    
    @abstractmethod
    def remove_rear(self) -> Any:
        pass
    
    @abstractmethod
    def is_empty(self) -> bool:
        pass
    
    @abstractmethod
    def size(self) -> int:
        pass

# 1. Стек на основе массива
class ArrayStack(BaseStack):
    def __init__(self):
        self._items = []
    
    def push(self, item: Any) -> None:
        self._items.append(item)
    
    def pop(self) -> Any:
        if self.is_empty():
            raise IndexError("Stack is empty")
        return self._items.pop()
    
    def peek(self) -> Any:
        if self.is_empty():
            raise IndexError("Stack is empty")
        return self._items[-1]
    
    def is_empty(self) -> bool:
        return len(self._items) == 0
    
    def size(self) -> int:
        return len(self._items)
    
    def __str__(self):
        return f"ArrayStack({self._items})"

# 2. Стек на основе связного списка
class Node:
    def __init__(self, data: Any):
        self.data = data
        self.next: Optional['Node'] = None

class LinkedListStack(BaseStack):
    def __init__(self):
        self._top: Optional[Node] = None
        self._size = 0
    
    def push(self, item: Any) -> None:
        new_node = Node(item)
        new_node.next = self._top
        self._top = new_node
        self._size += 1
    
    def pop(self) -> Any:
        if self.is_empty():
            raise IndexError("Stack is empty")
        data = self._top.data
        self._top = self._top.next
        self._size -= 1
        return data
    
    def peek(self) -> Any:
        if self.is_empty():
            raise IndexError("Stack is empty")
        return self._top.data
    
    def is_empty(self) -> bool:
        return self._top is None
    
    def size(self) -> int:
        return self._size
    
    def __str__(self):
        items = []
        current = self._top
        while current:
            items.append(current.data)
            current = current.next
        return f"LinkedListStack({items})"

# 3. Очередь на основе массива
class ArrayQueue(BaseQueue):
    def __init__(self):
        self._items = []
    
    def enqueue(self, item: Any) -> None:
        self._items.append(item)
    
    def dequeue(self) -> Any:
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self._items.pop(0)
    
    def is_empty(self) -> bool:
        return len(self._items) == 0
    
    def size(self) -> int:
        return len(self._items)
    
    def __str__(self):
        return f"ArrayQueue({self._items})"

# 4. Очередь на основе связного списка
class LinkedListQueue(BaseQueue):
    def __init__(self):
        self._front: Optional[Node] = None
        self._rear: Optional[Node] = None
        self._size = 0
    
    def enqueue(self, item: Any) -> None:
        new_node = Node(item)
        if self.is_empty():
            self._front = self._rear = new_node
        else:
            self._rear.next = new_node
            self._rear = new_node
        self._size += 1
    
    def dequeue(self) -> Any:
        if self.is_empty():
            raise IndexError("Queue is empty")
        data = self._front.data
        self._front = self._front.next
        if self._front is None:
            self._rear = None
        self._size -= 1
        return data
    
    def is_empty(self) -> bool:
        return self._front is None
    
    def size(self) -> int:
        return self._size
    
    def __str__(self):
        items = []
        current = self._front
        while current:
            items.append(current.data)
            current = current.next
        return f"LinkedListQueue({items})"

# 5. Дек на основе массива
class ArrayDeque(BaseDeque):
    def __init__(self):
        self._items = []
    
    def add_front(self, item: Any) -> None:
        self._items.insert(0, item)
    
    def add_rear(self, item: Any) -> None:
        self._items.append(item)
    
    def remove_front(self) -> Any:
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self._items.pop(0)
    
    def remove_rear(self) -> Any:
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self._items.pop()
    
    def is_empty(self) -> bool:
        return len(self._items) == 0
    
    def size(self) -> int:
        return len(self._items)
    
    def __str__(self):
        return f"ArrayDeque({self._items})"

# 6. Дек на основе связного списка
class LinkedListDeque(BaseDeque):
    def __init__(self):
        self._front: Optional[Node] = None
        self._rear: Optional[Node] = None
        self._size = 0
    
    def add_front(self, item: Any) -> None:
        new_node = Node(item)
        if self.is_empty():
            self._front = self._rear = new_node
        else:
            new_node.next = self._front
            self._front = new_node
        self._size += 1
    
    def add_rear(self, item: Any) -> None:
        new_node = Node(item)
        if self.is_empty():
            self._front = self._rear = new_node
        else:
            self._rear.next = new_node
            self._rear = new_node
        self._size += 1
    
    def remove_front(self) -> Any:
        if self.is_empty():
            raise IndexError("Deque is empty")
        data = self._front.data
        self._front = self._front.next
        if self._front is None:
            self._rear = None
        self._size -= 1
        return data
    
    def remove_rear(self) -> Any:
        if self.is_empty():
            raise IndexError("Deque is empty")
        if self._front == self._rear:
            data = self._front.data
            self._front = self._rear = None
        else:
            current = self._front
            while current.next != self._rear:
                current = current.next
            data = self._rear.data
            self._rear = current
            self._rear.next = None
        self._size -= 1
        return data
    
    def is_empty(self) -> bool:
        return self._front is None
    
    def size(self) -> int:
        return self._size
    
    def __str__(self):
        items = []
        current = self._front
        while current:
            items.append(current.data)
            current = current.next
        return f"LinkedListDeque({items})"
```

```python
def check_brackets(expression: str) -> bool:
    """
    Проверяет своевременность закрытия скобок в строке.
    Возвращает True если скобки расставлены правильно, иначе False.
    """
    stack = ArrayStack()
    bracket_pairs = {')': '(', ']': '[', '}': '{'}
    
    for char in expression:
        if char in '([{':
            # Если встретили открывающую скобку - добавляем в стек
            stack.push(char)
        elif char in ')]}':
            # Если встретили закрывающую скобку
            if stack.is_empty():
                return False  # Нет соответствующей открывающей скобки
            if stack.pop() != bracket_pairs[char]:
                return False  # Несоответствие скобок
    
    # Если стек пуст - все скобки закрыты правильно
    return stack.is_empty()

# Тестирование задания 2
def test_bracket_checker():
    test_cases = [
        ("()", True),
        ("[]", True),
        ("{}", True),
        ("()[]{}", True),
        ("([{}])", True),
        ("(", False),
        (")", False),
        ("([)]", False),
        ("({)}", False),
        ("((()))", True),
        ("", True)
    ]
    
    print("Тестирование проверки скобок:")
    for expr, expected in test_cases:
        result = check_brackets(expr)
        status = "✓" if result == expected else "✗"
        print(f"{status} '{expr}' -> {result} (ожидается: {expected})")

# test_bracket_checker()
```

```python
def evaluate_postfix(expression: str) -> float:
    """
    Вычисляет значение выражения в постфиксной записи.
    """
    stack = ArrayStack()
    operators = {
        '+': lambda x, y: x + y,
        '-': lambda x, y: x - y,
        '*': lambda x, y: x * y,
        '/': lambda x, y: x / y
    }
    
    for token in expression.split():
        if token.isdigit():
            # Если токен - число, добавляем в стек
            stack.push(float(token))
        elif token in operators:
            # Если токен - оператор, извлекаем два операнда
            if stack.size() < 2:
                raise ValueError("Недостаточно операндов для операции")
            operand2 = stack.pop()
            operand1 = stack.pop()
            result = operators[token](operand1, operand2)
            stack.push(result)
        else:
            raise ValueError(f"Неизвестный токен: {token}")
    
    if stack.size() != 1:
        raise ValueError("Некорректное выражение")
    
    return stack.pop()

# Тестирование задания 3
def test_postfix_evaluator():
    test_cases = [
        ("3 4 +", 7),
        ("5 2 * 3 +", 13),
        ("10 5 / 2 *", 4),
        ("4 2 + 3 *", 18),
        ("1 2 + 4 * 3 +", 15)
    ]
    
    print("\nТестирование вычисления постфиксных выражений:")
    for expr, expected in test_cases:
        result = evaluate_postfix(expr)
        status = "✓" if abs(result - expected) < 0.0001 else "✗"
        print(f"{status} '{expr}' = {result} (ожидается: {expected})")

# test_postfix_evaluator()
```

```python
def infix_to_postfix(expression: str) -> str:
    """
    Переводит математическое выражение из инфиксной в постфиксную запись.
    """
    stack = ArrayStack()
    output = []
    
    # Приоритеты операторов
    precedence = {'+': 1, '-': 1, '*': 2, '/': 2, '^': 3}
    
    i = 0
    while i < len(expression):
        char = expression[i]
        
        if char == ' ':
            i += 1
            continue
        
        # Если символ - операнд, добавляем в выходную строку
        if char.isalnum():
            # Собираем многосимвольные числа
            num = char
            while i + 1 < len(expression) and expression[i + 1].isalnum():
                i += 1
                num += expression[i]
            output.append(num)
        
        # Если символ - открывающая скобка, добавляем в стек
        elif char == '(':
            stack.push(char)
        
        # Если символ - закрывающая скобка
        elif char == ')':
            while not stack.is_empty() and stack.peek() != '(':
                output.append(stack.pop())
            if not stack.is_empty() and stack.peek() == '(':
                stack.pop()  # Удаляем открывающую скобку
        
        # Если символ - оператор
        else:
            while (not stack.is_empty() and 
                   stack.peek() != '(' and 
                   precedence.get(char, 0) <= precedence.get(stack.peek(), 0)):
                output.append(stack.pop())
            stack.push(char)
        
        i += 1
    
    # Извлекаем все операторы из стека
    while not stack.is_empty():
        output.append(stack.pop())
    
    return ' '.join(output)

# Тестирование задания 4
def test_infix_to_postfix():
    test_cases = [
        ("a + b", "a b +"),
        ("a + b * c", "a b c * +"),
        ("(a + b) * c", "a b + c *"),
        ("a + (b + c) * d", "a b c + d * +"),
        ("3 + 4 * 2", "3 4 2 * +"),
        ("(3 + 4) * 2", "3 4 + 2 *")
    ]
    
    print("\nТестирование перевода в постфиксную запись:")
    for infix, expected_postfix in test_cases:
        result = infix_to_postfix(infix)
        status = "✓" if result == expected_postfix else "✗"
        print(f"{status} '{infix}' -> '{result}' (ожидается: '{expected_postfix}')")

# test_infix_to_postfix()
```

```python
def main():
    print("=" * 50)
    print("ДЕМОНСТРАЦИЯ ВСЕХ ЗАДАНИЙ")
    print("=" * 50)
    
    # Демонстрация структур данных
    print("\n1. ДЕМОНСТРАЦИЯ СТРУКТУР ДАННЫХ:")
    
    # Стек на массиве
    stack1 = ArrayStack()
    stack1.push(1)
    stack1.push(2)
    stack1.push(3)
    print(f"ArrayStack: {stack1}")
    print(f"Pop: {stack1.pop()}")
    print(f"После pop: {stack1}")
    
    # Стек на связном списке
    stack2 = LinkedListStack()
    stack2.push('A')
    stack2.push('B')
    stack2.push('C')
    print(f"LinkedListStack: {stack2}")
    print(f"Pop: {stack2.pop()}")
    print(f"После pop: {stack2}")
    
    # Очередь на массиве
    queue1 = ArrayQueue()
    queue1.enqueue(1)
    queue1.enqueue(2)
    queue1.enqueue(3)
    print(f"ArrayQueue: {queue1}")
    print(f"Dequeue: {queue1.dequeue()}")
    print(f"После dequeue: {queue1}")
    
    # Дек на связном списке
    deque1 = LinkedListDeque()
    deque1.add_front(1)
    deque1.add_rear(2)
    deque1.add_front(0)
    print(f"LinkedListDeque: {deque1}")
    print(f"Remove front: {deque1.remove_front()}")
    print(f"Remove rear: {deque1.remove_rear()}")
    print(f"После операций: {deque1}")
    
    # Тестирование задания 2
    print("\n2. ПРОВЕРКА СКОБОК:")
    brackets_test = ["()", "([{}])", "([)]", "((())"]
    for test in brackets_test:
        result = check_brackets(test)
        print(f"'{test}' -> {'✓ Правильно' if result else '✗ Ошибка'}")
    
    # Тестирование задания 3
    print("\n3. ВЫЧИСЛЕНИЕ ПОСТФИКСНЫХ ВЫРАЖЕНИЙ:")
    postfix_tests = [
        ("3 4 +", 7),
        ("5 2 * 3 +", 13),
        ("10 5 / 2 *", 4)
    ]
    for expr, expected in postfix_tests:
        result = evaluate_postfix(expr)
        print(f"'{expr}' = {result} (ожидается: {expected})")
    
    # Тестирование задания 4
    print("\n4. ПЕРЕВОД В ПОСТФИКСНУЮ ЗАПИСЬ:")
    infix_tests = [
        ("a + b", "a b +"),
        ("a + b * c", "a b c * +"),
        ("(a + b) * c", "a b + c *")
    ]
    for infix, expected in infix_tests:
        result = infix_to_postfix(infix)
        print(f"'{infix}' -> '{result}' (ожидается: '{expected}')")
    
    print("\n" + "=" * 50)
    print("ДЕМОНСТРАЦИЯ ЗАВЕРШЕНА")
    print("=" * 50)

if __name__ == "__main__":
    main()
```

```python

```

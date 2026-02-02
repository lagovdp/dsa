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
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None


class SingleLinkedListV1:
    def __init__(self):
        self.head = None
    
    def is_empty(self):
        return self.head is None
    
    def insert_first_node(self, value):
        new_node = Node(value)
        new_node.next = self.head
        self.head = new_node
    
    def remove_first_node(self):
        if self.is_empty():
            raise IndexError("Список пуст")
        
        value = self.head.value
        self.head = self.head.next
        return value
    
    def insert_last_node(self, value):
        new_node = Node(value)
        
        if self.is_empty():
            self.head = new_node
            return
        
        current = self.head
        while current.next:
            current = current.next
        
        current.next = new_node
    
    def remove_last_node(self):
        if self.is_empty():
            raise IndexError("Список пуст")
        
        # Если список содержит только один элемент
        if self.head.next is None:
            value = self.head.value
            self.head = None
            return value
        
        # Находим предпоследний элемент
        current = self.head
        while current.next.next:
            current = current.next
        
        value = current.next.value
        current.next = None
        return value
    
    def display(self):
        elements = []
        current = self.head
        while current:
            elements.append(str(current.value))
            current = current.next
        return " -> ".join(elements) if elements else "Пустой список"
```

```python
class SingleLinkedListV2(SingleLinkedListV1):
    def __init__(self):
        super().__init__()
        self._size = 0
    
    def get_size(self):
        return self._size
    
    def insert_first_node(self, value):
        super().insert_first_node(value)
        self._size += 1
    
    def remove_first_node(self):
        value = super().remove_first_node()
        self._size -= 1
        return value
    
    def insert_last_node(self, value):
        super().insert_last_node(value)
        self._size += 1
    
    def remove_last_node(self):
        value = super().remove_last_node()
        self._size -= 1
        return value
    
    def find_node(self, value):
        current = self.head
        while current:
            if current.value == value:
                return current.value
            current = current.next
        return None
    
    def replace_node(self, old_value, new_value):
        current = self.head
        while current:
            if current.value == old_value:
                current.value = new_value
                return
            current = current.next
    
    def remove_node(self, value):
        if self.is_empty():
            raise ValueError(f"Значение {value} не найдено")
        
        # Если удаляем первый элемент
        if self.head.value == value:
            return self.remove_first_node()
        
        # Ищем элемент для удаления
        current = self.head
        while current.next:
            if current.next.value == value:
                removed_value = current.next.value
                current.next = current.next.next
                self._size -= 1
                return removed_value
            current = current.next
        
        raise ValueError(f"Значение {value} не найдено")
```

```python
class SingleLinkedListV3(SingleLinkedListV2):
    """
    Версия 3: Явно демонстрируем оптимизацию операции get_size() до O(1)
    за счет поддержания счетчика размера при всех операциях
    """
    def __init__(self):
        super().__init__()
        # В версии 3 мы явно подчеркиваем, что счетчик размера
        # должен обновляться при ВСЕХ операциях
        self._performance_counter = {
            'get_size_calls': 0,
            'operations_count': 0
        }
    
    def get_size(self):
        """O(1) операция благодаря счетчику"""
        self._performance_counter['get_size_calls'] += 1
        return self._size
    
    def insert_first_node(self, value):
        """O(1) операция, обновляем счетчик размера"""
        super().insert_first_node(value)
        self._performance_counter['operations_count'] += 1
    
    def remove_first_node(self):
        """O(1) операция, обновляем счетчик размера"""
        value = super().remove_first_node()
        self._performance_counter['operations_count'] += 1
        return value
    
    def insert_last_node(self, value):
        """O(n) операция без хвоста, но счетчик размера обновляется"""
        super().insert_last_node(value)
        self._performance_counter['operations_count'] += 1
    
    def remove_last_node(self):
        """O(n) операция без хвоста, но счетчик размера обновляется"""
        value = super().remove_last_node()
        self._performance_counter['operations_count'] += 1
        return value
    
    def get_performance_stats(self):
        """Метод для демонстрации эффективности"""
        return {
            'size': self._size,
            'get_size_calls': self._performance_counter['get_size_calls'],
            'total_operations': self._performance_counter['operations_count']
        }
    
    def demonstrate_size_performance(self):
        """Демонстрация того, что get_size() работает за O(1)"""
        print(f"Версия 3: Демонстрация производительности get_size()")
        print(f"Текущий размер: {self.get_size()} (вызовов get_size: {self._performance_counter['get_size_calls']})")
        print(f"Все операции выполняются за O(1) или O(n), но get_size() всегда O(1)")
```

```python
class SingleLinkedListV4(SingleLinkedListV3):
    def _find_node_and_previous(self, value):
        """Найти узел и его предыдущий узел по значению"""
        if self.is_empty():
            return None, None
        
        # Проверяем первый элемент
        if self.head.value == value:
            return self.head, None
        
        prev = self.head
        current = self.head.next
        
        while current:
            if current.value == value:
                return current, prev
            prev = current
            current = current.next
        
        return None, None
    
    def find_previous_node(self, value):
        current, prev = self._find_node_and_previous(value)
        return prev.value if prev else None
    
    def find_next_node(self, value):
        current, _ = self._find_node_and_previous(value)
        return current.next.value if current and current.next else None
    
    def insert_before_node(self, target_value, new_value):
        current, prev = self._find_node_and_previous(target_value)
        
        if not current:
            return  # Узел не найден
        
        new_node = Node(new_value)
        
        if prev is None:  # Вставляем перед первым элементом
            new_node.next = self.head
            self.head = new_node
        else:
            new_node.next = current
            prev.next = new_node
        
        self._size += 1
    
    def insert_after_node(self, target_value, new_value):
        current, _ = self._find_node_and_previous(target_value)
        
        if not current:
            return  # Узел не найден
        
        new_node = Node(new_value)
        new_node.next = current.next
        current.next = new_node
        self._size += 1
    
    def replace_previous_node(self, target_value, new_value):
        current, prev = self._find_node_and_previous(target_value)
        
        if prev:
            prev.value = new_value
    
    def replace_next_node(self, target_value, new_value):
        current, _ = self._find_node_and_previous(target_value)
        
        if current and current.next:
            current.next.value = new_value
    
    def remove_previous_node(self, target_value):
        current, prev = self._find_node_and_previous(target_value)
        
        if not prev:
            raise ValueError("Нет предыдущего узла")
        
        # Если удаляем элемент перед головой
        if prev == self.head:
            return self.remove_first_node()
        
        # Находим элемент перед prev
        prev_prev = self.head
        while prev_prev.next != prev:
            prev_prev = prev_prev.next
        
        value = prev.value
        prev_prev.next = current
        self._size -= 1
        return value
    
    def remove_next_node(self, target_value):
        current, _ = self._find_node_and_previous(target_value)
        
        if not current or not current.next:
            raise ValueError("Нет следующего узла")
        
        value = current.next.value
        current.next = current.next.next
        self._size -= 1
        return value
```

```python
class SingleLinkedListV5(SingleLinkedListV4):
    def __init__(self):
        super().__init__()
        self.tail = None
    
    def insert_first_node(self, value):
        super().insert_first_node(value)
        if self._size == 1:
            self.tail = self.head
    
    def insert_last_node(self, value):
        new_node = Node(value)
        
        if self.is_empty():
            self.head = new_node
            self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = new_node
        
        self._size += 1
    
    def remove_first_node(self):
        value = super().remove_first_node()
        if self.is_empty():
            self.tail = None
        return value
    
    def remove_last_node(self):
        if self.is_empty():
            raise IndexError("Список пуст")
        
        # Если список содержит только один элемент
        if self.head.next is None:
            value = self.head.value
            self.head = None
            self.tail = None
            self._size = 0
            return value
        
        # Находим предпоследний элемент
        current = self.head
        while current.next.next:
            current = current.next
        
        value = current.next.value
        current.next = None
        self.tail = current
        self._size -= 1
        return value
```

```python
class SingleLinkedListV6(SingleLinkedListV5):
    def insert_before_node(self, target_value, new_value):
        current, prev = self._find_node_and_previous(target_value)
        
        if not current:
            return  # Узел не найден
        
        # Оптимизация: вместо создания нового узла, 
        # меняем значения и создаем новый узел со старым значением
        old_value = current.value
        current.value = new_value
        
        # Создаем узел со старым значением и вставляем после текущего
        new_node = Node(old_value)
        new_node.next = current.next
        current.next = new_node
        
        # Обновляем tail если нужно
        if current == self.tail:
            self.tail = new_node
        
        self._size += 1
    
    def reverse(self):
        """Метод для переворота списка"""
        if self._size <= 1:
            return
        
        prev = None
        current = self.head
        self.tail = current  # Голова становится хвостом
        
        while current:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node
        
        self.head = prev
    
    def sort(self):
        """Сортировка списка методом пузырька (на месте)"""
        if self._size <= 1:
            return
        
        swapped = True
        while swapped:
            swapped = False
            current = self.head
            
            while current and current.next:
                if current.value > current.next.value:
                    # Меняем значения
                    current.value, current.next.value = current.next.value, current.value
                    swapped = True
                current = current.next
```

```python
def test_all_versions():
    print("=== Тестирование Версии 1 ===")
    list1 = SingleLinkedListV1()
    list1.insert_first_node(3)
    list1.insert_last_node(5)
    list1.insert_first_node(1)
    print(list1.display())
    
    print("\n=== Тестирование Версии 2 ===")
    list2 = SingleLinkedListV2()
    for i in [5, 3, 7, 1]:
        list2.insert_last_node(i)
    print(f"Размер: {list2.get_size()}")
    print(f"Найден 7: {list2.find_node(7)}")
    
    print("\n=== Тестирование Версии 4 ===")
    list4 = SingleLinkedListV4()
    for i in [1, 2, 3, 4, 5]:
        list4.insert_last_node(i)
    
    list4.insert_before_node(3, 2.5)
    print(f"После вставки перед 3: {list4.display()}")
    
    print("\n=== Тестирование Версии 6 (reverse и sort) ===")
    list6 = SingleLinkedListV6()
    for i in [5, 2, 8, 1, 9]:
        list6.insert_last_node(i)
    
    print(f"Исходный: {list6.display()}")
    
    list6.sort()
    print(f"Отсортированный: {list6.display()}")
    
    list6.reverse()
    print(f"Перевернутый: {list6.display()}")


# Индивидуальное задание: Реализация итератора для списка
class LinkedListIterator:
    def __init__(self, linked_list):
        self.current = linked_list.head
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.current is None:
            raise StopIteration
        value = self.current.value
        self.current = self.current.next
        return value


# Добавляем итератор к последней версии
SingleLinkedListV6.__iter__ = lambda self: LinkedListIterator(self)


if __name__ == "__main__":
    test_all_versions()
    
    # Пример использования итератора
    print("\n=== Использование итератора ===")
    list6 = SingleLinkedListV6()
    for i in [10, 20, 30, 40]:
        list6.insert_last_node(i)
    
    for value in list6:
        print(value, end=" ")
```

```python

```

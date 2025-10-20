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
    """Узел односвязного списка"""
    def __init__(self, data):
        self.data = data
        self.next = None

# Версия 1: Базовая реализация
class LinkedListV1:
    def __init__(self):
        self.head = None
    
    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            return
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node
    
    def display(self):
        elements = []
        current = self.head
        while current:
            elements.append(current.data)
            current = current.next
        return elements

# Версия 2: С двусвязными узлами
class DoubleNode:
    def __init__(self, data):
        self.data = data
        self.next = None
        self.prev = None

class LinkedListV2:
    def __init__(self):
        self.head = None
        self.tail = None
    
    def append(self, data):
        new_node = DoubleNode(data)
        if not self.head:
            self.head = self.tail = new_node
        else:
            self.tail.next = new_node
            new_node.prev = self.tail
            self.tail = new_node
    
    def display(self):
        elements = []
        current = self.head
        while current:
            elements.append(current.data)
            current = current.next
        return elements

# Версия 3: Циклический список
class LinkedListV3:
    def __init__(self):
        self.head = None
    
    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            new_node.next = self.head
        else:
            current = self.head
            while current.next != self.head:
                current = current.next
            current.next = new_node
            new_node.next = self.head
    
    def display(self):
        if not self.head:
            return []
        elements = [self.head.data]
        current = self.head.next
        while current != self.head:
            elements.append(current.data)
            current = current.next
        return elements

# Версия 4: С заголовочным узлом
class LinkedListV4:
    def __init__(self):
        self.header = Node(None)  # Заголовочный узел
    
    def append(self, data):
        new_node = Node(data)
        current = self.header
        while current.next:
            current = current.next
        current.next = new_node
    
    def display(self):
        elements = []
        current = self.header.next
        while current:
            elements.append(current.data)
            current = current.next
        return elements

# Версия 5: С хвостовым указателем
class LinkedListV5:
    def __init__(self):
        self.head = None
        self.tail = None
    
    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = new_node
    
    def display(self):
        elements = []
        current = self.head
        while current:
            elements.append(current.data)
            current = current.next
        return elements

# Версия 6: Итеративная версия с дополнительными методами
class LinkedListV6:
    def __init__(self):
        self.head = None
        self._size = 0
    
    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
        else:
            current = self.head
            while current.next:
                current = current.next
            current.next = new_node
        self._size += 1
    
    def __len__(self):
        return self._size
    
    def __iter__(self):
        current = self.head
        while current:
            yield current.data
            current = current.next
    
    def display(self):
        return list(self)
```

```python
class LinkedListWithReverse(LinkedListV1):
    def reverse(self):
        """Переворот списка (итеративный метод)"""
        prev = None
        current = self.head
        
        while current:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node
        
        self.head = prev
    
    def reverse_recursive(self):
        """Переворот списка (рекурсивный метод)"""
        def _reverse(current, prev):
            if not current:
                return prev
            next_node = current.next
            current.next = prev
            return _reverse(next_node, current)
        
        self.head = _reverse(self.head, None)
```

```python
class LinkedListWithSort(LinkedListV1):
    def sort(self):
        """Сортировка списка пузырьковым методом"""
        if not self.head or not self.head.next:
            return
        
        swapped = True
        while swapped:
            swapped = False
            current = self.head
            
            while current and current.next:
                if current.data > current.next.data:
                    current.data, current.next.data = current.next.data, current.data
                    swapped = True
                current = current.next
    
    def sort_merge(self):
        """Сортировка слиянием"""
        def merge_sort(head):
            if not head or not head.next:
                return head
            
            # Разделение списка
            middle = get_middle(head)
            next_to_middle = middle.next
            middle.next = None
            
            # Рекурсивная сортировка
            left = merge_sort(head)
            right = merge_sort(next_to_middle)
            
            # Слияние
            return merge(left, right)
        
        def get_middle(head):
            if not head:
                return head
            
            slow = head
            fast = head
            
            while fast.next and fast.next.next:
                slow = slow.next
                fast = fast.next.next
            
            return slow
        
        def merge(left, right):
            if not left:
                return right
            if not right:
                return left
            
            if left.data <= right.data:
                result = left
                result.next = merge(left.next, right)
            else:
                result = right
                result.next = merge(left, right.next)
            
            return result
        
        self.head = merge_sort(self.head)
```

```python
class LinkedListWithDuplicateEven(LinkedListV1):
    def duplicate_even_numbers(self):
        """Продублировать все четные числа в списке"""
        current = self.head
        
        while current:
            if current.data % 2 == 0:
                new_node = Node(current.data)
                new_node.next = current.next
                current.next = new_node
                current = new_node.next
            else:
                current = current.next
```

```python
class CircularLinkedList:
    def __init__(self):
        self.head = None
    
    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            new_node.next = self.head
        else:
            current = self.head
            while current.next != self.head:
                current = current.next
            current.next = new_node
            new_node.next = self.head
    
    def display(self):
        if not self.head:
            return []
        elements = [self.head.data]
        current = self.head.next
        while current != self.head:
            elements.append(current.data)
            current = current.next
        return elements
    
    def has_cycle(self):
        """Проверка наличия цикла (алгоритм Флойда)"""
        if not self.head:
            return False
        
        slow = self.head
        fast = self.head
        
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                return True
        
        return False
```

```python
def main():
    print("=== ТЕСТИРОВАНИЕ ВСЕХ ФУНКЦИЙ ===\n")
    
    # 1. Тестирование 6 версий списка
    print("1. Тестирование 6 версий списка:")
    versions = [LinkedListV1(), LinkedListV2(), LinkedListV3(), 
                LinkedListV4(), LinkedListV5(), LinkedListV6()]
    
    for i, lst in enumerate(versions, 1):
        for num in [3, 1, 4]:
            lst.append(num)
        print(f"Версия {i}: {lst.display()}")
    
    # 2. Тестирование reverse
    print("\n2. Тестирование reverse:")
    lst_rev = LinkedListWithReverse()
    for num in [1, 2, 3, 4, 5]:
        lst_rev.append(num)
    print(f"До reverse: {lst_rev.display()}")
    lst_rev.reverse()
    print(f"После reverse: {lst_rev.display()}")
    
    # 3. Тестирование sort
    print("\n3. Тестирование sort:")
    lst_sort = LinkedListWithSort()
    for num in [5, 2, 8, 1, 9]:
        lst_sort.append(num)
    print(f"До sort: {lst_sort.display()}")
    lst_sort.sort()
    print(f"После sort: {lst_sort.display()}")
    
    # 4. Тестирование дублирования четных чисел
    print("\n4. Тестирование дублирования четных чисел:")
    lst_dup = LinkedListWithDuplicateEven()
    for num in [1, 2, 3, 4, 5, 6]:
        lst_dup.append(num)
    print(f"До дублирования: {lst_dup.display()}")
    lst_dup.duplicate_even_numbers()
    print(f"После дублирования: {lst_dup.display()}")
    
    # 5. Тестирование циклического списка
    print("\n5. Тестирование циклического списка:")
    circular = CircularLinkedList()
    for num in [1, 2, 3]:
        circular.append(num)
    print(f"Циклический список: {circular.display()}")
    print(f"Есть цикл: {circular.has_cycle()}")

if __name__ == "__main__":
    main()
```

```python

```

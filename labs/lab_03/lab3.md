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


# ============================================
# 1. 6 ВЕРСИЙ ЛИНЕЙНОГО ОДНОСВЯЗНОГО СПИСКА
# ============================================

# Версия 1: Базовая реализация (без заголовка)
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
    
    def __str__(self):
        return "->".join(map(str, self.display())) if self.display() else "Empty"


# Версия 2: С фиктивным узлом (dummy head)
class LinkedListV2:
    def __init__(self):
        self.dummy = Node(None)  # фиктивный узел
    
    def append(self, data):
        new_node = Node(data)
        current = self.dummy
        while current.next:
            current = current.next
        current.next = new_node
    
    def display(self):
        elements = []
        current = self.dummy.next
        while current:
            elements.append(current.data)
            current = current.next
        return elements
    
    def __str__(self):
        return "->".join(map(str, self.display())) if self.display() else "Empty"


# Версия 3: С заголовочным узлом (header node с данными)
class LinkedListV3:
    def __init__(self):
        self.header = Node(0)  # заголовочный узел может хранить размер
    
    def append(self, data):
        new_node = Node(data)
        current = self.header
        while current.next:
            current = current.next
        current.next = new_node
        self.header.data += 1  # увеличиваем размер в заголовке
    
    def display(self):
        elements = []
        current = self.header.next
        while current:
            elements.append(current.data)
            current = current.next
        return elements
    
    def __str__(self):
        return "->".join(map(str, self.display())) if self.display() else "Empty"


# Версия 4: Циклический односвязный список
class LinkedListV4:
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
    
    def __str__(self):
        if not self.head:
            return "Empty"
        return "->".join(map(str, self.display())) + "->(head)"


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
    
    def __str__(self):
        return "->".join(map(str, self.display())) if self.display() else "Empty"


# Версия 6: Итеративная версия с поддержкой len()
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
    
    def __str__(self):
        return "->".join(map(str, self.display())) if self._size > 0 else "Empty"


# ============================================
# 2. МЕТОД REVERSE (добавляем в одну из версий)
# ============================================

class LinkedListWithReverse(LinkedListV1):
    def reverse(self):
        """Переворот списка - ПУНКТ 2"""
        prev = None
        current = self.head
        
        while current:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node
        
        self.head = prev


# ============================================
# 3. МЕТОД SORT (добавляем в одну из версий)
# ============================================

class LinkedListWithSort(LinkedListV1):
    def sort(self):
        """Сортировка пузырьком - ПУНКТ 3"""
        if not self.head or not self.head.next:
            return
        
        swapped = True
        while swapped:
            swapped = False
            current = self.head
            
            while current and current.next:
                if current.data > current.next.data:
                    # Меняем данные, не узлы
                    current.data, current.next.data = current.next.data, current.data
                    swapped = True
                current = current.next


# ============================================
# 4. ИНДИВИДУАЛЬНОЕ ЗАДАНИЕ (дублирование четных)
# ============================================

class LinkedListWithDuplicateEven(LinkedListV1):
    def duplicate_even(self):
        """Дублировать все четные числа - ПУНКТ 4"""
        current = self.head
        
        while current:
            if current.data % 2 == 0:  # если число четное
                new_node = Node(current.data)  # создаем копию
                new_node.next = current.next    # вставляем после текущего
                current.next = new_node
                current = new_node.next         # перескакиваем через добавленный
            else:
                current = current.next          # идем дальше


# ============================================
# 5. ОПЦИОНАЛЬНО: РЕАЛИЗАЦИЯ ЦИКЛА
# ============================================

class CircularLinkedListWithLoop(LinkedListV4):
    def do_while_traversal(self):
        """Цикл с постусловием (do-while) - ПУНКТ 5"""
        if not self.head:
            print("Список пуст")
            return
        
        current = self.head
        while True:
            print(f"{current.data}", end=" -> " if current.next != self.head else "")
            current = current.next
            if current == self.head:  # условие выхода
                break
        print("(head)" if self.head else "")


# ============================================
# ТЕСТИРОВАНИЕ ВСЕХ ФУНКЦИЙ
# ============================================

def main():
    print("=" * 60)
    print("ЛАБОРАТОРНАЯ РАБОТА: ЛИНЕЙНЫЕ ОДНОСВЯЗНЫЕ СПИСКИ")
    print("=" * 60)
    
    # 1. Тестирование 6 версий списка
    print("\n1. ТЕСТИРОВАНИЕ 6 ВЕРСИЙ СПИСКА:")
    print("-" * 40)
    
    # Создаем все версии
    lists = [
        ("V1: Базовая", LinkedListV1()),
        ("V2: С фиктивным узлом", LinkedListV2()),
        ("V3: С заголовком", LinkedListV3()),
        ("V4: Циклический", LinkedListV4()),
        ("V5: С хвостом", LinkedListV5()),
        ("V6: Итеративная", LinkedListV6())
    ]
    
    # Добавляем одинаковые данные во все списки
    test_data = [3, 1, 4, 2]
    for name, lst in lists:
        for data in test_data:
            lst.append(data)
        print(f"{name}: {lst}")
    
    # 2. Тестирование reverse
    print("\n\n2. ТЕСТИРОВАНИЕ МЕТОДА REVERSE:")
    print("-" * 40)
    
    lst_rev = LinkedListWithReverse()
    for num in [1, 2, 3, 4, 5]:
        lst_rev.append(num)
    
    print(f"До reverse:    {lst_rev}")
    lst_rev.reverse()
    print(f"После reverse: {lst_rev}")
    
    # 3. Тестирование sort
    print("\n\n3. ТЕСТИРОВАНИЕ МЕТОДА SORT:")
    print("-" * 40)
    
    lst_sort = LinkedListWithSort()
    for num in [5, 2, 8, 1, 9, 3]:
        lst_sort.append(num)
    
    print(f"До sort:    {lst_sort}")
    lst_sort.sort()
    print(f"После sort: {lst_sort}")
    
    # 4. Тестирование дублирования четных чисел
    print("\n\n4. ТЕСТИРОВАНИЕ ДУБЛИРОВАНИЯ ЧЕТНЫХ ЧИСЕЛ:")
    print("-" * 40)
    
    lst_dup = LinkedListWithDuplicateEven()
    for num in [1, 2, 3, 4, 5, 6]:
        lst_dup.append(num)
    
    print(f"До дублирования:    {lst_dup}")
    lst_dup.duplicate_even()
    print(f"После дублирования: {lst_dup}")
    
    # 5. Тестирование цикла do-while
    print("\n\n5. ТЕСТИРОВАНИЕ ЦИКЛА DO-WHILE:")
    print("-" * 40)
    
    circular = CircularLinkedListWithLoop()
    for num in [10, 20, 30, 40]:
        circular.append(num)
    
    print("Циклический список: ", end="")
    circular.do_while_traversal()
    
    print("\n" + "=" * 60)
    print("ВСЕ ЗАДАНИЯ ВЫПОЛНЕНЫ!")
    print("=" * 60)


if __name__ == "__main__":
    main()
```

```python

```

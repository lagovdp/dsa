---
jupyter:
  jupytext:
    formats: ipynb,md
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
# ============================================================================
# ЗАДАНИЯ 1-3: КЛАССЫ СТРУКТУР ДАННЫХ
# ============================================================================

class ListNode:
    """Узел связного списка для метода цепочек"""
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.next = None


class HashTableChaining:
    """Хеш-таблица на основе метода цепочек"""
    
    def __init__(self, capacity=8, load_factor=0.75):
        self.capacity = capacity
        self.load_factor = load_factor
        self.size = 0
        self.buckets = [None] * self.capacity
    
    def _hash(self, key):
        """Хеш-функция для ключа"""
        if isinstance(key, int):
            return key % self.capacity
        elif isinstance(key, str):
            # Полиномиальная хеш-функция для строк
            hash_val = 0
            for char in key:
                hash_val = (hash_val * 31 + ord(char)) % self.capacity
            return hash_val
        else:
            return hash(key) % self.capacity
    
    def _resize(self):
        """Увеличение размера таблицы при необходимости"""
        if self.size / self.capacity <= self.load_factor:
            return
        
        old_buckets = self.buckets
        self.capacity *= 2
        self.buckets = [None] * self.capacity
        self.size = 0
        
        for bucket in old_buckets:
            current = bucket
            while current:
                self.put(current.key, current.value)
                current = current.next
    
    def put(self, key, value):
        """Добавление элемента"""
        self._resize()
        index = self._hash(key)
        
        if self.buckets[index] is None:
            self.buckets[index] = ListNode(key, value)
            self.size += 1
            return
        
        # Проверяем, существует ли уже ключ
        current = self.buckets[index]
        while current:
            if current.key == key:
                current.value = value  # Обновляем значение
                return
            if current.next is None:
                break
            current = current.next
        
        # Добавляем новый узел в конец цепочки
        current.next = ListNode(key, value)
        self.size += 1
    
    def get(self, key):
        """Получение значения по ключу"""
        index = self._hash(key)
        current = self.buckets[index]
        
        while current:
            if current.key == key:
                return current.value
            current = current.next
        
        raise KeyError(f"Key {key} not found")
    
    def remove(self, key):
        """Удаление элемента по ключу"""
        index = self._hash(key)
        current = self.buckets[index]
        prev = None
        
        while current:
            if current.key == key:
                if prev:
                    prev.next = current.next
                else:
                    self.buckets[index] = current.next
                self.size -= 1
                return current.value
            prev = current
            current = current.next
        
        raise KeyError(f"Key {key} not found")
    
    def contains(self, key):
        """Проверка наличия ключа"""
        try:
            self.get(key)
            return True
        except KeyError:
            return False
    
    def __str__(self):
        result = []
        for i, bucket in enumerate(self.buckets):
            chain = []
            current = bucket
            while current:
                chain.append(f"{current.key}:{current.value}")
                current = current.next
            if chain:
                result.append(f"Bucket {i}: {' -> '.join(chain)}")
        return "\n".join(result)


class HashTableOpenAddressing:
    """Хеш-таблица на основе открытой адресации с линейным пробированием"""
    
    def __init__(self, capacity=8, load_factor=0.75):
        self.capacity = capacity
        self.load_factor = load_factor
        self.size = 0
        self.keys = [None] * self.capacity
        self.values = [None] * self.capacity
        self.DELETED = object()  # Маркер для удаленных элементов
    
    def _hash(self, key):
        """Хеш-функция"""
        if isinstance(key, int):
            return key % self.capacity
        elif isinstance(key, str):
            hash_val = 0
            for char in key:
                hash_val = (hash_val * 31 + ord(char)) % self.capacity
            return hash_val
        else:
            return hash(key) % self.capacity
    
    def _resize(self):
        """Увеличение размера таблицы"""
        if self.size / self.capacity <= self.load_factor:
            return
        
        old_keys = self.keys
        old_values = self.values
        self.capacity *= 2
        self.keys = [None] * self.capacity
        self.values = [None] * self.capacity
        self.size = 0
        
        for key, value in zip(old_keys, old_values):
            if key is not None and key != self.DELETED:
                self.put(key, value)
    
    def _probe(self, key, for_insert=False):
        """Линейное пробирование"""
        index = self._hash(key)
        start_index = index
        
        while True:
            # Для вставки: ищем пустую ячейку или ячейку с DELETED
            if for_insert:
                if (self.keys[index] is None or 
                    self.keys[index] == self.DELETED or 
                    self.keys[index] == key):
                    return index
            # Для поиска/удаления: ищем ключ или пустую ячейку
            else:
                if self.keys[index] is None:
                    return -1
                if self.keys[index] == key:
                    return index
            index = (index + 1) % self.capacity
            if index == start_index:  # Прошли весь массив
                return -1
    
    def put(self, key, value):
        """Добавление элемента"""
        self._resize()
        index = self._probe(key, for_insert=True)
        
        if index == -1:
            self._resize()
            index = self._probe(key, for_insert=True)
        
        if self.keys[index] is None or self.keys[index] == self.DELETED:
            self.size += 1
        
        self.keys[index] = key
        self.values[index] = value
    
    def get(self, key):
        """Получение значения по ключу"""
        index = self._probe(key)
        if index == -1:
            raise KeyError(f"Key {key} not found")
        return self.values[index]
    
    def remove(self, key):
        """Удаление элемента по ключу"""
        index = self._probe(key)
        if index == -1:
            raise KeyError(f"Key {key} not found")
        
        value = self.values[index]
        self.keys[index] = self.DELETED
        self.values[index] = None
        self.size -= 1
        return value
    
    def contains(self, key):
        """Проверка наличия ключа"""
        try:
            self.get(key)
            return True
        except KeyError:
            return False
    
    def __str__(self):
        result = []
        for i in range(self.capacity):
            if self.keys[i] is not None and self.keys[i] != self.DELETED:
                result.append(f"Index {i}: {self.keys[i]} -> {self.values[i]}")
        return "\n".join(result)


import hashlib

class Block:
    """Блок для блокчейна"""
    def __init__(self, data, previous_hash=""):
        self.data = data
        self.previous_hash = previous_hash
        self.hash = self.calculate_hash()
    
    def calculate_hash(self):
        """Вычисление хеша блока"""
        data_string = str(self.data) + self.previous_hash
        return hashlib.sha256(data_string.encode()).hexdigest()


class Blockchain:
    """Простая реализация блокчейна"""
    
    def __init__(self):
        self.chain = [self._create_genesis_block()]
    
    def _create_genesis_block(self):
        """Создание генезис-блока (первого блока)"""
        return Block("Genesis Block", "0")
    
    def add_block(self, data):
        """Добавление нового блока"""
        previous_block = self.chain[-1]
        new_block = Block(data, previous_block.hash)
        self.chain.append(new_block)
    
    def is_valid(self):
        """Проверка целостности блокчейна"""
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i - 1]
            
            # Проверка хеша текущего блока
            if current_block.hash != current_block.calculate_hash():
                return False
            
            # Проверка связи с предыдущим блоком
            if current_block.previous_hash != previous_block.hash:
                return False
        
        return True
    
    def __str__(self):
        result = []
        for i, block in enumerate(self.chain):
            result.append(f"Block {i}:")
            result.append(f"  Data: {block.data}")
            result.append(f"  Previous Hash: {block.previous_hash}")
            result.append(f"  Hash: {block.hash}")
            result.append("")
        return "\n".join(result)


# ============================================================================
# ЗАДАНИЯ 4-7: ФУНКЦИИ ДЛЯ РЕШЕНИЯ ЗАДАЧ
# ============================================================================

def array_intersection(arr1, arr2):
    """
    Задача 4: Проверка пересечения двух массивов
    Возвращает True, если массивы имеют хотя бы один общий элемент
    """
    if not arr1 or not arr2:
        return False
    
    # Используем множество для быстрой проверки
    seen = set(arr1)
    
    for item in arr2:
        if item in seen:
            return True
    
    return False


def all_unique_elements(arr):
    """
    Задача 5: Проверка уникальности элементов в массиве
    Возвращает True, если все элементы уникальны
    """
    if not arr:
        return True
    
    seen = set()
    
    for item in arr:
        if item in seen:
            return False
        seen.add(item)
    
    return True


def find_pairs_with_sum(arr, target_sum):
    """
    Задача 6: Нахождение пар с заданной суммой
    Возвращает список пар (a, b), где a + b = target_sum
    """
    if not arr:
        return []
    
    pairs = []
    seen = set()
    
    for num in arr:
        complement = target_sum - num
        if complement in seen:
            # Добавляем пару в правильном порядке (меньшее число первым)
            if complement < num:
                pairs.append((complement, num))
            else:
                pairs.append((num, complement))
        seen.add(num)
    
    return pairs


def are_anagrams(str1, str2):
    """
    Задача 7: Проверка анаграмм
    Возвращает True, если строки являются анаграммами
    """
    if len(str1) != len(str2):
        return False
    
    # Используем словарь для подсчета символов
    char_count = {}
    
    # Подсчет символов в первой строке
    for char in str1:
        char_count[char] = char_count.get(char, 0) + 1
    
    # Проверка второй строки
    for char in str2:
        if char not in char_count:
            return False
        char_count[char] -= 1
        if char_count[char] == 0:
            del char_count[char]
    
    return len(char_count) == 0


# ============================================================================
# ДЕМОНСТРАЦИЯ РАБОТЫ
# ============================================================================

def demonstrate_task_1_2():
    """Демонстрация хеш-таблиц"""
    print("=" * 60)
    print("ЗАДАНИЯ 1-2: ХЕШ-ТАБЛИЦЫ")
    print("=" * 60)
    
    print("\n1. Хеш-таблица с методом цепочек:")
    print("-" * 40)
    ht_chain = HashTableChaining()
    
    # Добавляем элементы
    ht_chain.put("apple", 10)
    ht_chain.put("banana", 20)
    ht_chain.put("orange", 30)
    ht_chain.put("grape", 40)
    ht_chain.put("apple", 15)  # Обновляем значение
    
    print("Содержимое таблицы:")
    print(ht_chain)
    
    print(f"\nПолучение значений:")
    print(f"apple -> {ht_chain.get('apple')}")
    print(f"banana -> {ht_chain.get('banana')}")
    
    print(f"\nПроверка наличия ключей:")
    print(f"Содержит 'orange': {ht_chain.contains('orange')}")
    print(f"Содержит 'melon': {ht_chain.contains('melon')}")
    
    print(f"\nУдаление 'banana':")
    ht_chain.remove('banana')
    print(f"После удаления:")
    print(ht_chain)
    
    print("\n2. Хеш-таблица с открытой адресацией:")
    print("-" * 40)
    ht_open = HashTableOpenAddressing()
    
    ht_open.put("one", 1)
    ht_open.put("two", 2)
    ht_open.put("three", 3)
    ht_open.put("four", 4)
    ht_open.put("one", 10)  # Обновление
    
    print("Содержимое таблицы:")
    print(ht_open)
    
    print(f"\nПолучение 'three': {ht_open.get('three')}")
    print(f"Удаление 'two': {ht_open.remove('two')}")
    print(f"После удаления:")
    print(ht_open)


def demonstrate_task_3():
    """Демонстрация блокчейна"""
    print("\n" + "=" * 60)
    print("ЗАДАНИЕ 3: БЛОКЧЕЙН")
    print("=" * 60)
    
    # Создаем блокчейн
    blockchain = Blockchain()
    
    # Добавляем блоки с транзакциями
    blockchain.add_block("Транзакция 1: Алиса -> Боб 10 BTC")
    blockchain.add_block("Транзакция 2: Боб -> Чарли 5 BTC")
    blockchain.add_block("Транзакция 3: Чарли -> Дэвид 3 BTC")
    
    print("Структура блокчейна:")
    print(blockchain)
    
    print(f"Проверка целостности: {blockchain.is_valid()}")
    
    # Попробуем изменить данные в блоке (симуляция атаки)
    print("\nСимуляция попытки изменения данных...")
    blockchain.chain[1].data = "Транзакция 1: Алиса -> Злоумышленник 100 BTC"
    print(f"Проверка целостности после изменения: {blockchain.is_valid()}")


def demonstrate_tasks_4_7():
    """Демонстрация задач 4-7"""
    print("\n" + "=" * 60)
    print("ЗАДАНИЯ 4-7: РЕШЕНИЕ ЗАДАЧ")
    print("=" * 60)
    
    # Задача 4: Пересечение массивов
    print("\nЗадача 4: Проверка пересечения двух массивов")
    print("-" * 40)
    arr1 = [1, 2, 3, 4, 5]
    arr2 = [6, 7, 8, 9, 10]
    arr3 = [5, 6, 7, 8, 9]
    
    print(f"Массив 1: {arr1}")
    print(f"Массив 2: {arr2}")
    print(f"Есть пересечение? {array_intersection(arr1, arr2)}")
    
    print(f"\nМассив 1: {arr1}")
    print(f"Массив 3: {arr3}")
    print(f"Есть пересечение? {array_intersection(arr1, arr3)}")
    
    # Задача 5: Уникальность элементов
    print("\nЗадача 5: Проверка уникальности элементов в массиве")
    print("-" * 40)
    unique_arr = [1, 2, 3, 4, 5]
    duplicate_arr = [1, 2, 3, 2, 4]
    
    print(f"Массив {unique_arr}: все элементы уникальны? {all_unique_elements(unique_arr)}")
    print(f"Массив {duplicate_arr}: все элементы уникальны? {all_unique_elements(duplicate_arr)}")
    
    # Задача 6: Пары с заданной суммой
    print("\nЗадача 6: Нахождение пар с заданной суммой")
    print("-" * 40)
    numbers = [2, 7, 11, 15, 3, 6, 8, 1]
    target = 9
    
    print(f"Массив: {numbers}")
    print(f"Целевая сумма: {target}")
    pairs = find_pairs_with_sum(numbers, target)
    print(f"Найденные пары: {pairs}")
    
    if pairs:
        print("Проверка:")
        for a, b in pairs:
            print(f"  {a} + {b} = {a + b}")
    
    # Задача 7: Проверка анаграмм
    print("\nЗадача 7: Проверка анаграмм")
    print("-" * 40)
    test_cases = [
        ("listen", "silent"),
        ("hello", "world"),
        ("анаграмма", "маграана"),
        ("python", "typhon")
    ]
    
    for str1, str2 in test_cases:
        result = are_anagrams(str1, str2)
        print(f"'{str1}' и '{str2}': {'Анаграммы' if result else 'Не анаграммы'}")


def main():
    """Основная функция для демонстрации всех заданий"""
    print("=" * 70)
    print("ЛАБОРАТОРНАЯ РАБОТА: ХЕШ-ТАБЛИЦЫ И АЛГОРИТМЫ")
    print("=" * 70)
    
    # Демонстрируем все задания
    demonstrate_task_1_2()
    demonstrate_task_3()
    demonstrate_tasks_4_7()
    
    print("\n" + "=" * 70)
    print("ВСЕ ЗАДАНИЯ ВЫПОЛНЕНЫ!")
    print("=" * 70)


# ============================================================================
# ТЕСТИРОВАНИЕ
# ============================================================================

def run_tests():
    """Запуск тестов для проверки корректности"""
    print("Запуск тестов...")
    
    # Тест для задачи 4
    assert array_intersection([1, 2, 3], [4, 5, 6]) == False
    assert array_intersection([1, 2, 3], [3, 4, 5]) == True
    assert array_intersection([], [1, 2, 3]) == False
    
    # Тест для задачи 5
    assert all_unique_elements([1, 2, 3, 4]) == True
    assert all_unique_elements([1, 2, 3, 1]) == False
    assert all_unique_elements([]) == True
    
    # Тест для задачи 6
    pairs = find_pairs_with_sum([1, 2, 3, 4, 5], 6)
    assert (1, 5) in pairs and (2, 4) in pairs
    assert len(pairs) == 2
    
    # Тест для задачи 7
    assert are_anagrams("listen", "silent") == True
    assert are_anagrams("hello", "world") == False
    assert are_anagrams("", "") == True
    
    print("✓ Все тесты пройдены успешно!")
    return True


if __name__ == "__main__":
    # Запускаем тесты
    if run_tests():
        print("\n" + "=" * 70)
        print("ТЕСТЫ УСПЕШНО ПРОЙДЕНЫ. ЗАПУСК ДЕМОНСТРАЦИИ...")
        print("=" * 70)
        
        # Запускаем демонстрацию
        main()
    else:
        print("Тесты не пройдены. Проверьте код.")
```

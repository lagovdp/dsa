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
class TreeNode:
    """Узел бинарного дерева"""
    def __init__(self, key):
        self.key = key
        self.left = None
        self.right = None
        self.height = 1  # для AVL-дерева


# ============================================
# ЗАДАНИЕ 1: БИНАРНОЕ ДЕРЕВО ПОИСКА (BST)
# ============================================

class BinarySearchTree:
    """Бинарное дерево поиска"""
    
    def __init__(self):
        self.root = None
    
    # -------------------------------------------------
    # 1. Формирование бинарного дерева
    # -------------------------------------------------
    
    def insert(self, key):
        """Вставка элемента в дерево"""
        if not self._is_valid_key(key):
            raise ValueError(f"Некорректный ключ: {key}")
        
        self.root = self._insert_recursive(self.root, key)
    
    def _insert_recursive(self, node, key):
        """Рекурсивная вставка"""
        if node is None:
            return TreeNode(key)
        
        if key < node.key:
            node.left = self._insert_recursive(node.left, key)
        elif key > node.key:
            node.right = self._insert_recursive(node.right, key)
        
        return node
    
    def insert_multiple(self, keys):
        """Вставка нескольких элементов"""
        for key in keys:
            self.insert(key)
    
    # -------------------------------------------------
    # 2. Обходы дерева
    # -------------------------------------------------
    
    def preorder_traversal(self):
        """Прямой обход (корень → левое → правое)"""
        result = []
        self._preorder_recursive(self.root, result)
        return result
    
    def _preorder_recursive(self, node, result):
        if node:
            result.append(node.key)
            self._preorder_recursive(node.left, result)
            self._preorder_recursive(node.right, result)
    
    def inorder_traversal(self):
        """Симметричный обход (левое → корень → правое)"""
        result = []
        self._inorder_recursive(self.root, result)
        return result
    
    def _inorder_recursive(self, node, result):
        if node:
            self._inorder_recursive(node.left, result)
            result.append(node.key)
            self._inorder_recursive(node.right, result)
    
    def postorder_traversal(self):
        """Обратный обход (левое → правое → корень)"""
        result = []
        self._postorder_recursive(self.root, result)
        return result
    
    def _postorder_recursive(self, node, result):
        if node:
            self._postorder_recursive(node.left, result)
            self._postorder_recursive(node.right, result)
            result.append(node.key)
    
    # -------------------------------------------------
    # 3. Удаление вершины
    # -------------------------------------------------
    
    def delete(self, key):
        """Удаление элемента из дерева"""
        if self.is_empty():
            raise ValueError("Дерево пустое")
        
        if not self.search(key):
            raise ValueError(f"Элемент {key} не найден в дереве")
        
        self.root = self._delete_recursive(self.root, key)
    
    def _delete_recursive(self, node, key):
        """Рекурсивное удаление"""
        if node is None:
            return node
        
        if key < node.key:
            node.left = self._delete_recursive(node.left, key)
        elif key > node.key:
            node.right = self._delete_recursive(node.right, key)
        else:
            # Нашли узел для удаления
            
            # Узел с одним или без детей
            if node.left is None:
                return node.right
            elif node.right is None:
                return node.left
            
            # Узел с двумя детьми: находим минимальный в правом поддереве
            temp = self._min_value_node(node.right)
            node.key = temp.key
            node.right = self._delete_recursive(node.right, temp.key)
        
        return node
    
    def _min_value_node(self, node):
        """Находит узел с минимальным значением"""
        current = node
        while current.left is not None:
            current = current.left
        return current
    
    # -------------------------------------------------
    # 4. Поиск вершины
    # -------------------------------------------------
    
    def search(self, key):
        """Поиск элемента в дереве"""
        if not self._is_valid_key(key):
            return False
        
        return self._search_recursive(self.root, key)
    
    def _search_recursive(self, node, key):
        """Рекурсивный поиск"""
        if node is None:
            return False
        
        if node.key == key:
            return True
        elif key < node.key:
            return self._search_recursive(node.left, key)
        else:
            return self._search_recursive(node.right, key)
    
    # -------------------------------------------------
    # 5. Печать дерева
    # -------------------------------------------------
    
    def print_tree(self):
        """Красивый вывод дерева на экран"""
        if self.is_empty():
            print("Дерево пустое")
            return
        
        lines = self._build_tree_string(self.root, 0, False, "")
        for line in lines:
            print(line)
    
    def _build_tree_string(self, node, level, is_right, prefix):
        """Рекурсивное построение строкового представления"""
        if node is None:
            return []
        
        result = []
        new_prefix = prefix + ("    " if is_right else "│   ")
        
        # Правое поддерево
        right_lines = self._build_tree_string(node.right, level + 1, True, new_prefix)
        result.extend(right_lines)
        
        # Текущий узел
        connector = "└── " if is_right else "┌── " if level > 0 else ""
        result.append(prefix + connector + str(node.key))
        
        # Левое поддерево
        left_lines = self._build_tree_string(node.left, level + 1, False, new_prefix)
        result.extend(left_lines)
        
        return result
    
    def __str__(self):
        """Строковое представление дерева"""
        if self.is_empty():
            return "BinarySearchTree: пустое"
        
        inorder = self.inorder_traversal()
        return f"BinarySearchTree: {inorder}"
    
    # -------------------------------------------------
    # 6. Проверка пустоты
    # -------------------------------------------------
    
    def is_empty(self):
        """Проверка, пустое ли дерево"""
        return self.root is None
    
    # -------------------------------------------------
    # 7. Определение высоты
    # -------------------------------------------------
    
    def height(self):
        """Высота дерева"""
        return self._height_recursive(self.root)
    
    def _height_recursive(self, node):
        """Рекурсивное вычисление высоты"""
        if node is None:
            return 0
        
        left_height = self._height_recursive(node.left)
        right_height = self._height_recursive(node.right)
        
        return max(left_height, right_height) + 1
    
    # -------------------------------------------------
    # Вспомогательные методы
    # -------------------------------------------------
    
    def _is_valid_key(self, key):
        """Проверка корректности ключа"""
        return isinstance(key, (int, float))


# ============================================
# ЗАДАНИЕ 2: AVL-ДЕРЕВО (для четных вариантов)
# ============================================

class AVLTree(BinarySearchTree):
    """Самобалансирующееся AVL-дерево"""
    
    def insert(self, key):
        """Вставка с балансировкой"""
        if not self._is_valid_key(key):
            raise ValueError(f"Некорректный ключ: {key}")
        
        self.root = self._insert_avl(self.root, key)
    
    def _insert_avl(self, node, key):
        """Рекурсивная вставка с балансировкой"""
        # Обычная вставка BST
        if node is None:
            return TreeNode(key)
        
        if key < node.key:
            node.left = self._insert_avl(node.left, key)
        elif key > node.key:
            node.right = self._insert_avl(node.right, key)
        else:
            return node  # Дубликаты не допускаются
        
        # Обновление высоты
        node.height = 1 + max(self._get_height(node.left), 
                              self._get_height(node.right))
        
        # Балансировка
        balance = self._get_balance(node)
        
        # Левый-левый случай
        if balance > 1 and key < node.left.key:
            return self._rotate_right(node)
        
        # Правый-правый случай
        if balance < -1 and key > node.right.key:
            return self._rotate_left(node)
        
        # Левый-правый случай
        if balance > 1 and key > node.left.key:
            node.left = self._rotate_left(node.left)
            return self._rotate_right(node)
        
        # Правый-левый случай
        if balance < -1 and key < node.right.key:
            node.right = self._rotate_right(node.right)
            return self._rotate_left(node)
        
        return node
    
    def delete(self, key):
        """Удаление с балансировкой"""
        if self.is_empty():
            raise ValueError("Дерево пустое")
        
        if not self.search(key):
            raise ValueError(f"Элемент {key} не найден в дереве")
        
        self.root = self._delete_avl(self.root, key)
    
    def _delete_avl(self, node, key):
        """Рекурсивное удаление с балансировкой"""
        # Обычное удаление BST
        if node is None:
            return node
        
        if key < node.key:
            node.left = self._delete_avl(node.left, key)
        elif key > node.key:
            node.right = self._delete_avl(node.right, key)
        else:
            # Нашли узел для удаления
            
            # Узел с одним или без детей
            if node.left is None:
                return node.right
            elif node.right is None:
                return node.left
            
            # Узел с двумя детьми
            temp = self._min_value_node(node.right)
            node.key = temp.key
            node.right = self._delete_avl(node.right, temp.key)
        
        # Если дерево было с одним узлом
        if node is None:
            return node
        
        # Обновление высоты
        node.height = 1 + max(self._get_height(node.left), 
                              self._get_height(node.right))
        
        # Балансировка
        balance = self._get_balance(node)
        
        # Балансировка после удаления
        # Левый-левый
        if balance > 1 and self._get_balance(node.left) >= 0:
            return self._rotate_right(node)
        
        # Левый-правый
        if balance > 1 and self._get_balance(node.left) < 0:
            node.left = self._rotate_left(node.left)
            return self._rotate_right(node)
        
        # Правый-правый
        if balance < -1 and self._get_balance(node.right) <= 0:
            return self._rotate_left(node)
        
        # Правый-левый
        if balance < -1 and self._get_balance(node.right) > 0:
            node.right = self._rotate_right(node.right)
            return self._rotate_left(node)
        
        return node
    
    def _get_height(self, node):
        """Получение высоты узла"""
        if node is None:
            return 0
        return node.height
    
    def _get_balance(self, node):
        """Получение баланса узла"""
        if node is None:
            return 0
        return self._get_height(node.left) - self._get_height(node.right)
    
    def _rotate_right(self, z):
        """Правый поворот"""
        y = z.left
        T3 = y.right
        
        # Поворот
        y.right = z
        z.left = T3
        
        # Обновление высот
        z.height = 1 + max(self._get_height(z.left), 
                           self._get_height(z.right))
        y.height = 1 + max(self._get_height(y.left), 
                           self._get_height(y.right))
        
        return y
    
    def _rotate_left(self, z):
        """Левый поворот"""
        y = z.right
        T2 = y.left
        
        # Поворот
        y.left = z
        z.right = T2
        
        # Обновление высот
        z.height = 1 + max(self._get_height(z.left), 
                           self._get_height(z.right))
        y.height = 1 + max(self._get_height(y.left), 
                           self._get_height(y.right))
        
        return y
    
    def __str__(self):
        if self.is_empty():
            return "AVLTree: пустое"
        
        inorder = self.inorder_traversal()
        return f"AVLTree: {inorder}"


# ============================================
# ИНДИВИДУАЛЬНОЕ ЗАДАНИЕ (вариант 9)
# ============================================

def is_binary_search_tree(tree):
    """
    Проверяет, является ли бинарное дерево деревом поиска.
    
    Args:
        tree: экземпляр BinarySearchTree или AVLTree
    
    Returns:
        bool: True если дерево является BST, иначе False
    """
    def _is_bst_recursive(node, min_val, max_val):
        """Рекурсивная проверка"""
        if node is None:
            return True
        
        # Проверяем условие BST: min_val < node.key < max_val
        if (min_val is not None and node.key <= min_val) or \
           (max_val is not None and node.key >= max_val):
            return False
        
        # Рекурсивно проверяем левое и правое поддеревья
        return (_is_bst_recursive(node.left, min_val, node.key) and
                _is_bst_recursive(node.right, node.key, max_val))
    
    return _is_bst_recursive(tree.root, None, None)


# ============================================
# ДЕМОНСТРАЦИЯ И ТЕСТИРОВАНИЕ
# ============================================

def demonstrate_bst():
    """Демонстрация работы BST"""
    print("=" * 60)
    print("ДЕМОНСТРАЦИЯ БИНАРНОГО ДЕРЕВА ПОИСКА (BST)")
    print("=" * 60)
    
    # Создание дерева
    bst = BinarySearchTree()
    bst.insert_multiple([50, 30, 70, 20, 40, 60, 80])
    
    print("1. Исходное дерево:")
    bst.print_tree()
    
    print(f"\n2. Высота дерева: {bst.height()}")
    print(f"3. Дерево пустое? {bst.is_empty()}")
    
    print("\n4. Обходы дерева:")
    print(f"   Прямой обход: {bst.preorder_traversal()}")
    print(f"   Симметричный обход: {bst.inorder_traversal()}")
    print(f"   Обратный обход: {bst.postorder_traversal()}")
    
    print("\n5. Поиск элементов:")
    print(f"   Поиск 40: {'Найден' if bst.search(40) else 'Не найден'}")
    print(f"   Поиск 90: {'Найден' if bst.search(90) else 'Не найден'}")
    
    print("\n6. Удаление элемента 20:")
    bst.delete(20)
    print("   Дерево после удаления:")
    bst.print_tree()
    
    print(f"\n7. Новая высота: {bst.height()}")
    print(f"8. Симметричный обход: {bst.inorder_traversal()}")


def demonstrate_avl():
    """Демонстрация работы AVL-дерева"""
    print("\n" + "=" * 60)
    print("ДЕМОНСТРАЦИЯ AVL-ДЕРЕВА")
    print("=" * 60)
    
    # Создание AVL-дерева
    avl = AVLTree()
    
    # Вставляем элементы в порядке, который создает несбалансированное BST
    avl.insert_multiple([10, 20, 30, 40, 50, 25])
    
    print("1. AVL-дерево после вставки 10, 20, 30, 40, 50, 25:")
    avl.print_tree()
    
    print(f"\n2. Высота AVL-дерева: {avl.height()}")
    print(f"3. Симметричный обход: {avl.inorder_traversal()}")
    
    print("\n4. Удаление элемента 40:")
    avl.delete(40)
    print("   Дерево после удаления:")
    avl.print_tree()
    
    print(f"\n5. Новая высота: {avl.height()}")


def test_individual_task():
    """Тестирование индивидуального задания"""
    print("\n" + "=" * 60)
    print("ТЕСТИРОВАНИЕ: ЯВЛЯЕТСЯ ЛИ ДЕРЕВО BST?")
    print("=" * 60)
    
    # Тест 1: Правильное BST
    bst1 = BinarySearchTree()
    bst1.insert_multiple([50, 30, 70, 20, 40, 60, 80])
    
    print("Тест 1 - Правильное BST:")
    bst1.print_tree()
    print(f"Результат проверки: {is_binary_search_tree(bst1)} ✓")
    
    # Тест 2: AVL-дерево (тоже BST)
    avl = AVLTree()
    avl.insert_multiple([50, 30, 70, 20, 40, 60, 80])
    
    print("\nТест 2 - AVL-дерево:")
    avl.print_tree()
    print(f"Результат проверки: {is_binary_search_tree(avl)} ✓")
    
    # Тест 3: Неправильное "дерево" (не BST)
    print("\nТест 3 - НЕ BST (создаем вручную неправильную структуру):")
    
    # Создаем неправильное дерево вручную
    root = TreeNode(50)
    root.left = TreeNode(30)
    root.right = TreeNode(40)  # Ошибка: 40 < 50, должно быть в левом поддереве
    
    # Создаем временное дерево для теста
    class TempTree:
        def __init__(self, root):
            self.root = root
    
    wrong_tree = TempTree(root)
    print("   Структура: 50 -> левое: 30, правое: 40 (ошибка!)")
    print(f"   Результат проверки: {is_binary_search_tree(wrong_tree)} ✗")
    
    # Тест 4: Пустое дерево
    empty_tree = BinarySearchTree()
    print("\nТест 4 - Пустое дерево:")
    print(f"   Результат проверки: {is_binary_search_tree(empty_tree)} ✓")


def main():
    """Основная функция демонстрации"""
    print("\n" + "=" * 60)
    print("ЛАБОРАТОРНАЯ РАБОТА: БИНАРНЫЕ ДЕРЕВЬЯ")
    print("=" * 60)
    
    demonstrate_bst()
    demonstrate_avl()
    test_individual_task()
    
    print("\n" + "=" * 60)
    print("ВСЕ ЗАДАНИЯ ВЫПОЛНЕНЫ УСПЕШНО!")
    print("=" * 60)


if __name__ == "__main__":
    main()
```

```python

```

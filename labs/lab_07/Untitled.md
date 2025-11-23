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
# === ЗАДАНИЕ 1: КЛАСС GRAPH ===

from collections import deque
import heapq

class Graph:
    def __init__(self, directed=True):
        self.graph = {}
        self.directed = directed
        self.vertices = set()
    
    def add_vertex(self, vertex):
        """Добавление вершины в граф"""
        if vertex not in self.graph:
            self.graph[vertex] = {}
            self.vertices.add(vertex)
    
    def add_edge(self, from_vertex, to_vertex, weight=1):
        """Добавление ребра в граф"""
        self.add_vertex(from_vertex)
        self.add_vertex(to_vertex)
        
        self.graph[from_vertex][to_vertex] = weight
        
        if not self.directed:
            if to_vertex not in self.graph:
                self.graph[to_vertex] = {}
            self.graph[to_vertex][from_vertex] = weight
    
    def remove_vertex(self, vertex):
        """Удаление вершины из графа"""
        if vertex in self.graph:
            del self.graph[vertex]
            self.vertices.remove(vertex)
            
        for v in self.graph:
            if vertex in self.graph[v]:
                del self.graph[v][vertex]
    
    def remove_edge(self, from_vertex, to_vertex):
        """Удаление ребра из графа"""
        if from_vertex in self.graph and to_vertex in self.graph[from_vertex]:
            del self.graph[from_vertex][to_vertex]
            
        if not self.directed and to_vertex in self.graph and from_vertex in self.graph[to_vertex]:
            del self.graph[to_vertex][from_vertex]
    
    def get_vertices(self):
        """Получение списка всех вершин"""
        return list(self.vertices)
    
    def get_edges(self):
        """Получение списка всех рёбер"""
        edges = []
        for from_vertex in self.graph:
            for to_vertex, weight in self.graph[from_vertex].items():
                edges.append((from_vertex, to_vertex, weight))
        return edges
    
    def has_vertex(self, vertex):
        """Проверка наличия вершины"""
        return vertex in self.graph
    
    def has_edge(self, from_vertex, to_vertex):
        """Проверка наличия ребра"""
        return (from_vertex in self.graph and 
                to_vertex in self.graph[from_vertex])
    
    def get_neighbors(self, vertex):
        """Получение соседей вершины"""
        if vertex in self.graph:
            return list(self.graph[vertex].items())
        return []
    
    def get_degree(self, vertex):
        """Получение степени вершины"""
        if vertex not in self.graph:
            return 0
        return len(self.graph[vertex])
    
    def display(self):
        """Отображение графа"""
        print("Граф:")
        for vertex in sorted(self.graph.keys()):
            neighbors = self.graph[vertex]
            if neighbors:
                neighbors_str = ", ".join([f"{neighbor}({weight})" 
                                         for neighbor, weight in neighbors.items()])
                print(f"  {vertex} -> {neighbors_str}")
            else:
                print(f"  {vertex} -> нет исходящих рёбер")

print("=== ЗАДАНИЕ 1 ВЫПОЛНЕНО: КЛАСС Graph РЕАЛИЗОВАН ===")

# === ЗАДАНИЕ 2: ДИАЛОГОВОЕ ПРИЛОЖЕНИЕ ===

def initialize_graph():
    """Инициализация пустого графа"""
    print("Выберите тип графа:")
    print("1. Ориентированный")
    print("2. Неориентированный")
    
    choice = input("Ваш выбор (1-2): ").strip()
    directed = choice == '1'
    
    graph = Graph(directed=directed)
    graph_type = "ориентированный" if directed else "неориентированный"
    print(f"Создан пустой {graph_type} граф!")
    return graph

def manual_graph_initialization():
    """Ручная инициализация графа"""
    graph = initialize_graph()
    
    print("\nДобавление вершин и рёбер:")
    while True:
        print("\n1. Добавить вершину")
        print("2. Добавить ребро")
        print("3. Завершить инициализацию")
        
        choice = input("Ваш выбор (1-3): ").strip()
        
        if choice == '1':
            try:
                vertex = int(input("Введите номер вершины: "))
                graph.add_vertex(vertex)
                print(f"Вершина {vertex} добавлена!")
            except ValueError:
                print("Ошибка: введите целое число!")
                
        elif choice == '2':
            try:
                from_v = int(input("Введите начальную вершину: "))
                to_v = int(input("Введите конечную вершину: "))
                weight = int(input("Введите вес ребра: "))
                graph.add_edge(from_v, to_v, weight)
                print(f"Ребро {from_v} -> {to_v} (вес: {weight}) добавлено!")
            except ValueError:
                print("Ошибка: введите целые числа!")
                
        elif choice == '3':
            break
        else:
            print("Неверный выбор!")
    
    return graph

def dialog_loop():
    """Основной диалоговый цикл приложения"""
    graph = None
    
    print("=== ПРОГРАММА ДЛЯ РАБОТЫ С ГРАФАМИ ===")
    
    while True:
        print("\n" + "="*50)
        print("ГЛАВНОЕ МЕНЮ:")
        print("1. Инициализировать граф")
        print("2. Ручная инициализация графа")
        print("3. Показать граф")
        print("4. Добавить вершину")
        print("5. Добавить ребро")
        print("6. Удалить вершину")
        print("7. Удалить ребро")
        print("8. Список вершин")
        print("9. Список рёбер")
        print("10. Перейти к индивидуальным заданиям")
        print("11. Выход")
        print("="*50)
        
        choice = input("Выберите действие (1-11): ").strip()
        
        if choice == '1':
            graph = initialize_graph()
            
        elif choice == '2':
            graph = manual_graph_initialization()
            
        elif choice == '3':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            graph.display()
            
        elif choice == '4':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            try:
                vertex = int(input("Введите номер вершины: "))
                graph.add_vertex(vertex)
                print(f"Вершина {vertex} добавлена!")
            except ValueError:
                print("Ошибка: введите целое число!")
                
        elif choice == '5':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            try:
                from_v = int(input("Введите начальную вершину: "))
                to_v = int(input("Введите конечную вершину: "))
                weight = int(input("Введите вес ребра: "))
                graph.add_edge(from_v, to_v, weight)
                print(f"Ребро {from_v} -> {to_v} (вес: {weight}) добавлено!")
            except ValueError:
                print("Ошибка: введите целые числа!")
                
        elif choice == '6':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            try:
                vertex = int(input("Введите номер вершины для удаления: "))
                if graph.has_vertex(vertex):
                    graph.remove_vertex(vertex)
                    print(f"Вершина {vertex} удалена!")
                else:
                    print(f"Вершина {vertex} не существует!")
            except ValueError:
                print("Ошибка: введите целое число!")
                
        elif choice == '7':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            try:
                from_v = int(input("Введите начальную вершину: "))
                to_v = int(input("Введите конечную вершину: "))
                if graph.has_edge(from_v, to_v):
                    graph.remove_edge(from_v, to_v)
                    print(f"Ребро {from_v} -> {to_v} удалено!")
                else:
                    print(f"Ребро {from_v} -> {to_v} не существует!")
            except ValueError:
                print("Ошибка: введите целые числа!")
                
        elif choice == '8':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            vertices = graph.get_vertices()
            print(f"Вершины графа: {sorted(vertices)}")
            
        elif choice == '9':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            edges = graph.get_edges()
            print("Рёбра графа:")
            for from_v, to_v, weight in edges:
                print(f"  {from_v} -> {to_v} (вес: {weight})")
                
        elif choice == '10':
            if graph is None:
                print("Сначала инициализируйте граф!")
                continue
            individual_task_menu(graph)
                
        elif choice == '11':
            print("Выход из программы...")
            break
            
        else:
            print("Неверный выбор! Попробуйте снова.")

print("=== ЗАДАНИЕ 2 ВЫПОЛНЕНО: ДИАЛОГОВОЕ ПРИЛОЖЕНИЕ РЕАЛИЗОВАНО ===")

# === ЗАДАНИЕ 3: ИНДИВИДУАЛЬНОЕ ЗАДАНИЕ ===

def analyze_graph(graph):
    """Анализ графа - индивидуальное задание"""
    print("\n" + "="*50)
    print("ИНДИВИДУАЛЬНОЕ ЗАДАНИЕ: АНАЛИЗ ГРАФА")
    print("="*50)
    
    if not graph.get_vertices():
        print("Граф пуст!")
        return
    
    vertices = graph.get_vertices()
    edges = graph.get_edges()
    
    print(f"Количество вершин: {len(vertices)}")
    print(f"Количество рёбер: {len(edges)}")
    
    # Анализ степеней вершин
    max_degree = -1
    min_degree = float('inf')
    max_degree_vertices = []
    min_degree_vertices = []
    
    for vertex in vertices:
        degree = graph.get_degree(vertex)
        if degree > max_degree:
            max_degree = degree
            max_degree_vertices = [vertex]
        elif degree == max_degree:
            max_degree_vertices.append(vertex)
            
        if degree < min_degree:
            min_degree = degree
            min_degree_vertices = [vertex]
        elif degree == min_degree:
            min_degree_vertices.append(vertex)
    
    print(f"Максимальная степень: {max_degree} (вершины: {max_degree_vertices})")
    print(f"Минимальная степень: {min_degree} (вершины: {min_degree_vertices})")
    
    # Изолированные вершины
    isolated = [v for v in vertices if graph.get_degree(v) == 0]
    if isolated:
        print(f"Изолированные вершины: {isolated}")
    
    # Петли
    loops = [(v, v, w) for v, to, w in edges if v == to]
    if loops:
        print("Найдены петли:")
        for from_v, to_v, weight in loops:
            print(f"  {from_v} -> {to_v} (вес: {weight})")
    
    # Статистика по весам
    if edges:
        weights = [weight for _, _, weight in edges]
        print(f"Вес рёбер: средний = {sum(weights)/len(weights):.2f}, макс = {max(weights)}, мин = {min(weights)}")

print("=== ЗАДАНИЕ 3 ВЫПОЛНЕНО: ИНДИВИДУАЛЬНОЕ ЗАДАНИЕ РЕАЛИЗОВАНО ===")

# === ЗАДАНИЕ 4: КРАТЧАЙШИЙ ПУТЬ (ПО ВАРИАНТАМ) ===

def load_graph_4():
    """Загрузка графа для задания 4"""
    graph = Graph(directed=True)
    
    edges_data = [
        (1, 7, 9), (1, 9, 1), (9, 5, 8), 
        (1, 4, 2), (3, 4, 5), (6, 4, 3), 
        (2, 2, 3)
    ]
    
    for from_v, to_v, weight in edges_data:
        graph.add_edge(from_v, to_v, weight)
    
    return graph

def dijkstra_shortest_path(graph, start_vertex, end_vertex):
    """Алгоритм Дейкстры для поиска кратчайшего пути"""
    if start_vertex not in graph.graph or end_vertex not in graph.graph:
        return None, float('inf')
    
    distances = {vertex: float('inf') for vertex in graph.vertices}
    distances[start_vertex] = 0
    previous = {vertex: None for vertex in graph.vertices}
    visited = set()
    
    priority_queue = [(0, start_vertex)]
    
    while priority_queue:
        current_distance, current_vertex = heapq.heappop(priority_queue)
        
        if current_vertex in visited:
            continue
            
        visited.add(current_vertex)
        
        if current_vertex == end_vertex:
            break
        
        for neighbor, weight in graph.get_neighbors(current_vertex):
            if neighbor in visited:
                continue
                
            new_distance = current_distance + weight
            if new_distance < distances[neighbor]:
                distances[neighbor] = new_distance
                previous[neighbor] = current_vertex
                heapq.heappush(priority_queue, (new_distance, neighbor))
    
    if distances[end_vertex] == float('inf'):
        return None, float('inf')
    
    path = []
    current = end_vertex
    while current is not None:
        path.append(current)
        current = previous[current]
    path.reverse()
    
    return path, distances[end_vertex]

def bfs_shortest_path_unweighted(graph, start_vertex, end_vertex):
    """BFS для поиска кратчайшего пути в невзвешенном графе"""
    if start_vertex not in graph.graph or end_vertex not in graph.graph:
        return None, float('inf')
    
    visited = set()
    queue = deque([(start_vertex, [start_vertex])])
    
    while queue:
        current_vertex, path = queue.popleft()
        
        if current_vertex == end_vertex:
            return path, len(path) - 1
        
        if current_vertex not in visited:
            visited.add(current_vertex)
            
            for neighbor, _ in graph.get_neighbors(current_vertex):
                if neighbor not in visited:
                    queue.append((neighbor, path + [neighbor]))
    
    return None, float('inf')

def task_4_interface():
    """Интерфейс для задания 4"""
    print("\n" + "="*50)
    print("ЗАДАНИЕ 4: КРАТЧАЙШИЙ ПУТЬ")
    print("="*50)
    
    graph = load_graph_4()
    print("Загружен граф для задания 4:")
    graph.display()
    
    vertices = graph.get_vertices()
    print(f"\nДоступные вершины: {sorted(vertices)}")
    
    while True:
        print("\nВыберите алгоритм:")
        print("1. Дейкстра (для взвешенного графа)")
        print("2. BFS (для невзвешенного графа)")
        print("3. Вернуться в меню")
        
        choice = input("Ваш выбор (1-3): ").strip()
        
        if choice in ['1', '2']:
            try:
                start = int(input("Введите начальную вершину: "))
                end = int(input("Введите конечную вершину: "))
                
                if start not in vertices or end not in vertices:
                    print("Ошибка: вершины не существуют в графе!")
                    continue
                
                if choice == '1':
                    path, distance = dijkstra_shortest_path(graph, start, end)
                    algorithm_name = "Дейкстра"
                else:
                    path, distance = bfs_shortest_path_unweighted(graph, start, end)
                    algorithm_name = "BFS"
                
                if path:
                    print(f"\nКратчайший путь от {start} до {end} ({algorithm_name}):")
                    print(" -> ".join(map(str, path)))
                    if choice == '1':
                        print(f"Общее расстояние: {distance}")
                    else:
                        print(f"Количество шагов: {distance}")
                else:
                    print(f"Путь от {start} до {end} не существует!")
                    
            except ValueError:
                print("Ошибка: введите целые числа!")
                
        elif choice == '3':
            break
        else:
            print("Неверный выбор!")

print("=== ЗАДАНИЕ 4 ВЫПОЛНЕНО: КРАТЧАЙШИЙ ПУТЬ РЕАЛИЗОВАН ===")

# === ЗАДАНИЕ 5: АЛГОРИТМ ДЕЙКСТРЫ ===

def load_graph_5():
    """Загрузка графа для задания 5"""
    graph = Graph(directed=True)
    
    edges_data = [
        (9, 2, 7), (1, 4, 5), (6, 1, 2),
        (3, 2, 8), (7, 5, 2), (8, 8, 0)
    ]
    
    for from_v, to_v, weight in edges_data:
        graph.add_edge(from_v, to_v, weight)
    
    return graph

def task_5_interface():
    """Интерфейс для задания 5"""
    print("\n" + "="*50)
    print("ЗАДАНИЕ 5: АЛГОРИТМ ДЕЙКСТРЫ")
    print("="*50)
    
    graph = load_graph_5()
    print("Загружен граф для задания 5:")
    graph.display()
    
    vertices = graph.get_vertices()
    print(f"\nДоступные вершины: {sorted(vertices)}")
    
    while True:
        print("\nАЛГОРИТМ ДЕЙКСТРЫ")
        print("1. Найти кратчайший путь между двумя вершинами")
        print("2. Найти кратчайшие пути от одной вершины ко всем")
        print("3. Вернуться в меню")
        
        choice = input("Ваш выбор (1-3): ").strip()
        
        if choice == '1':
            try:
                start = int(input("Введите начальную вершину: "))
                end = int(input("Введите конечную вершину: "))
                
                if start not in vertices or end not in vertices:
                    print("Ошибка: вершины не существуют в графе!")
                    continue
                
                path, distance = dijkstra_shortest_path(graph, start, end)
                
                if path:
                    print(f"\nКратчайший путь от {start} до {end}:")
                    print(" -> ".join(map(str, path)))
                    print(f"Общее расстояние: {distance}")
                else:
                    print(f"Путь от {start} до {end} не существует!")
                    
            except ValueError:
                print("Ошибка: введите целые числа!")
                
        elif choice == '2':
            try:
                start = int(input("Введите начальную вершину: "))
                
                if start not in vertices:
                    print("Ошибка: вершина не существует в графе!")
                    continue
                
                print(f"\nКратчайшие пути от вершины {start}:")
                for end_vertex in sorted(vertices):
                    if end_vertex != start:
                        path, distance = dijkstra_shortest_path(graph, start, end_vertex)
                        if path:
                            print(f"  {start} -> {end_vertex}: {' -> '.join(map(str, path))} (расстояние: {distance})")
                        else:
                            print(f"  {start} -> {end_vertex}: путь не существует")
                
            except ValueError:
                print("Ошибка: введите целое число!")
                
        elif choice == '3':
            break
        else:
            print("Неверный выбор!")

print("=== ЗАДАНИЕ 5 ВЫПОЛНЕНО: АЛГОРИТМ ДЕЙКСТРЫ РЕАЛИЗОВАН ===")

# === ЗАДАНИЕ 6: ДРУГИЕ АЛГОРИТМЫ ===

def bellman_ford_shortest_path(graph, start_vertex, end_vertex):
    """Алгоритм Беллмана-Форда для поиска кратчайшего пути"""
    if start_vertex not in graph.graph or end_vertex not in graph.graph:
        return None, float('inf')
    
    distances = {vertex: float('inf') for vertex in graph.vertices}
    distances[start_vertex] = 0
    previous = {vertex: None for vertex in graph.vertices}
    
    edges = graph.get_edges()
    
    for _ in range(len(graph.vertices) - 1):
        updated = False
        for from_v, to_v, weight in edges:
            if distances[from_v] != float('inf') and distances[from_v] + weight < distances[to_v]:
                distances[to_v] = distances[from_v] + weight
                previous[to_v] = from_v
                updated = True
        
        if not updated:
            break
    
    for from_v, to_v, weight in edges:
        if distances[from_v] != float('inf') and distances[from_v] + weight < distances[to_v]:
            print("Внимание: граф содержит отрицательный цикл!")
            return None, float('inf')
    
    if distances[end_vertex] == float('inf'):
        return None, float('inf')
    
    path = []
    current = end_vertex
    while current is not None:
        path.append(current)
        current = previous[current]
    path.reverse()
    
    return path, distances[end_vertex]

def task_6_interface():
    """Интерфейс для задания 6"""
    print("\n" + "="*50)
    print("ЗАДАНИЕ 6: АЛГОРИТМ БЕЛЛМАНА-ФОРДА")
    print("="*50)
    
    graph = load_graph_5()  # Используем тот же граф, что и в задании 5
    print("Загружен граф для задания 6:")
    graph.display()
    
    vertices = graph.get_vertices()
    print(f"\nДоступные вершины: {sorted(vertices)}")
    
    while True:
        print("\n1. Найти путь алгоритмом Беллмана-Форда")
        print("2. Сравнить с алгоритмом Дейкстры")
        print("3. Вернуться в меню")
        
        choice = input("Ваш выбор (1-3): ").strip()
        
        if choice in ['1', '2']:
            try:
                start = int(input("Введите начальную вершину: "))
                end = int(input("Введите конечную вершину: "))
                
                if start not in vertices or end not in vertices:
                    print("Ошибка: вершины не существуют в графе!")
                    continue
                
                if choice == '1':
                    path, distance = bellman_ford_shortest_path(graph, start, end)
                    algorithm_name = "Беллмана-Форда"
                else:
                    print("\nСРАВНЕНИЕ АЛГОРИТМОВ:")
                    path1, dist1 = dijkstra_shortest_path(graph, start, end)
                    path2, dist2 = bellman_ford_shortest_path(graph, start, end)
                    
                    print(f"Дейкстра: {' -> '.join(map(str, path1)) if path1 else 'нет пути'} (расстояние: {dist1})")
                    print(f"Беллман-Форд: {' -> '.join(map(str, path2)) if path2 else 'нет пути'} (расстояние: {dist2})")
                    continue
                
                if path:
                    print(f"\nКратчайший путь от {start} до {end} (алгоритм {algorithm_name}):")
                    print(" -> ".join(map(str, path)))
                    print(f"Общее расстояние: {distance}")
                else:
                    print(f"Путь от {start} до {end} не существует!")
                    
            except ValueError:
                print("Ошибка: введите целые числа!")
                
        elif choice == '3':
            break
        else:
            print("Неверный выбор!")

print("=== ЗАДАНИЕ 6 ВЫПОЛНЕНО: АЛГОРИТМ БЕЛЛМАНА-ФОРДА РЕАЛИЗОВАН ===")

# === ОБЩЕЕ МЕНЮ ИНДИВИДУАЛЬНЫХ ЗАДАНИЙ ===

def individual_task_menu(graph):
    """Меню индивидуальных заданий"""
    while True:
        print("\n" + "="*50)
        print("ИНДИВИДУАЛЬНЫЕ ЗАДАНИЯ")
        print("="*50)
        print("1. Задание 3: Анализ графа")
        print("2. Задание 4: Кратчайший путь (граф 1)")
        print("3. Задание 5: Алгоритм Дейкстры (граф 2)")
        print("4. Задание 6: Алгоритм Беллмана-Форда (граф 2)")
        print("5. Вернуться в главное меню")
        
        choice = input("Выберите задание (1-5): ").strip()
        
        if choice == '1':
            analyze_graph(graph)
        elif choice == '2':
            task_4_interface()
        elif choice == '3':
            task_5_interface()
        elif choice == '4':
            task_6_interface()
        elif choice == '5':
            break
        else:
            print("Неверный выбор!")

# === ГЛАВНАЯ ПРОГРАММА ===

def main():
    """Главная функция программы"""
    print("=== КОМПЛЕКСНАЯ ПРОГРАММА ДЛЯ РАБОТЫ С ГРАФАМИ ===")
    print("Реализованы все 6 заданий")
    
    while True:
        print("\n" + "="*50)
        print("ГЛАВНОЕ МЕНЮ")
        print("="*50)
        print("1. Задания 1-2: Основное приложение")
        print("2. Задания 3-6: Индивидуальные задания")
        print("3. Выход")
        
        choice = input("Выберите режим (1-3): ").strip()
        
        if choice == '1':
            dialog_loop()
        elif choice == '2':
            graph = Graph(directed=True)
            individual_task_menu(graph)
        elif choice == '3':
            print("Выход из программы...")
            break
        else:
            print("Неверный выбор!")

# Запуск программы
if __name__ == "__main__":
    main()
```

```python

```

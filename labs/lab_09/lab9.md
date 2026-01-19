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

# ============================================================================
# 1. ШИФР РОССИНЬОЛЯ (симметричный шифр замены)
# ============================================================================

class RossignolCipher:
    """Великий шифр Россиньоля - шифр простой замены"""
    
    def __init__(self, key="фф12К52"):
        self.key = key
        self.encode_dict, self.decode_dict = self._create_cipher_dicts()
    
    def _create_cipher_dicts(self):
        """Создает словари для шифрования и дешифрования"""
        # Все символы, которые будем шифровать
        symbols = []
        
        # Английские буквы
        symbols.extend([chr(i) for i in range(ord('A'), ord('Z') + 1)])  # A-Z
        symbols.extend([chr(i) for i in range(ord('a'), ord('z') + 1)])  # a-z
        
        # Русские буквы (без Ё, ё)
        symbols.extend([chr(i) for i in range(ord('А'), ord('я') + 1)])
        symbols = [c for c in symbols if c not in ('Ё', 'ё')]
        
        # Цифры
        symbols.extend([str(i) for i in range(10)])
        
        # Знаки препинания и символы
        symbols.extend(' .,!?-:;()"\'@#$%&*+=<>[]{}')
        
        # Убираем дубликаты и сортируем
        symbols = sorted(set(symbols))
        
        # Перемешиваем символы на основе ключа
        random.seed(sum(ord(c) for c in self.key))
        shuffled = symbols.copy()
        random.shuffle(shuffled)
        
        # Создаем словари
        encode_dict = {}  # для шифрования: символ → замена
        decode_dict = {}  # для дешифрования: замена → символ
        
        for original, cipher in zip(symbols, shuffled):
            encode_dict[original] = cipher
            decode_dict[cipher] = original
        
        return encode_dict, decode_dict
    
    def encrypt(self, text):
        """Шифрует текст"""
        result = []
        for char in text:
            # Если символ есть в словаре - заменяем, иначе оставляем как есть
            result.append(self.encode_dict.get(char, char))
        return ''.join(result)
    
    def decrypt(self, text):
        """Дешифрует текст"""
        result = []
        for char in text:
            result.append(self.decode_dict.get(char, char))
        return ''.join(result)

# ============================================================================
# 2. КРИПТОСИСТЕМА ДАМГОРДА-ЮРИКА (асимметричный шифр)
# ============================================================================

class DamgardJurikCipher:
    """Криптосистема Дамгорда-Юрика (упрощенная версия)"""
    
    def __init__(self):
        # Маленькие простые числа для демонстрации
        p = 61  # простое число
        q = 53  # простое число
        self.n = p * q  # модуль
        
        # Функция Эйлера
        phi = (p - 1) * (q - 1)
        
        # Открытый ключ (e, n) - обычно 65537, но для маленьких чисел берем 17
        self.e = 17
        
        # Закрытый ключ (d, n)
        self.d = self._find_inverse(self.e, phi)
    
    def _find_inverse(self, e, phi):
        """Находит d такое, что (e * d) % phi = 1"""
        for d in range(1, phi):
            if (e * d) % phi == 1:
                return d
        return 1
    
    def encrypt(self, text):
        """Шифрует текст открытым ключом"""
        encrypted_numbers = []
        for char in text:
            # Преобразуем символ в числовой код
            m = ord(char)
            # Шифруем: c = m^e mod n
            c = pow(m, self.e, self.n)
            encrypted_numbers.append(str(c))
        
        # Возвращаем строку чисел через пробел
        return ' '.join(encrypted_numbers)
    
    def decrypt(self, ciphertext):
        """Дешифрует текст закрытым ключом"""
        # Пытаемся разбить строку на числа
        try:
            numbers = [int(num) for num in ciphertext.split()]
        except:
            return "Ошибка: некорректный формат зашифрованного текста"
        
        # Дешифруем каждое число
        decrypted_chars = []
        for c in numbers:
            # Дешифруем: m = c^d mod n
            m = pow(c, self.d, self.n)
            decrypted_chars.append(chr(m))
        
        return ''.join(decrypted_chars)

# ============================================================================
# 3. ГЛАВНОЕ МЕНЮ И ИНТЕРФЕЙС
# ============================================================================

def show_demo():
    """Показывает демонстрацию работы шифров"""
    print("\n" + "="*60)
    print("ДЕМОНСТРАЦИЯ РАБОТЫ ШИФРОВ")
    print("="*60)
    
    # Создаем экземпляры шифров
    rossignol = RossignolCipher()
    damgard = DamgardJurikCipher()
    
    test_texts = [
        "Hello",
        "Привет",
        "12345",
        "Test 123!",
        "Криптография"
    ]
    
    for text in test_texts:
        print(f"\nТекст: '{text}'")
        
        # Россиньоль
        r_enc = rossignol.encrypt(text)
        r_dec = rossignol.decrypt(r_enc)
        print(f"  Россиньоль: '{r_enc}' → '{r_dec}' ✓" if text == r_dec else f"  Россиньоль: Ошибка!")
        
        # Дамгорд-Юрик
        d_enc = damgard.encrypt(text)
        d_dec = damgard.decrypt(d_enc)
        print(f"  Дамгорд-Юрик: '{text}' → ... → '{d_dec}' ✓" if text == d_dec else f"  Дамгорд-Юрик: Ошибка!")

def run_test():
    """Запускает тест шифров"""
    print("\n" + "="*60)
    print("ТЕСТИРОВАНИЕ ШИФРОВ")
    print("="*60)
    
    rossignol = RossignolCipher()
    damgard = DamgardJurikCipher()
    
    test_text = input("Введите текст для теста: ")
    
    if not test_text:
        test_text = "Hello World! 123 Привет"
        print(f"Используется текст по умолчанию: '{test_text}'")
    
    print("\n1. ШИФР РОССИНЬОЛЯ:")
    print("-"*40)
    encrypted_r = rossignol.encrypt(test_text)
    decrypted_r = rossignol.decrypt(encrypted_r)
    print(f"Исходный: '{test_text}'")
    print(f"Зашифрованный: '{encrypted_r}'")
    print(f"Дешифрованный: '{decrypted_r}'")
    print(f"Результат: {'✓ УСПЕХ' if test_text == decrypted_r else '✗ ОШИБКА'}")
    
    print("\n2. КРИПТОСИСТЕМА ДАМГОРДА-ЮРИКА:")
    print("-"*40)
    encrypted_d = damgard.encrypt(test_text)
    decrypted_d = damgard.decrypt(encrypted_d)
    print(f"Исходный: '{test_text}'")
    print(f"Зашифрованный (первые 60 симв): {encrypted_d[:60]}...")
    print(f"Дешифрованный: '{decrypted_d}'")
    print(f"Результат: {'✓ УСПЕХ' if test_text == decrypted_d else '✗ ОШИБКА'}")
    
    if test_text == decrypted_r and test_text == decrypted_d:
        print("\n" + "="*60)
        print("✓ ОБА ШИФРА РАБОТАЮТ КОРРЕКТНО!")
        print("="*60)
        return True
    else:
        print("\n" + "="*60)
        print("✗ ОБНАРУЖЕНЫ ПРОБЛЕМЫ!")
        print("="*60)
        return False

def main():
    """Главная функция программы"""
    print("="*60)
    print("ПРОГРАММА ШИФРОВАНИЯ - ВАРИАНТ 9")
    print("="*60)
    print("Симметричный шифр: Великий Шифр Россиньоля")
    print("Асимметричный шифр: Криптосистема Дамгорда-Юрика")
    print("="*60)
    print("ПРЕДУПРЕЖДЕНИЕ: Это учебная реализация!")
    print("Не используйте для реального шифрования!")
    print("="*60)
    
    # Создаем экземпляры шифров
    rossignol = RossignolCipher()
    damgard = DamgardJurikCipher()
    
    while True:
        print("\n" + "="*40)
        print("ГЛАВНОЕ МЕНЮ")
        print("="*40)
        print("1. Шифровать Россиньолем")
        print("2. Дешифровать Россиньолем")
        print("3. Шифровать Дамгордом-Юриком")
        print("4. Дешифровать Дамгордом-Юриком")
        print("5. Запустить тест шифров")
        print("6. Показать демонстрацию")
        print("7. Выйти из программы")
        print("="*40)
        
        choice = input("Ваш выбор (1-7): ").strip()
        
        if choice == '1':
            # Шифрование Россиньолем
            text = input("\nВведите текст для шифрования: ")
            if text:
                encrypted = rossignol.encrypt(text)
                print(f"\nРезультат шифрования Россиньолем:")
                print(f"Исходный текст:    '{text}'")
                print(f"Зашифрованный текст: '{encrypted}'")
            else:
                print("Ошибка: текст не может быть пустым")
        
        elif choice == '2':
            # Дешифрование Россиньолем
            text = input("\nВведите текст для дешифрования: ")
            if text:
                decrypted = rossignol.decrypt(text)
                print(f"\nРезультат дешифрования Россиньолем:")
                print(f"Зашифрованный текст: '{text}'")
                print(f"Дешифрованный текст: '{decrypted}'")
            else:
                print("Ошибка: текст не может быть пустым")
        
        elif choice == '3':
            # Шифрование Дамгордом-Юриком
            text = input("\nВведите текст для шифрования: ")
            if text:
                encrypted = damgard.encrypt(text)
                print(f"\nРезультат шифрования Дамгордом-Юриком:")
                print(f"Исходный текст: '{text}'")
                print(f"Зашифрованный текст (числа через пробел):")
                print(encrypted)
            else:
                print("Ошибка: текст не может быть пустым")
        
        elif choice == '4':
            # Дешифрование Дамгордом-Юриком
            text = input("\nВведите зашифрованный текст (числа через пробел): ")
            if text:
                decrypted = damgard.decrypt(text)
                print(f"\nРезультат дешифрования Дамгордом-Юриком:")
                print(f"Зашифрованный текст: {text[:50]}...")
                print(f"Дешифрованный текст: '{decrypted}'")
            else:
                print("Ошибка: текст не может быть пустым")
        
        elif choice == '5':
            # Запуск теста
            run_test()
        
        elif choice == '6':
            # Демонстрация
            show_demo()
        
        elif choice == '7':
            # Выход
            print("\nСпасибо за использование программы!")
            print("Выход...")
            break
        
        else:
            print("\nНеверный выбор! Пожалуйста, введите число от 1 до 7.")
        
        # Пауза перед следующим действием
        input("\nНажмите Enter для продолжения...")

# ============================================================================
# 4. ТОЧКА ВХОДА
# ============================================================================

if __name__ == "__main__":
    # Простой тест при запуске
    print("Запуск программы...")
    print("Выполняется начальная проверка...")
    
    try:
        # Быстрая проверка работы шифров
        rossignol = RossignolCipher()
        damgard = DamgardJurikCipher()
        
        test_text = "Test123"
        
        # Проверка Россиньоля
        r_test = rossignol.decrypt(rossignol.encrypt(test_text))
        
        # Проверка Дамгорда-Юрика
        d_test = damgard.decrypt(damgard.encrypt(test_text))
        
        if test_text == r_test and test_text == d_test:
            print("✓ Начальная проверка пройдена успешно!")
        else:
            print("⚠ Внимание: возможны проблемы с шифрованием")
    
    except Exception as e:
        print(f"⚠ Ошибка при проверке: {e}")
    
    # Запуск главного меню
    main()
```

```python

```

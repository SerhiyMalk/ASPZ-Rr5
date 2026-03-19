# ASPZ-Rr5
<img width="931" height="398" alt="image" src="https://github.com/user-attachments/assets/18121178-9eb6-4557-88c1-53effb6a23c5" />
<img width="944" height="799" alt="image" src="https://github.com/user-attachments/assets/8a95d2ee-bcfb-4723-a9d6-83398c350e9d" />
<img width="1129" height="650" alt="image" src="https://github.com/user-attachments/assets/57f9e978-3ca1-47da-b0f0-65f896a04849" />
# Аналіз помилок роботи з пам'яттю за допомогою Valgrind

## Індивідуальне завдання (Варіант 11)
[cite_start]**Завдання:** Реалізувати помилку через неправильний `sizeof` (використання `sizeof(ptr)` замість `sizeof(*ptr)`), яка проявляється лише при зміні архітектури.

### Опис проблеми
Розмір вказівника (`sizeof(ptr)`) залежить від розрядності архітектури: 8 байтів на 64-бітних системах та 4 байти на 32-бітних. Якщо виділити пам'ять для структури розміром 8 байтів помилковою конструкцією `malloc(sizeof(ptr))`, то на 64-бітній платформі помилка не проявиться (буде виділено 8 байтів). Однак при перенесенні коду на 32-бітну платформу буде виділено лише 4 байти. При спробі запису в другу половину структури відбудеться вихід за межі виділеної пам'яті (Out-of-bounds access).

### Програмний код (`lab5.c`)
```c
#include <stdio.h>
#include <stdlib.h>

struct Data {
    int a; // 4 байти
    int b; // 4 байти
}; // Загальний розмір 8 байтів

int main() {
    struct Data *ptr;
    
    // ПОМИЛКА: Виділяємо пам'ять за розміром вказівника
    ptr = malloc(sizeof(ptr)); 
    if (!ptr) return 1;

    ptr->a = 10;
    ptr->b = 20; // На 32-бітній системі тут відбувається запис за межі виділеного блоку

    printf("Значення: a = %d, b = %d\n", ptr->a, ptr->b);

    free(ptr);
    return 0;
}


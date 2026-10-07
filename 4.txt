#include <stdio.h>
#include <stdlib.h>
#include <locale.h>
#ifdef _WIN32
#include <windows.h>
#endif

struct Node {
    int data;
    struct Node* left;
    struct Node* right;
};

struct Node* root;

struct Node* CreateTree(struct Node* root, struct Node* r, int data)
{
    if (r == NULL)
    {
        r = (struct Node*)malloc(sizeof(struct Node));
        if (r == NULL)
        {
            printf("Ошибка выделения памяти");
            exit(0);
        }
        r->left = NULL;
        r->right = NULL;
        r->data = data;
        if (root == NULL) return r;
        if (data > root->data) root->left = r;
        else root->right = r;
        return r;
    }

    if (data > r->data)
        CreateTree(r, r->left, data);
    else
        CreateTree(r, r->right, data);

    return root;
}

struct Node* CreateTreeUnique(struct Node* root, struct Node* r, int data, int* added)
{
    if (r == NULL)
    {
        r = (struct Node*)malloc(sizeof(struct Node));
        if (r == NULL)
        {
            printf("Ошибка выделения памяти");
            exit(0);
        }
        r->left = NULL;
        r->right = NULL;
        r->data = data;
        *added = 1;
        if (root == NULL) return r;
        if (data > root->data) root->left = r;
        else root->right = r;
        return r;
    }

    if (data == r->data)
    {
        *added = 0;
        return root;
    }

    if (data > r->data)
        CreateTreeUnique(r, r->left, data, added);
    else
        CreateTreeUnique(r, r->right, data, added);

    return root;
}

void print_tree(struct Node* r, int l)
{
    if (r == NULL)
    {
        return;
    }
    print_tree(r->right, l + 1);
    for (int i = 0; i < l; i++)
    {
        printf("   ");
    }
    printf("%d\n", r->data);
    print_tree(r->left, l + 1);
}

struct Node* SearchTree(struct Node* r, int data)
{
    if (r == NULL)
    {
        return NULL;
    }
    if (data == r->data)
    {
        return r;
    }
    if (data > r->data)
    {
        return SearchTree(r->left, data);
    }
    else
    {
        return SearchTree(r->right, data);
    }
}

int CountOccurrences(struct Node* r, int data)
{
    if (r == NULL)
    {
        return 0;
    }

    int count = (r->data == data) ? 1 : 0;

    count += CountOccurrences(r->left, data);
    count += CountOccurrences(r->right, data);

    return count;
}

int main()
{
#ifdef _WIN32
    SetConsoleOutputCP(CP_UTF8);
    SetConsoleCP(CP_UTF8);
    setlocale(LC_ALL, ".UTF8");
#else
    setlocale(LC_ALL, "");
#endif
    int D, start = 1, added, allowDuplicates;
    root = NULL;

    printf("Разрешить повторяющиеся элементы в дереве?\n");
    printf("1 - да (обычное добавление), 0 - нет (без дублей, задание 3*): ");
    scanf_s("%d", &allowDuplicates);

    printf("\nПостроение дерева\n");
    printf("-1 - окончание построения дерева\n");
    while (start)
    {
        printf("Введите число: ");
        scanf_s("%d", &D);
        if (D == -1)
        {
            printf("Построение дерева окончено\n\n");
            start = 0;
        }
        else if (allowDuplicates)
        {
            root = CreateTree(root, root, D);
        }
        else
        {
            added = 0;
            root = CreateTreeUnique(root, root, D, &added);
            if (!added)
            {
                printf("Элемент %d уже есть в дереве, добавление пропущено\n", D);
            }
        }
    }

    printf("Полученное дерево (повернуто на 90 градусов, корень слева):\n");
    print_tree(root, 0);

    printf("\nПоиск элемента\n");
    printf("Введите число для поиска: ");
    scanf_s("%d", &D);
    if (SearchTree(root, D) != NULL)
    {
        printf("Элемент %d найден в дереве\n", D);
    }
    else
    {
        printf("Элемент %d в дереве отсутствует\n", D);
    }

    printf("\nПодсчёт числа вхождений\n");
    printf("Введите число для подсчёта вхождений: ");
    scanf_s("%d", &D);
    printf("Элемент %d встречается в дереве %d раз(а)\n", D, CountOccurrences(root, D));

    return 0;
}
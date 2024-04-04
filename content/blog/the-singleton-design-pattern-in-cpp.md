+++
title = "The Singleton Design Pattern in C++"
date = 2024-04-03
draft = false

[taxonomies]
categories = [ "programming" ]
tags = [ "cpp", "design-patterns" ]

[extra]
lang = "en"
toc = true
comment = true
copy = true
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
featured = false
+++

## Thread-Unsafe Implementation

### Source Code

```cpp
#include <cstdio>

class Singleton {
public:
    Singleton(const Singleton& other) = delete;
    Singleton(Singleton&& other) = delete;

    Singleton& operator=(const Singleton& other) = delete;
    Singleton& operator=(Singleton&& other) = delete;

    static Singleton* GetInstance() {
        if (sInstance == nullptr) {
            sInstance = new Singleton();
        }
        return sInstance;
    };

private:
    Singleton() = default;
    ~Singleton() = default;

    static Singleton* sInstance;
};

Singleton* Singleton::sInstance = nullptr;

int main() {
    auto instance1 = Singleton::GetInstance();
    auto instance2 = Singleton::GetInstance();

    std::printf("instance1: %p\n", instance1);
    std::printf("instance2: %p\n", instance2);

    return 0;
}
```

### Notes
- The instance is **dynamically initialized**
- The function `GetInstance()` is **not thread safe**

## Thread-Safe Implementation 1

### Source Code

```cpp
#include <cstdio>
#include <mutex>

class Singleton {
public:
    Singleton(const Singleton& other) = delete;
    Singleton(Singleton&& other) = delete;

    Singleton& operator=(const Singleton& other) = delete;
    Singleton& operator=(Singleton&& other) = delete;

    static Singleton* GetInstance() {
        std::lock_guard<std::mutex> lock(sInstanceMutex);
        if (sInstance == nullptr) {
            sInstance = new Singleton();
        }
        return sInstance;
    };

private:
    Singleton() = default;
    ~Singleton() = default;

    static Singleton* sInstance;
    static std::mutex sInstanceMutex;
};

Singleton* Singleton::sInstance = nullptr;
std::mutex Singleton::sInstanceMutex;

int main() {
    auto instance1 = Singleton::GetInstance();
    auto instance2 = Singleton::GetInstance();

    std::printf("instance1: %p\n", instance1);
    std::printf("instance2: %p\n", instance2);

    return 0;
}
```

### Notes
- The instance is **dynamically initialized**
- The function `GetInstance()` is **thread safe**

## Thread-Safe Implementation 2

### Source Code

```cpp
#include <cstdio>

class Singleton {
public:
    Singleton(const Singleton& other) = delete;
    Singleton(Singleton&& other) = delete;

    Singleton& operator=(const Singleton& other) = delete;
    Singleton& operator=(Singleton&& other) = delete;

    static Singleton* GetInstance() {
        static Singleton *instance = new Singleton();
        return instance;
    };

private:
    Singleton() = default;
    ~Singleton() = default;
};

int main() {
    auto instance1 = Singleton::GetInstance();
    auto instance2 = Singleton::GetInstance();

    std::printf("instance1: %p\n", instance1);
    std::printf("instance2: %p\n", instance2);

    return 0;
}
```

### Notes
- The instance is **dynamically initialized**
- The function `GetInstance()` is **thread safe**

## Scott Meyers' Implementation

### Source Code

```cpp
#include <cstdio>
#include <mutex>

class Singleton {
public:
    Singleton(const Singleton& other) = delete;
    Singleton& operator=(const Singleton& other) = delete;

    static Singleton& GetInstance() {
        static Singleton instance;
        return instance;
    };

private:
    Singleton() = default;
    ~Singleton() = default;
};

int main() {
    auto &instance1 = Singleton::GetInstance();
    auto &instance2 = Singleton::GetInstance();

    std::printf("instance1: %p\n", &instance1);
    std::printf("instance2: %p\n", &instance2);

    return 0;
}
```

### Notes
- [Static Initialization Order Fiasco](https://en.cppreference.com/w/cpp/language/siof)
- The function `GetInstance()` is **thread safe**

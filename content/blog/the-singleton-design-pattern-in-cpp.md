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

## Implementation 1

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
    auto singleton1 = Singleton::GetInstance();
    auto singleton2 = Singleton::GetInstance();

    std::printf("singleton1: %p\n", singleton1);
    std::printf("singleton2: %p\n", singleton2);

    return 0;
}
```

### Notes
- The instance is **dynamically initialized**
- The function `GetInstance()` is **not thread safe**

## Implementation 2

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
    auto singleton1 = Singleton::GetInstance();
    auto singleton2 = Singleton::GetInstance();

    std::printf("singleton1: %p\n", singleton1);
    std::printf("singleton2: %p\n", singleton2);

    return 0;
}
```

### Notes
- The instance is **dynamically initialized**
- The function `GetInstance()` is **thread safe**

## Implementation 3

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
    auto &singleton1 = Singleton::GetInstance();
    auto &singleton2 = Singleton::GetInstance();

    std::printf("singleton1: %p\n", &singleton1);
    std::printf("singleton2: %p\n", &singleton2);

    return 0;
}
```

### Notes
- Instance is **statically initialized** [Static Initialization Order Fiasco](https://en.cppreference.com/w/cpp/language/siof)
- The function `GetInstance()` is **thread safe**

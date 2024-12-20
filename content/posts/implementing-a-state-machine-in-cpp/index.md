---
date: '2024-11-01T01:04:09+02:00'
draft: false
title: 'Implementing a State Machine in C++'

author: 'xorz57'
categories: ['programming']
tags: ['c++','c++11','fsm']
---

Implementing a state machine in C++ can seem daunting, but with C++11's features, we can build one in under 100 lines of code. In this article, I'll walk you through three implementations of a state machine, each with increasing complexity, and show you how to use them with simple examples. You can find the full source code on [GitHub](https://github.com/xorz57/StateMachine).

### What is a state machine?

A state machine is a computational model used to design systems that can be in one of a finite number of states at any given time. It consists of a set of states, transitions between those states, and actions that occur as a result of those transitions. The machine begins in an initial state and changes states based on input or events, following predefined rules. State machines are useful for modeling behaviors in various fields such as software development, digital circuit design, and robotics, allowing for clear and organized representation of complex processes and decision logic.

### Implementations

#### Implementation 1 (57 LOC)

```cpp
#pragma once

#include <algorithm>
#include <functional>
#include <tuple>
#include <utility>
#include <vector>
```

```cpp
using action_t = std::function<void()>;

template<typename state_t, typename event_t>
using transition_t = std::pair<std::pair<state_t, event_t>, std::tuple<action_t, state_t>>;

template<typename state_t, typename event_t>
using transition_table_t = std::vector<transition_t<state_t, event_t>>;
```

```cpp
template<typename state_t, typename event_t>
class state_machine_t {
public:
    state_machine_t() = default;

    state_machine_t(const state_t &state, transition_table_t<state_t, event_t> transition_table) :
        m_state(state),
        m_transition_table(std::move(transition_table)) {}

    bool handle_event(const event_t &event) {
        const auto it = std::find_if(
            m_transition_table.begin(),
            m_transition_table.end(),
            [&](const transition_t<state_t, event_t> &transition) {
                return transition.first.first == m_state && transition.first.second == event;
            }
        );
        if (it != m_transition_table.end()) {
            const action_t &action = std::get<0>(it->second);
            const state_t &state = std::get<1>(it->second);
            m_state = state;
            action();
            return true;
        }
        return false;
    }

    void set_state(const state_t &state) {
        m_state = state;
    }

    void set_transition_table(const transition_table_t<state_t, event_t> &transition_table) {
        m_transition_table = transition_table;
    }

    state_t get_state() const {
        return m_state;
    }

    transition_table_t<state_t, event_t> get_transition_table() const {
        return m_transition_table;
    }

private:
    state_t m_state;
    transition_table_t<state_t, event_t> m_transition_table;
};
```

#### Implementation 2 (92 LOC)

```cpp
#pragma once

// ...

#include <unordered_map>
```

```cpp
// ...

using enter_action_t = std::function<void()>;

using leave_action_t = std::function<void()>;

template<typename state_t>
using enter_actions_t = std::unordered_map<state_t, enter_action_t>;

template<typename state_t>
using leave_actions_t = std::unordered_map<state_t, leave_action_t>;
```

```cpp
template<typename state_t, typename event_t>
class state_machine_t {
public:
    // ...

    bool handle_event(const event_t &event) {
        const auto it = std::find_if(
            m_transition_table.begin(),
            m_transition_table.end(),
            [&](const transition_t<state_t, event_t> &transition) {
                return transition.first.first == m_state && transition.first.second == event;
            }
        );
        if (it != m_transition_table.end()) {
            const action_t &action = std::get<0>(it->second);
            const state_t &state = std::get<1>(it->second);
            const auto it1 = m_leave_actions.find(m_state);
            if (it1 != m_leave_actions.end()) {
                it1->second();
            }
            m_state = state;
            action();
            const auto it2 = m_enter_actions.find(m_state);
            if (it2 != m_enter_actions.end()) {
                it2->second();
            }
            return true;
        }
        return false;
    }

    // ...

    void set_enter_action(const state_t &state, const enter_action_t &enter_action) {
        m_enter_actions[state] = enter_action;
    }

    void set_leave_action(const state_t &state, const leave_action_t &leave_action) {
        m_leave_actions[state] = leave_action;
    }

    // ...

    enter_actions_t<state_t> get_enter_actions() const {
        return m_enter_actions;
    }

    leave_actions_t<state_t> get_leave_actions() const {
        return m_leave_actions;
    }

private:
    // ...

    enter_actions_t<state_t> m_enter_actions;
    leave_actions_t<state_t> m_leave_actions;
};
```

#### Implementation 3 (97 LOC)

```cpp
#pragma once

// ...
```

```cpp
// ...

using guard_t = std::function<bool()>;

template<typename state_t, typename event_t>
using transition_t = std::pair<std::pair<state_t, event_t>, std::tuple<guard_t, action_t, state_t>>;
```

```cpp
template<typename state_t, typename event_t>
class state_machine_t {
public:
    // ...

    bool handle_event(const event_t &event) {
        const auto it = std::find_if(
            m_transition_table.begin(),
            m_transition_table.end(),
            [&](const transition_t<state_t, event_t> &transition) {
                return transition.first.first == m_state && transition.first.second == event;
            }
        );
        if (it != m_transition_table.end()) {
            const guard_t &guard = std::get<0>(it->second);
            const action_t &action = std::get<1>(it->second);
            const state_t &state = std::get<2>(it->second);
            if (guard()) {
                const auto it1 = m_leave_actions.find(m_state);
                if (it1 != m_leave_actions.end()) {
                    it1->second();
                }
                m_state = state;
                action();
                const auto it2 = m_enter_actions.find(m_state);
                if (it2 != m_enter_actions.end()) {
                    it2->second();
                }
                return true;
            }
            return true;
        }
        return false;
    }

    // ...

private:
    // ...
};
```

### Examples

#### Example 1

```cpp
#include "StateMachine/StateMachine1.hpp"

#include <iostream>

enum class state {
    state0,
    state1,
    state2
};

enum class event {
    event1,
    event2
};

static std::string to_string(const state &state) {
    switch (state) {
        case state::state0:
            return "state0";
        case state::state1:
            return "state1";
        case state::state2:
            return "state2";
    }
    return "unknown";
}

namespace action {
    const auto action1 = []() { std::cout << "action1" << std::endl; };
    const auto action2 = []() { std::cout << "action2" << std::endl; };
}// namespace action

int main() {
    transition_table_t<state, event> tt{
            {{state::state0, event::event1}, {action::action1, state::state1}},
            {{state::state1, event::event2}, {action::action2, state::state2}},
            {{state::state2, event::event1}, {action::action1, state::state1}},
    };

    state_machine_t<state, event> sm(state::state0, tt);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.handle_event(event::event1);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.handle_event(event::event2);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.handle_event(event::event1);
    std::cout << to_string(sm.get_state()) << std::endl;

    return 0;
}
```

#### Example 2

```cpp
#include "StateMachine/StateMachine2.hpp"

#include <iostream>

enum class state {
    state0,
    state1,
    state2
};

enum class event {
    event1,
    event2
};

static std::string to_string(const state &state) {
    switch (state) {
        case state::state0:
            return "state0";
        case state::state1:
            return "state1";
        case state::state2:
            return "state2";
    }
    return "unknown";
}

namespace action {
    const auto action1 = []() { std::cout << "action1" << std::endl; };
    const auto action2 = []() { std::cout << "action2" << std::endl; };
}// namespace action

int main() {
    transition_table_t<state, event> tt{
            {{state::state0, event::event1}, {action::action1, state::state1}},
            {{state::state1, event::event2}, {action::action2, state::state2}},
            {{state::state2, event::event1}, {action::action1, state::state1}},
    };

    state_machine_t<state, event> sm(state::state0, tt);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.set_enter_action(state::state1, []() { std::cout << "enter_action1" << std::endl; });
    sm.set_leave_action(state::state1, []() { std::cout << "leave_action1" << std::endl; });

    sm.handle_event(event::event1);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.handle_event(event::event2);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.handle_event(event::event1);
    std::cout << to_string(sm.get_state()) << std::endl;

    return 0;
}
```

#### Example 3

```cpp
#include "StateMachine/StateMachine3.hpp"

#include <iostream>

enum class state {
    state0,
    state1,
    state2
};

enum class event {
    event1,
    event2
};

static std::string to_string(const state &state) {
    switch (state) {
        case state::state0:
            return "state0";
        case state::state1:
            return "state1";
        case state::state2:
            return "state2";
    }
    return "unknown";
}

namespace action {
    const auto action1 = []() { std::cout << "action1" << std::endl; };
    const auto action2 = []() { std::cout << "action2" << std::endl; };
}// namespace action

namespace guard {
    const auto guard1 = []() { return true; };
    const auto guard2 = []() { return true; };
    const auto guard3 = []() { return false; };
}// namespace guard

int main() {
    transition_table_t<state, event> tt{
            {{state::state0, event::event1}, {guard::guard1, action::action1, state::state1}},
            {{state::state1, event::event2}, {guard::guard2, action::action2, state::state2}},
            {{state::state2, event::event1}, {guard::guard3, action::action1, state::state1}},
    };

    state_machine_t<state, event> sm(state::state0, tt);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.set_enter_action(state::state1, []() { std::cout << "enter_action1" << std::endl; });
    sm.set_leave_action(state::state1, []() { std::cout << "leave_action1" << std::endl; });

    sm.handle_event(event::event1);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.handle_event(event::event2);
    std::cout << to_string(sm.get_state()) << std::endl;

    sm.handle_event(event::event1);
    std::cout << to_string(sm.get_state()) << std::endl;

    return 0;
}
```

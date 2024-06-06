+++
title = "Implementing a state machine in less than 100 lines of C++11"
date = 2024-06-06
draft = false

[taxonomies]
categories = [ "programming" ]
tags = [ "cpp", "tutorials" ]

[extra]
lang = "en"
toc = true
comment = true
copy = true
math = false
mermaid = false
outdate_alert = true
outdate_alert_days = false
display_tags = true
truncate_summary = false
featured = false
+++

Explore the full source code on [GitHub](https://github.com/xorz57/StateMachine).

## What is a state machine?

A state machine is a computational model used to design systems that can be in one of a finite number of states at any given time. It consists of a set of states, transitions between those states, and actions that occur as a result of those transitions. The machine begins in an initial state and changes states based on input or events, following predefined rules. State machines are useful for modeling behaviors in various fields such as software development, digital circuit design, and robotics, allowing for clear and organized representation of complex processes and decision logic.

## Example

![mermaid-diagram-2024-06-06-040638](https://github.com/xorz57/StateMachine/assets/84932056/c0399182-3b46-4dfe-9449-d053fc7a074b)

1. The machine starts in `state0`.
2. In `state0`
    - On `event1`, it transitions to `state1` and performs `action1`.
3. In `state1`
    - On `event2`, it transitions to `state2` and performs `action2`.
4. In `state2`
    - On `event1`, it transitions back to `state1` and performs `action1`.

### States

In C++11, we can use an `enum class` to define the various states of a state machine. This approach provides a clear and type-safe way to represent the possible states the machine can be in. Here’s how we can use an `enum class` to describe the states of our state machine:

```cpp
enum class state {
    state0,
    state1,
    state2
};
```

### Events

Similarly, we can use an `enum class` to define the various events that trigger state transitions in a state machine. Here’s how we can use an `enum class` to describe the events of our state machine:

```cpp
enum class event {
    event1,
    event2
};
```

### Actions

In C++11, `lambdas` provide a concise way to define inline functions without needing to declare them separately. When encapsulated within a namespace, `lambdas` can be used effectively to define actions that correspond to specific events or states in our state machine.

```cpp
namespace action {
    const auto action1 = []() { std::cout << "action1" << std::endl; };
    const auto action2 = []() { std::cout << "action2" << std::endl; };
}// namespace action
```

### Transition Table

Initializing our transition table.

```cpp
transition_table_t<state, event> tt{
        {{state::state0, event::event1}, {action::action1, state::state1}},
        {{state::state1, event::event2}, {action::action2, state::state2}},
        {{state::state2, event::event1}, {action::action1, state::state1}},
};
```

### State Machine

Initializing our state machine.

```cpp
state_machine_t<state, event> sm(state::state0, tt);
```

### Handling Events

Handling the events in our state machine.

```cpp
sm.handle_event(event::event1);
sm.handle_event(event::event2);
sm.handle_event(event::event1);
```

## Implementation

```cpp
#include <algorithm>
#include <functional>
#include <tuple>
#include <utility>
#include <vector>

using action_t = std::function<void()>;

template<typename state_t, typename event_t>
using transition_t = std::pair<std::pair<state_t, event_t>, std::tuple<action_t, state_t>>;

template<typename state_t, typename event_t>
using transition_table_t = std::vector<transition_t<state_t, event_t>>;

template<typename state_t, typename event_t>
class state_machine_t {
public:
    state_machine_t() = default;

    state_machine_t(const state_t &state, transition_table_t<state_t, event_t> transition_table) : m_state(state), m_transition_table(std::move(transition_table)) {}

    bool handle_event(const event_t &event) {
        const auto it = std::find_if(m_transition_table.begin(), m_transition_table.end(), [&](const transition_t<state_t, event_t> &transition) {
            return transition.first.first == m_state && transition.first.second == event;
        });
        if (it != m_transition_table.end()) {
            const action_t &action = std::get<0>(it->second);
            const state_t &state = std::get<1>(it->second);

            m_state = state;
            action();
            return true;
        }
        return false;
    }

    [[nodiscard]] state_t get_state() const {
        return m_state;
    }

    [[nodiscard]] transition_table_t<state_t, event_t> get_transition_table() const {
        return m_transition_table;
    }

    void set_state(const state_t &state) {
        m_state = state;
    }

    void set_transition_table(const transition_table_t<state_t, event_t> &transition_table) {
        m_transition_table = transition_table;
    }

private:
    state_t m_state;
    transition_table_t<state_t, event_t> m_transition_table;
};
```

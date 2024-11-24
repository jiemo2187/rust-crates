# rdev

支持MacOs,Windows,Linux的监听和发送鼠标、键盘事件的库。


文档地址：
[Docs](https://docs.rs/rdev/latest/rdev/)

## 代码示例

随机输入0~9的数字，并每秒汇报输入次数。

```rust
use std::collections::HashMap;
use std::sync::Mutex;
use std::thread;
use std::time::Duration;
use std::time::Instant;

use lazy_static::lazy_static;
use rand::Rng;
use rdev::listen;
use rdev::simulate;
use rdev::Event;
use rdev::EventType;
use rdev::Key;

lazy_static! {
    static ref KEY_PRESS_COUNTS: Mutex<HashMap<char, u32>> = Mutex::new(HashMap::new());
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let handle_listen = thread::spawn(|| {
        // block
        if let Err(error) = listen(callback) {
            eprintln!("Error: {:?}", error);
        }
    });

    let handle_simulate = thread::spawn(|| {
        simulate_num_press();
    });

    let mut last_report = Instant::now();
    for _ in 0..10 {
        thread::sleep(Duration::from_secs(1));
        let elapsed = last_report.elapsed();
        if elapsed >= Duration::from_secs(1) {
            report_key_press_counts();
            last_report = Instant::now();
        }
    }

    let _ = handle_simulate.join();

    let _ = handle_listen.join();

    Ok(())
}

fn simulate_num_press() {
    let key_press = [
        EventType::KeyPress(Key::Num0),
        EventType::KeyPress(Key::Num1),
        EventType::KeyPress(Key::Num2),
        EventType::KeyPress(Key::Num3),
        EventType::KeyPress(Key::Num4),
        EventType::KeyPress(Key::Num5),
        EventType::KeyPress(Key::Num6),
        EventType::KeyPress(Key::Num7),
        EventType::KeyPress(Key::Num8),
        EventType::KeyPress(Key::Num9),
    ];
    let mut rng = rand::thread_rng();
    loop {
        let index = rng.gen_range(0..10);
        let key = key_press[index];
        match simulate(&key) {
            Ok(()) => {}
            Err(err) => {
                eprintln!("send key press event error: {:?}", err);
            }
        }
        thread::sleep(Duration::from_millis(rng.gen_range(1..=5) * 100));
    }
}

fn report_key_press_counts() {
    if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
        let total_keys_pressed: u32 = guard.values().sum();
        println!("0~9 input counts: {}", total_keys_pressed);
        *guard = HashMap::new();
    }
}

fn callback(event: Event) {
    match event.event_type {
        rdev::EventType::KeyPress(key) => match key {
            rdev::Key::Num1 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('1').or_insert(0) += 1;
                }
            }
            rdev::Key::Num2 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('2').or_insert(0) += 1;
                }
            }
            rdev::Key::Num3 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('3').or_insert(0) += 1;
                }
            }
            rdev::Key::Num4 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('4').or_insert(0) += 1;
                }
            }
            rdev::Key::Num5 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('6').or_insert(0) += 1;
                }
            }
            rdev::Key::Num6 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('6').or_insert(0) += 1;
                }
            }
            rdev::Key::Num7 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('7').or_insert(0) += 1;
                }
            }
            rdev::Key::Num8 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('8').or_insert(0) += 1;
                }
            }
            rdev::Key::Num9 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('9').or_insert(0) += 1;
                }
            }
            rdev::Key::Num0 => {
                if let Ok(mut guard) = KEY_PRESS_COUNTS.try_lock() {
                    *guard.entry('0').or_insert(0) += 1;
                }
            }
            _ => {}
        },
        _ => {}
    }
}
```

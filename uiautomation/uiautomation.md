# uiautomation

windows uiautomation 的包装器。

可以用来实现一些自动化的工作，比如点击某个软件执行命令等。

文档地址：
[Docs](https://docs.rs/uiautomation/latest/uiautomation/)

## 代码示例

```rust
use uiautomation::UIAutomation;
use uiautomation::UIElement;
use uiautomation::UITreeWalker;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let automation = UIAutomation::new()?;
    let walker = automation.get_content_view_walker()?;
    let root = automation.get_root_element()?;

    print_element(&walker, &root, 0)?;
    Ok(())
}

fn print_element(
    walker: &UITreeWalker,
    element: &UIElement,
    level: usize,
) -> Result<(), Box<dyn std::error::Error>> {
    for _ in 0..level {
        print!(" ")
    }
    println!("{} - {}", element.get_classname()?, element.get_name()?);
    if let Ok(child) = walker.get_first_child(&element) {
        print_element(walker, &child, level + 1)?;

        let mut next = child;
        while let Ok(sibling) = walker.get_next_sibling(&next) {
            print_element(walker, &sibling, level + 1)?;

            next = sibling;
        }
    }
    Ok(())
}
```

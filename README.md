# tasklet
[![CircleCI](https://circleci.com/gh/unix121/tasklet.svg?style=shield)](https://circleci.com/gh/unix121/tasklet)
![Crates.io](https://img.shields.io/crates/d/tasklet)
![Crates.io](https://img.shields.io/crates/v/tasklet)
![GitHub last commit](https://img.shields.io/github/last-commit/unix121/tasklet)

⏱️ A task scheduling library written in Rust

## Dependencies

* cron (0.7.0)
* chrono (0.4.19)
* time (0.2.23)
* log (0.4)

## Use this library

In your `Cargo.toml` add:
```
[dependencies]
tasklet = "0.1.0"
```

## Example
Find more examples in the [examples](/examples) folder.
```rust
use tasklet::{TaskScheduler, Task};
use simple_logger::SimpleLogger;
use log::{info, error};

/// A simple example of a task with two step,
/// that might work or fail some times.
fn main() {
    // Init the logger.
    SimpleLogger::new().init().unwrap();

    // A variable to be passed in the task.
    let mut exec_count = 0;

    // Task scheduler with 2000ms loop frequency.
    let mut scheduler = TaskScheduler::new(2000, chrono::Local);

    // Create a task with 2 steps and add it to the scheduler.
    let mut task = Task::new("1 * * * * * *",
                             Some("A simple task"),
                             None,
                             chrono::Local);
    // A normal step 1.
    task.add_step(Some("Step 1"), || {
        info!("Hello from step 1");
        Ok(()) // Let the scheduler know this step was a success.
    });
    // A step 2 that can fail some times.
    task.add_step(Some("Step 2"), move || {
        if exec_count % 2 == 0 {
            error!("Oh no this step failed!");
            exec_count += 1;
            Err(()) // Indicate that this step was a fail.
        } else {
            info!("Hello from step 2");
            exec_count += 1;
            Ok(()) // Indicate that this step was a success.
        }
    });

    // Append the task to the scheduler.
    scheduler.add_task(task);

    // Execute the scheduler.
    scheduler.run();
}
```

## Author

Stavros Grigoriou ([unix121](github.com/unix121))
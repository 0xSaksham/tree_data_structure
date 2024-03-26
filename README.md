# Rust Tree Structure with Weak References

This repository demonstrates the construction of a tree structure in Rust using strong and weak references. The code showcases how to create nodes with parent-child relationships and manage references using `Rc` (Reference Counted) and `Weak` types.

## Overview

The Rust code in `main.rs` defines a `Node` struct representing a tree node with an integer value, a reference to its parent node, and a vector of child nodes.

```rust
use std::cell::RefCell;
use std::rc::{Rc, Weak};

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}
```

## Features

- **Tree Node Structure**: Defines a `Node` struct with a value, a weak reference to its parent, and a vector of strong references to its children.
- **Parent-Child Relationships**: Establishes parent-child relationships between nodes using weak references for parent nodes and strong references for child nodes.
- **Reference Counting**: Utilizes reference counting to manage ownership of shared nodes and prevent memory leaks.
- **Weak References**: Uses weak references (`Weak`) to prevent strong reference cycles in the tree structure, allowing nodes to be deallocated when no longer needed.

## Demonstration

The `main()` function demonstrates the creation of a tree structure with parent and child nodes. It creates a leaf node and a branch node, establishing a parent-child relationship between them.

```rust
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    // Printing reference counts for the leaf node
    println!(
        "Strong Leaf: {}, Weak Leaf: {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf)
    );

    // Creating a branch node with the leaf node as its child
    {
        let branch = Rc::new(Node {
            value: 5,
            parent: RefCell::new(Weak::new()),
            children: RefCell::new(vec![Rc::clone(&leaf)]),
        });

        // Establishing parent-child relationship
        *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

        // Printing reference counts for the branch node and the leaf node
        println!(
            "Strong Branch: {}, Weak Branch: {}",
            Rc::strong_count(&branch),
            Rc::weak_count(&branch)
        );

        println!(
            "Strong Leaf: {}, Weak Leaf: {}",
            Rc::strong_count(&leaf),
            Rc::weak_count(&leaf)
        );
    }

    // Printing the parent of the leaf node after the branch node is dropped
    println!("\nLeaf Parent: {:?}", leaf.parent.borrow().upgrade());
    println!(
        "Strong Leaf: {}, Weak Leaf: {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf)
    );
}
```

## Running the Code

To run the code, simply clone the repository and execute the following command:

```
cargo run
```

## Note

- Weak references are used to break strong reference cycles in the tree structure, allowing nodes to be deallocated when they are no longer needed.
- Care should be taken to manage references appropriately to prevent memory leaks and dangling references.

## Conclusion

This example illustrates how to implement a tree structure in Rust using strong and weak references. By leveraging reference counting and weak references, Rust provides a safe and efficient way to manage complex data structures with cyclic relationships.
<h1 style="color: orange; font-weight: bold; text-align: center;">Key Features and Design Pillars</h1>


The **Jac programming language** and **Jaseci runtime** build on Python, introducing **AI-first constructs**, **object-spatial programming (OSP)**, and **scale-native capabilities**.

This document provides a quick walkthrough of the key features and design pillars of the **Jac-lang / Jaseci stack**, and how each pillar enables faster and more streamlined development.


## 📘 Document Organization

- **Part 1:** Conceptual Overview of Key Features
- **Part 2:** Code Snippets and Detailed Descriptions

---

##  Part 1. Conceptual Overview

### 1. Object-Spatial Programming (OSP)

Jac introduces a new programming model that lets developers articulate **relationships between objects** in a **graph-like structure** and express computation as **walkers** that traverse this graph.

This model is particularly effective for applications involving **connected data**, such as social networks, knowledge graphs, or file systems, and can greatly reduce code complexity. OSP also provides the foundation for **agentic workflows** and enables **Jaseci’s scale-native execution**, reducing backend development and deployment overhead.

<!--  TODO: Insert illustrative graph diagram here -->


<figure id="fig-features" style="text-align: center;">
    <img src="../assets/Features.svg" alt="Features illustration" style="width:65%; height:auto;">
    <figcaption><strong>Figure 1:</strong> Overview of Jac's key features and capabilities.</figcaption>
</figure>


---

### 2. Programming Abstractions for AI


Jac is designed from the ground up to integrate AI directly into the programming model to simplify development of AI-powered applications.

-  **`by llm`**   - Jac introduces language-level constructs such as the `by()` keyword that automatically generate optimized prompts. This **removes the need for manual prompt engineering** and enables seamless model integration.  In production systems, this feature has reduced hundreds of lines of prompt code to a single line.

<!-- [TODO: Insert example line of `by llm()` code here] -->

-  **Native Agentic AI Workflows (enabled by OSP)** - By leveraging OSP’s graph-based semantics, Jac naturally supports the creation and articulation of **agentic workflows**, allowing developers to create flows of interacting agents that collaborate, share memory, and act on dynamic context.

***Together, OSP and `by llm` form a powerful foundation for rapid agentic AI development***.

---

### 3. Scale-Native Execution

Jac allows developers to **write code once and run it anywhere**: from local environments to distributed cloud deployments, without code modification.  Jac also automates the process of **generating APIs** and **managing data persistence**, simplifying deployment and scaling of your applications.

---

### 4. Python Super-Set

Jac-lang is intentionally designed as an extension of Python.  It provides **Python-like syntax** while adding new capabilities for **graph-based** and **AI-first programming**.

Developers can freely mix **Jac** and **Python**:

- Import Python libraries and call Python functions from Jac
- Inline Python snippets inside Jac code
- Import Jac modules directly into Python programs

This tight interoperability enables teams to adopt Jac incrementally and integrate it seamlessly with existing Python ecosystems.

## Who Jac/Jaseci Is For

Jac/Jaseci is designed for anyone who wants to build applications quickly and cleanly.


### Startups 
Jac is perfect for rapid prototyping and iteration. One or two engineers can build features that normally require separate frontend, backend, ML, and infrastructure roles. Jac scales from prototype to production with the same code.


### AI/ML Engineers
Jac is AI-first: language-level constructs and runtime that use machine learning models seamlessly, reducing prompt engineering and making agent workflows natural. Ideal for building LLM agents, multimodal systems, and graph-based reasoning pipelines.

### Experienced Developers and Large Teams
For developers who want modern language features—optional type checking, cleaner syntax, structural modeling via OSP, and strong tooling—while keeping full access to Python’s ecosystem.

### Frontend Engineers
A great fit if you want to move into full-stack development without heavy backend or DevOps work. Jac lets developers build backend logic, manage state, integrate AI, and deploy, using a Python-like syntax.

## When to Use Jac

Jac is particularly well-suited when:

- **Your domain is inherently graph-shaped**, where relationships matter as much as entities.
- **You want LLMs and other AI models deeply integrated** into your application logic, rather than isolated in separate microservices and wrappers.
- **You need to move from prototype to scalable service seamlessly**, without rewriting your system for microservices, orchestration, or extensive dev-ops.
- **You already rely heavily on Python code or libraries** and want a smooth path to something more structured, graph-aware, and AI-centric.


---

## 📖 Part 2 Code Snippets and Detailed Description

For each feature, we will show simple code examples and explanation to highlight how Jac's key features enables rapid development of scalable, AI-powered applications.


### Programming Abstractions for AI - `by llm`

Jac provides novel constructs for integrating LLMs into code. A function body can simply be replaced with a call to an LLM, removing the need for prompt engineering. `by llm()` delegates execution to an LLM without any extra library code. For more information on `by llm`, check out more documentation [here](https://docs.jaseci.org/learn/jac-byllm/with_llm/).

```jac
import from byllm.lib { Model }
glob llm = Model(model_name="gpt-4o");

enum Personality {
    INTROVERT,
    EXTROVERT,
    AMBIVERT
}

def get_personality(name: str) -> Personality by llm();

with entry {
    name = "Albert Einstein";
    result = get_personality(name);
    print(f"{result} detected for {name}");
}
```

??? info "How To Run"
    1. Install the byLLM plugin by `pip install byllm`
    2. Get a free Gemini API key: Visit [Google AI Studio](https://aistudio.google.com/app/apikey)
    3. Save your Gemini API as an environment variable (`export GEMINI_API_KEY="xxxxxxxx"`).
    > **Note:** > > You can use OpenAI, Anthropic or other API services as well as host your own LLM using Ollama or Huggingface.
    4. Copy this code into `example.jac` file and run with `jac run example.jac`

??? example "Output"
    `   Introvert personality detected for Albert Einstein
    `




### Object Spatial Programming: Going Beyond OOP

Traditional OOP with python classes (`class` or Jac's dataclass-like `obj`) that expresses object hierarchy and behavior is fully supported in Jac. Additionally, Jac programmers can also express object relationships with node classes (`node`), edge classes (`edge`), and object interactions with walker classes (`walker`) for richer modeling of problems called Object-Spatial Programing (OSP). This approach can be used where needed and maps nicely to may categories of problems (which happen to include agentic workflows ;-))

Instances of node and edge classes allow for assembling objects in a graph structure to express semantic relationships between objects. This goes beyond only modeling objects in memory as a disconnected soup of instances. Walker classes enables to expression of objects interacting with each other through special methods called abilities.

In this example, nodes represent meaningful entities (like Libraries and Shelves), while walkers (borrowers) traverse these node objects and process them.

```jac
node Library {
    has location: str;
    can search_shelves with borrower entry;
}

node Shelf {
    has category: str;
    can check_books with borrower entry;
}

node Book {
    has title: str;
    has available: bool;
}

walker borrower {
    has book_needed: str;
    can find_book with `root entry;
}

with entry {
    # Building the world is just linking nodes
    lib1 = root ++> Library("Central Library");
    lib2 = root ++> Library("Community Library");

    shelf1 = lib1 ++> Shelf("Fiction");
    shelf2 = lib1 ++> Shelf("Non-Fiction");
    shelf3 = lib2 ++> Shelf("Science");

    book1 = shelf1 ++> Book("1984", True);
    book2 = shelf1 ++> Book("Brave New World", False);
    book3 = shelf2 ++> Book("Sapiens", True);
    book4 = shelf3 ++> Book("A Brief History of Time", False);
    book5 = shelf3 ++> Book("The Selfish Gene", True);

    # Send Borrower walking
    borrower("1984") spawn root;
}

impl Library.search_shelves {
    visit [-->(`?Shelf)]; # No loops, just visit
}

impl Shelf.check_books {
    found_book = [self -->(`?Book)](
        ?title == visitor.book_needed, available == True
    );

    if (found_book) {
        print(f"Borrowed: {found_book}");
        print(f"From Shelf: {self.category}");
        disengage; # Stop traversal cleanly
    } else {
        print("Book not available in shelf", self.category);
    }
}

impl borrower.find_book {
    visit [-->(`?Library)];
}
```

??? info "How To Run"
    Copy this code into `example.jac` file and run with `jac run example.jac`

??? example "Output"
    `   Starting journey!
        Hello, Alice!
        Hello, Bob!
        Hello, Charlie!
        Total greetings: 3
    `



To read more on how Jac/Jaseci enables rapid development of Agentic AI using combination of `by llm` and OSP, check out [Building Agentic AI Applications with byLLM and Object Spatial Programming](https://docs.jaseci.org/learn/jac-byllm/agentic_ai/)

## Zero to Infinite Scale without any Code Changes

Jac's cloud-native abstractions make persistence and user concepts part of the language so that simple programs can run unchanged locally or in the cloud. Much like every object instance has a self referencial `this` or `self` reference. Every instance of a Jac program invocation has a `root` node reference that is unique to every user and for which any other node or edge objects connected to `root` will persist across code invocations. That's it. Using `root` to access persistent user state and data, Jac deployments can be scaled from local environments infinitely into to the cloud with no code changes.

```jac
node Post {
    has content: str;
    has author: str;
}

walker create_post {
    has content: str, author: str;

    can func_name with `root entry {
        new_post = Post(content=self.content, author=self.author);
        here ++> new_post;
        report {"id": new_post.id, "status": "posted"};
    }
}
```

??? info "How To Run"
    1. Install the Jac Cloud by `pip install jac-cloud`
    2. Copy this code into `example.jac` file and run with `jac serve example.jac`

??? example "Output"
    `   INFO:     Started server process [26286]
        INFO:     Waiting for application startup.
        INFO - DATABASE_HOST is not available! Using LocalDB...
        INFO - Scheduler started
        INFO:     Application startup complete.
        INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
    `

![Fast API Server](../assets/jac_cloud_example.jpg)

## Python Superset Philosophy: All of Python Plus More

Jac is a drop-in replacement for Python and supersets Python, much like Typescript supersets Javascript or C++ supersets C. It extends Python's semantics while maintaining full interoperability with the Python ecosystem, introducing cutting-edge abstractions designed to minimize complexity and embrace AI-forward development. Learn how we achieve full compatiblity and 5 ways you can use jac together with Python, check out [here] (https://docs.jaseci.org/learn/superset_python/).

<div class="code-block run-dot" >
```jac
import math;
import from random { uniform }

def calc_distance(x1: float, y1: float, x2: float, y2: float) -> float {
return math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2);
}

with entry { # Generate random points
(x1, y1) = (uniform(0, 10), uniform(0, 10));
(x2, y2) = (uniform(0, 10), uniform(0, 10));

    distance = calc_distance(x1, y1, x2, y2);
    area = math.pi * (distance / 2) ** 2;

    print("Distance:", round(distance, 2), ", Circle area:", round(area, 2));

}

```
</div>

This snippet natively imports Python packages `math` and `random` and runs identically to its Python counterpart. Jac targets Python bytecode, so all Python libraries work with Jac.


## Better Organized and Well Typed Codebases

Jac focuses on type safety and readability. Type hints are required and the built-in typing system eliminates boilerplate imports. Code structure can be split across multiple files, allowing definitions and implementations to be organized separately while still being checked by Jac's native type system.

=== "tweet.jac"

    ```jac
    obj Tweet {
        has content: str, author: str, timestamp: str, likes: int = 0;

        def like() -> None;
        def unlike() -> None;
        def get_preview(max_length: int) -> str;
        def get_like_count() -> int;
    }
    ```

=== "tweet.impl.jac"

    ```jac
    impl Tweet.like() -> None {
        self.likes += 1;
    }

    impl Tweet.unlike() -> None {
        if self.likes > 0 {
            self.likes -= 1;
        }
    }

    impl Tweet.get_preview(max_length: int) -> str {
        return self.content[:max_length] + "..." if len(self.content) > max_length else self.content;
    }

    impl Tweet.get_like_count() -> int {
        return self.likes;
    }
    ```

    This shows how declarations and implementations can live in separate files for maintainable, typed codebases.

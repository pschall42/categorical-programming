# A Categorical View of Programming
The purpose of this document is to outline my thoughts on how programming works on a fundamental level. These insights have taken shape over several years, mostly driven by thinking that there must be a better way of doing things, then spending hours of my free time searching for. As a consequence, this document is a sort of synthesis of several ideas from computer science and mathematics developed over countless years by several brilliant people, with a few of my own ideas mixed in on the future of the industry.

My views here are influenced by several others in the field whose blog posts, lecture videos, papers, and repositories have influenced my views. I can't list all of them since I wasn't tracking them before now, so I'm certain I've missed some of them. I'm certain there's many more people working on related problems who I've never heard about, or other aspects of computer science I've never heard of; if anyone has any insight there I'd much appreciate some references.

## Motivations

### The Act of Programming

#### Levels of Programming
Most of the time spent physically writing code (in implementation) is spent doing some combination of the following:
- Moving data from one location to another.
- Building structured data.
- Accessing some component in structured data.
- Updating some component in structured data.
- Interpreting structured data.
- Parsing data

The closest prerequisite to the code is building up a mental model of the data and the execution flow, which comes from some combination of the following (dependent on the project):
- Reading the documentation.
- Looking up examples.
- Experimenting with the data to see what actually works and what doesn't.
- Reading the source code of the dependencies.

The next prerequisite up from that is designing a system that solves the problem you're working on, which requires some combination of the following:
- Knowledge of design patterns.
- Knowledge of what is and isn't possible at this time regardless of programming language and dependencies of choice.
- Knowledge of what is and isn't difficult to do in your programming language and dependencies of choice.
- Knowledge of component problems and the existing solutions to those.
- Knowledge of when to choose one solution or design pattern over another.

The final prerequisite is actually knowing the problem you want to solve, which requires the following:
- Specification of the problem at hand.
- Domain knowlege, which can be of arbitrary complexity.

#### Potential Problems
Each stage has it's own problems that can arise over time:
- Implementation
    - It doesn't run fast enough.
    - It isn't memory efficient enough.
    - It isn't flexible enough for what the user wants to do.
    - It doesn't give the correct result.
    - Something that was thought to be correct and consistent breaks when a dependency is updated, either introducing a new bug or fixing one where the implementation depended on incorrect behavior.
- Mental Model
    - Subtlely incorrect model of execution flow.
    - Documentation being non-existant, out of date, or outright wrong.
    - Dependencies behaving inconsistently because each dependency (and all their transitive dependencies up the tree) have their own versions of these problems.
    - Something that was thought to be stable and consistent turns out to be inconsistent in very specific cases.
- Design
    - Missing information on what design patterns are available, requiring either research to obtain that knowledge or ad-hoc attempts at solving it brute force through implementation.
    - Missing knowledge of what isn't possible, leading to wasting time either researching a solution, or worse, trying to build a solution to an impossible problem from scratch.
    - Missing knowledge of what is difficult and what is easy to do, leading down paths that can take far longer to implement and yield a worse solution than a simpler one.
    - Forcing a particular solution or design pattern to solve a problem it doesn't actually solve, making a real solution even more difficult to implement.
- Knowing the problem
    - Inconsistent or conflicting requirements for the solution.
    - Vague specification of the problem at hand.
    - Missing domain knowledge, which can happen whenever:
        - The client assumes the implementer has all of the prerequisite domain knowledge.
        - The client assumes the implementer knows where to access the prerequisite domain knowledge.
        - The client only has partial domain knowledge themselves.
    - Domain knowledge and best practices change over time.
    - Requirements change over time.
    - Processes change over time.

#### Relationships Between Levels
These levels are typically seen as hierarchical, but the truth is that none of them exist in a vacuum:
  - The implementation influences the mental model providing feedback on what will and will not work, while the mental model influences what gets tried for implementation.
  - The mental model influences design because the design has to be encoded into the mental model, while the design influences the mental model as the problem must be broken down into manageable chunks.
  - The implementation influences the design because some designs are simply not as efficient as others, while the design dictates the overall structure of the program.
  - The design influences knowledge of the problem because it will eventually force a thorough exploration of the solution space (which in turn requires thorough knowledge of the problem), and knowledge of the problem influences the design because as more knowledge is gained more designs can be ruled out and it may require more scaleable designs or the invention of entirely new designs.
  - The mental model influences knowledge of the problem by forcing the consideration of different possibilities, while knowledge of the problem influences what those possibilities are that the mental model must encode.
  - The implementation influences knowledge of the problem by opening up new possibilities due to the structure of the program (ie. more flexible implementations allow for a greater scope, prompting to consult whether or not that greater scope can be encountered in the context of the problem), and knowledge of the problem influences the overall direction the implementation must go.

An astute reader will notice that there are actually parallels to this in any profession, meaning that this is actually the core of _what experience is_: how much of this knowledge has someone already accumulated, and how can they apply it in a different context.

Back to the case of programming, a developer must have at least some understanding of each level before they can even _begin_ working on _an_ implementation. Almost all first implementations are guaranteed not to work exactly as planned, sometimes in obvious ways (eg. failing to compile or run at all), sometimes in subtle but limited magnitude ways (eg. failing to send an overnight email the day of a time zone change), and sometimes in subtle but potentially catostrophic ways (eg. off by 1 errors corrupting some records in a database table but not others, with no way to consistently detect which is which).

As far as I'm aware, there's no real scientific study analyzing the frequency of these errors or their magnitude, so for the purpose of this document I'm effectively going to consider them to be probabilistic where less experienced developers are more likely to make errors than senior developers. This is important because people will make mistakes which sometimes wind up in production, which will ultimately cause the start of this process all over again with this new problem. This ultimately means the following:
- Mistakes can compound, forcing more cycles through the process than anticipated.
- An entirely different solution must be implemented.

There are also the additional constraints of time and budget, which can result in cutting corners in areas they really shouldn't be cut. Ultimately this practice will just cause even more time and money to be spent fixing what should have been correct in the first place.

The kicker is that all of this is before you do anything actually interesting with it (ie. your business logic, and why your building the system in the first place).


### Errors and Exploits
Building a system incorrectly gives rise to errors, some of them program errors, others logic errors, but all of them give rise to flaws in the system. These flaws can range in size:
- Minor (small impact, mostly just an inconvenience)
    - Not displaying the correct text on a web page.
    - Flicker of a UI element.
- Major (wide impact, but doesn't lead to substantial long-term damages)
    - 2016-03-23 [The package `left-pad` is unpublished from `npm`, which broke build processes for an unknown number of projects as it was a transitive dependency used by popular projects such as React.](https://www.theregister.com/2016/03/23/npm_left_pad_chaos/)
    - 2018-07-24 [The existance of a ~3 year exploit is revealed and documented. Through social engineering, an innocuous fix is added to a server program for a popular game. This fix simply performed a check on whether a given area of the world was loaded or unloaded before returning a response, then exploited to track the movement of every player on the server, create heatmaps, associate paths with individuals, and allow remotely viewing everything someone else has made on the server.](https://github.com/nerdsinspace/nocom-explanation/tree/main)
    - 2024-04-17 [The existance of a game exploit using RNG manipulation to track players on a server (similar to the earlier one above) is disclosed. The bug had existed since 2011, where an RNG instance was reused for multiple purposes, including world generation and events caused by player activity, where the RNG turned out to be entirely reversable. Although patched on more recent versions, servers running older versions still existed. Servers running new versions are still vulnerable to some extent if it had been running a version this exploit would work on and someone had logged packets during that time.](https://github.com/spawnmason/randar-explanation)
- Catastrophic (the impact is extremely damaging, in some cases leading to incapacitation or death)
    - 1985 - 1987 - [Incorrect error messages which ultimately caused giving patients a lethal dose of radiation.](https://users.csc.calpoly.edu/~jdalbey/SWE/Papers/THERAC25.html)
    - 2020-12-13 [A cybersecurity firm discovers the source of an infiltration in their network is from a backdoor in their admin software, which was added as a supply chain attack. Many government and corporate entities are affected by the attack, and it is unknown if unauthorized access is still possible on machines originally compromised.](https://www.npr.org/2021/04/16/985439655/a-worst-nightmare-cyberattack-the-untold-story-of-the-solarwinds-hack)
    - 2021-10-14 [A system exposing the personal information, including social security numbers, over plaintext on the open internet for anyone to see. This information had been exposed for over 10 years.](https://krebsonsecurity.com/2022/02/report-missouri-governors-office-responsible-for-teacher-data-leak/)
    - 2021-12-09 - [A zero-day exploit in a logging library, found to allow arbitrary code execution, is disclosed. Although patches exist, vulnerable versions of it still exist, and patched versions still had the risk of having malicious code run or data stolen prior to applying the patch. It remains one of the most widely exploited vulnerabilities in history.](https://www.ibm.com/topics/log4shell)
    - 2024-07-12 - [A major phone service provider discloses that call records all of its cellular customers were stolen via a popular third-party data storage and analytics service.](https://www.cnn.com/2024/07/12/business/att-customers-massive-breach/index.html)
    - 2024-07-18 - [First signs of suspicious activity are detected on a city's network.](https://www.dispatch.com/story/news/local/2024/08/13/columbus-mayor-andrew-ginther-releases-timeline-of-cyberattack/74780225007/) [Initially the city claims to have thwarted a ransomware attack, followed by later claiming that any data stolen was encrypted and unusable, before finally nearly a month after the start being forced to admit that the data of nearly half a million private citizens and thousands of city employees may have been compromised. Although the city is offering credit monitoring, it is likely it will cost taxpayers millions of dollars to restore and replace compromised systems, without factoring in potential identity theft and credit issues.](https://www.dispatch.com/story/news/local/2024/08/19/cyber-security-hack-columbus-data-breach-rhysida-lawsuit/74827528007/)
    - 2024-07-19 - [An automatic update causing hundreds of servers to fail, taking several businesses offline and causing several days if not weeks worth of work sorting out customer issues and fixing business records, in addition to several billion dollars in losses.](https://www.cnn.com/2024/07/24/tech/crowdstrike-outage-cost-cause/index.html) [Ultimately the update reconfigured what content was gathered, which caused an out-of-bounds access which resulted in a system crash.](https://www.crowdstrike.com/falcon-content-update-remediation-and-guidance-hub/)
    - 2024-08-17 - [A private company that provides background checks confirms a breach where the records of billions of people were stolen. The records include names, addresses, social security numbers, and relatives dating back at least 30 years. Since the company provides background checks, it is highly unlikely that the majority of those affected were notified directly, if at all.](https://www.usatoday.com/story/tech/2024/08/17/social-security-hack-national-public-data-confirms/74843810007/) [It turns out to have been caused by a sister site hosting plaintext usernames and passwords.](https://krebsonsecurity.com/2024/08/national-public-data-published-its-own-passwords/)
- Unknown (an issue existed for years but the true impact is unknown and/or unknowable)
    - 2018-01-03 - [Meltdown and Spectre speculative execution exploits are disclosed. As a hardware exploit, these affect _practically all CPUs manufactured since 1995_, although it can be mitigated with software patches.](https://spectreattack.com/) [It is still an ongoing issue that is unlikely to ever truely be fixed.](https://en.wikipedia.org/wiki/Transient_execution_CPU_vulnerability#2024)
    - 2022-01-25 - [Pwnkit exploit is disclosed, demonstrating a memory corruption vulnerability to achieve privilege escalation on Linux systems. The vulnerability had existed since 2009.](https://blog.qualys.com/vulnerabilities-threat-research/2022/01/25/pwnkit-local-privilege-escalation-vulnerability-discovered-in-polkits-pkexec-cve-2021-4034)

This isn't even close to the number of medium to high profile bugs and exploits over the years, nor is it even close to some of the most severe ones. The points I want to get across here are the following:
1. There is no real way to find and fix all mistakes and bugs in any given program, since some of these have existed for several years. Testing helps to mitigate the possibilities, but it can only show the presence of bugs, not their absence.
    - The closest we have to proving the absence of bugs to my knowledge is proof assistants and dependently typed languages like Coq, Agda, and Idris, but in general with any Turing complete language it [_isn't possible_](https://en.wikipedia.org/wiki/Halting_problem) to determine the total absence of bugs for any arbitrary program.
2. The causes of these issues at first glance are varied but they all tend to be rather innocuous:
    - Something can't be found.
    - Performing a basic optimization to check if something is currently loaded or not.
    - Reusing an existing component.
    - Displaying incorrect messages to the user.
    - Automatic updates are sent out, and someone obtained unauthorized access to the update server compromising it.
    - Displaying all information, even when only some of it is necessary.
    - Providing a convenient way of referencing something else and automatically loading it.
    - Using a third party service.
    - Suspected drive-by download and social engineering.
    - A content update that caused a mismatch in input count.
    - Allegedly outsourcing site development, with a lack of proper review.
    - Hardware optimizations.
    - Assuming arguments are at least a minimum size and writing to it.
3. The effects when a bug is encountered or an exploit is realized do not correlate to the human perceived relevance of its cause. In other words, each of these could be considered a minor issue in isolation until it actually occurs, where it could suddenly turn disastrous.
4. The frequency of these issues is _increasing, **not** decreasing_.
5. A lot of these are caused by some combination of the following:
    - Dependencies written by someone else existing in a larger system.
    - Part of the program being written by someone who doesn't actually understand all of the details about something.
    - Unwritten assumptions.
    - Unknown dependencies.
    - Unknown limitations.
6. Software is ultimately becoming more complex and more interconnected over time, which will only trend upward.

One of the biggest unwritten assumptions in software development is that it is the responsibility of the programmer to fully understand exactly what the machine is doing at all times and from all perspectives. Based on the evidence above, this mindset is ultimately harmful, unsustainable, and quite frankly impossible to uphold. Ultimately this is because:
- It's simply not possible to predict all the ways the code you write today will be used now and in the future.
- [Turing completeness is everywhere](https://beza1e1.tuxen.de/articles/accidentally_turing_complete.html), where the more complex the system is, the higher probability it is that the system is Turing complete.
- Systems are becoming more complex, both internally (dependencies) and externally (integrations with other systems).
- The field itself is enormous, it's not just "writing a program that works" anymore, it's "writing a program that works AND is always available AND is secure from known threats AND is easy to use AND is always accessible to everyone AND integrates with external services AND..."
    - I want to elaborate that my problem isn't that these things shouldn't be there, it's that these fields are both very wide and deep in content and knowledge: building up the prerequisite knowledge to do everything in the best possible way would require _decades_ of work, let alone _building_ such a system.
- Programming is not done in isolation anymore: even in cases where something is written entirely by one person, they still rely on dependencies and transient dependencies written by others, sometimes written years ago by someone who may no longer be around.
    - This is actually _the_ most important point in this regard: every additional developer is an additional node in the network, with some knowledge redundencies, some knowledge impedence mismatch, and some knowledge gaps.
- As programming work becomes more interconnected, the complexity is going to increase exponentially.
- Generative AI is all the rage now, which means some people will use it for programming. I guarantee that not every line of code generated in this manner will be reviewed, and because these AIs are really good at generating a lot of content but not so good at generating well formed programs/fragments free of errors, all of these problems will become exacerbated in the future.

If not changed, this mindset will keep programming exactly where it is and where it has been for the past 50 years: reinventing the same patterns and mistakes over and over again. The solution to this is of course finding a better way to manage this complexity: instead of relying on human responsibility (which is falliable at the best of times let alone the worst), designing a system that abstracts over the complexities of programming.

But what would such a system look like? To find this out, we need to understand the core of what programming actually is.

TODO: Expand on this

The paradoxes of programming

Reuse vs. Robust

TODO: Relocate this

Additionally, we live in a world where the success of any given service is defined by how many services it integrates and how well it does so, with each of those systems having their own tech stack and the unknowable number of errors that could happen with them. With the rise of generative AI, companies are looking to integrate it more into code production, and though it has been trained on more information than anyone could possibly consume in one hundred lifetimes let alone a single one, it is prone to "hallucinations," which can produce incorrect code, or even worse, technically correct code with gaping security flaws in it.

There are also many who would take advantage of these flaws, some of them rather benign (someone exploiting a game for a speedrun), some of them outright malicious (exploiting an online system to obtain contact and financial information), and some of them seemingly fine until they suddenly aren't (a developer doing a neat trick dependent on a bug in a dependency to implement a feature, only for that bug to be fixed which breaks the downstream implementation). And with the break neck speed everything progresses at in tech, it is impossible for everyone to keep up.

## Mindset
The core idea I want to focus on is the essence of programming and finding a universal way to structure and organize programs, and from there automate more of the process of building programs than ever before.

This may seem impossible with the vast quantity of programming languages and frameworks out there, but at its core programming has roots in mathematics, meaning that any universal way of constructing and manipulating programs will ultimately be rooted in the foundations of mathematics, of which I think the branch of Category Theory makes an excellent candidate.

### Programming Language(s)
As I want to focus on a universal way to construct and manipulate programs, this solution must work for any given programming language. General purpose programming languages have the property of undecidability as a consequence of Turing completeness, while several systems based on Category Theory are provably decidable and are therefore not Turing complete.

Ultimately there are some constructs that general purpose programming languages allow that will not be reproducible in a system based purely on Category Theory. Likewise, any program constructed to conform completely to concepts from Category Theory will, assuming correct implementation, not be Turing complete. This is actually a _good_ thing, because the additional constraints make the output of such a program deterministic and prevents cases of arbitrary code execution.

Ultimately I want to focus on the general concepts behind programming, rather than any particular language or implementation. When programming languages are brought up, I primarily want to demonstrate what the concept looks like under a particular paradigm.

### Libraries and Frameworks
As with programming languages, I want to focus on the nature of programming itself rather than any particular library or implementation. The difference between these that I want to note is that frameworks tend to implement other execution models than those natively supported by the host programming language, so libraries can be viewed as similar to an embedded domain specific language.

## Foundations
I want to quickly review some of the foundational concepts in computer science and mathematics before delving into the meat of what I envision, as any attempts to progress the field must eventually address these anyway.

### Execution Models
Eventually all programs must execute in accordance with some sort of semantics, called an execution model. There are several possible execution models, though at this time the most targeted by general purpose programming languages are variants of the Von Neumann architecture, which has the following properties:
- Variables as memory addresses
- One instruction is processed at a time
- Data must be marshaled between storage and processing

This model initially arose as a hardware architecture to allow reprogrammable machines, although many programming languages use it as a base execution model even when they're targeting a custom virtual machine (the most common being stack based virtual machines). However, this model has many problems, as [noted by John Backus in his 1977 ACM Turing Award lecture.](https://dl.acm.org/doi/10.1145/359576.359579). I want to take a moment to point out the age of this document, at the time of this writing it is 47 years old, and many of the flaws mentioned therein about programming languages at the time are still valid today, and the solutions proposed are remarkably similar to the changes that are slowly trickling into mainstream programming languages. The point I want to make here is that these ideas aren't anything new, they've been around for a long time.

In any case, one particular artifact of this model that I have noticed is [the difference between referential equality and object equality](https://web.mit.edu/6.005/www/fa15/classes/15-equality/), with most languages opting for the default equality operator, `==`, to mean referential equality (ie. for non-primitive types they are only recognized as equal when they refer to the same object in memory), simply because it's easier. Ultimately, I believe this leads to some combination of the following:
- Non-standardized equality comparisons (eg. `==` vs `isEqual()` vs. `equals()` depending on language and dependency choices)
- Manual comparison of all the components of a non-primitive type
- Primitive obsession
- Inconsistencies in the codebase when more than one developer is involved, when development takes years to complete, or during ongoing maintenance caused by the above issues

The root of my problem with the above is that this ultimately comes down to attempting to satisfy two different and sometimes conflicting motivations when building software: performance vs. abstraction. On one hand, you need the notion of referential equality when working on the lower abstraction levels, particularly when optimizing. On the other hand, you need the notion of object equality whenever you're attempting to perform some complex domain-specific logic, or whenever you're building a higher abstraction level. Problems arise when you mix these two notions, for example:
- You think you're working on one
- Junior developers don't know the difference between the different notions of equality, and just use the most well known and convenient one (typically `==`).
- You're refactoring an existing system to be more abstract (eg. `==` -> `equals()`).
- You're optimizing an existing abstract system (eg. `equals()` -> `==` for internal operations).

To clarify, the bigger problem here is that _the notion of referential equality exists as an artifact of the underlying execution model_, in this case the Von Neumann architecture. This means, ultimately, that the underlying execution model is a [leaky abstraction](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/), one that most mainstream programming languages fall victim to because there is no structured and language enforced way to enforce the separation between different abstraction layers. Without such structure and a way to enforce it, there can be no true separation of an algorithm and its implementation (at least not without a ton of engineering work, which may very well be infeasible for a given project).

One last point I want to bring up here is that _this problem will exist regardless of the underlying execution model_. We don't live in an ideal world, and we have to make trade offs to deal with constraints, the underlying execution model and the hardware it runs on just being one example of this. There are alternative models that could be used, and hardware that could efficiently handle them, though existing programs that push the boundaries of performance may very well be incredibly inefficient running on that hardware. This is what I mean by true separation of an algorithm and an implementation: the _algorithm_ is what happens on an _abstract_ level (_what_ you are doing), and the _implementation_ is what _runs_ under a given execution model (_how_ you are doing it).

Below is a list (albeit incomplete I'm sure) of alternative execution models, some of them Turing complete (and therefore potential general computer architectures in the future) and others not (either specialized computer architectures or VM targets):
- [Petri nets](https://en.wikipedia.org/wiki/Petri_net) (not Turing complete)
    - [Extended Petri nets](https://www.sciencedirect.com/science/article/pii/S0890540114001072) are the Turing complete variants.
- Rules engines
- [Rewrite logic](https://en.wikipedia.org/wiki/Rewriting) can be either Turing complete or incomplete.
- [Untyped Lambda calculus](https://en.m.wikipedia.org/wiki/Lambda_calculus) (Turing complete)
    - [Typed Lambda calculus](https://en.wikipedia.org/wiki/Typed_lambda_calculus) includes both Turing complete and incomplete variants.
- [Formal logic](https://en.wikipedia.org/wiki/Logic#Formal_logic) (Turing complete)
   - Also see the [Curry-Howard correspondence](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence) (proofs as programs).
- [S-expressions](https://en.wikipedia.org/wiki/S-expression) (Turing complete)
- [Combinatory logic](https://en.wikipedia.org/wiki/Combinatory_logic) (Turing complete)
- [Actor model](https://en.wikipedia.org/wiki/Actor_model) (Turing complete)
- Graph execution
- [Cellular Automata](https://en.wikipedia.org/wiki/Cellular_automaton) can be either Turing complete or incomplete.
- [Finite State Machines](https://en.wikipedia.org/wiki/Finite-state_machine) (not Turing complete)
- [Pushdown automata](https://en.wikipedia.org/wiki/Pushdown_automaton) typically not Turing complete, but employing multiple stacks can make one Turing complete.


### Type Theory
#### Algebraic Data Types (ADTs)
##### Product Types
##### Sum Types
#### Dependent Types

### Category Theory
#### Isomorphisms
#### Functors
#### Natural Transformations
#### Algebraic Structures
##### Monoids
##### Monads
#### Categorical Representations of ADTs
##### Categorical Product
##### Categorical Coproduct (Sum)
##### Recursive Categories
###### Recursive Definition
###### Instance
#### The Yoneda Lemma

## Programming From a Categorical Lens
### Type Category
#### Polymorphism
### Dependency Categories
#### Module Dependency
#### Data Dependency
### Data Flow Category
### Semantics Category
### Syntax Category
### Execution Categories
#### Static or Compile Time Category
#### Runtime Category
### The Act of Programming
#### Graph Traversal
#### Refactoring Category (Isomorphism + Functor)

## A Categorical Model For Programming
### Isomorphism Categories (Abstract Data Types)
### Homset Morphisms (Morphisms Between Abstract Data Types)

## Relating Back to Programming Paradigms
### Procedural
### Object Oriented
#### Design Patterns
### Functional
### Logic

## A Potential Future for Programming
### Abstract Algorithm Categories
### Implementation Categories
### Compilation Categories
#### Abstract Algorithm -> Implementation Functors
#### Implementation -> Abstract Algorithm Functors
#### Optimization Categories
### Graph Pathfinding
#### Automated Graph Traversal
#### Implementation Selection
#### Automated Optimization
### Category <-> Code Isomorphism
#### Code Generation From Categories
#### Category Generation From Code
### Online Categorical Repository/Database
#### Partial Library Useage
#### Function Level Versioning
#### Static Paths for Consistent Builds
#### Universal Standardized Security Updates
#### Universal Optimization Updates
### Graphical Composition of Data and Algorithms
### Why Generative AI Is Not Enough

## Existing Tools and Resources
Below is a work in progress list of tools and resources I have found that can be useful in implementing such a system, either directly or serving as inspiration.

I haven't had time to properly read or test all of these myself, and a good portion of them I've only made it partway through, so this is more of an exercise in getting an idea of what already exists in this area and tracking the topics I'm at least aware of for future reference. I'll be expanding on this list as I find links and papers I've downloaded on my laptop and phone.

### Open Source
- [JetBrains Meta Programming System (MPS)](https://www.jetbrains.com/mps/) - A programming language workbench for designing domain specific languages. Allows direct manipulation of the Abstract Syntax Tree through projectional editors, with some examples being plain text and diagrams. Allows code generation targeting other languages.
    - [Implementation (bootstrapping)](https://github.com/JetBrains/MPS)
- [Maude](https://maude.cs.illinois.edu/w/index.php/The_Maude_System) - A reflective programming language based on equational reasoning and rewriting logic. Comes with [several proof and verification tools](https://maude.cs.illinois.edu/wiki/Maude_Tools) that [have been applied in several domains](https://maude.cs.illinois.edu/wiki/Applications).
    - [C++ implementation](https://github.com/maude-lang/Maude)
- [A Categorical Programming Language (by Tatsuya Hagino - 1987)](https://arxiv.org/abs/2010.05167) - Paper that outlines a Categorical Specification Language, Categorical Data Types, and lastly a Categorical Programming Language.
  - [Interpreter written in Haskell](https://github.com/msakai/cpl)
- [Categorical Databases](https://www.categoricaldata.net/) and [CQL](https://categoricaldata.github.io/) - A data schema and language designed with applied category theory for moving data from one location to another.
    - [Java implementation](https://github.com/CategoricalData/CQL)
    - [Haskell implementation](https://github.com/statebox/cql)
- [CHARITY](https://pll.cpsc.ucalgary.ca/charity1/www/home.html) - A categorical programming language based on strong categorical datatypes, allowing both inductive datatypes and coinductive datatypes.
    - [C implementation (now archived)](https://github.com/dobesv/charity)
    - [OCaml implementation](https://github.com/edwinans/catprog)
- [Flat](https://github.com/Quid2/flat) - A compact binary data format based on choice and sequence operators. Acts as a primitive for serializing algebraic data types, but lacks the ability to distinguish between different types of the same shape.
    - [Specification paper](https://quid2.org/docs/Flat.pdf)
- [正名/ZM/Zhèng Míng](https://github.com/Quid2/zm) - Algebraic data type serialization and deserialization built as a layer on top of [Flat](https://github.com/Quid2/flat). Allows detection between different types with the same shape, though it doesn't have any built-in types (all types must be explicitly defined, including primitives), type definitions cannot be mutually recursive, and it doesn't support serialization of higher kinded types.
    - [Specification paper](https://quid2.org/docs/ZhengMing.pdf)
- [Type Oriented Protocol](https://github.com/Quid2/top) - Networking layer on top of [正名/ZM/Zhèng Míng](https://github.com/Quid2/zm) and [Flat](https://github.com/Quid2/flat). Allows the construction of type channels to send and receive binary representations of algebraic data types across a network.
    - [Specification paper](https://quid2.org/docs/Top.pdf)
- [Idris](https://www.idris-lang.org/) - Purely functional programming language with dependent types. Supports [multiple code-generation backends](https://idris2.readthedocs.io/en/latest/backends/index.html), compiling to [Chez Scheme](https://idris2.readthedocs.io/en/latest/backends/chez.html) by default but supports targeting other languages such as [Racket](https://idris2.readthedocs.io/en/latest/backends/racket.html), [JavaScript](https://idris2.readthedocs.io/en/latest/backends/javascript.html), [C](https://idris2.readthedocs.io/en/latest/backends/refc.html), and [allowing custom](https://idris2.readthedocs.io/en/latest/backends/custom.html) [code-generation backends](https://idris2.readthedocs.io/en/latest/backends/backend-cookbook.html).
    - [Implementation (bootstrapping)](https://github.com/idris-lang/Idris2)
- [Agda](https://wiki.portal.chalmers.se/agda/pmwiki.php) - Another dependently typed programming language that can also be used as a proof assistant. Like Idris, it supports multiple code-generation backends, namely [GHC](https://agda.readthedocs.io/en/latest/tools/compilers.html#ghc-backend) and [JavaScript](https://agda.readthedocs.io/en/latest/tools/compilers.html#javascript-backend).
    - [Haskell + Agda implementation](https://github.com/agda/agda)
- [Coq](https://coq.inria.fr/) - Mathematical proof assistant and functional programming language.
    - [OCaml + Coq implementation](https://github.com/coq/coq)
- [DisCoPy](https://discopy.org/) - Python toolkit for implementing string diagrams.
    - [Python implementation](https://github.com/discopy/discopy)
    - [Docs](https://docs.discopy.org/en/main/)
- [Lawvere](https://github.com/jameshaydon/lawvere) - A categorical programming language with effects that offers an interpreter written in Haskell, an experimental compiler targeting [JavaScript](https://github.com/jameshaydon/lawvere#compiling-to-javascript), and [another compiler](https://github.com/jameshaydon/lawvere?tab=readme-ov-file#the-categorical-abstract-machine) targeting a [Categorical Abstract Machine](https://www.sciencedirect.com/science/article/pii/0167642387900207).
    - Of interest, all data is modeled as arrows (equivalent to functions), with primitives starting from the unit type.
- [Apache TinkerPop™](https://tinkerpop.apache.org/index.html) - Graph computing framework supporting [several graph databases](https://tinkerpop.apache.org/providers.html) and [multiple programming languages](https://tinkerpop.apache.org/community.html).
    - [Java implmentation](https://github.com/apache/tinkerpop)
- [Enso](https://github.com/enso-org/enso) - A hybrid programming language aiming to support both textual and visual representations.
- [Hydra](https://github.com/categoricaldata/hydra) - A DSL for program generation based on [algebraic property graphs](https://github.com/CategoricalData/hydra/wiki/Property-graphs). The objective appears similar to the perspective of this document.
- [EdgeDB](https://www.edgedb.com/) - Markets itself as a graph-relational database that attempts to combine the best aspects of relational databases and graph databases while fixing the composability problems of SQL via [sets](https://docs.edgedb.com/database/edgeql/sets#ref-eql-everything-is-a-set).
    - [Python implementation](https://github.com/edgedb/edgedb)
- [Program Transformation Wiki](https://www.program-transformation.org/) - A huge repository of resources on program transformation.
- [nLab](https://ncatlab.org) - _The_ wiki to go for [category theory](https://ncatlab.org/nlab/show/category+theory), [type theory](https://ncatlab.org/nlab/show/type+theory), [topology](https://ncatlab.org/nlab/show/topology), and [many other topics](https://ncatlab.org/nlab/all_pages) in mathematics and science.
    - Of particular interest is the [computational trilogy](https://ncatlab.org/nlab/show/computational+trilogy) page, which posits that the domains of computation, formal logic, and ∞-category theory are three different perspectives of the foundations of mathematics.
- [Programming Language Theory λΠ](https://github.com/steshaw/plt) - An aggregation list of resources on learning topics in theoretical computer science.
- [Category Theory for Programmers Blog - Bartosz Milewski](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)
    - [Unofficial PDF version](https://github.com/hmemcpy/milewski-ctfp-pdf)
    - [YouTube Lectures](https://www.youtube.com/watch?v=I8LbkfSSR58&list=PLbgaMIhjbmEnaH_LTkxLI7FMa2HsnawM_)
- [From design patterns to category theory](https://blog.ploeh.dk/2017/10/04/from-design-patterns-to-category-theory/) - An excellent series of articles by Mark Seemann relating patterns from Object Oriented Programming to concepts in Category Theory.
- [Wikifunctions](https://www.wikifunctions.org) - A new project by the Wikimedia Foundation, its purpose is to provide an open repository of executable functions open for anyone to compose and run.
    - [List of all currently available functions.](https://www.wikifunctions.org/wiki/Special:ListObjectsByType/Z8)


### Closed Source
- [DMS® Software Reengineering Toolkit™](https://www.semanticdesigns.com/Products/DMS/DMSToolkit.html) - Multi-language compatible code analysis and transformation tool allowing source-to-source transformation.
    - Although it is closed source, [there](https://www.semanticdesigns.com/Products/DMS/LifeAfterParsing.html?Home=DMSToolkit) [are](https://www.semanticdesigns.com/Products/DMS/ProgramTransformation.html?Home=DMSToolkit) [several](https://www.semanticdesigns.com/Company/Publications/DMS-for-ICSE2004-reprint.pdf) [resources](https://www.semanticdesigns.com/Company/Publications/DMS-for-ICSE2004-slides.pdf) on that page and other pages on the site that are open, including a [bibliography](https://www.semanticdesigns.com/Company/Publications/) page with many additional links.
- [Waylay Rules Engine](https://www.waylay.io/components/rules-engine) -
    - [Article](https://www.waylay.io/articles/waylay-engine-one-rules-engine-to-rule-them-all) explaining the reasons for the engine, [linking](https://www.waylay.io/articles/bayesian-inference-based-programming-using-smart-agent-concept) [to](https://waylay.io/articles/the-curse-of-dimensionality-in-decision-trees-branching-problem) [several](https://docs.waylay.io/#/features/rules/) [articles](https://www.waylay.io/articles/cloud-function-orchestration-made-easy) [on](https://waylay.io/articles/challenges-of-applying-artificial-intelligence-in-iot-using-deep-learning) [the](https://waylay.io/articles/deep-learning-and-bayesian-modelling-building-the-automation-of-the-future) [subject](https://waylay.io/articles/holistic-approach-to-ai-and-iot-building-automation-of-the-future) and [the documentation](https://docs.waylay.io/#/).
- [Matterbeam](https://matterbeam.com/) - Abstracted ETL-like application designed to separate data producers from consumers, using an immutable data stream where producers can publish to a generic store and consumers read from that generic store. Every operation is a transformation, with intermediate steps stored in the data store as well, so it effectively works as an abstracted state monad layered overtop of data sources.
    - [Whitepaper](https://matterbeam.docsend.com/view/zxkm5hk4qbhwsrw3) explaining the motivation and architecture.

### Unknown Status
- [Cation Language](https://beta.cation-lang.org/) - Either not public or under development, and based on the `beta` subdomain likely won't be for a while. It does link to [data type algebra docs](https://www.strict-types.org/type-system/data-primitives) for [Strict types](https://www.strict-types.org/), which appears to be a work in progress specification for strict encoding for algebraic data types.


## References


# TODO:
## Look at integrating these somewhere, (probably need to review and de-dup these)

https://www.cp.eng.chula.ac.th/~prabhas//talk/2011/fp/a1977-backus.pdf

https://homepages.inf.ed.ac.uk/cheunen/publications/2008/arrows/arrows.pdf

https://github.com/zeromq/gsl

https://zguide.zeromq.org/docs/chapter7/

https://q.uiver.app

https://www.semanticscholar.org/paper/Can-Programming-Be-Liberated-from-the-von-Neumann-A-Backus/3eb24a1d6094ff9ddf940ad22dc848533d62ff89
https://arxiv.org/pdf/1602.02715.pdf

https://www.linkedin.com/pulse/von-neumann-architecture-becoming-obsolete-age-natural-daniel-danter-zm9pf

https://cs.stackexchange.com/questions/107277/why-do-we-still-use-a-von-neumann-architecture-in-modern-computers

https://interestingengineering.com/culture/john-von-neumann-human-the-computer-behind-project-manhattan

https://www.engineering.cornell.edu/news/building-new-computer

https://www.startribune.com/the-man-behind-major-advances-in-atomic-energy-open-source-ai-and-game-theory/600267304/?refresh=true

https://duckduckgo.com/?q=categorical+programming+language&t=lm&ia=web

https://github.blog/news-insights/product-news/introducing-github-models/

https://duckduckgo.com/?t=lm&q=theory+of+strong+categorical+datatypes&ia=web

https://www.sciencedirect.com/science/article/pii/0304397594000995

https://pdf.sciencedirectassets.com/271538/1-s2.0-S0304397500X00370/1-s2.0-0304397594000995/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEML%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJGMEQCIC9qjQVtxnKN4L%2FBjsAY0RPAECeXw8Ppy%2Fek9OrmlrM5AiAz170CSUFxXhAuSJaR5VcU%2FHQqxMkEYn0fd2AmgQpT%2Byq7BQir%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAUaDDA1OTAwMzU0Njg2NSIM7CrlBuRHRzJZGWdaKo8FQO%2FPnWIiMt70BQ7zT1o9W0GBXbYAxGI97aE7kWFO4xkBRFmV1ngNITwLjvAlTLGvqke%2F%2BAXTUIMFUMJSGPQfgMBs8XMbo62Hk8GE9ekMBcLAH9Pcnw3KnkGyQYJKIW3C6UU7VOAvZ0rqAQxE96H8tlx1ygHidzZeVf0XtNVP9j4ur0kk0pJeAQV2wQACClOSSurat5lveP6RndtMd0Yj8BxzuYFPGgFfAA%2Fr0tb1UbobVeV6Ed0LFbK%2FcsPmxLgqeMBMaiqCZK%2BnzOfySOhQbfAeiWLMw4pQL948PdZCWbmnepkwEGeKOSdDCDqbKjv01CKX13QQiLNlxaduul%2FxF%2F1u011gQxrKdnTJf5w9iClc3T4t5o9OHNFO4IR3G85uPc6AfOXuHkonAqX3i7lMg1taJfOANfOUMLapsX8LCZivq8FdAStkOzIQCsWeFmOmZyfYLWlJT%2F5WLYoE8jWgnP%2FcTTi%2B%2FFuJ%2F2gIJoraW5wnMAxdTHYFjeW9WTe1FbxbSezvUVoxFzffxu7TiplQp7U8SD%2FPg0cMMQzhGzy9CSPGGYa%2B6BHozxemN0OfhkQKlIu6DtFnSHZN1UraRa0pcxfMk1kJZmu5UZoon1vw3%2BXNSQFBMQZ2E1gQjZCK%2Fmznfa3dOKfw0U3lD9gsXHupEqL9sEGeFb6of8bN%2BxAWYGS4qa36glRWCZGKs8k38nA78o5tFC6kO%2BKtKGB4joC6PurJb6Wr%2ByAWrYbcKYYzWz%2F6cvzj8WtOSw2cANXoSo8zs%2FcAwDkdNoSZD8M9EN9ubbD3SIj%2Bnzs3qkbctXStGmARF%2Fc9rt07xXLdJqScqDPpbsLkVMMhwtcVtnfOPdje%2F%2BCDYEWk5XJvYWjnZrAgbzCg1Lm1BjqyAdQVJ5XoqpsZ9gEfCsQh5G%2FGlkgUQGQX6Pxe1q2fkwzpC9qfiHwSR6o8T%2FKmQP5rae2P5DL0TjcuHQwlSWYARPhGCAI9xH3LzxbKiT4boKV9MEa7gY0PLXeumvopc5jIEGTP4Qpj5LJe0x9tcfts8w91KODOMAQBMfgOCfork8rPeMMey68pb04d3khJaJPa8%2FZNsFsxDX%2FwM911%2B3DQu9UvE7isCu64BzOCGTa31fkCbOM%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20240803T181907Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTYZAWJZALP%2F20240803%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=b73a080e2e9b77b40ec4fe3de9df9f7385ab1be41df65df8ff31bebb8df96dc7&hash=cc2b6c7f942bfa363bda563aaf9ff32c8daddf09e312204f54b29c5f83218210&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=0304397594000995&tid=spdf-c8e7674a-78bf-4e32-930a-259539007d6b&sid=5fa56a308b76d042f60beba0d7fe338e1913gxrqa&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=131d5e02045d55555306&rr=8ad8506b29e1395e&cc=us

https://duckduckgo.com/?t=lm&q=Cation+Language+Categorical+programming+language&ia=web

https://github.com/tangentforks/catlang?tab=readme-ov-file

https://www.categoricaldata.net/papers

https://www.categoricaldata.net/examples

https://www.cs.ox.ac.uk/boris.motik/pubs/bkmmpst17becnhmarking-chase.pdf

https://silmarils.tech/

https://en.wikipedia.org/wiki/Applied_category_theory

https://topos.institute/

https://topos.institute/vision

https://legacy-www.math.harvard.edu/theses/senior/lehner/lehner.pdf

https://venturebeat.com/ai/move-over-deep-learning-symbolicas-structured-approach-could-transform-ai/

https://www.mm-adt.org/

https://www.appliedcategorytheory.org/

https://hackage.haskell.org/package/kan-extensions

https://www.algebraicjulia.org/

https://conexus.com/symbolic-generative-ai/

https://arxiv.org/abs/1903.10579

https://arxiv.org/pdf/1903.10579

https://www.antlr.org/

https://www.cs.man.ac.uk/~david/categories/book/book.pdf

https://arxiv.org/pdf/2209.01259

https://hypefortypes.github.io/S21/lectures/10-category-theory.pdf

https://github.com/search?q=categorical+programming+language&type=repositories&p=2

https://arxiv.org/abs/2103.10385

https://serokell.io/blog/best-haskell-open-source-projects
  https://github.com/Carnap/Carnap
  https://github.com/facebook/duckling
  https://github.com/github/semantic
  https://github.com/unisonweb/unison
  https://www.grammaticalframework.org/
  https://github.com/lamdu/lamdu

https://web.archive.org/web/20230307174938/https://math.mit.edu/~dspivak/informatics/

https://argumatronic.com/posts/2019-06-21-algebra-cheatsheet.html

https://refactoring.guru/design-patterns/bridge

[Evan Patterson: Principles and pitfalls of designing software for applied category theory](https://www.youtube.com/watch?v=cnWfksLlh1g)

https://link.springer.com/chapter/10.1007/978-3-030-17184-1_6

https://hackage.haskell.org/package/Paraiso
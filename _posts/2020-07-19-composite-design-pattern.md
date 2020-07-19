---

layout: post
comments: true
title: "Composite Design Pattern: An agile story"
category: design pattern
---

# 02-04: Composite Design Pattern

### Type: Structural
### Scope: Object
### Symptoms: Hierarchial Data

## Intent

Composite design pattern is a structural design pattern which defines part-whole relationship between objects within a tree structure.
In a complex tree structure, it's a challenge to discriminate between a leaf and branch. We need to have type check before performing any operation on the tree structure and complexity grows if we have varied types of leaf and branches. This leads to higher maintenance.

<img src="/images/composite-design-pattern/composite-tree.png" style="zoom:80%;" />

A composite design pattern helps simplify above mentioned issue by introducing a common interface which needs to be implemented by leaf and branches thereby generalizing the solution to a problem.

## Components

There are 3 main components in a composite pattern

<img src="/images/composite-design-pattern/composite-class.png" style="zoom:80%;" />

**1. Component:** This is the abstraction/interface for all the elements in a tree. It includes both leaf and branches. The operations performed on all the elements is defined here and this is how it eliminates the need for a type checking. The client instantiates the component class and doesn't need to know what is going inside the class.

**2. Leaf:** It represents the leaf element in a tree and doesn't contain any child elements. It also implements all the operations defined in a component. 

**3. Composite:** It is composed of multiple elements as child. It does implement all the operations defined in a component and mostly delegate the exectution to child elemenets.

## Implementation

### Example

**Domain:** Agile-Scrum

**Problem Statement:** We need to find a way to calculate the total story points for a project

In a typical scrum project, a project contains Epics and User stories. A project may contain a user story directly also without an Epic.
We are going to calulate total story points(34) for below hierarchy/tree structure

<img src="/images/composite-design-pattern/project-tree.png" style="zoom:80%;" />



## Solutions

### Solution 1: No Pattern

<img src="/images/composite-design-pattern/no-pattern.png" style="zoom:80%;" />

We have three classes(Project, Epic and UserStory). Project contains a list of Epic and User Story. Also, there are different methods for adding/removing each type(Epic/UserStory) such as AddEpic() and AddUserStory(). This makes the class fragile and for any new type introduced, we need to introduce corresponding Add/Remove methods.  

```C#
 class NoComposite
    {
        public void Run()
        {
            /*
             * Project
             *      |
             *      |-----Epic-1
             *      |       |
             *      |       |----US-1(5)
             *      |       |
             *      |       |----US-2(8)
             *      |
             *      |-----Epic-2
             *      |       |
             *      |       |----US-3(3)
             *      |       |
             *      |       |----US-4(8)
             *      |       
             *      |
             *      |----US-5(2)
             *      |
             *      |----US-6(8)
             *
             * Total story points in a project: 34
             */

            // User stories
            var us1 = new UserStory(5);
            var us2 = new UserStory(8);
            var us3 = new UserStory(3);
            var us4 = new UserStory(8);
            var us5 = new UserStory(2);
            var us6 = new UserStory(8);

            // Epics
            var epic1 = new Epic();
            epic1.AddUserStory(us1);
            epic1.AddUserStory(us2);

            var epic2 = new Epic();
            epic2.AddUserStory(us3);
            epic2.AddUserStory(us4);

            // Project
            var project = new Project();
            project.AddEpic(epic1);
            project.AddEpic(epic2);
            project.AddUserStory(us5);
            project.AddUserStory(us6);

            // Calculate total sum, it should be 34
            var result = project.CalculateStoryPoints();
            Console.WriteLine($"Total number of story points in a Project is {result}");
        }

        class UserStory
        {
            private int _storyPoints;

            public UserStory(int storyPoints)
            {
                _storyPoints = storyPoints;
            }

            public int CalculateStoryPoints()
            {
                return _storyPoints;
            }
        }

        class Epic
        {
            private readonly List<UserStory> _userStories;
            private int _storyPoints;

            public Epic()
            {
                _userStories = new List<UserStory>();
            }

            public int CalculateStoryPoints()
            {
                foreach (var userStory in _userStories)
                {
                    _storyPoints += userStory.CalculateStoryPoints();
                }

                return _storyPoints;
            }

            public void AddUserStory(UserStory userStory)
            {
                _userStories.Add(userStory);
            }

            public void RemoveUserStory(UserStory userStory)
            {
                _userStories.Remove(userStory);
            }
        }

        class Project
        {
            private readonly List<UserStory> _userStories;
            private readonly List<Epic> _epics;
            private int _storyPoints;

            public Project()
            {
                _userStories = new List<UserStory>();
                _epics = new List<Epic>();
            }

            public int CalculateStoryPoints()
            {
                // Add story points from all the user stories
                foreach (var userStory in _userStories)
                {
                    _storyPoints += userStory.CalculateStoryPoints();
                }

                // Add story points from all the epics
                foreach (var epic in _epics)
                {
                    _storyPoints += epic.CalculateStoryPoints();
                }

                return _storyPoints;
            }

            public void AddUserStory(UserStory userStory)
            {
                _userStories.Add(userStory);
            }

            public void RemoveUserStory(UserStory userStory)
            {
                _userStories.Remove(userStory);
            }

            public void AddEpic(Epic epic)
            {
                _epics.Add(epic);
            }

            public void RemoveEpic(Epic epic)
            {
                _epics.Remove(epic);
            }
        }
    }

var noComposite = new NoComposite();
noComposite.Run();
```

Total number of story points in a Project is 34

**Issues with above solution**

- Tight coupling between Project and Epic/UserStory
- Add/Remove methods for each type(Epic/UserStory)
- Open Closed Principle(OCP) violation. If a new type(Features) gets introduced, we need to modify Project class implementation and write Add/Remove methods separately.
- For a new type, there will be addition of new type in CalculateStoryPoints() method.

### Solution 2 : With Composite Pattern

<img src="/images/composite-design-pattern/composite-pattern-class.png" style="zoom:80%;" />



As seen above, this is a general solution with no coupling. IScrumComponent is the interface to be implemented by all elements within a tree.
Add/Remove methods have been generalized as well. If a new type gets introduced, it just needs to be inherited from IScrumComponent and everything will work. 

```c#
interface IScrumComponent
{
    int CalculateStoryPoints();
}

class WithComposite
{
    public void Run()
    {
        /*
         * Project
         *      |
         *      |-----Epic-1
         *      |       |
         *      |       |----US-1(5)
         *      |       |
         *      |       |----US-2(8)
         *      |
         *      |-----Epic-2
         *      |       |
         *      |       |----US-3(3)
         *      |       |
         *      |       |----US-4(8)
         *      |       
         *      |
         *      |----US-5(2)
         *      |
         *      |----US-6(8)
         *
         * Total story points in a project: 34
         */

        // User stories
        var us1 = new UserStory(5);
        var us2 = new UserStory(8);
        var us3 = new UserStory(3);
        var us4 = new UserStory(8);
        var us5 = new UserStory(2);
        var us6 = new UserStory(8);

        // Epics
        var epic1 = new Epic();
        epic1.AddComponent(us1);
        epic1.AddComponent(us2);

        var epic2 = new Epic();
        epic2.AddComponent(us3);
        epic2.AddComponent(us4);

        // Project
        var project = new Project();
        project.AddComponent(epic1);
        project.AddComponent(epic2);
        project.AddComponent(us5);
        project.AddComponent(us6);

        // Calculate total sum, it should be 34
        var result = project.CalculateStoryPoints();
        Console.WriteLine($"Total number of story points in a Project is {result}");
    }

    class UserStory : IScrumComponent
    {
        private int _storyPoints;

        public UserStory(int storyPoints)
        {
            _storyPoints = storyPoints;
        }

        public int CalculateStoryPoints()
        {
            return _storyPoints;
        }
    }

    class Epic : IScrumComponent
    {
        private readonly List<IScrumComponent> _components;
        private int _storyPoints;

        public Epic()
        {
            _components = new List<IScrumComponent>();
        }

        public int CalculateStoryPoints()
        {
            foreach (var component in _components)
            {
                _storyPoints += component.CalculateStoryPoints();
            }

            return _storyPoints;
        }

        public void AddComponent(IScrumComponent component)
        {
            _components.Add(component);
        }

        public void RemoveComponent(IScrumComponent component)
        {
            _components.Remove(component);
        }
    }

    class Project
    {
        private readonly List<IScrumComponent> _components;
        private int _storyPoints;

        public Project()
        {
            _components = new List<IScrumComponent>();
        }

        public int CalculateStoryPoints()
        {
            // Add story points from all the user stories
            foreach (var component in _components)
            {
                _storyPoints += component.CalculateStoryPoints();
            }

            return _storyPoints;
        }

        public void AddComponent(IScrumComponent component)
        {
            _components.Add(component);
        }

        public void RemoveComponent(IScrumComponent component)
        {
            _components.Remove(component);
        }
    }
}

var withComposite = new WithComposite();
withComposite.Run();
```

Total number of story points in a Project is 34

### Comparison - Class Diagrams

<img src="/images/composite-design-pattern/composite-vs-no-composite.png" style="zoom:80%;" />



## Conclusion

Composite design pattern is very useful in simplifying the complexity involved in a hierarchial/tree structure. It allows adding new types into the system without changing the client code and this makes the system very extensible. As a result, the maintenance is reduced to a great extent.

**Source:** https://github.com/praveenraghuvanshi/design-patterns/tree/master/02-Structural/02-04-composite

Please share your feedback as it'll encourage me to write more.

**Twitter:** @praveenraghuvan

**Github:** praveenraghuvanshi

**Blog:** praveenraghuvanshi.github.io
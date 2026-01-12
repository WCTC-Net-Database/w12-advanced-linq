# Week 12: Advanced LINQ & Inventory Management

> **Template Purpose:** This template represents a working solution through Week 11. Use YOUR repo if you're caught up. Use this as a fresh start if needed.

---

## Overview

This week you'll implement an **Inventory Management System** using advanced LINQ queries. You'll search, sort, filter, and group items in a player's inventory. This brings together everything you've learned about LINQ, EF Core, and SOLID principles into a cohesive system.

## Learning Objectives

By completing this assignment, you will:
- [ ] Use advanced LINQ queries (`GroupBy`, `OrderBy`, `Select`)
- [ ] Implement inventory management (add, equip, use, remove items)
- [ ] Create submenus for complex operations
- [ ] Apply LINQ to database queries with EF Core
- [ ] Build a complete feature from requirements

## Prerequisites

Before starting, ensure you have:
- [ ] Completed Week 11 assignment (or are using this template)
- [ ] Working Equipment and Item system
- [ ] Working Room Navigation (N/S/E/W) from Week 11
- [ ] Understanding of LINQ basics (`Where`, `FirstOrDefault`, `Sum`)
- [ ] EF Core context configured with Items and Rooms

## What's New This Week

| Concept | Description |
|---------|-------------|
| `GroupBy` | Group items by a property (e.g., type) |
| `OrderBy`/`OrderByDescending` | Sort items by property |
| `Select` | Project to new shape |
| Submenus | Nested menu options for complex features |
| Inventory | Collection of items on a player |

---

## Assignment Tasks

### Task 1: Understand the Inventory Structure

**What's already set up:**

The template uses a separate `Inventory` entity to hold items. This is different from the Equipment slot (which holds what's currently equipped).

**Inventory.cs** (in Models/Equipments/):
```csharp
public class Inventory
{
    public int Id { get; set; }
    public int PlayerId { get; set; }
    public virtual Player Player { get; set; }
    public virtual ICollection<Item> Items { get; set; }
}
```

**Player.cs** has:
```csharp
public class Player : Character
{
    // Existing properties...

    // Equipment: what's currently equipped (from Week 11)
    public virtual Equipment Equipment { get; set; }

    // Inventory: all items the player is carrying
    public virtual Inventory Inventory { get; set; }
}
```

> **Important:** To access inventory items, use `player.Inventory.Items`, not `player.Inventory` directly.

### Task 2: Implement Inventory Methods

**What to do:**
- Add methods to manage the inventory

**Player.cs:**
```csharp
public void AddItem(Item item)
{
    Inventory.Items.Add(item);
    Console.WriteLine($"Added {item.Name} to inventory.");
}

public void RemoveItem(Item item)
{
    Inventory.Items.Remove(item);
    Console.WriteLine($"Removed {item.Name} from inventory.");
}

public void UseItem(Item item)
{
    Console.WriteLine($"Used {item.Name}!");
    // Implement item-specific effects
}
```

### Task 3: Implement Search by Name

**What to do:**
- Search the inventory for items matching a name

**GameEngine.cs:**
```csharp
public void SearchInventoryByName(Player player)
{
    Console.Write("Enter item name to search: ");
    var searchTerm = Console.ReadLine();

    var results = player.Inventory.Items
        .Where(item => item.Name.Contains(searchTerm, StringComparison.OrdinalIgnoreCase))
        .ToList();

    if (results.Any())
    {
        Console.WriteLine($"Found {results.Count} items:");
        foreach (var item in results)
        {
            Console.WriteLine($"  - {item.Name} (Attack: {item.Attack}, Defense: {item.Defense})");
        }
    }
    else
    {
        Console.WriteLine("No items found.");
    }
}
```

### Task 4: Implement List by Type

**What to do:**
- Group inventory items by their type

**GameEngine.cs:**
```csharp
public void ListInventoryByType(Player player)
{
    var groupedItems = player.Inventory.Items
        .GroupBy(item => item.Type)
        .ToList();

    foreach (var group in groupedItems)
    {
        Console.WriteLine($"\n{group.Key}:");
        foreach (var item in group)
        {
            Console.WriteLine($"  - {item.Name}");
        }
    }
}
```

### Task 5: Implement Sort Options (Submenu)

**What to do:**
- Create a submenu for sorting options
- Implement sorting by Name, Attack, and Defense

**MenuManager.cs:**
```csharp
public void DisplaySortMenu()
{
    Console.WriteLine("\nSort Options:");
    Console.WriteLine("1. Sort by Name");
    Console.WriteLine("2. Sort by Attack Value");
    Console.WriteLine("3. Sort by Defense Value");
    Console.WriteLine("0. Back");
}
```

**GameEngine.cs:**
```csharp
public void SortInventory(Player player, string sortBy)
{
    var sorted = sortBy switch
    {
        "name" => player.Inventory.Items.OrderBy(i => i.Name).ToList(),
        "attack" => player.Inventory.Items.OrderByDescending(i => i.Attack).ToList(),
        "defense" => player.Inventory.Items.OrderByDescending(i => i.Defense).ToList(),
        _ => player.Inventory.Items.ToList()
    };

    Console.WriteLine($"\nInventory (sorted by {sortBy}):");
    foreach (var item in sorted)
    {
        Console.WriteLine($"  {item.Name} - Atk: {item.Attack}, Def: {item.Defense}");
    }
}
```

### Task 6: Update MenuManager

**What to do:**
- Add inventory management options to the menu

**MenuManager.cs:**
```csharp
Console.WriteLine("\nInventory Management:");
Console.WriteLine("1. Search for item by name");
Console.WriteLine("2. List items by type");
Console.WriteLine("3. Sort items");
Console.WriteLine("4. Equip item");
Console.WriteLine("5. Use item");
Console.WriteLine("0. Back to main menu");
```

---

## LINQ Quick Reference

```csharp
// Search by name (case-insensitive)
var results = items.Where(i => i.Name.Contains(term, StringComparison.OrdinalIgnoreCase));

// Group by type
var groups = items.GroupBy(i => i.Type);

// Sort ascending
var sorted = items.OrderBy(i => i.Name);

// Sort descending
var sorted = items.OrderByDescending(i => i.Attack);

// Get total value
var totalValue = items.Sum(i => i.Value);

// Count by type
var weaponCount = items.Count(i => i.Type == "Weapon");

// Check if any match
var hasWeapons = items.Any(i => i.Type == "Weapon");

// Project to new shape
var names = items.Select(i => i.Name);

// Chain multiple operations
var topWeapons = items
    .Where(i => i.Type == "Weapon")
    .OrderByDescending(i => i.Attack)
    .Take(5)
    .ToList();
```

---

## Menu Structure

```
Main Menu:
1. Explore
2. Combat
3. Inventory Management ─────┐
4. Character Stats           │
0. Exit                      │
                             │
Inventory Management: ◀──────┘
1. Search for item by name
2. List items by type
3. Sort items ────────────────┐
4. Equip item                 │
5. Use item                   │
0. Back                       │
                              │
Sort Options: ◀───────────────┘
1. Sort by Name
2. Sort by Attack Value
3. Sort by Defense Value
0. Back
```

---

## Stretch Goal (+10%)

**Inventory Weight Limit**

Add a weight system to limit inventory capacity:

**Player.cs:**
```csharp
public int MaxWeight { get; set; } = 100;

public int GetCurrentWeight()
{
    return Inventory.Items.Sum(i => i.Weight);
}

public bool CanAddItem(Item item)
{
    return GetCurrentWeight() + item.Weight <= MaxWeight;
}

public void AddItem(Item item)
{
    if (!CanAddItem(item))
    {
        Console.WriteLine($"Cannot add {item.Name} - would exceed weight limit!");
        Console.WriteLine($"Current: {GetCurrentWeight()}/{MaxWeight}, Item weight: {item.Weight}");
        return;
    }

    Inventory.Items.Add(item);
    Console.WriteLine($"Added {item.Name}. Weight: {GetCurrentWeight()}/{MaxWeight}");
}
```

**Filter by weight:**
```csharp
// Items that can still be added
var addableItems = availableItems
    .Where(i => player.GetCurrentWeight() + i.Weight <= player.MaxWeight)
    .ToList();
```

---

## Project Structure

This template uses a **two-project architecture**:

```
ConsoleRpgFinal.sln
│
├── ConsoleRpg/                        # UI & Game Logic
│   ├── Program.cs
│   ├── GameEngine.cs                  # Inventory operations
│   └── Helpers/
│       └── MenuManager.cs             # Menu with submenus
│
└── ConsoleRpgEntities/                # Data & Models
    ├── Models/
    │   ├── Characters/
    │   │   └── Player.cs              # Has Inventory property
    │   └── Equipments/
    │       ├── Equipment.cs           # From Week 11
    │       ├── Inventory.cs           # Inventory entity
    │       └── Item.cs
    └── Data/
        └── GameContext.cs
```

---

## Grading Rubric

| Criteria | Points | Description |
|----------|--------|-------------|
| Inventory Property | 15 | Player has Inventory collection |
| Search by Name | 20 | LINQ search works correctly |
| List by Type | 20 | GroupBy displays items by type |
| Sort Submenu | 25 | All three sort options work |
| Menu Integration | 10 | Inventory menu accessible and functional |
| Code Quality | 10 | Clean, readable, uses LINQ properly |
| **Total** | **100** | |
| **Stretch: Weight Limit** | **+10** | Weight system with validation |

---

## How This Connects to the Final Project

- Inventory management is a core RPG feature
- These LINQ patterns appear throughout the final project
- Submenus demonstrate proper menu organization
- This feature prepares you for complex user interactions

---

## Tips

- Use `ToList()` to execute LINQ queries immediately
- Remember `StringComparison.OrdinalIgnoreCase` for case-insensitive search
- Chain LINQ methods for complex operations
- Test with various inventory contents

---

## Submission

1. Commit your changes with a meaningful message
2. Push to your GitHub Classroom repository
3. Submit the repository URL in Canvas

---

## Resources

- [LINQ GroupBy](https://learn.microsoft.com/en-us/dotnet/csharp/linq/group-query-results)
- [LINQ OrderBy](https://learn.microsoft.com/en-us/dotnet/csharp/linq/order-results-of-join-clause)
- [LINQ Query Syntax vs Method Syntax](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)

---

## Need Help?

- Post questions in the Canvas discussion board
- Attend office hours
- Review the in-class repository for additional examples

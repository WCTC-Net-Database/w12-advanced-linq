# Assignment Tips: Week 12 – Advanced LINQ & Inventory Management

Welcome to Week 12! This guide will help you tackle the Inventory Management assignment using C# and LINQ.

## LINQ Cheat Sheet for Inventory Operations

### Searching for Items by Name
Search for items that contain a keyword (case-insensitive):

```csharp
// This will be translated to SQL and is case-sensitive depending on the DB collation
var results = context.Items
    .Where(i => i.Name.ToLower().Contains(searchTerm.ToLower()))
    .ToList();
```

### Grouping Items by Type (e.g., Weapon, Armor, Potion)

Group items and display each group:

```csharp
var groupedItems = player.Inventory
    .GroupBy(i => i.Type);

foreach (var group in groupedItems)
{
    Console.WriteLine($"\n{group.Key}:");
    foreach (var item in group)
    {
        Console.WriteLine($" - {item.Name}");
    }
}
```

#### Sort by Name

```csharp
var sortedByName = player.Inventory
    .OrderBy(i => i.Name)
    .ToList();
```

#### Sort by Attack Value

```csharp
var sortedByAttack = player.Inventory
    .OrderByDescending(i => i.Attack)
    .ToList();
```

#### Sort by Defense Value

```csharp
var sortedByDefense = player.Inventory
    .OrderByDescending(i => i.Defense)
    .ToList();
```

## Player Inventory Methods (Example Signatures)

Add these (or similar) methods to the `Player` class:

```csharp
public void AddItem(Item item) { ... }
public void RemoveItem(Item item) { ... }
public void EquipItem(Item item) { ... }
public void UseItem(Item item) { ... }
public void DisplayInventory() { ... }
```

Tip: Only allow equipping items with `Attack > 0` or `Defense > 0`.

## GameEngine Hints

- Call inventory-related methods from your menu.
- Use `switch` or nested menus to direct the player’s actions.
- Make use of `Console.WriteLine()` to guide the player.

## Seeding Example (GameContext)

```csharp
modelBuilder.Entity<Item>().HasData(
    new Item { Id = 1, Name = "Iron Sword", Type = "Weapon", Attack = 10, Defense = 0, Weight = 5 },
    new Item { Id = 2, Name = "Steel Shield", Type = "Armor", Attack = 0, Defense = 12, Weight = 7 },
    new Item { Id = 3, Name = "Healing Potion", Type = "Potion", Attack = 0, Defense = 0, Weight = 1 }
);
```

## Stretch Goal: Weight Limit Tips

- Add this to `Player`:

  ```csharp
  public int MaxWeight { get; set; } = 30;
  
  public int CurrentWeight => Inventory.Sum(i => i.Weight);
  ```

- Prevent adding items if:

  ```csharp
  if (CurrentWeight + newItem.Weight > MaxWeight)
  {
      Console.WriteLine("Too heavy!");
  }
  ```


# LOSearch: Flexible Database Search for PHP

A lightweight, flexible PHP class for simplifying database searches with customizable criteria, part of a modular suite including LOForm and LODatabase for building dynamic PHP applications.

---

## Introduction

**LOSearch** is a lightweight, flexible PHP class designed to simplify database searches with customizable criteria. It is part of a suite of tools that includes **LOForm** (for form handling) and **LODatabase** (for database management). Together, these classes provide a powerful, modular approach to building dynamic, data-driven applications in PHP.

LOSearch allows you to define search criteria programmatically, making it easy to filter, match, and retrieve data from your database. Whether you need exact matches, partial matches, or complex conditions, LOSearch provides the tools to handle it all.

---

## Why Use LOSearch?

- **Flexible Criteria**: Define search conditions using operators like `=`, `<`, `>`, `%` (contains), and `%i` (case-insensitive contains).
- **Array Support**: Search within arrays, with options for exact or partial matches.
- **Seamless Integration**: Works out-of-the-box with **LODatabase** and **LOForm**, allowing you to build complete search interfaces quickly.
- **Lightweight**: No external dependencies—just pure PHP.
- **Customizable**: Easily extend or modify criteria to fit your project’s needs.

---

## 🌐 Demo

Test LOSearch live on our official webpage: 
🔗 [LOSearch Demo](https://lacasuriortodoxe.ro/academia/open-source/losearch/)

More open-source projects: 
🔗 [Open Source projects](https://lacasuriortodoxe.ro/academia/open-source/)

---

## 🙏 Supporters

We are grateful to our supporters:

- **[NETCreator Hosting](https://netcreator.us)** and **[NETCreator Regio](http://regio.netcreator.us)**, two web hosting companies providing free and paid hosting in multiple locations.

---

## Installation

LOSearch is a single-class library. Simply include the `LOSearch.php` file in your project:

```php
require_once 'losearch.php';
```

---

## Basic Usage

### 1. Initialize LOSearch with a Database

LOSearch requires a database handler (e.g., **LODatabase**) to perform searches. Here’s how to set it up:

```php
require_once 'lodatabase.php';
require_once 'losearch.php';

// Initialize LODatabase
$db = new LODatabase('data.json');

// Initialize LOSearch with the database
$search = new LOSearch($db);
```

---

### 2. Define Search Criteria

Use the `criteria()` method to define how each field should be matched. For example:

```php
// Add criteria for the 'name' field (exact match)
$search->criteria([
    'column' => 'name',
    'operator' => '='
]);

// Add criteria for the 'age' field (greater than)
$search->criteria([
    'column' => 'age',
    'operator' => '>'
]);

// Add criteria for the 'tags' field (partial array match)
$search->criteria([
    'column' => 'tags',
    'exact_match' => false
]);
```

---

### 3. Perform a Search

Pass an associative array of search parameters to the `search()` method:

```php
$results = $search->search([
    'name' => 'John Doe',
    'age' => 25,
    'tags' => ['php', 'developer']
]);

// Output results
print_r($results);
```

---

## Advanced Examples

### Example 1: Case-Insensitive Search

```php
$search->criteria([
    'column' => 'title',
    'operator' => '%i' // Case-insensitive contains
]);

$results = $search->search(['title' => 'buna']);
// Matches "Buna", "BUNA", "bUnA", "bunĂ", "bună", etc.
```

---

### Example 2: Search with LOForm

Combine LOSearch with **LOForm** to create a dynamic search form:

```php
require_once 'loform.php';
require_once 'lodatabase.php';
require_once 'losearch.php';

// Initialize classes
$db = new LODatabase('users.json');
$search = new LOSearch($db);
$form = new LOForm();

// Define search criteria
$search->criteria(['column' => 'name', 'operator' => '%']);
$search->criteria(['column' => 'email', 'operator' => '%']);

// Create a search form
$form->field([
    'type' => 'text',
    'name' => 'name',
    'title' => 'Name',
    'holder' => 'Enter name...'
]);

$form->field([
    'type' => 'text',
    'name' => 'email',
    'title' => 'Email',
    'holder' => 'Enter email...'
]);

$form->field([
    'type' => 'button',
    'holder' => 'Search',
    'buttontype' => 'submit'
]);

// Load form values (e.g., from $_GET or $_POST)
$form->load($_GET);

// Perform search if form is submitted
if (!empty($_GET)) {
    $results = $search->search($_GET);
    echo "<h2>Search Results</h2>";
    print_r($results);
}

// Render the form
$form->form([
    'action' => 'get'
]);
```

---

### Example 3: Complete Workflow with LODatabase, LOForm, and LOSearch

This example demonstrates a full workflow: creating a database, adding data, building a search form, and displaying results.

```php
<?php
require_once 'lodatabase.php';
require_once 'loform.php';
require_once 'losearch.php';

// --- Step 1: Initialize Database ---
$db = new LODatabase('products.json');

// Add sample data
$db->insert([
    'name' => 'Laptop',
    'price' => 999.99,
    'tags' => ['electronics', 'computing']
]);
$db->insert([
    'name' => 'Smartphone',
    'price' => 699.99,
    'tags' => ['electronics', 'mobile']
]);
$db->insert([
    'name' => 'Headphones',
    'price' => 149.99,
    'tags' => ['electronics', 'audio']
]);
$db->commit();

// --- Step 2: Initialize LOSearch ---
$search = new LOSearch($db);

// Define search criteria
$search->criteria(['column' => 'name', 'operator' => '%i']);
$search->criteria(['column' => 'price', 'operator' => '<']);
$search->criteria(['column' => 'tags', 'exact_match' => false]);

// --- Step 3: Create Search Form ---
$form = new LOForm();

$form->field([
    'type' => 'text',
    'name' => 'name',
    'title' => 'Product Name',
    'holder' => 'Enter product name...'
]);

$form->field([
    'type' => 'number',
    'name' => 'price',
    'title' => 'Max Price',
    'holder' => 'Enter max price...'
]);

$form->field([
    'type' => 'select',
    'name' => 'tags',
    'title' => 'Tags',
    'options' => [
        'electronics' => 'Electronics',
        'computing' => 'Computing',
        'mobile' => 'Mobile',
        'audio' => 'Audio'
    ]
]);

$form->field([
    'type' => 'button',
    'holder' => 'Search',
    'buttontype' => 'submit'
]);

// Load form values from GET
$form->load($_GET);

// --- Step 4: Perform Search and Display Results ---
if (!empty($_GET)) {
    $results = $search->search($_GET);
    
    echo "<h2>Search Results</h2>";
    if (empty($results)) {
        echo "<p>No products found.</p>";
    } else {
        echo "<ul>";
        foreach ($results as $product) {
            echo "<li>{$product['name']} - \${$product['price']} (Tags: " . implode(', ', $product['tags']) . ")</li>";
        }
        echo "</ul>";
    }
}

// Render the form
$form->form([
    'method' => 'get'
]);

// Output headers and footers for LOForm (if needed)
LOForm::header();
LOForm::footer();
?>
```

---

## Methods

### `LOSearch::criteria(array $options)`

Adds a new search criterion. Accepts an associative array with the following keys:


| Key           | Type   | Default | Description                                                       |
| ------------- | ------ | ------- | ----------------------------------------------------------------- |
| `column`      | string | `''`    | The database column to search.                                    |
| `operator`    | string | `'='`   | Comparison operator (`=`, `<`, `>`, `>=`, `<=`, `<>`, `%`, `%i`). |
| `exact_match` | bool   | `false` | If `true`, requires an exact array match.                         |
| `empty_match` | bool   | `false` | If `true`, matches empty values.                                  |


**Example:**

```php
$search->criteria([
    'column' => 'age',
    'operator' => '>',
    'empty_match' => false
]);
```

---

### `LOSearch::search(array $post)`

Performs the search using the defined criteria and the provided `$post` data. Returns an array of matching rows from the database.

**Example:**

```php
$results = $search->search(['name' => 'John', 'age' => 30]);
```

---

### `LOSearch::identicalArray(array $a, array $b)`

**Private method**: Checks if two arrays are identical (same elements).

---

### `LOSearch::partialSubarray(array $all, array $in)`

**Private method**: Checks if `$in` is a partial subarray of `$all` (i.e., if there is any intersection).

---

## Operators

LOSearch supports the following operators for criteria:


| Operator | Description                 |
| -------- | --------------------------- |
| `=`      | Exact match                 |
| `<`      | Less than                   |
| `>`      | Greater than                |
| `>=`     | Greater than or equal to    |
| `<=`     | Less than or equal to       |
| `<>`     | Not equal to                |
| `%`      | Contains (case-sensitive)   |
| `%i`     | Contains (case-insensitive) |


---

## Notes

- LOSearch is designed to work with **LODatabase** but can be adapted for other database handlers by implementing the `select()` method.
- For array fields, use `exact_match` to enforce strict equality or leave it as `false` for partial matches.
- The `%i` operator is useful for case-insensitive searches, especially for text fields.

---

## License

LOSearch is open-source and free to use. Feel free to contribute or report issues on [GitHub](https://github.com/your-repo).

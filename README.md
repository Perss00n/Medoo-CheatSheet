# **📌 Medoo PHP Cheatsheet**

## **🛠️ Installera Medoo**
```sh
Ladda ner Medoo.php ifrån medoo.in och inkludera den i din fil, ex db.php

require __DIR__ . '/Medoo.php';
```

## **🔗 Anslut till Databas**
```php
use Medoo\Medoo;

$database = new Medoo([
    'type' => 'mysql',  // mysql, pgsql, sqlite, mssql, etc.
    'host' => 'localhost',
    'database' => 'testdb',
    'username' => 'root',
    'password' => '',
    /* Dessa är optional, finns mängder med inställningar du kan ange här på deras hemsida */
    'charset' => 'utf8',
    'error' => PDO::ERRMODE_EXCEPTION /* Standard är Silent Mode så kan va bra att sätta denna till just exeption /*
]);
```

---

## **🔍 SELECT – Hämta data**
### **Hämta alla rader**
```php
$data = $database->select("users", "*");
```

### **Hämta specifika kolumner**
```php
$data = $database->select("users", ["id", "name", "email"]);
```

### **WHERE – Villkor**
```php
$data = $database->select("users", "*", [
    "status" => "active"
]);
```

### **Flera WHERE-villkor (AND)**
```php
$data = $database->select("users", "*", [
    "AND" => [
        "status" => "active",
        "age[>]" => 18
    ]
]);
```

### **OR-villkor**
```php
$data = $database->select("users", "*", [
    "OR" => [
        "role" => "admin",
        "role" => "editor"
    ]
]);
```

### **ORDER BY, SORT, LIMIT & OFFSET**
```php
$data = $database->select("users", "*", [
    "ORDER" => ["created_at" => "DESC"],  // ASC eller DESC
    "LIMIT" => 10,   // Antal poster
    "OFFSET" => 5    // Hoppa över X poster
]);
```

### **Hämta en rad (fetch single row)**
```php
$user = $database->get("users", "*", ["id" => 1]);
```

---

## **➕ INSERT – Lägg till data**
```php
$database->insert("users", [
    "name" => "John Doe",
    "email" => "john@example.com",
    "status" => "active"
]);
```

### **Hämta senaste ID:t på den insatta raden**
```php
$last_id = $database->id();
```

---

## **✏️ UPDATE – Uppdatera data**
```php
$database->update("users", [
    "status" => "inactive"
], [
    "id" => 1
]);
```

---

## **🗑 DELETE – Ta bort data**
```php
$database->delete("users", [
    "id" => 1
]);
```

---

## **🔢 COUNT – Räkna rader**
```php
$count = $database->count("users", [
    "status" => "active"
]);
```

---

## **🔗 JOIN – Kombinera flera tabeller**
### **INNER JOIN**
```php
$data = $database->select("users", [
    "[>]profiles" => ["users.id" => "profiles.user_id"]
], [
    "users.id",
    "users.name",
    "profiles.bio"
]);
```

### **LEFT JOIN**
```php
$data = $database->select("users", [
    "[<]profiles" => ["users.id" => "profiles.user_id"]
], [
    "users.id",
    "users.name",
    "profiles.bio"
]);
```

---

## **🎯 Avancerade WHERE-villkor**
### **Mellan två värden (BETWEEN)**
```php
$data = $database->select("users", "*", [
    "age[<>]" => [18, 30]  // Mellan 18 och 30 år
]);
```

### **LIKE (Sökning med wildcard)**
```php
$data = $database->select("users", "*", [
    "name[~]" => "%John%"
]);
```

### **IN (Matcha flera värden)**
```php
$data = $database->select("users", "*", [
    "id" => [1, 2, 3, 4, 5]
]);
```

### **NOT IN**
```php
$data = $database->select("users", "*", [
    "id[!]" => [1, 2, 3]
]);
```

---

## **🔧 Aggregatfunktioner**
### **SUM**
```php
$totalSalary = $database->sum("employees", "salary", ["department" => "IT"]);
```

### **AVG**
```php
$averageAge = $database->avg("users", "age");
```

### **MIN/MAX**
```php
$oldestUser = $database->max("users", "age");
$youngestUser = $database->min("users", "age");
```

---

## **⏳ Transaktioner**
```php
$database->pdo->beginTransaction();

try {
    $database->insert("users", ["name" => "Alice"]);
    $database->insert("users", ["name" => "Bob"]);

    $database->pdo->commit();  // Spara ändringar
} catch (Exception $e) {
    $database->pdo->rollBack();  // Återställ om något går fel
}
```

---

## **🛑 Felhantering**
```php
$error = $database->error();
if ($error[0] !== "00000") {
    echo "Fel: " . json_encode($error);
}
```

---

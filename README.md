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

## **🔍 Hämta enskilda resultat**

### **Hämta en rad (`get`)**
```php
$user = $database->get("users", "*", [
    "id" => 1
]);

print_r($user);
```
**Förklaring:**  
- Hämtar **första matchande raden** från `users`-tabellen där `id` är 1.  
- Returnerar en **array** med data eller `null` om ingen matchning finns.  

---

### **Hämta en enskild kolumn (`get`)**
```php
$name = $database->get("users", "name", [
    "id" => 1
]);

echo $name; // Skriver ut användarens namn
```
**Förklaring:**  
- Hämtar **endast värdet** från kolumnen `name` där `id` är 1.  

---

### **Hämta en rad med villkor**
```php
$user = $database->get("users", "*", [
    "AND" => [
        "status" => "active",
        "role" => "admin"
    ]
]);
```
**Förklaring:**  
- Hämtar **första matchande raden** där `status` är `"active"` och `role` är `"admin"`.  

---

### **Hämta en specifik kolumn baserat på flera villkor**
```php
$email = $database->get("users", "email", [
    "name" => "John Doe",
    "status" => "active"
]);

echo $email;
```
**Förklaring:**  
- Hämtar e-postadressen för en aktiv användare med namnet `"John Doe"`.  

---

### **Använd `fetchColumn()` för att hämta en kolumn från en SQL-fråga**
```php
$email = $database->query("SELECT email FROM users WHERE id = 1")->fetchColumn();
echo $email;
```
**Förklaring:**  
- Om du vill hämta en **enskild kolumn** med en ren SQL-fråga.  

---

### **Hämta den senaste insatta raden**
```php
$lastUser = $database->get("users", "*", [
    "ORDER" => ["id" => "DESC"]
]);

print_r($lastUser);
```
**Förklaring:**  
- Hämtar den **senast insatta** användaren baserat på `id` i fallande ordning (`DESC`).  

---

## **🚀 Sammanfattning**
- Använd `get("table", "*", ["villkor"])` för att hämta en **hel rad**.
- Använd `get("table", "kolumn", ["villkor"])` för att hämta **ett enskilt värde**.
- Kombinera `ORDER`, `LIMIT`, `WHERE` för mer exakt hämtning.

---

## **🔍 SELECT – Hämta data**
### **Hämta alla rader**
```php
$data = $database->select("users", "*");
```
**Förklaring:**  
- Hämtar **alla rader** från tabellen `users`.  

### **Hämta enskilda resultat**
```php
$data = $database->get("users", "*", [
    "id" => 1
]);
```
**Förklaring:**  
- Hämtar **första matchande raden** där `id` är 1.  

### **Hämta specifika kolumner**
```php
$data = $database->select("users", ["id", "name", "email"]);
```
**Förklaring:**  
- Hämtar **endast valda kolumner** `id`, `name` och `email`.  

### **WHERE – Villkor**
```php
$data = $database->select("users", "*", [
    "status" => "active"
]);
```
**Förklaring:**  
- Hämtar alla användare där `status` är `"active"`.  

### **Flera WHERE-villkor (AND)**
```php
$data = $database->select("users", "*", [
    "AND" => [
        "status" => "active",
        "age[>]" => 18
    ]
]);
```
**Förklaring:**  
- Hämtar användare där `status` är `"active"` **och** `age` är större än `18`.  

### **OR-villkor**
```php
$data = $database->select("users", "*", [
    "OR" => [
        "role" => "admin",
        "role" => "editor"
    ]
]);
```
**Förklaring:**  
- Hämtar användare där `role` är antingen `"admin"` **eller** `"editor"`.  

### **ORDER BY, SORT, LIMIT & OFFSET**
```php
$data = $database->select("users", "*", [
    "ORDER" => ["created_at" => "DESC"],  // ASC eller DESC
    "LIMIT" => 10,   // Antal poster
    "OFFSET" => 5    // Hoppa över X poster
]);
```
**Förklaring:**  
- Sorterar efter `created_at` i **fallande ordning (`DESC`)**.  
- Begränsar resultatet till **10 rader**.  
- Hoppar över de **5 första raderna**.  

### **Hämta en rad (fetch single row)**
```php
$user = $database->get("users", "*", ["id" => 1]);
```
**Förklaring:**  
- Hämtar **första matchande raden** där `id` är 1.  

---

## **➕ INSERT – Lägg till data**
```php
$database->insert("users", [
    "name" => "John Doe",
    "email" => "john@example.com",
    "status" => "active"
]);
```
**Förklaring:**  
- Infogar en ny användare med namn, e-post och status.  

### **Hämta senaste ID:t på den insatta raden**
```php
$last_id = $database->id();
```
**Förklaring:**  
- Returnerar ID:t på den senast insatta posten.  

---

## **✏️ UPDATE – Uppdatera data**
```php
$database->update("users", [
    "status" => "inactive"
], [
    "id" => 1
]);
```
**Förklaring:**  
- Uppdaterar `status` till `"inactive"` för användaren med `id` = 1.  

---

## **🗑 DELETE – Ta bort data**
```php
$database->delete("users", [
    "id" => 1
]);
```
**Förklaring:**  
- Tar bort användaren där `id` = 1.  

---

## **🔢 COUNT – Räkna rader**
```php
$count = $database->count("users", [
    "status" => "active"
]);
```
**Förklaring:**  
- Räknar hur många användare som har `status` = `"active"`.  

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
**Förklaring:**  
- Hämtar data från `users` och `profiles` där `users.id` matchar `profiles.user_id`.  

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
**Förklaring:**  
- Hämtar alla användare och deras profiler **även om de saknar en profil**.  

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

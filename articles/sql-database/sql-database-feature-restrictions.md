---
title: Omezení funkce databáze SQL Azure | Dokumentace Microsoftu
description: Omezení služby Azure SQL Database funkce zvyšuje zabezpečení databáze a omezit jejich funkce v databázi, kterou lze útočníci získají přístup k informacím v nich.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259449"
---
# <a name="azure-sql-database-feature-restrictions"></a>Omezení služby Azure SQL Database funkce

Běžné příčiny útoky systému SQL Server je prostřednictvím webových aplikací, které přistupují k databázi, kde různé formy útoky prostřednictvím injektáže SQL umožňují glean informací o databázi.  V ideálním případě je kód aplikace vyvinuté tak nepovoluje pro útok prostřednictvím injektáže SQL.  Ale ve velkých-základů kódu, které zahrnují starší verze a externí kód, jeden nikdy jistotu, že všechny případy vyřeší, tak, aby byly realitou, který máme k ochraně proti injektáže SQL.  Cílem funkce omezení je zabránit některé formy útok prostřednictvím injektáže SQL, abyste zabránili úniku informací o databázi, i když je úspěšný útok prostřednictvím injektáže SQL.

## <a name="enabling-feature-restrictions"></a>Povolení omezení funkcí

Povolení omezení funkcí se provádí pomocí `sp_add_feature_restriction` uloženou proceduru následujícím způsobem:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Je možné omezit následující funkce:

| Funkce          | Popis |
|------------------|-------------|
| N'ErrorMessages. | Když s omezením pomocí specifikátoru, se nezobrazí žádná data uživatelů v rámci chybovou zprávu z důvodu. Zobrazit [chybové zprávy obsahují omezení](#error-messages-feature-restriction) |
| N'Waitfor.       | Když s omezením pomocí specifikátoru, příkaz vrátí okamžitě bez jakéhokoli zpoždění. Zobrazit [WAITFOR omezení funkce](#waitfor-feature-restriction) |

Hodnota `object_class` může být buď `N'User'` nebo `N'Role'` k označení, zda `object_name` je uživatelské jméno nebo název Role v databázi.

Následující příklad způsobí, že všechny chybové zprávy pro uživatele `MyUser` k nezobrazí z důvodu:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Zákaz omezení funkcí

Zákaz omezení funkcí se provádí pomocí `sp_drop_feature_restriction` uloženou proceduru následujícím způsobem:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

Následující příklad zakazuje maskování chybová zpráva pro uživatele `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Omezení zobrazení funkcí

`sys.sql_feature_restrictions` Poskytuje všechny omezení aktuálně definovaných funkcí v databázi. Má následující sloupce:

| Název sloupce | Typ dat | Popis |
|-------------|-----------|-------------|
| třída       | nvarchar(128) | Třídu objektu, na kterou se vztahují omezení |
| objekt      | nvarchar(256) | Název objektu, na kterou se vztahují omezení |
| Funkce     | nvarchar(128) | Funkce, která je s omezeným přístupem |

## <a name="feature-restrictions"></a>Omezení funkcí

### <a name="error-messages-feature-restriction"></a>Omezení funkcí chybové zprávy

Jeden běžnou metodou útoku prostřednictvím injektáže SQL se vkládat kód, který způsobí chybu.  Prověřením chybové zprávě útočník Další informace o systému, povolení dalších více cílenými útoky.  Tohoto útoku může být obzvláště užitečný, pokud aplikace nejsou zobrazeny výsledky dotazu, ale zobrazení chybové zprávy.

Vezměte v úvahu webovou aplikaci, která má požadavek v podobě:

```html
http://www.contoso.com/employee.php?id=1
```

Které provádí následující databáze:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Pokud hodnota předaná jako `id` parametr požadavku webové aplikace je zkopírován do nahradit $EmpId v databázového dotazu, útočník by mohl provést požadavek na následující:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

A následující chyba by vrátila, umožňuje útočníkovi získat název databáze:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Po povolení chybové zprávy obsahují omezení pro uživatele aplikace v databázi, je vrácená chybová zpráva maskované tak, aby nevrácení žádné interních informací o databázi:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Podobně útočník by mohl provést požadavek na následující:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

A následující chyba by vrátila, mu další salary zaměstnance:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Pomocí omezení funkce chybové zprávy, by vrátil databáze:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Omezení funkcí WAITFOR

Je skrytá útok prostřednictvím injektáže SQL, když aplikace není má útočník s výsledky vložené SQL nebo s chybovou zprávou, ale útočník může odvodit informace z databáze pomocí podmíněného dotazu, ve kterém dvě podmínkových větví trvat jiný čas ke spuštění. Porovnání doby odezvy, může útočník vědět, jaká větev se provedl a tak další informace o systému. Pomocí nejjednodušší variant útoku `WAITFOR` příkaz zavést zpoždění.

Vezměte v úvahu webovou aplikaci, která má požadavek v podobě:

```html
http://www.contoso.com/employee.php?id=1
```

které provádí následující databáze:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Pokud hodnotu předanou jako parametr id žádosti o webové aplikace je zkopírován do nahradit $EmpId v databázového dotazu, útočník může provést požadavek na následující:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

A dotaz by trvalo dalších 5 sekund, pokud `sa` účtu se používal. Pokud `WAITFOR` omezení funkce zakázaná v databázi, `WAITFOR` příkaz bude ignorována a ne informace je nevrácení pomocí útoku.
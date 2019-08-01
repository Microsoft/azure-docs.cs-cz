---
title: Omezení funkcí Azure SQL Database | Microsoft Docs
description: Azure SQL Database omezení funkcí zlepšuje zabezpečení databáze tím, že omezují funkce v databázi, které můžou útočníci zneužít k získání přístupu k informacím v nich.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: 5f5123624b5b9388baf799b48127b5b796eec21b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568210"
---
# <a name="azure-sql-database-feature-restrictions"></a>Omezení funkcí Azure SQL Database

Jedním z běžných zdrojů SQL Server útoku je prostřednictvím webových aplikací, které přistupují k databázi, ve které se k gleaní informací o databázi používají různé formy útoků prostřednictvím injektáže SQL.  V ideálním případě je vytvořen kód aplikace, aby nepovoloval vkládání SQL.  Nicméně ve velkých základech kódu, které obsahují starší a externí kód, nemůže jedna z nich nikdy zkontrolovat, zda byly vyřešeny všechny případy, takže injektáže SQL jsou faktem životního cyklu, který je nutné chránit před.  Cílem omezení funkcí je zabránit tomu, aby některé formy injektáže SQL neunikly informace o databázi, a to i v případě, že je vkládání SQL úspěšné.

## <a name="enabling-feature-restrictions"></a>Povolování omezení funkcí

Povolení omezení funkcí se provádí pomocí `sp_add_feature_restriction` uložené procedury následujícím způsobem:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Je možné omezit následující funkce:

| Funkce          | Popis |
|------------------|-------------|
| N'ErrorMessages' | V případě omezení se všechna uživatelská data v této chybové zprávě budou maskovat. Zobrazit [omezení funkce chybové zprávy](#error-messages-feature-restriction) |
| N'Waitfor'       | V případě omezení se příkaz vrátí hned bez zpoždění. Viz [omezení funkcí příkazu WAITFOR](#waitfor-feature-restriction) |

Hodnota `object_class` může být buď `N'User'` nebo `N'Role'` k označení, zda `object_name` se jedná o uživatelské jméno nebo název role v databázi.

Následující příklad způsobí, že jsou všechny chybové zprávy pro `MyUser` uživatele maskovány:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Zákaz omezení funkcí

Zakázání omezení funkcí se provádí pomocí `sp_drop_feature_restriction` uložené procedury následujícím způsobem:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

Následující příklad zakáže maskování chybových zpráv pro uživatele `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Zobrazení omezení funkcí

`sys.sql_feature_restrictions` Zobrazení prezentuje všechna aktuálně definovaná omezení funkcí v databázi. Má následující sloupce:

| Název sloupce | Datový typ | Popis |
|-------------|-----------|-------------|
| třída       | nvarchar (128) | Třída objektu, na kterou se vztahuje omezení |
| objekt      | nvarchar(256) | Název objektu, na který se vztahuje omezení |
| funkce     | nvarchar (128) | Funkce, která je omezená |

## <a name="feature-restrictions"></a>Omezení funkcí

### <a name="error-messages-feature-restriction"></a>Omezení funkcí chybových zpráv

Jednou z běžných metod útoku na injektáže SQL je vložení kódu, který způsobí chybu.  Prozkoumáním chybové zprávy může útočník získat informace o systému a tím umožnit další cílené útoky.  Tento útok může být obzvláště užitečný, když aplikace nezobrazuje výsledky dotazu, ale zobrazuje chybové zprávy.

Vezměte v úvahu webovou aplikaci, která má požadavek ve formě:

```html
http://www.contoso.com/employee.php?id=1
```

Který spustí následující databázový dotaz:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Pokud je hodnota předaná jako `id` parametr žádosti webové aplikace zkopírována, aby nahradila $EmpID v databázovém dotazu, mohl by útočník provést následující požadavek:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

A vrátí se následující chyba, která útočníkovi umožní zjistit název databáze:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Po povolení omezení funkce chybové zprávy pro uživatele aplikace v databázi je vrácená chybová zpráva maskována tak, že žádné interní informace o databázi nevrátí:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Podobně by útočník mohl vytvořit následující požadavek:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

A vrátí se následující chyba, která útočníkovi umožní zjistit mzdu zaměstnance:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Při použití omezení funkce chybové zprávy se vrátí databáze:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR – omezení funkcí

Nevidomé injektáže SQL je v případě, že aplikace neposkytuje útočník s výsledky vloženého SQL nebo s chybovou zprávou, ale útočník může odvodit informace z databáze vytvořením podmíněného dotazu, ve kterém jsou dvě podmíněné větve proveďte jinou dobu, než se spustí. Porovnáním doby odezvy může útočník vědět, která větev byla provedena, a tím se dozví informace o systému. Nejjednodušší varianta tohoto útoku `WAITFOR` používá příkaz k zavedení zpoždění.

Vezměte v úvahu webovou aplikaci, která má požadavek ve formě:

```html
http://www.contoso.com/employee.php?id=1
```

který spustí následující databázový dotaz:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Pokud je hodnota předaná jako parametr ID do požadavků webové aplikace zkopírována na hodnotu nahradit $EmpId v databázovém dotazu, útočník může provést následující požadavek:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

A dotaz by v případě `sa` použití účtu trvat dalších 5 sekund. Pokud `WAITFOR` je omezení funkcí v databázi zakázané `WAITFOR` , příkaz se ignoruje a při tomto útoku nedojde k úniku informací.
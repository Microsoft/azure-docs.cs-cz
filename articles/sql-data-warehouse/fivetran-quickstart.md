---
title: Fivetran rychlý start s Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Začněte rychle pomocí Fivetran a Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355405"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Rychlé zahájení práce díky Fivetran a SQL Data Warehouse

V tomto rychlém startu se předpokládá, že už máte existující instanci služby SQL Data Warehouse.

## <a name="setup-connection"></a>Nastavit připojení

1. Najdete plně kvalifikovaný název serveru a název databáze pro připojení k Azure SQL Data Warehouse.

   [Jak najít název serveru a název databáze z portálu?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. V Průvodci instalací rozhodněte, jestli chcete pro připojení databáze přímo nebo prostřednictvím tunelu SSH.

   Pokud se rozhodnete připojit přímo k databázi, je potřeba vytvořit pravidlo brány firewall umožňující přístup. Tato metoda je nejjednodušší a nejbezpečnější metodu.

   Pokud se rozhodnete připojení přes tunelové propojení SSH, Fivetran se připojí na samostatný server ve vaší síti, která poskytuje tunelového propojení SSH k vaší databázi. Tato metoda je nezbytná, pokud je vaše databáze do nedostupný podsítě ve virtuální síti.

3. Přidat "52.0.2.4" IP adresu v bráně firewall úrovni serveru umožňuje příchozí připojení k Azure SQL Data Warehouse z Fivetran.

   [Postup přidání brány firewall na úrovni serveru?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>Nastavení přihlašovacích údajů uživatele

Připojení k Azure SQL Data Warehouse pomocí aplikace SQL Server Management Studio nebo nástroje podle výběru jako uživatel s rolí správce serveru a spusťte následující příkazy SQL pro vytvoření uživatele pro Fivetran:

V hlavní databázi: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

V databázi SQL Data Warehouse:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

Po vytvoření uživatele fivetran jí udělte následující oprávnění do skladu:

```
GRANT CONTROL to fivetran;
```

Přidáte třídu prostředků se vhodné k vytvořenému uživateli, v závislosti na požadavek na paměť vytváření indexu columnstore. Například integrace, jako jsou služby Marketo nebo Salesforce potřebovat vyšší třídě prostředků z důvodu velký počet sloupců nebo větší objem dat, který vyžaduje víc paměti k vytvoření indexů columnstore.

Doporučuje se použití statických tříd prostředků. Můžete začít s Třída prostředků `staticrc20`, který přiděluje 200 MB pro uživatele bez ohledu na úroveň výkonu můžete použít. Pokud indexu columnstore selže s aktuální třídě prostředků, máme zvýšit třídy prostředků.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

Další informace, podívejte se na dokumenty, kde [omezení paměti a souběžnosti](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) a [třídy prostředků](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)

Ovládací PRVEK oprávnění je potřeba k vytvoření přihlašovacích údajů s rozsahem databáze, které se použijí při načítání souborů z úložiště objektů Blob pomocí PolyBase.

Zadejte přihlašovací údaje pro přístup k Azure SQL Data Warehouse

1. Hostitele (název vašeho serveru)
2. Port
3. Databáze
4. Uživatel (uživatelské jméno by měly být `fivetran@<server_name>` kde `<server_name>` je součástí váš identifikátor uri pro azure hostitele: `<server_name>.database.windows.net`)
5. Heslo
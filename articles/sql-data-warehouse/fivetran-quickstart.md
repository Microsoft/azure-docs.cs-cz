---
title: Fivetran quickstart pro Azure SQL Data Warehouse | Dokumentace Microsoftu
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
ms.openlocfilehash: 50f5f813444ddf38d15863d028b1f61bb9b0d55c
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52580523"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Rychlé zahájení práce díky Fivetran a SQL Data Warehouse

Tento rychlý start popisuje způsob nastavení nového uživatele Fivetran pro práci s Azure SQL Data Warehouse. Tento článek předpokládá, že máte existující instanci služby SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Nastavit připojení

1. Najdete plně kvalifikovaný název serveru a název databáze, který použijete k připojení k SQL Data Warehouse.
    
    Pokud potřebujete pomoc, jak najít tyto informace, přečtěte si téma [připojení k Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. V Průvodci instalací vyberte, jestli se má připojit databázi přímo nebo pomocí tunelu SSH.

   Pokud budete chtít připojit přímo k databázi, musíte vytvořit pravidlo brány firewall umožňující přístup. Tato metoda je nejjednodušší a nejbezpečnější metodu.

   Pokud budete chtít připojit pomocí tunelu SSH, Fivetran připojí na samostatný server ve vaší síti. Server poskytuje tunelového propojení SSH k vaší databázi. Pokud je vaše databáze do nedostupný podsítě ve virtuální síti, musíte použít tuto metodu.

3. Přidat IP adresu **52.0.2.4** do vaší brány firewall na úrovni serveru umožňuje příchozí připojení k vaší instanci SQL Data Warehouse z Fivetran.

   Další informace najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Nastavit přihlašovací údaje uživatele

1. Připojení k Azure SQL Data Warehouse pomocí SQL Server Management Studio nebo nástroj, který preferujete. Přihlaste se jako uživatel správce serveru. Potom spusťte následující příkazy SQL pro vytvoření uživatele pro Fivetran:
    - V hlavní databázi: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - V databázi SQL Data Warehouse:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Udělte oprávnění ke svému skladu Fivetran uživatele:

    ```
    GRANT CONTROL to fivetran;
    ```

    Vytvořte přihlašovací údaje s rozsahem databáze, které se používají, když uživatel načte soubory z úložiště objektů Blob v Azure pomocí PolyBase je potřeba oprávnění ovládacího PRVKU.

3. Přidáte třídu prostředků se vhodné Fivetran uživateli. Třída prostředků, které používáte, závisí na paměti, které je nutné vytvořit columnstore index. Například integrace s produkty, jako jsou služby Marketo nebo Salesforce vyžadují vyšší třídě prostředků z důvodu velkého počtu sloupců a větší objem dat produkty používat. Vyšší třídě prostředků vyžaduje víc paměti k vytvoření indexů columnstore.

    Doporučujeme použít statických tříd prostředků. Můžete začít s `staticrc20` třídy prostředků. `staticrc20` Třída prostředků se přiděluje 200 MB pro každého uživatele, bez ohledu na úroveň výkonu použít. Pokud indexu columnstore selže na úrovni třídy původní zdroj, zvýšit třídy prostředků.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Další informace, přečtěte si informace o [omezení paměti a souběžnosti](memory-and-concurrency-limits.md) a [třídy prostředků](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Přihlaste se k Fivetran

Pro přihlášení k Fivetran, zadejte přihlašovací údaje, které používáte pro přístup k SQL Data Warehouse: 

* Hostitele (název vašeho serveru).
* Port.
* Databáze.
* Uživatel (uživatelské jméno by měly být **fivetran @_název_serveru_**  kde *název_serveru* je součástí identifikátor URI pro Azure hostitele: ***server_name*.database.windows .net** ).
* Heslo.

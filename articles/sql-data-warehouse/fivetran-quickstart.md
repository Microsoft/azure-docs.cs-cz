---
title: Rychlý Start Fivetran
description: Fivetran a Azure SQL Data Warehouse vám umožní rychle začít.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 250df3e106ae65cafc84a412c155e3a27c535c79
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686118"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Rychlé zahájení práce s Fivetran a SQL Data Warehouse

V tomto rychlém startu se dozvíte, jak nastavit nového uživatele Fivetran pro práci s Azure SQL Data Warehouse. Článek předpokládá, že máte existující instanci SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Nastavení připojení

1. Vyhledejte plně kvalifikovaný název serveru a databázi, který používáte k připojení k SQL Data Warehouse.
    
    Pokud potřebujete podporu najít tyto informace, přečtěte si téma [připojení k Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. V Průvodci instalací vyberte, zda chcete připojit databázi přímo nebo pomocí tunelu SSH.

   Pokud se rozhodnete připojit přímo k databázi, je nutné vytvořit pravidlo brány firewall pro povolení přístupu. Tato metoda je nejjednodušší a nejbezpečnější metodou.

   Pokud se rozhodnete připojit pomocí tunelu SSH, Fivetran se připojí k samostatnému serveru v síti. Server poskytuje tunel SSH pro vaši databázi. Tuto metodu je nutné použít, pokud je databáze v nepřístupné podsíti ve virtuální síti.

3. Přidejte IP adresu **52.0.2.4** do brány firewall na úrovni serveru, abyste umožnili příchozí připojení k vaší SQL Data Warehouse instanci z Fivetran.

   Další informace najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Nastavení přihlašovacích údajů uživatele

1. Připojte se k Azure SQL Data Warehouse pomocí SQL Server Management Studio nebo nástroje, kterému dáváte přednost. Přihlaste se jako uživatel s oprávněními správce serveru. Pak spusťte následující příkazy SQL pro vytvoření uživatele pro Fivetran:
    - V hlavní databázi: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - V SQL Data Warehouse databáze:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Udělte uživateli Fivetran následující oprávnění k vašemu skladu:

    ```
    GRANT CONTROL to fivetran;
    ```

    Aby bylo možné vytvořit přihlašovací údaje v databázi, které se používají, když uživatel načte soubory ze služby Azure Blob Storage pomocí základu, je nutné mít oprávnění k řízení.

3. Přidejte do uživatele Fivetran vhodnou třídu prostředků. Použitá Třída prostředků závisí na paměti, která je potřebná k vytvoření indexu columnstore. Například integrace s produkty, jako je Marketo a Salesforce, vyžadují vyšší třídu prostředků z důvodu velkého počtu sloupců a většího objemu dat, která používají produkty. Vyšší Třída prostředků vyžaduje více paměti pro vytváření indexů columnstore.

    Doporučujeme použít statické třídy prostředků. Můžete začít s `staticrc20` třídy prostředků. Třída prostředků `staticrc20` přiděluje každému uživateli 200 MB, bez ohledu na úroveň výkonu, kterou používáte. Pokud indexování columnstore na počáteční úrovni třídy prostředků selhává, zvyšte třídu prostředků.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Další informace najdete v tématu o [omezeních paměti a souběžnosti] paměť-Concurrency-limits.md) a [třídách prostředků](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Přihlásit se k Fivetran

Pokud se chcete přihlásit k Fivetran, zadejte přihlašovací údaje, které používáte pro přístup k SQL Data Warehouse: 

* Hostitel (název vašeho serveru).
* Přístavní.
* Databáze.
* Uživatel (uživatelské jméno by mělo být **fivetran\@_název_serveru_**  , kde *název_serveru* je součástí identifikátoru URI hostitele Azure: ***název_serveru *. Database. Windows. NET**).
* Zadáno.

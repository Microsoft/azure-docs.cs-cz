---
title: 'Úvodní příručka: Fivetran a datový sklad'
description: Začínáme s datovým skladem Fivetran a Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b4742f48ee9ad0db60e21dd53c5c0f447c1ded67
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348925"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Úvodní příručka: Fivetran s datovým skladem 

Tento rychlý start popisuje, jak nastavit nového uživatele Fivetran pro práci s datovým skladem Azure Synapse Analytics zřízeným pomocí fondu SQL. Článek předpokládá, že máte existující datový sklad.

## <a name="set-up-a-connection"></a>Nastavení připojení

1. Najděte úplný název serveru a název databáze, který používáte pro připojení k datovému skladu.
    
    Pokud potřebujete pomoc s vyhledáním těchto informací, přečtěte si téma [Připojení k datovému skladu](sql-data-warehouse-connect-overview.md).

2. V průvodci nastavením zvolte, zda chcete databázi připojit přímo nebo pomocí tunelového propojení SSH.

   Pokud se rozhodnete připojit přímo k databázi, musíte vytvořit pravidlo brány firewall, které umožní přístup. Tato metoda je nejjednodušší a nejbezpečnější metoda.

   Pokud se rozhodnete připojit pomocí tunelového propojení SSH, Fivetran se připojí k samostatnému serveru v síti. Server poskytuje tunelové propojení SSH do databáze. Tuto metodu je nutné použít, pokud je databáze v nepřístupné podsíti ve virtuální síti.

3. Přidejte ip adresu **52.0.2.4** do brány firewall na úrovni serveru, abyste povolili příchozí připojení k instanci datového skladu z Fivetranu.

   Další informace naleznete [v tématu Vytvoření pravidla brány firewall na úrovni serveru](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Nastavení pověření uživatele

1. Připojte se k datovému skladu pomocí SQL Server Management Studio (SSMS) nebo nástroj, který dáváte přednost. Přihlaste se jako uživatel správce serveru. Potom spusťte následující příkazy SQL a vytvořte uživatele pro Fivetran:

    - V hlavní databázi: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - V databázi datového skladu:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Udělte uživateli Fivetran udělit vašemu datovému skladu následující oprávnění:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    Oprávnění CONTROL je vyžadováno k vytvoření pověření s rozsahem databáze, která se používají, když uživatel načte soubory z úložiště objektů Blob Azure pomocí PolyBase.

3. Přidejte vhodné třídy prostředků pro uživatele Fivetran. Třída prostředků, kterou používáte, závisí na paměti, která je vyžadována k vytvoření indexu columnstore. Například integrace s produkty, jako je Marketo a Salesforce vyžadují vyšší třídu prostředků z důvodu velkého počtu sloupců a většíobjem dat, které produkty používají. Vyšší třída prostředků vyžaduje více paměti k vytvoření indexů columnstore.

    Doporučujeme použít statické třídy prostředků. Můžete začít s `staticrc20` třídou prostředků. Třída `staticrc20` prostředků přiděluje 200 MB pro každého uživatele bez ohledu na úroveň výkonu, kterou používáte. Pokud columnstore indexování selže na počáteční úrovni třídy prostředků, zvýšit třídu prostředků.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Další informace naleznete o [omezení paměti a souběžnosti](memory-concurrency-limits.md) a [třídách prostředků](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Přihlášení k Fivetran

Pokud se chcete přihlásit k Fivetranu, zadejte přihlašovací údaje, které používáte pro přístup k datovému skladu: 

* Host (název serveru).
* Port.
* Databáze.
* Uživatel (uživatelské jméno by mělo být **\@fivetran server_name** kde *server_name* je součástí identifikátoru URI hostitele Azure: ** _název serveru\__.database.windows.net**).
* Heslo.

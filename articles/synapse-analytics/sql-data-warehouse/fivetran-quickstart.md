---
title: 'Rychlý Start: Fivetran a datový sklad'
description: Začínáme s Fivetran a datovým skladem Azure synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e679c0b284cc649dbde3fba1b640f4e09df05e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201646"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Rychlý Start: Fivetran s datovým skladem 

V tomto rychlém startu se dozvíte, jak nastavit nového uživatele Fivetran pro práci s datovým skladem Azure synapse Analytics zřízeném s fondem SQL. Článek předpokládá, že máte existující datový sklad.

## <a name="set-up-a-connection"></a>Nastavení připojení

1. Vyhledejte plně kvalifikovaný název serveru a databázi, který používáte pro připojení k datovému skladu.
    
    Pokud potřebujete podporu najít tyto informace, podívejte se [na téma připojení k datovému skladu](../sql/connect-overview.md).

2. V Průvodci instalací vyberte, zda chcete připojit databázi přímo nebo pomocí tunelu SSH.

   Pokud se rozhodnete připojit přímo k databázi, je nutné vytvořit pravidlo brány firewall pro povolení přístupu. Tato metoda je nejjednodušší a nejbezpečnější metodou.

   Pokud se rozhodnete připojit pomocí tunelu SSH, Fivetran se připojí k samostatnému serveru v síti. Server poskytuje tunel SSH pro vaši databázi. Tuto metodu je nutné použít, pokud je databáze v nepřístupné podsíti ve virtuální síti.

3. Přidejte IP adresu **52.0.2.4** do brány firewall na úrovni serveru, abyste umožnili příchozí připojení k instanci datového skladu z Fivetran.

   Další informace najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Nastavení přihlašovacích údajů uživatele

1. Připojte se k datovému skladu pomocí SQL Server Management Studio (SSMS) nebo nástroje, kterému dáváte přednost. Přihlaste se jako uživatel s oprávněními správce serveru. Pak spusťte následující příkazy SQL pro vytvoření uživatele pro Fivetran:

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

2. Udělte uživateli Fivetran následující oprávnění k vašemu datovému skladu:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    Aby bylo možné vytvořit přihlašovací údaje v databázi, které se používají, když uživatel načte soubory ze služby Azure Blob Storage pomocí základu, je nutné mít oprávnění k řízení.

3. Přidejte do uživatele Fivetran vhodnou třídu prostředků. Použitá Třída prostředků závisí na paměti, která je potřebná k vytvoření indexu columnstore. Například integrace s produkty, jako je Marketo a Salesforce, vyžadují vyšší třídu prostředků z důvodu velkého počtu sloupců a většího objemu dat, která používají produkty. Vyšší Třída prostředků vyžaduje více paměti pro vytváření indexů columnstore.

    Doporučujeme použít statické třídy prostředků. Můžete začít s `staticrc20` třídou prostředků. `staticrc20`Třída prostředků přiděluje každému uživateli 200 MB bez ohledu na úroveň výkonu, kterou používáte. Pokud indexování columnstore na počáteční úrovni třídy prostředků selhává, zvyšte třídu prostředků.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Další informace najdete v článku o [omezeních paměti a souběžnosti](memory-concurrency-limits.md) a [třídách prostředků](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="connect-from-fivetran"></a>Připojení z Fivetran

Pokud se chcete připojit k datovému skladu z účtu Fivetran, zadejte přihlašovací údaje, které používáte pro přístup k datovému skladu: 

* Hostitel (název vašeho serveru).
* Přístavní.
* Databáze.
* Uživatel (uživatelské jméno by mělo být **fivetran \@ _server_name_ ** , kde *server_name* je součástí identifikátoru URI hostitele Azure: ** _ \_ název serveru_. Database.Windows.NET**).
* Heslo.

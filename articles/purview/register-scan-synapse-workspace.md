---
title: Jak kontrolovat pracovní prostory Azure synapse
description: Naučte se kontrolovat pracovní prostor synapse ve službě Azure dosah Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031383"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Registrace a kontrola pracovních prostorů Azure synapse

Tento článek popisuje, jak v dosah zaregistrovat pracovní prostor Azure synapse a jak na něm nastavovat kontrolu.

## <a name="supported-capabilities"></a>Podporované funkce

Azure synapse Workspace kontroluje podporu zachytávání metadat a schématu pro vyhrazené databáze SQL bez serveru. Také klasifikuje data automaticky podle systémových a vlastních pravidel klasifikace.

## <a name="prerequisites"></a>Požadavky

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Musíte být správcem zdroje dat služby Azure dosah.
- Nastavení ověřování, jak je popsáno v následujících částech

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Nastavení ověřování pro vytváření výčtu vyhrazených prostředků SQL Database v pracovním prostoru synapse

1. Přejděte do **skupiny prostředků** nebo **předplatného** , ve kterém je pracovní prostor synapse, ve Azure Portal.  
1. V navigační nabídce vlevo vyberte **Access Control (IAM)**.   
1. Abyste mohli přidat roli do skupiny prostředků nebo předplatného, musíte být vlastníkem nebo správcem uživatelského přístupu. Tlačítko vybrat *+ Přidat* . 
1. Nastavte roli **Čtenář** a v části Vybrat vstupní pole zadejte název vašeho účtu Azure dosah (který představuje jeho MSI). Kliknutím na *Uložit* dokončete přiřazení role.
1. Postupujte podle kroků 2 až 4 výše a přidejte taky roli **čtečky dat objektů BLOB úložiště** pro Azure dosah MSI ve skupině prostředků nebo předplatném, ve kterém je pracovní prostor synapse.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Nastavení ověřování pro vytváření výčtu prostředků SQL Database bez serveru v pracovním prostoru synapse

> [!NOTE]
> Aby bylo možné spustit tyto příkazy, musíte být **správcem synapse** v pracovním prostoru. Další informace o oprávněních synapse [najdete tady](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Přejít k pracovnímu prostoru synapse
1. Přejděte do části **data** a do jedné z databází SQL bez serveru.
1. Klikněte na ikonu se třemi tečkami a spusťte nový skript SQL.
1. Pomocí příkazu uvedeného níže v SQL skriptu **přidejte do databáze** SQL serveru (reprezentovaného názvem účtu) službu MSI dosah účtu Azure.
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Nastavení ověřování pro skenování prostředků v pracovním prostoru synapse

Existují tři způsoby, jak nastavit ověřování pro zdroj synapse Azure:

- Spravovaná identita
- Instanční objekt
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Použití spravované identity pro vyhrazené databáze SQL

1. Přejít k **pracovnímu prostoru synapse**
1. Přejděte do části **data** a do jedné z databází SQL bez serveru.
1. Klikněte na ikonu se třemi tečkami a spusťte nový skript SQL.
1. Pomocí příkazu níže v dosah skriptu přidejte do vyhrazené databáze SQL účet služby MSI (reprezentovaný názvem účtu) jako **db_owner** .

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Použití spravované identity pro databáze SQL bez serveru

1. Přejít k **pracovnímu prostoru synapse**
1. Přejděte do části **data** a do jedné z databází SQL bez serveru.
1. Klikněte na ikonu se třemi tečkami a spusťte nový skript SQL.
1. Pomocí příkazu uvedeného níže v SQL skriptu **přidejte do databáze** SQL serveru (reprezentovaného názvem účtu) službu MSI dosah účtu Azure.
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Použití instančního objektu pro vyhrazené databáze SQL

> [!NOTE]
> Nejdřív musíte nastavit nové **přihlašovací údaje** typu instančního objektu podle pokynů uvedených [tady](manage-credentials.md).

1. Přejít k **pracovnímu prostoru synapse**
1. Přejděte do části **data** a do jedné z databází SQL bez serveru.
1. Klikněte na ikonu se třemi tečkami a spusťte nový skript SQL.
1. Přidejte **ID instančního objektu** jako **db_owner** do vyhrazené databáze SQL spuštěním příkazu níže v rámci skriptu SQL:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Použití instančního objektu pro databáze SQL bez serveru

1. Přejít k **pracovnímu prostoru synapse**
1. Přejděte do části **data** a do jedné z databází SQL bez serveru.
1. Klikněte na ikonu se třemi tečkami a spusťte nový skript SQL.
1. Pomocí příkazu uvedeného níže v SQL skriptu **přidejte do databáze** SQL serveru (reprezentovaného názvem účtu) službu MSI dosah účtu Azure.
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> Je nutné nastavit ověřování pro každou vyhrazenou databázi SQL v pracovním prostoru synapse, kterou máte v úmyslu registrovat a kontrolovat. Výše uvedená oprávnění pro databáze SQL bez serveru se vztahují na všechny z nich v pracovním prostoru, tj. budete ji muset spustit jenom jednou.
    
## <a name="register-an-azure-synapse-source"></a>Registrace zdroje Azure synapse

Pokud chcete zaregistrovat nový zdroj Azure synapse v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.
1. Výběr **zdrojů** na levém navigačním panelu
1. Vybrat **registraci**
1. V **seznamu registrovat zdroje** vyberte **Azure synapse Analytics (více)** .
1. Vyberte **Pokračovat**.

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Nastavení zdroje Azure synapse":::

Na obrazovce **Registrovat zdroje (Azure synapse Analytics)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
1. Volitelně můžete zvolit **předplatné** , ke kterému se má filtrovat.
1. Z rozevíracího seznamu **Vyberte název pracovního prostoru synapse** . Koncové body SQL se automaticky vyplní na základě vašeho výběru pracovního prostoru. 
1. Vyberte **kolekci** nebo vytvořte novou (volitelné).
1. **Dokončit** pro registraci zdroje dat

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Vyplnit podrobnosti pro Azure synapse source":::

## <a name="creating-and-running-a-scan"></a>Vytvoření a spuštění kontroly

Pokud chcete vytvořit a spustit novou kontrolu, udělejte toto:

1. Přejděte do části **sources (zdroje** ).

1. Vyberte zdroj dat, který jste zaregistrovali.

1. Klikněte na Zobrazit podrobnosti a vyberte **+ Nová kontrola** nebo použijte ikonu prohledat rychlou akci na dlaždici zdroje.

1. Zadejte *název* a vyberte všechny typy prostředků, které chcete kontrolovat v rámci tohoto zdroje. **SQL Database** je jediným typem, který podporujeme v současnosti v rámci pracovního prostoru synapse.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Kontrola zdrojů Azure synapse":::

1. Vyberte **přihlašovací údaje** pro připojení k prostředkům v rámci zdroje dat. 
  
1. V rámci každého typu můžete vybrat buď kontrolu všech prostředků, nebo jejich podmnožinu podle názvu.
1.  Pokračujte kliknutím na **pokračovat** . 

1.  Vyberte **sady pravidel skenování** typu Azure synapse SQL. Můžete také vytvořit sady pravidel skenování inline.

1. Vyberte aktivační událost kontroly. Můžete naplánovat, aby se spouštěla **týdně** nebo  měsíčně.

1. Zkontrolujte prověřování a vyberte Uložit a dokončete nastavení.   

## <a name="viewing-your-scans-and-scan-runs"></a>Zobrazení kontrol a spuštění kontroly

1. Zobrazte podrobnosti o zdroji kliknutím na tlačítko **Zobrazit podrobnosti** na dlaždici v části zdroje. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Podrobnosti o zdroji synapse Azure"::: 

1. Podívejte se na stránku s **podrobnostmi o prověřování** a Prohlédněte si podrobnosti o kontrole spuštění.
    1. *Stavový řádek* je stručný souhrn stavu podřízených prostředků, které jsou v provozu. Zobrazí se na úrovni prohledávání pracovního prostoru.
    1. Zelená znamená úspěšné, zatímco červené prostředky selhaly. Šedá znamená, že kontrola stále probíhá.
    1. Kliknutím na každou kontrolu můžete zobrazit podrobněji odstupňované podrobnosti.

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Podrobnosti o prověřování Azure synapse" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Zobrazí shrnutí posledních neúspěšných kontrol, které se spouští v dolní části stránky podrobností zdroje. Můžete také kliknout na zobrazit podrobnější informace týkající se těchto spuštění.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Správa kontrol – úpravy, odstranění nebo zrušení
Pokud chcete kontrolu spravovat nebo odstranit, postupujte takto:

- Přejděte do centra pro správu. V části zdroje a skenování vyberte zdroje dat a potom vyberte požadovaný zdroj dat.

- Vyberte kontrolu, kterou chcete spravovat. Kontrolu můžete upravit výběrem možnosti upravit.

- Kontrolu můžete odstranit výběrem možnosti odstranit.
- Pokud je kontrola spuštěná, můžete ji také zrušit.

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)   
---
title: Povolení runtime integrace AAD pro Azure SSIS
description: Tento článek popisuje, jak povolit ověřování Azure Active Directory se spravovanou identitou pro Azure Data Factory k vytvoření runtime integrace Azure-SSIS.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 70367a38fbf7b59486e2eaaf6c05634aa7575869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260706"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Povolení ověřování Azure Active Directory pro Azure-SSIS Integration Runtime

Tento článek ukazuje, jak povolit ověřování azure active directory (Azure AD) se spravovanou identitou pro vaši Azure Data Factory (ADF) a použít ji místo konvenčních metod ověřování (jako je ověřování SQL) k:

- Vytvořte runtime integrace Azure-SSIS (IR), který zase zřídí databázi katalogu SSIS (SSISDB) na serveru Azure SQL Database/spravované instanci vaším jménem.

- Připojte se k různým prostředkům Azure při spouštění balíčků SSIS na Azure-SSIS IR.

Další informace o spravované identitě pro váš podavač Služby ADF najdete v [tématu Správa identity pro data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  V tomto scénáři ověřování Azure AD se spravovanou identitou pro váš Podavač ADF se používá pouze při vytváření a následné spuštění operace vašeho SSIS Ir, který bude zase zřízení a připojení k SSISDB. Pro spuštění balíčku SSIS se vaše služba SSIS IR bude stále připojovat k SSISDB pomocí ověřování SQL s plně spravovanými účty, které jsou vytvořeny během zřizování SSISDB.
>-  Pokud jste už vytvořili Službu SSIS IR pomocí ověřování SQL, nemůžete ho v tuto chvíli překonfigurovat tak, aby používal ověřování Azure AD přes Prostředí PowerShell, ale můžete tak učinit prostřednictvím aplikace Azure Portal/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Povolení Azure AD v Azure SQL Database

Server Azure SQL Database podporuje vytváření databáze s uživatelem Azure AD. Nejprve je potřeba vytvořit skupinu Azure AD se spravovanou identitou pro váš Podavač ADF jako člen. Dále je potřeba nastavit uživatele Azure AD jako správce služby Active Directory pro váš server Azure SQL Database a pak se k němu připojit ve službě SQL Server Management Studio (SSMS) pomocí tohoto uživatele. Nakonec je potřeba vytvořit obsaženého uživatele představujícího skupinu Azure AD, aby spravovanou identitu pro váš ADF mohl použít Azure-SSIS IR k vytvoření SSISDB vaším jménem.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Vytvoření skupiny Azure AD se spravovanou identitou pro váš Podavač ADF jako člena

Můžete použít existující skupinu Azure AD nebo vytvořit novou pomocí Azure AD PowerShell.

1.  Nainstalujte modul [Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

2.  Přihlaste `Connect-AzureAD`se pomocí , spusťte následující rutinu a vytvořte skupinu a uložte ji do proměnné:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Výsledek vypadá jako následující příklad, který také zobrazuje hodnotu proměnné:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Přidejte do skupiny spravovanou identitu pro podavač ADF. Můžete sledovat článek [Spravované identiy pro Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) získat hlavní ID spravovaného objektu identity (například 765ad4ab-XXXX-XXXX-51ed985819dc, ale nepoužívejte ID spravované identity aplikace pro tento účel).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Členství ve skupině můžete také zkontrolovat později.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurace ověřování Azure AD pro databázový server Azure SQL

Ověřování [Azure AD pomocí SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) můžete konfigurovat a spravovat pomocí následujících kroků:

1.  Na webu Azure Portal vyberte všechny **služby** -> **SQL servery** z levé navigace.

2.  Vyberte svůj server Azure SQL Database, který chcete nakonfigurovat pomocí ověřování Azure AD.

3.  V části **Nastavení** v okně vyberte **položku Správce služby Active Directory**.

4.  Na panelu příkazů vyberte **Nastavit správce**.

5.  Vyberte uživatelský účet Azure AD, který se má vytvořit jako správce serveru, a pak vyberte **Vybrat.**

6.  Na panelu příkazů vyberte **Uložit.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Vytvoření obsaženého uživatele na serveru Azure SQL Database představujícískupinu Azure AD

Pro tento další krok potřebujete [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Spusťte SSMS.

2. V dialogovém okně **Připojit k serveru** zadejte název serveru Azure SQL Database do pole Název **serveru.**

3. V poli **Ověřování** vyberte **službu Active Directory – univerzální s podporou vícefaktorové ověřování** (můžete také použít další dva typy ověřování služby Active Directory, přečtěte si část [Konfigurace a správa ověřování Azure AD pomocí SQL).](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

4. Do pole **Uživatelské jméno** zadejte název účtu Azure AD, který jste nastavili jako správce serveru, například testuser@xxxonline.com.

5. vyberte **Připojit** a dokončete proces přihlášení.

6. V **Průzkumníkovi objektů**rozbalte složku **Databáze** -> **systémových databází.**

7. Klepněte pravým tlačítkem myši na **hlavní** databázi a vyberte **nový dotaz**.

8. V okně dotazu zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit.**

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Příkaz by měl být úspěšně dokončen a vytvořit obsaženého uživatele, který bude představovat skupinu.

9. Vymažte okno dotazu, zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit.**

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Příkaz by měl být úspěšně dokončen a udělit obsaženému uživateli možnost vytvořit databázi (SSISDB).

10. Pokud byl váš SSISDB vytvořen pomocí ověřování SQL a chcete přepnout na použití ověřování Azure AD pro azure-SSIS IR pro přístup k němu, klikněte pravým tlačítkem myši na databázi **SSISDB** a vyberte **nový dotaz**.

11. V okně dotazu zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit.**

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Příkaz by měl být úspěšně dokončen a vytvořit obsaženého uživatele, který bude představovat skupinu.

12. Vymažte okno dotazu, zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit.**

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Příkaz by měl být úspěšně dokončen a udělit obsaženému uživateli možnost přístupu k SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Povolení Azure AD ve spravované instanci Azure SQL Database

Správa databáze Azure SQL Instance podporuje vytváření databáze se spravovanou identitou pro váš ADF přímo. Nemusíte se připojit ke spravované identitě pro váš Podavač ADF do skupiny Azure AD ani vytvořit obsaženého uživatele představujícího tuto skupinu ve spravované instanci.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurace ověřování Azure AD pro spravovanou instanci Azure SQL Database

Postupujte podle pokynů v [části Zřízení správce služby Azure Active Directory pro spravovanou instanci](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Přidání spravované identity pro váš Podavač ADF jako uživatele v azure SQL database spravované instanci

Pro tento další krok potřebujete [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Spusťte SSMS.

2.  Připojte se ke spravované instanci pomocí účtu SQL Serveru, který je **správcem**. Toto je dočasné omezení, které se odebere, jakmile se objekty serveru Azure AD (přihlášení) pro spravanou instanci Azure SQL Database stane ga. Zobrazí se následující chyba, pokud se pokusíte použít účet správce Azure AD k vytvoření přihlášení: Msg 15247, Úroveň 16, Stav 1, Řádek 1 Uživatel nemá oprávnění k provedení této akce.

3.  V **Průzkumníkovi objektů**rozbalte složku **Databáze** -> **systémových databází.**

4.  Klepněte pravým tlačítkem myši na **hlavní** databázi a vyberte **nový dotaz**.

5.  V okně dotazu spusťte následující skript T-SQL a přidejte spravovanou identitu pro adf jako uživatele

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Příkaz by měl být úspěšně dokončen a udělit spravované identitě pro váš ADF možnost vytvořit databázi (SSISDB).

6.  Pokud byl váš SSISDB vytvořen pomocí ověřování SQL a chcete přepnout na použití ověřování Azure AD pro azure-SSIS IR pro přístup k němu, klikněte pravým tlačítkem myši na databázi **SSISDB** a vyberte **nový dotaz**.

7.  V okně dotazu zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit.**

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Příkaz by měl být úspěšně dokončen a udělit spravované identitě pro váš ADF možnost přístupu k SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Zřízení infračerveného systému Azure-SSIS v aplikaci Azure Portal/ADF

Když zřídíte infračervený přenos Azure-SSIS v aplikaci Azure Portal/ADF, na stránce **Nastavení SQL** vyberte Použít **ověřování AAD se spravovanou identitou pro možnost ADF.** Následující snímek obrazovky ukazuje nastavení pro infračervený přenos s Serverazure SQL Database hosting SSISDB. Pro infračervený přenos se spravovanou instanci hostující SSISDB, **vrstvy databázové služby katalogu** a **povolit služby Azure přístup k** nastavení nejsou použitelné, zatímco ostatní nastavení jsou stejné.

Další informace o tom, jak vytvořit infračervený přenos Azure-SSIS, najdete [v tématu Vytvoření runtime integrace Azure-SSIS v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Nastavení pro runtime integrace Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Zřízení infračerveného počítače Azure-SSIS pomocí prostředí PowerShell

Pokud chcete zřídit infračervený přenos Azure-SSIS pomocí PowerShellu, postupujte takto:

1.  Nainstalujte modul [Azure PowerShell.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 

2.  Ve skriptu nenastavujte `CatalogAdminCredential` parametr. Například:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Spuštění balíčků SSIS se spravovaným ověřováním identity

Když spustíte balíčky SSIS v zařízení Azure-SSIS IR, můžete se k připojení k různým prostředkům Azure použít spravované ověřování identity. V současné době jsme již podpořili ověřování spravovaných identit v následujících správcích připojení.

- [Správce připojení technologie OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Správce připojení](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Správce připojení k úložišti Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)

---
title: Povolení AAD pro Azure SSIS Integration Runtime
description: Tento článek popisuje, jak povolit Azure Active Directory ověřování pomocí spravované identity pro Azure Data Factory k vytvoření Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: cd3f590e1869b28f0ac08ce98da32a98160e4e86
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392727"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Povolení ověřování Azure Active Directory pro Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku se dozvíte, jak povolit ověřování Azure Active Directory (Azure AD) se spravovanou identitou pro váš Azure Data Factory (ADF) a použít místo běžných metod ověřování (jako je ověřování SQL) pro:

- Vytvořte Azure-SSIS Integration Runtime (IR), která umožní zřídit databázi katalogu SSIS (SSISDB) v SQL Database nebo spravované instanci SQL vaším jménem.

- Připojte se k různým prostředkům Azure při spouštění balíčků SSIS na Azure-SSIS IR.

Další informace o spravované identitě pro ADF najdete v tématu [spravovaná identita pro Data Factory](./data-factory-service-identity.md).

> [!NOTE]
>
> - V tomto scénáři se ověřování Azure AD se spravovanou identitou pro váš ADF používá jenom při vytváření a dalších počátečních operacích v SSIS IR, které se pak budou moct zřídit a připojit k SSISDB. V případě spouštění balíčků SSIS se vaše SSIS IR pořád připojí k SSISDB pomocí ověřování SQL s plně spravovanými účty, které se vytvoří během zřizování SSISDB.
> - Pokud jste už SSIS IR vytvořili pomocí ověřování SQL, nemůžete ho v tuto chvíli znovu nakonfigurovat tak, aby používalo ověřování Azure AD prostřednictvím PowerShellu, ale můžete to udělat prostřednictvím aplikace Azure Portal/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Povolit Azure AD v Azure SQL Database

SQL Database podporuje vytváření databází pomocí uživatele Azure AD. Nejdřív je potřeba vytvořit skupinu Azure AD se spravovanou identitou pro váš ADF jako člen. Dál je potřeba nastavit uživatele Azure AD jako správce služby Active Directory pro vaši SQL Database a pak se k němu připojit na SQL Server Management Studio (SSMS) pomocí tohoto uživatele. Nakonec je potřeba vytvořit obsaženého uživatele reprezentujícího skupinu Azure AD, takže spravovanou identitu pro ADF můžete použít Azure-SSIS IR k vytvoření SSISDB vaším jménem.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Vytvoření skupiny Azure AD se spravovanou identitou pro ADF jako členem

Můžete použít existující skupinu Azure AD nebo vytvořit novou pomocí Azure AD PowerShellu.

1.  Nainstalujte modul [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) .

2.  Přihlaste `Connect-AzureAD` se pomocí, spuštěním následující rutiny vytvořte skupinu a uložte ji do proměnné:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Výsledek vypadá jako v následujícím příkladu, který také zobrazuje hodnotu proměnné:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Přidejte do skupiny spravovanou identitu svého ADF. Můžete postupovat podle článku [spravovaná identita pro Data Factory](./data-factory-service-identity.md) získat ID objektu spravované identity (např. 765AD4AB-XXXX-XXXX-XXXX-51ed985819dc, ale nepoužívat ID aplikace spravované identity pro tento účel).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Členství ve skupině můžete také ověřit později.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>Konfigurace ověřování Azure AD pro SQL Database

[Ověřování Azure AD pomocí SQL můžete nakonfigurovat a spravovat](../azure-sql/database/authentication-aad-configure.md) pomocí následujících kroků:

1.  V Azure Portal v levém navigačním panelu vyberte **všechny služby**  ->  **SQL servery** .

2.  Vyberte server v SQL Database, který se má nakonfigurovat s ověřováním Azure AD.

3.  V části **Nastavení** v okně vyberte **Správce služby Active Directory**.

4.  Na panelu příkazů vyberte **nastavit správce**.

5.  Vyberte uživatelský účet Azure AD, který chcete nastavit jako správce serveru, a pak vyberte **Vybrat.**

6.  Na panelu příkazů vyberte **Uložit.**

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>Vytvoření obsaženého uživatele v SQL Database reprezentujícím skupinu Azure AD

Pro tento další krok potřebujete [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Spusťte SSMS.

2. V dialogovém okně **připojit k serveru** zadejte do pole **název serveru** název svého serveru.

3. V poli **ověřování** vyberte **Active Directory – univerzální s podporou vícefaktorového** ověřování (můžete také použít další dva typy ověřování služby Active Directory. Další informace najdete v tématu [Konfigurace a Správa ověřování Azure AD pomocí SQL](../azure-sql/database/authentication-aad-configure.md)).

4. Do pole **uživatelské jméno** zadejte název účtu Azure AD, který jste nastavili jako správce serveru, třeba testuser@xxxonline.com .

5. Vyberte **připojit** a dokončete proces přihlašování.

6. V **Průzkumník objektů** rozbalte složku **databáze**  ->  **systémových databází** .

7. Klikněte pravým tlačítkem na **Hlavní** databázi a vyberte **Nový dotaz**.

8. V okně dotazu zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit** .

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Příkaz by měl být úspěšně dokončen, vytvořením obsaženého uživatele, který bude představovat skupinu.

9. Zrušte zaškrtnutí okna dotazu, zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit** .

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Příkaz by měl úspěšně doplňovat a udělit tak uživateli možnost vytvořit databázi (SSISDB).

10. Pokud byl váš SSISDB vytvořen pomocí ověřování SQL a chcete pro přístup k tomuto Azure-SSIS IR použít ověřování Azure AD, nejprve se ujistěte, že kroky pro udělení oprávnění k **Hlavní** databázi byly úspěšně dokončeny. Pak klikněte pravým tlačítkem na databázi **SSISDB** a vyberte **Nový dotaz**.

11. V okně dotazu zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit** .

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Příkaz by měl být úspěšně dokončen, vytvořením obsaženého uživatele, který bude představovat skupinu.

12. Zrušte zaškrtnutí okna dotazu, zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit** .

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Příkaz by měl úspěšně doplňovat a udělit tak uživateli možnost přístupu k SSISDB.

## <a name="enable-azure-ad-on-sql-managed-instance"></a>Povolit Azure AD na spravované instanci SQL

Spravovaná instance SQL podporuje přímé vytvoření databáze se spravovanou identitou pro váš ADF. Ke skupině Azure AD se nemusíte připojit ke spravované identitě svého ADF ani vytvořit obsaženého uživatele, který představuje tuto skupinu ve spravované instanci SQL.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Konfigurace ověřování Azure AD pro spravovanou instanci Azure SQL

Postupujte podle kroků v části [zřízení správce Azure Active Directory pro spravovanou instanci SQL](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>Přidání spravované identity pro ADF jako uživatele ve spravované instanci SQL

Pro tento další krok potřebujete [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Spusťte SSMS.

2.  Připojte se k spravované instanci SQL pomocí účtu SQL Server, který je **sysadmin**. Toto je dočasné omezení, které se odebere, když se objekty služby Azure AD Server (přihlášení) pro spravovanou instanci Azure SQL stávají GA. Pokud se pokusíte vytvořit přihlášení pomocí účtu správce Azure AD, zobrazí se následující chyba: Msg 15247, úroveň 16, stav 1, řádek 1 uživatel nemá oprávnění k provedení této akce.

3.  V **Průzkumník objektů** rozbalte složku **databáze**  ->  **systémových databází** .

4.  Klikněte pravým tlačítkem na **Hlavní** databázi a vyberte **Nový dotaz**.

5.  V okně dotazu spusťte následující skript T-SQL, který přidá spravovanou identitu pro váš ADF jako uživatel.

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Příkaz by měl úspěšně doplňovat a udělit spravované identitě pro váš ADF, aby bylo možné vytvořit databázi (SSISDB).

6.  Pokud byl váš SSISDB vytvořen pomocí ověřování SQL a chcete pro přístup k tomuto Azure-SSIS IR použít ověřování Azure AD, nejprve se ujistěte, že kroky pro udělení oprávnění k **Hlavní** databázi byly úspěšně dokončeny. Pak klikněte pravým tlačítkem na databázi **SSISDB** a vyberte **Nový dotaz**.

7.  V okně dotazu zadejte následující příkaz T-SQL a na panelu nástrojů vyberte **Spustit** .

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Příkaz by měl úspěšně doplňovat a udělit spravované identitě ADF, aby přístup k SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Zřízení Azure-SSIS IR v aplikaci Azure Portal/ADF

Při zřizování Azure-SSIS IR v aplikaci Azure Portal/ADF klikněte na stránce **nastavení SQL** na možnost **použít ověřování AAD se spravovanou identitou pro vaši možnost ADF** . Následující snímek obrazovky ukazuje nastavení pro IR s SQL Database hostování SSISDB. Pro technologii IR s hostováním SSISDB spravované instance SQL nelze použít **vrstvu služby databáze katalogu** a **Povolení přístupu ke službám Azure** , zatímco další nastavení jsou stejná.

Další informace o tom, jak vytvořit Azure-SSIS IR, najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory](./create-azure-ssis-integration-runtime.md).

![Nastavení pro prostředí Azure-SSIS Integration runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Zřízení Azure-SSIS IR pomocí prostředí PowerShell

Pokud chcete zřídit Azure-SSIS IR pomocí PowerShellu, udělejte tyto věci:

1.  Nainstalujte modul [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Ve vašem skriptu nenastavte `CatalogAdminCredential` parametr. Příklad:

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Spouštění balíčků SSIS pomocí spravovaného ověřování identity

Když na Azure-SSIS IR spustíte balíčky SSIS, můžete se pomocí spravovaného ověřování identity připojit k různým prostředkům Azure. V následujících správcích připojení už v současné době podporujeme ověřování spravované identity.

- [Správce připojení OLE DB](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Správce připojení ADO.NET](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Správce připojení Azure Storage](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)

---
title: Povolit ověřování Azure Active Directory pro prostředí Azure-SSIS Integration Runtime | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit ověřování Azure Active Directory s identitou pro služby Azure Data Factory vytvořit prostředí Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: a67436f09d6e28db8d19679e446ac4cf98383709
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65593795"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Povolit ověřování Azure Active Directory pro prostředí Azure-SSIS Integration Runtime

V tomto článku se dozvíte, jak povolit ověřování Azure Active Directory (Azure AD) pomocí spravované identity pro váš Azure Data Factory (ADF) a používat ho místo ověřování systému SQL k vytvoření Azure-SSIS Integration Runtime (IR), který pak bude poskytovat Služby SSIS catalog databázi (SSISDB) v databázi Azure SQL server nebo spravované Instance vaším jménem.

Další informace o spravované identity pro vaše ADF [identiy spravované služby Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  V tomto scénáři ověřování Azure AD s využitím spravované identity pro vaše ADF slouží pouze k vytvoření a další počáteční operacích SSIS IR, který bude v poskytování a připojit do databáze SSISDB. Za spouštění balíčků služby SSIS bude spojím prostředí IR služby SSIS do databáze SSISDB pomocí ověřování SQL s plně spravované účty, které jsou vytvořeny během zřizování SSISDB.
>-  Pokud jste již vytvořili SSIS IR pomocí ověřování SQL, nelze překonfigurovat na používání ověřování Azure AD prostřednictvím prostředí PowerShell v tuto chvíli, ale můžete to udělat přes Azure portal/ADF aplikace. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Povolení služby Azure AD pro službu Azure SQL Database

Server služby Azure SQL Database podporuje vytváření databáze pomocí uživatele Azure AD. Nejprve musíte vytvořit skupinu Azure AD pomocí spravované identity pro vaše ADF jako člena. Dále je třeba nastavit uživatele služby Azure AD jako správce Active Directory pro váš server Azure SQL Database a připojte se k němu na SQL Server Management Studio (SSMS) pomocí tohoto uživatele. A konečně je potřeba vytvořit představující skupinu Azure AD, takže spravovanou identitu pro vaši ADF můžete použít v prostředí Azure-SSIS IR k vytvoření databáze SSISDB vaším jménem uživatele.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Vytvoření skupiny Azure AD pomocí spravované identity pro vaše ADF jako člena

Můžete použít existující skupinu Azure AD nebo vytvořit novou zásadu pomocí prostředí PowerShell Azure AD.

1.  Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu.

2.  Přihlaste se pomocí `Connect-AzureAD`spuštěním následující rutiny můžete vytvořit skupinu a uložte ho jako proměnnou:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Výsledek bude vypadat jako následující příklad, kdy se také zobrazuje hodnoty proměnné:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Přidáte spravovanou identitu pro vaši ADF do skupiny. Můžete postupovat podle článku [identiy spravované služby Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) k získání ID objektu zabezpečení spravované Identity objektu (například 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ale nepoužívejte spravované ID aplikace Identity pro tento účel).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Můžete také zkontrolovat členství ve skupině později.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurovat ověřování Azure AD pro server Azure SQL Database

Je možné [konfigurovat a spravovat ověřování Azure AD s SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) pomocí následujících kroků:

1.  Na webu Azure portal, vyberte **všechny služby** -> **SQL servery** v levém navigačním panelu.

2.  Vyberte váš server Azure SQL Database nakonfigurovat s ověřováním Azure AD.

3.  V **nastavení** části okna vyberte **správce Active Directory**.

4.  Na panelu příkazů vyberte **nastavit správce**.

5.  Vyberte uživatelský účet služby Azure AD provádí správce serveru a potom vyberte **vyberte.**

6.  Na panelu příkazů vyberte **uložit.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Vytvořte uživatele na serveru Azure SQL Database představující skupinu Azure AD

Tento další krok, budete potřebovat [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Spusťte aplikaci SSMS.

2. V **připojit k serveru** dialogové okno, zadejte název vašeho serveru Azure SQL Database v **název serveru** pole.

3. V **ověřování** pole, vyberte **univerzální podporující vícefaktorové ověřování služby Active Directory –** (můžete také použít druhou dva typy ověřování služby Active Directory, naleznete v tématu [konfigurovat a spravovat Ověřování Azure AD s SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. V **uživatelské jméno** pole, zadejte název účtu Azure AD, kterou jste nastavili jako správce serveru, třeba testuser@xxxonline.com.

5. Vyberte **připojit** a dokončete proces přihlašování.

6. V **Průzkumník objektů**, rozbalte **databází** -> **systémové databáze** složky.

7. Klikněte pravým tlačítkem na **hlavní** databáze a vyberte **nový dotaz**.

8. V okně dotazu zadejte následující příkaz jazyka T-SQL a vyberte **Execute** na panelu nástrojů.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Příkaz je úspěšně dokončena, vytváření uživatele k reprezentování skupiny.

9. Zrušte v okně dotazu zadejte následující příkaz jazyka T-SQL a vyberte **Execute** na panelu nástrojů.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Příkaz je úspěšně dokončena, poskytování omezením uživateli možnost vytvářet databáze (SSISDB).

10. Pokud vaše databáze SSISDB byl vytvořen pomocí ověřování SQL a chcete přejít k němu přístup pomocí ověřování Azure AD pro Azure-SSIS IR, klikněte pravým tlačítkem na **SSISDB** databáze a vyberte **nový dotaz**.

11. V okně dotazu zadejte následující příkaz jazyka T-SQL a vyberte **Execute** na panelu nástrojů.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Příkaz je úspěšně dokončena, vytváření uživatele k reprezentování skupiny.

12. Zrušte v okně dotazu zadejte následující příkaz jazyka T-SQL a vyberte **Execute** na panelu nástrojů.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Příkaz je úspěšně dokončena, poskytování omezením uživatel měl přístup SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Povolení služby Azure AD na spravované instanci Azure SQL Database

Azure SQL Database Managed Instance podporuje vytvoření databáze pomocí spravované identity pro vaše ADF přímo. Nemusí připojit spravovanou identitu pro vaši ADF ke skupině Azure AD ani vytvořte skupiny ve spravované instanci představující uživatele.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurovat ověřování Azure AD pro Azure SQL Database Managed Instance

1.   Na webu Azure portal, vyberte **všechny služby** -> **SQL servery** v levém navigačním panelu.

2.   Vyberte spravované instanci nakonfigurovat s ověřováním Azure AD.

3.   V **nastavení** části okna vyberte **správce Active Directory**.

4.   Na panelu příkazů vyberte **nastavit správce**.

5.   Vyberte uživatelský účet služby Azure AD provádí správce serveru a potom vyberte **vyberte**.

6.   Na panelu příkazů vyberte **Uložit**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Přidat spravovanou identitu pro vaši ADF jako uživatele v Azure SQL Database Managed Instance

Tento další krok, budete potřebovat [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Spusťte aplikaci SSMS.

2.  Připojte k Managed Instance pomocí účtu správce SQL nebo služba Active Directory.

3.  V **Průzkumník objektů**, rozbalte **databází** -> **systémové databáze** složky.

4.  Klikněte pravým tlačítkem na **hlavní** databáze a vyberte **nový dotaz**.

5.  Získejte spravovanou identitu pro vaši ADF. Můžete postupovat podle článku [identiy spravované služby Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) k získání ID objektu zabezpečení spravované Identity aplikace (ale nepoužívejte spravované Identity ID objektu pro tento účel).

6.  V okně dotazu spusťte následující skript T-SQL k převodu spravovanou identitu pro vaši ADF na binární typ:

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your Managed Identity Application ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    Příkaz je úspěšně dokončena, zobrazení spravovanou identitu pro vaši ADF jako binární.

7.  Vymazat okno dotazu a spusťte následující skript T-SQL pro přidání spravovanou identitu pro vaši ADF jako uživatel

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your Managed Identity Application ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    Příkaz je úspěšně dokončena, poskytování spravovanou identitu pro vaši ADF možnost vytvářet databáze (SSISDB).

8.  Pokud vaše databáze SSISDB byl vytvořen pomocí ověřování SQL a chcete přejít k němu přístup pomocí ověřování Azure AD pro Azure-SSIS IR, klikněte pravým tlačítkem na **SSISDB** databáze a vyberte **nový dotaz**.

9.  V okně dotazu zadejte následující příkaz jazyka T-SQL a vyberte **Execute** na panelu nástrojů.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    Příkaz je úspěšně dokončena, poskytování spravovanou identitu pro vaši ADF možnost přístupu k SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Zřízení Azure-SSIS IR v aplikaci Azure portal/ADF

Při zřízení prostředí Azure-SSIS IR v aplikaci Azure portal/ADF, na **nastavení SQL** stránce **ověřování pomocí AAD pomocí spravované identity pro vaše ADF** možnost. Následující snímek obrazovky ukazuje nastavení pro reakcí na Incidenty pomocí serveru Azure SQL Database, který je hostitelem databáze SSISDB. Pro prostředí IR Managed instance, který je hostitelem databáze SSISDB **úroveň služeb databáze katalogu** a **služby Azure umožňují pro přístup k** nastavení nejsou použitelná, zatímco ostatní nastavení nejsou stejné.

Další informace o tom, jak vytvořit prostředí Azure-SSIS IR najdete v tématu [vytvořit prostředí Azure-SSIS integration runtime ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Nastavení prostředí Azure-SSIS integration runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Zřízení prostředí Azure-SSIS IR pomocí Powershellu

Ke zřízení prostředí Azure-SSIS IR pomocí Powershellu, proveďte následující akce:

1.  Nainstalujte [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modulu.

2.  Ve skriptu, nenastavujte `CatalogAdminCredential` parametru. Příklad:

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

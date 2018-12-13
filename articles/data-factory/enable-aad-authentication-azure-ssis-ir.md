---
title: Povolit ověřování Azure Active Directory pro prostředí Azure-SSIS integration runtime | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat prostředí Azure-SSIS integration runtime umožňující připojení, která používají ověřování Azure Active Directory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: d2000e626166304e92556e3c965df175a27046ad
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321063"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Povolit ověřování Azure Active Directory pro prostředí Azure-SSIS integration runtime

V tomto článku se dozvíte, jak vytvořit prostředí Azure-SSIS IR s identitou služby Azure Data Factory. Vám pomůže ověřování Azure Active Directory (Azure AD) s využitím identity spravované služby Azure Data Factory namísto ověřování SQL vytvořit prostředí Azure-SSIS integration runtime.

Další informace o spravovaných identit pro službu data factory najdete v tématu [identitu služby Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Pokud jste již vytvořili prostředí Azure-SSIS integration runtime pomocí ověřování SQL, nelze změnit konfiguraci prostředí IR používat ověřování Azure AD pomocí prostředí PowerShell v tuto chvíli.

## <a name="enable-azure-ad-on-azure-sql-database"></a>Povolení služby Azure AD pro službu Azure SQL Database

Azure SQL Database podporuje vytváření databáze pomocí uživatele Azure AD. V důsledku toho můžete nastavit uživatele služby Azure AD jako správce Active Directory a pak se přihlásit na SQL Server Management Studio (SSMS) pomocí uživatele Azure AD. Potom můžete vytvořit uživatele pro skupiny služby Azure AD umožňuje prostředí IR k vytvoření katalogu SQL Server Integration Services (SSIS) na serveru.

### <a name="create-a-group-in-azure-ad-and-make-the-managed-identity-for-your-data-factory-a-member-of-the-group"></a>Vytvoření skupiny ve službě Azure AD a nastavte spravovanou identitu pro vaši datovou továrnu jako člena skupiny

Můžete vytvořit stávající skupinu Azure AD nebo pomocí Azure AD PowerShellu vytvořit novou.

1.  Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu.

2.  Přihlaste se pomocí `Connect-AzureAD`a spusťte následující příkaz k vytvoření skupiny a uložte ho jako proměnnou:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Výstup bude vypadat podobně jako v následujícím příkladu, který také zkontroluje hodnotu proměnné:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Přidáte spravovanou identitu pro službu data factory do skupiny. Můžete postupovat podle [identitu služby Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) k získání ID objektu zabezpečení IDENTITY služby (například 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ale není pro tento účel použít ID aplikace IDENTITY služby).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Také můžete zkontrolovat členství ve skupinách i později.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Povolit ověřování Azure AD pro Azure SQL Database

Je možné [konfigurovat ověřování Azure AD pro službu SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) pomocí následujících kroků:

1.  Na webu Azure Portal, vyberte **všechny služby** -> **SQL servery** v levém navigačním panelu.

2.  Vyberte databázi SQL, aby byla povolená pro ověřování Azure AD.

3.  V **nastavení** části okna vyberte **správce Active Directory**.

4.  Na panelu příkazů vyberte **nastavit správce**.

5.  Vyberte uživatelský účet služby Azure AD provádí správce serveru a potom vyberte **vyberte.**

6.  Na panelu příkazů vyberte **uložit.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Vytvoření uživatele v databázi reprezentujícího skupinu Azure AD

Tento další krok, budete potřebovat [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Spusťte aplikaci SQL Server Management Studio.

2.  V **připojit k serveru** dialogové okno, zadejte název vašeho SQL serveru v **název serveru** pole.

3.  V **ověřování** pole, vyberte **univerzální podporující vícefaktorové ověřování služby Active Directory –**. (Můžete také použít další dva typy ověřování služby Active Directory. Zobrazit [konfigurovat a spravovat ověřování Azure Active Directory s využitím SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  V **uživatelské jméno** pole, zadejte název účtu služby Azure AD, kterou jste nastavili jako správce serveru – například testuser@xxxonline.com.

5.  Vyberte **připojit**. Dokončete proces přihlašování.

6.  V **Průzkumník objektů**, rozbalte **databází** -> složku systémové databáze.

7.  Stisknutém pravém tlačítku vyberte na **hlavní** databáze a vyberte **nový dotaz**.

8.  V okně dotazu zadejte následující řádek a vyberte **Execute** na panelu nástrojů:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Příkaz by se měl úspěšně provést a vytvořit uživatele pro skupinu.

9.  Zrušte v okně dotazu zadejte následující řádek a vyberte **Execute** na panelu nástrojů:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Příkaz je úspěšně dokončena, poskytování omezením uživateli možnost vytvářet databáze.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Povolení služby Azure AD na spravované instanci Azure SQL Database

Azure SQL Database Managed Instance podporuje vytvoření databáze pomocí MSI přímo. Nemusíte připojit k datové továrně MSI do skupiny AD nebo vytvořit omezením uživatele v USA.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database-managed-instance"></a>Povolit ověřování Azure AD pro Azure SQL Database Managed Instance

1.   Na webu Azure Portal, vyberte **všechny služby** -> **SQL servery** v levém navigačním panelu.

1.   Vyberte SQL server, aby byla povolená pro ověřování Azure AD.

1.   V **nastavení** části okna vyberte **správce Active Directory**.

1.   Na panelu příkazů vyberte **nastavit správce**.

1.   Vyberte uživatelský účet služby Azure AD provádí správce serveru a potom vyberte **vyberte**.

1.   Na panelu příkazů vyberte **Uložit**.

### <a name="add-data-factory-msi-as-a-user-to-the-azure-sql-database-managed-instance"></a>Přidání služby data factory MSI jako uživatele do Azure SQL Database Managed Instance

1.  Spusťte aplikaci SQL Server Management Studio.

2.  Přihlaste se pomocí účtu správce SQL nebo účet správce služby Active Directory.

3.  V Průzkumníku objektů rozbalte databáze -> složku systémové databáze.

4.  Klikněte pravým tlačítkem na hlavní databázi a vyberte **nový dotaz**.

5.  Můžete postupovat podle článku [identitu služby Azure Data Factory](data-factory-service-identity.md) zobrazíte instančního objektu služby IDENTITY ID aplikace. (Nepoužívejte ID IDENTITY služby pro tento účel.)

6.  V okně dotazu spusťte následující skript k převedení ID aplikace IDENTITY služby na binární typ:

    ```sql
    DECLARE @applicationId uniqueidentifier = {your service identity application id}
    select CAST(@applicationId AS varbinary)
    ```

7.  Získá hodnotu z okna výsledků.

8.  Vymazat okno dotazu a spusťte následující skript:

    ```sql
    CREATE LOGIN [{MSI name}] FROM EXTERNAL PROVIDER with SID ={your service identity application id in binary type}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{MSI name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{MSI name}]
    ```

9.  Příkaz úspěšně dokončí.

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Zřízení prostředí Azure-SSIS IR na portálu

Při zřízení prostředí Azure-SSIS IR pomocí webu Azure portal, na **nastavení SQL** stránky, zkontrolujte "použití AAD ověřování pomocí spravované identity pro vaše ADF" možnost. (Na následujícím snímku obrazovky ukazuje nastavení pro prostředí IR s Azure SQL Database. Pro prostředí IR Managed instance není k dispozici; vlastnost "Vrstvy služeb databáze katalogu" Další nastavení jsou stejné.)

Další informace o tom, jak vytvořit prostředí Azure-SSIS integration runtime najdete v tématu [vytvořit prostředí Azure-SSIS integration runtime ve službě Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Nastavení prostředí Azure-SSIS integration runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Zřízení prostředí Azure-SSIS IR pomocí Powershellu

Ke zřízení prostředí Azure-SSIS IR pomocí Powershellu, proveďte následující akce:

1.  Nainstalujte [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modulu.

2.  Ve skriptu, nenastavujte *CatalogAdminCredential* parametru. Příklad:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```

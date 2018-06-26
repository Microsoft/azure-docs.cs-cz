---
title: Povolení ověřování Azure Active Directory pro modul runtime integrace Azure SSIS | Microsoft Docs
description: Tento článek popisuje postup konfigurace modulu runtime integrace Azure SSIS povolit připojení, které používají ověřování Azure Active Directory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: douglasl
ms.openlocfilehash: 93d3e25957fb1f04400fa78423a5658d32f7d5fd
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749714"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Povolení ověřování Azure Active Directory pro modul runtime integrace Azure SSIS

Tento článek ukazuje, jak vytvořit Azure SSIS IR s identitou služby Azure Data Factory. Ověřování Azure Active Directory (Azure AD) se na spravované služby Identity (MSI) pro modul runtime integrace Azure SSIS umožňuje používali k vytváření modulu runtime integrace Azure SSIS MSI objekt pro vytváření dat místo ověřování SQL.

Další informace o MSI objekt pro vytváření dat najdete v tématu [identita služby Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Pokud jste již vytvořili modulu runtime integrace Azure SSIS pomocí ověřování SQL, nelze změnit konfiguraci IR používat ověřování Azure AD pomocí prostředí PowerShell v tuto chvíli.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Vytvoření skupiny ve službě Azure AD a nastavte MSI objekt pro vytváření dat jako člena skupiny

Můžete použít existující skupinu pro Azure AD, nebo vytvořte novou pomocí Azure AD PowerShell.

1.  Nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulu.

2.  Přihlaste se pomocí `Connect-AzureAD`a spusťte následující příkaz pro vytvoření skupiny a uložte ho do proměnné:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Výstup bude vypadat jako v následujícím příkladu, který také zkoumá hodnotu proměnné:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Přidejte soubor MSI objekt pro vytváření dat do skupiny. Můžete postupovat podle [identita služby Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity) získat ID objektu zabezpečení IDENTITY služby (například 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ale pro tento účel nepoužívejte ID aplikace IDENTITY služby).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Také můžete zkontrolovat členství ve skupině i později.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Povolení služby Azure AD na databázi Azure SQL

Azure SQL Database podporuje vytváření databáze pomocí uživatele Azure AD. V důsledku toho můžete nastavit uživatele Azure AD jako správce Active Directory a pak se přihlaste do aplikace SSMS pomocí uživatele Azure AD. Potom můžete vytvořit uživatele Azure AD skupiny pro povolení IR vytvoření katalogu integrační služby SSIS (SQL Server) na serveru.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Povolení ověřování Azure AD pro databázi SQL Azure

Můžete [nakonfigurovat ověřování Azure AD pro databázi SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) pomocí následujících kroků:

1.  Na portálu Azure vyberte **všechny služby** -> **servery SQL** z levé navigaci.

2.  Vyberte databázi SQL, aby byl povolen pro ověřování Azure AD.

3.  V **nastavení** části okna vyberte **správce Active Directory**.

4.  Na panelu příkazů vyberte **nastavit správce**.

5.  Vyberte uživatelský účet služby Azure AD budou správce serveru, a potom vyberte **vyberte.**

6.  Na panelu příkazů vyberte **uložit.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Vytvořte uživatele v databázi, která reprezentuje skupinu Azure AD

Pro tento další krok, je nutné [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Spusťte aplikaci SQL Server Management Studio.

2.  V **připojit k serveru** dialogové okno, zadejte název serveru SQL v **název serveru** pole.

3.  V **ověřování** pole, vyberte **Universal s podpora vícefaktorového ověřování služby Active Directory -**. (Můžete také použít další dva typy ověřování služby Active Directory. V tématu [konfigurovat a spravovat ověřování Azure Active Directory s databází SQL, spravované Instance](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure).)

4.  V **uživatelské jméno** pole, zadejte název účtu Azure AD, které jste nastavili jako správce serveru – například testuser@xxxonline.com.

5.  Vyberte **Connect**. Dokončete proces přihlášení.

6.  V **Průzkumník objektů**, rozbalte **databáze** -> složku systémové databáze.

7.  Vyberte práva na **hlavní** databáze a vyberte **nový dotaz**.

8.  V okně dotazu zadejte následující řádek a vyberte **Execute** na panelu nástrojů:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Příkaz je úspěšně dokončena, vytvoření obsažené uživatele pro skupinu.

9.  Vymazat okno dotazu, zadejte následující řádek a vybrat **Execute** na panelu nástrojů:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Příkaz je úspěšně dokončena, udělení obsažené uživateli možnost pro vytvoření databáze.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Povolení služby Azure AD na spravované Instance databáze Azure SQL

Azure SQL Database spravované Instance nepodporuje vytvoření databáze pomocí žádné uživatele Azure AD, než správce AD. V důsledku toho je nutné nastavit skupiny AD Azure jako správce služby Active Directory. Nemusíte vytvořit obsažené uživatele.

Můžete [konfigurovat ověřování služby Azure AD pro spravované Instance databáze SQL serveru](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) pomocí následujících kroků:

7.  Na portálu Azure vyberte **všechny služby** -> **servery SQL** z levé navigaci.

8.  Vyberte SQL server, aby byl povolen pro ověřování Azure AD.

9.  V **nastavení** části okna vyberte **správce Active Directory**.

10. Na panelu příkazů vyberte **nastavit správce**.

11. Hledání a vyberte skupiny AD Azure (například SSISIrGroup) a vyberte **vyberte.**

12. Na panelu příkazů vyberte **uložit.**

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Zřízení Azure SSIS Reakcí na portálu

Při přidělení vaší IR Azure SSIS pomocí portálu Azure, na **nastavení SQL** stránka, zkontrolujte "použití AAD ověřování s vaší ADF MSI" možnost. (Následující snímek obrazovky ukazuje nastavení pro reakcí na Incidenty s Azure SQL Database. Pro IR s spravované Instance vlastnost "Katalogu databáze služby vrstvy" není k dispozici. Další nastavení jsou stejné.)

Další informace o tom, jak vytvořit modulu runtime integrace Azure SSIS najdete v tématu [v Azure Data Factory vytvořit modulu runtime integrace Azure SSIS](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime).

![Nastavení pro modul runtime integrace Azure SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Zřídit IR Azure SSIS pomocí prostředí PowerShell

Pokud chcete zřídit vaší IR Azure SSIS pomocí prostředí PowerShell, proveďte následující akce:

1.  Nainstalujte [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modulu.

2.  Ve vašem skriptu, nenastavujte *CatalogAdminCredential* parametr. Příklad:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Type Managed `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -Description $AzureSSISDescription `
                                               -Edition $AzureSSISEdition `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```

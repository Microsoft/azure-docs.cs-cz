---
title: Použití PowerShellu k registraci a kontrole Power BI (Preview)
description: Naučte se používat PowerShell k registraci a skenování klienta Power BI v Azure dosah.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551899"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Použití PowerShellu k registraci a kontrole Power BI v Azure dosah (Preview) 

Tento článek popisuje, jak pomocí PowerShellu nastavit kontrolu Power BIho tenanta v katalogu Azure dosah.

## <a name="power-bi-authentication-background"></a>Pozadí ověřování Power BI

Katalog dosah se musí připojit k rozhraní API pro správu Power BI, aby kontroloval artefakty v klientovi Power BI. Rozhraní API pro správu Power BI aktuálně podporuje dva typy ověřování:

- Spravovaná identita (MSI).
- Delegovaný uživatel ověřování.

> [!Note]
> Doporučuje se MSI, pokud není vyžadováno delegované ověřování uživatelů.

## <a name="create-a-security-group"></a>Vytvoření skupiny zabezpečení

Každý katalog dosah má vlastní spravovanou identitu přiřazenou systémem, která musí mít přístup k tenantovi Power BI, aby bylo možné povolit kontrolu. Název katalogu lze použít k vyhledání jeho identity v Azure Portal.

1. V [Azure Portal](https://portal.azure.com)vyhledejte Azure Active Directory.
1. Vytvořte ve svém Azure Active Directory novou skupinu zabezpečení, a to tak, že [vytvoříte základní skupinu a přidáte členy pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Tento krok můžete přeskočit, pokud již máte skupinu zabezpečení, kterou chcete použít.

1. Ujistěte se, že jste jako **typ skupiny** vybrali zabezpečení.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ skupiny zabezpečení":::

1. Do této skupiny zabezpečení přidejte spravovanou identitu vašeho katalogu tak, že vyberete členy a potom **přidáte členy**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Přidat spravovanou instanci katalogu do skupiny":::

1. Vyhledejte svůj katalog a vyberte ho.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Přidání katalogu hledáním":::

1. Mělo by se zobrazit oznámení o úspěšnosti, které vám ukáže, že bylo přidáno.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Přidání úspěšného souboru MSI katalogu":::

## <a name="associate-the-security-group-with-power-bi"></a>Přidružte skupinu zabezpečení k Power BI

1. Přihlaste se na [portál pro správu Power BI](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Připojit tento příznak funkce:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Tento příznak umožňuje funkci, která umožňuje přidružení skupiny zabezpečení. Příklad:

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Aby bylo možné zobrazit stránku nastavení klienta, musíte být správcem Power BI.

1. Vyberte **Nastavení vývojářů**  >  **, aby instanční objekty mohly používat rozhraní API Power BI jen pro čtení (Preview)**.
1. Vyberte **konkrétní skupiny zabezpečení**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Obrázek ukazující, jak povolíte instančním objektům oprávnění k přístupu Power BI oprávnění API pro správu ":::

    > [!Caution]
    > Když povolíte skupinu zabezpečení, kterou jste vytvořili (která má vaši identitu spravovanou v katalogu Data Catalog), aby používala rozhraní API Správce Power BI jen pro čtení, umožníte jí také přístup k metadatům (např. řídicím panelem a názvům sestav, vlastníkům, popisům atd.) pro všechny vaše Power BI artefakty v tomto tenantovi. Po navýšení metadat do oprávnění Azure dosah, dosah, ne Power BI oprávnění Zjistěte, kdo uvidí tato metadata.

    > [!Note]
    > Skupinu zabezpečení můžete odebrat z nastavení pro vývojáře, ale metadata, která byla dříve extrahována, nebudou odebrána z účtu dosah. Můžete ho odstranit samostatně.

## <a name="register-power-bi-and-set-up-a-scan"></a>Registrace Power BI a nastavení kontroly

Teď, když jste udělili oprávnění ke katalogu pro připojení k rozhraní API pro správu vašeho tenanta Power BI, je potřeba nastavit kontrolu v katalogu. Uděláte to tak, že nakonfigurujete a spustíte PowerShellový skript.

1. Stáhněte a extrahujte rutiny prostředí PowerShell pro ADC.
1. Nakonfigurujte svůj skript zadáním hodnot pro přiřazení v horní části skriptu.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > V předplatném, ve kterém spouštíte příkazy, musíte být přispěvatel nebo vlastník.

1. Spusťte kontrolu spuštěním následujícího skriptu:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Registrovat a kontrolovat Power BI

Doporučenou metodou ověřování je MSI. Pokud ale chcete kontrolovat klienta Power BI, který je v jiném tenantovi Azure než v katalogu, použijete delegované ověřování.

Chcete-li provést delegované ověřování, musíte mít přihlašovací údaje uživatele správce a také Power BI přihlašovací údaje správce. Musíte také vytvořit aplikaci Azure a udělit jí Power BI oprávnění tenant. ReadAll.

1. Přejděte na [Azure Portal](https://portal.azure.com) a vyhledejte **Registrace aplikací**.

1. Z **Registrace aplikací** vyberte **+ Nová registrace**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Obrázek ukazující, jak vytvořit novou registraci aplikace Azure":::

1. Zadejte název vaší aplikace.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="zaregistrovat novou aplikaci":::

1. Po vytvoření aplikace vyberte **oprávnění rozhraní API** a potom **+ Přidat oprávnění**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Obrázek ukazující, jak přidat oprávnění k aplikaci":::

1. V **oprávněních rozhraní API pro vyžádání** vyberte **službu Power BI** .

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Obrázek ukazující, jak vybrat službu PBI":::

1. Vyberte **delegovaná oprávnění** a **tenanta. Read. All**. Pak vyberte **Přidat oprávnění**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Obrázek ukazující, jak požádat o oprávnění API":::

1. Vyberte **udělit souhlas správce** .

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Obrázek ukazující, jak udělit souhlas správce":::

1. Zkopírujte **ID aplikace (klienta)** a hodnoty **ID adresáře (tenant)** .  Tyto hodnoty použijete při nastavování kontroly.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Obrázek znázorňující kopírování ID klientů a klientů":::

1. Nakonfigurujte kontrolu v prostředí PowerShell. Skript zobrazí výzvu k zadání přihlašovacích údajů. V předplatném Azure, které máte v úmyslu používat, musíte mít aspoň roli přispěvatel a roli správce zdroje dat dosah.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Spusťte kontrolu.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít s Azure dosah, najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).

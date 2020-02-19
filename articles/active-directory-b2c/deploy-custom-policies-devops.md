---
title: Nasazení vlastních zásad pomocí Azure Pipelines
titleSuffix: Azure AD B2C
description: Naučte se, jak nasadit Azure AD B2C vlastní zásady v kanálu CI/CD pomocí Azure Pipelines v Azure DevOps Services.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 21fde69f404ee535bfe0019a91843297b1752a92
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463138"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Nasazení vlastních zásad pomocí Azure Pipelines

Pomocí kanálu průběžné integrace a doručování (CI/CD), který jste nastavili v [Azure Pipelines][devops-pipelines], můžete zahrnout vlastní zásady Azure AD B2C v rámci poskytování softwaru a automatizace řízení kódu. Při nasazení do různých Azure AD B2C prostředí, například pro vývoj, testování a produkci, doporučujeme odebrat ruční procesy a provádět automatizované testování pomocí Azure Pipelines.

Pro povolení Azure Pipelines správy vlastních zásad v rámci Azure AD B2C jsou potřeba tři hlavní kroky:

1. Vytvoření registrace webové aplikace ve vašem tenantovi Azure AD B2C
1. Konfigurace úložiště Azure
1. Konfigurace kanálu Azure

> [!IMPORTANT]
> Správa Azure AD B2C vlastních zásad pomocí kanálu Azure aktuálně používá operace ve **verzi Preview** , které jsou k dispozici ve službě Microsoft Graph API `/beta` koncového bodu. Použití těchto rozhraní API v produkčních aplikacích není podporováno. Další informace najdete v referenčních informacích ke [koncovému bodu Microsoft Graph REST API beta](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Předpoklady

* [Azure AD B2C tenant](tutorial-create-tenant.md)a přihlašovací údaje uživatele v adresáři s rolí [správce zásad IEF B2C](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Vlastní zásady](custom-policy-get-started.md) odeslané do vašeho tenanta
* [Kanál Azure](https://azure.microsoft.com/services/devops/pipelines/)a přístup k [projektu Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Tok udělení přihlašovacích údajů klienta

Scénář, který je zde popsán, používá volání služby Service-to-Service mezi Azure Pipelines a Azure AD B2C pomocí [toku udělení přihlašovacích údajů klienta](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)OAuth 2,0. Tento tok udělení povoluje webové službě, jako je například Azure Pipelines (důvěrný klient) používat vlastní přihlašovací údaje místo zosobnění uživatele k ověření při volání jiné webové služby (v tomto případě rozhraní Microsoft Graph API). Azure Pipelines získá token neinteraktivně, provede požadavky na rozhraní Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registrace aplikace pro úlohy správy

Začněte tím, že vytvoříte registraci aplikace, kterou skripty PowerShellu spouštěné nástrojem Azure Pipelines použijí ke komunikaci s Azure AD B2C. Pokud již máte registraci aplikace, kterou používáte pro úlohy služby Automation, můžete přejít na oddíl [udělení oprávnění](#grant-permissions) .

### <a name="register-application"></a>Registrovat aplikaci

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-permissions"></a>Udělení oprávnění

Potom udělte aplikaci oprávnění používat rozhraní Microsoft Graph API ke čtení a zápisu vlastních zásad ve vašem tenantovi Azure AD B2C.

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Na stránce Přehled **zaregistrovaných aplikací** vyberte **Nastavení**.
1. V části **přístup přes rozhraní API**vyberte **požadovaná oprávnění**.
1. Vyberte **Přidat**a pak **Vyberte rozhraní API**.
1. Vyberte **Microsoft Graph**a pak **Vyberte**.
1. V části **oprávnění aplikace**vyberte **číst a zapsat zásady pro vztah důvěryhodnosti vaší organizace**.
1. Vyberte **Vybrat**a pak **Hotovo**.
1. Vyberte **udělit oprávnění**a pak vyberte **Ano**. Aby bylo možné plně šířit oprávnění, může trvat několik minut.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Vyberte **Registrace aplikací (Preview)** a pak vyberte webovou aplikaci, která má mít přístup k rozhraní API Microsoft Graph. Například *managementapp1*.
1. V části **Spravovat**vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **rozhraní Microsoft API** a pak vyberte **Microsoft Graph**.
1. Vyberte **oprávnění aplikace**.
1. Rozbalte položku **zásady** a vyberte **zásady. TrustFramework**.
1. Vyberte **Přidat oprávnění**. Jak je směrované, počkejte několik minut, než budete pokračovat k dalšímu kroku.
1. Vyberte **udělit souhlas správce pro (název vašeho tenanta)** .
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v Azure AD B2C tenantovi, kterému byla přiřazena alespoň role *správce cloudové aplikace* .
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat**a pak ověřte, že "uděleno pro..." zobrazí se pod položkou **stav**. Rozšíření oprávnění může trvat několik minut.

* * *

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

Pro ověření pomocí Azure AD B2C musí skript prostředí PowerShell zadat tajný klíč klienta, který vytvoříte pro aplikaci.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-an-azure-repo"></a>Konfigurace úložiště Azure

S registrovanou aplikací pro správu jste připraveni nakonfigurovat úložiště pro soubory zásad.

1. Přihlaste se ke svojí Azure DevOps Services organizaci.
1. [Vytvořte nový projekt][devops-create-project] nebo vyberte existující projekt.
1. V projektu přejděte do **úložišť** a vyberte stránku **soubory** . Vyberte existující úložiště nebo ho vytvořte pro toto cvičení.
1. Vytvořte složku s názvem *B2CAssets*. Pojmenujte požadovaný zástupný soubor *Readme.MD* a **potvrďte** ho. Pokud chcete, můžete tento soubor později odebrat.
1. Přidejte soubory zásad Azure AD B2C do složky *B2CAssets* . To zahrnuje *TrustFrameworkBase. XML*, *TrustFrameWorkExtensions. XML*, *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*a všechny další zásady, které jste vytvořili. Poznamenejte si název souboru zásad Azure AD B2C pro použití v pozdějším kroku (používá se jako argumenty skriptu PowerShellu).
1. V kořenovém adresáři úložiště vytvořte složku s názvem *Scripts* a pojmenujte zástupný soubor *DeployToB2c. ps1*. V tomto okamžiku soubor nepotvrďte, a to v pozdějším kroku.
1. Do souboru *DeployToB2c. ps1*vložte následující skript PowerShellu a pak soubor **potvrďte** . Skript získá token z Azure AD a zavolá rozhraní Microsoft Graph API, které odešle zásady do složky *B2CAssets* do vašeho tenanta Azure AD B2C.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Konfigurace kanálu Azure

Po inicializaci a naplnění vašeho úložiště pomocí vlastních souborů zásad jste připraveni k nastavení kanálu vydání.

### <a name="create-pipeline"></a>Vytvoření kanálu

1. Přihlaste se ke svojí organizaci Azure DevOps Services a přejděte do svého projektu.
1. V projektu vyberte **kanály** > **vydané verze** > **Nový kanál**.
1. V části **Vybrat šablonu**vyberte **prázdná úloha**.
1. Zadejte **název fáze**, například *DeployCustomPolicies*, a pak zavřete podokno.
1. Vyberte **Přidat artefakt**a v části **typ zdroje**vyberte **úložiště Azure**.
    1. Vyberte zdrojové úložiště obsahující složku *skripty* , kterou jste naplnili pomocí skriptu PowerShellu.
    1. Vyberte **výchozí větev**. Pokud jste v předchozí části vytvořili nové úložiště, výchozí větev je *Hlavní*.
    1. U výchozí větve ponechte **výchozí nastavení verze** *nejnovější*.
    1. Zadejte **zdrojový alias** úložiště. Například *policyRepo*. V názvu aliasu nezahrnujte mezery.
1. Vyberte **Přidat**.
1. Přejmenujte kanál tak, aby odrážel jeho záměr. Nasaďte například *vlastní kanál zásad*.
1. Vyberte **Uložit** a uložte konfiguraci kanálu.

### <a name="configure-pipeline-variables"></a>Konfigurovat proměnné kanálu

1. Vyberte kartu **proměnné** .
1. Přidejte následující proměnné pod **proměnnou kanálu** a nastavte jejich hodnoty tak, jak jsou zadány:

    | Název | Hodnota |
    | ---- | ----- |
    | `clientId` | **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali dříve. |
    | `clientSecret` | Hodnota **tajného klíče klienta** , který jste vytvořili dříve. <br /> Změňte typ proměnné na **tajný kód** (vyberte ikonu zámku). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, kde *B2C-tenant* je název vašeho tenanta Azure AD B2C. |

1. Vyberte **Uložit** a uložte proměnné.

### <a name="add-pipeline-tasks"></a>Přidat úlohy kanálu

Pak přidejte úkol pro nasazení souboru zásad.

1. Vyberte kartu **úlohy** .
1. Vyberte **úloha agenta**a pak vyberte znaménko plus ( **+** ), chcete-li přidat úkol do úlohy agenta.
1. Vyhledejte a vyberte **PowerShell**. Nevybírejte "Azure PowerShell," PowerShell na cílových počítačích "nebo jinou položku prostředí PowerShell.
1. Vyberte nově přidaný úkol **skriptu prostředí PowerShell** .
1. Zadejte následující hodnoty pro úlohu skript prostředí PowerShell:
    * **Verze úlohy**: 2. *
    * **Zobrazovaný název**: název zásady, kterou by měl tento úkol odeslat. Například *B2C_1A_TrustFrameworkBase*.
    * **Typ**: cesta k souboru
    * **Cesta ke skriptu**: vyberte tři tečky (***...***), přejděte do složky *Scripts* a pak vyberte soubor *DeployToB2C. ps1* .
    * **Náhodné**

        Pro **argumenty**zadejte následující hodnoty. Nahraďte `{alias-name}` aliasem, který jste zadali v předchozí části.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Například pokud je alias, který jste zadali, *policyRepo*, řádek argumentu by měl:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/contosob2cpolicies/B2CAssets/TrustFrameworkBase.xml
        ```

1. Kliknutím na **Uložit** uložte úlohu agenta.

Úloha, kterou jste právě přidali, nahrává *jeden* soubor zásad pro Azure AD B2C. Než budete pokračovat, ručně aktivujte úlohu (**vytvořit vydání**), abyste zajistili, že se úspěšně dokončí před vytvořením dalších úkolů.

Pokud se úloha úspěšně dokončí, přidejte úlohy nasazení provedením předchozích kroků pro všechny soubory vlastních zásad. Upravte hodnoty argumentu `-PolicyId` a `-PathToFile` pro každou zásadu.

`PolicyId` je hodnota, která se nachází na začátku souboru zásad XML v uzlu TrustFrameworkPolicy. Například `PolicyId` v následujících zásadách XML je *B2C_1A_TrustFrameworkBase*:

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Když spustíte agenty a nahrajete soubory zásad, ujistěte se, že se nahrály v tomto pořadí:

1. *TrustFrameworkBase. XML*
1. *TrustFrameworkExtensions. XML*
1. *SignUpOrSignin. XML*
1. *ProfileEdit. XML*
1. *PasswordReset. XML*

Rozhraní identity Experience Framework toto pořadí vynutilo, protože struktura souborů je postavená na hierarchickém řetězu.

## <a name="test-your-pipeline"></a>Test kanálu

Testování kanálu pro vydávání verzí:

1. Vyberte **kanály** a pak **vydané verze**.
1. Vyberte kanál, který jste vytvořili dříve, například *DeployCustomPolicies*.
1. Vyberte **vytvořit vydání**a pak vyberte **vytvořit** a zařadíte do fronty verzi.

Měl by se zobrazit informační zpráva s oznámením, že vydaná verze byla zařazena do fronty. Chcete-li zobrazit jeho stav, vyberte odkaz v oznamovací hlavičce nebo ho vyberte v seznamu na kartě **vydané verze** .

## <a name="next-steps"></a>Další kroky

Další informace:

* [Volání služby mezi službami pomocí přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
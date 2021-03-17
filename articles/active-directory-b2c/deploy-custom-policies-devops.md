---
title: Nasazení vlastních zásad pomocí Azure Pipelines
titleSuffix: Azure AD B2C
description: Naučte se, jak nasadit Azure AD B2C vlastní zásady v kanálu CI/CD pomocí Azure Pipelines v Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13f5f8da0bd58cef0974e8ea8f5f3c5172daa0ba
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928728"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Nasazení vlastních zásad pomocí Azure Pipelines

Pomocí kanálu průběžné integrace a doručování (CI/CD), který jste nastavili v [Azure Pipelines][devops-pipelines], můžete zahrnout vlastní zásady Azure AD B2C v rámci poskytování softwaru a automatizace řízení kódu. Při nasazení do různých Azure AD B2C prostředí, například pro vývoj, testování a produkci, doporučujeme odebrat ruční procesy a provádět automatizované testování pomocí Azure Pipelines.

Pro povolení Azure Pipelines správy vlastních zásad v rámci Azure AD B2C jsou potřeba tři hlavní kroky:

1. Vytvoření registrace webové aplikace ve vašem tenantovi Azure AD B2C
1. Konfigurace úložiště Azure
1. Konfigurace kanálu Azure

> [!IMPORTANT]
> Správa Azure AD B2C vlastních zásad pomocí kanálu Azure aktuálně používá operace ve **verzi Preview** , které jsou k dispozici na `/beta` KONCOVÉM bodu rozhraní API Microsoft Graph. Použití těchto rozhraní API v produkčních aplikacích není podporováno. Další informace najdete v referenčních informacích ke [koncovému bodu Microsoft Graph REST API beta](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Předpoklady

* [Azure AD B2C tenant](tutorial-create-tenant.md)a přihlašovací údaje uživatele v adresáři s rolí [správce zásad IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Vlastní zásady](custom-policy-get-started.md) odeslané do vašeho tenanta
* Ve vašem tenantovi se zaregistrovala [aplikace pro správu](microsoft-graph-get-started.md) se zásadami oprávnění Microsoft Graph API *. TrustFramework* .
* [Kanál Azure](https://azure.microsoft.com/services/devops/pipelines/)a přístup k [projektu Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Tok udělení přihlašovacích údajů klienta

Scénář, který je zde popsán, používá volání služby Service-to-Service mezi Azure Pipelines a Azure AD B2C pomocí [toku udělení přihlašovacích údajů klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)OAuth 2,0. Tento tok udělení povoluje webové službě, jako je například Azure Pipelines (důvěrný klient) používat vlastní přihlašovací údaje místo zosobnění uživatele k ověření při volání jiné webové služby (v tomto případě rozhraní Microsoft Graph API). Azure Pipelines získá token neinteraktivně, provede požadavky na rozhraní Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registrace aplikace pro úlohy správy

Jak je uvedeno v části [požadavky](#prerequisites), budete potřebovat registraci aplikace, kterou skripty PowerShellu spouštěné pomocí Azure Pipelines – můžou použít pro přístup k prostředkům ve vašem tenantovi.

Pokud už máte registraci aplikace, kterou používáte pro úlohy automatizace, ujistěte se, že je jí udělené oprávnění **Microsoft Graph** zásady  >  **zásad**  >  **. TrustFramework** v rámci **oprávnění API** k registraci aplikace.

Pokyny k registraci aplikace pro správu najdete v tématu [správa Azure AD B2C s](microsoft-graph-get-started.md)využitím Microsoft Graph.

## <a name="configure-an-azure-repo"></a>Konfigurace úložiště Azure

S registrovanou aplikací pro správu jste připraveni nakonfigurovat úložiště pro soubory zásad.

1. Přihlaste se ke svojí Azure DevOps Services organizaci.
1. [Vytvořte nový projekt][devops-create-project] nebo vyberte existující projekt.
1. V projektu přejděte do **úložišť** a vyberte stránku **soubory** . Vyberte existující úložiště nebo ho vytvořte pro toto cvičení.
1. Vytvořte složku s názvem *B2CAssets*. Pojmenujte požadovaný zástupný soubor *Readme.MD* a **potvrďte** ho. Pokud chcete, můžete tento soubor později odebrat.
1. Přidejte soubory zásad Azure AD B2C do složky *B2CAssets* . To zahrnuje *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* a všechny další zásady, které jste vytvořili. Poznamenejte si název souboru zásad Azure AD B2C pro použití v pozdějším kroku (používá se jako argumenty skriptu PowerShellu).
1. V kořenovém adresáři úložiště vytvořte složku s názvem *Scripts* a pojmenujte zástupný soubor *DeployToB2c.ps1*. V tomto okamžiku soubor nepotvrďte, a to v pozdějším kroku.
1. Do *DeployToB2c.ps1* vložte následující skript PowerShellu a pak soubor **potvrďte** . Skript získá token z Azure AD a zavolá rozhraní Microsoft Graph API, které odešle zásady do složky *B2CAssets* do vašeho tenanta Azure AD B2C.

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
1. V projektu vyberte **kanály**  >  **release**  >  **New Pipeline**.
1. V části **Vybrat šablonu** vyberte **prázdná úloha**.
1. Zadejte **název fáze**, například *DeployCustomPolicies*, a pak zavřete podokno.
1. Vyberte **Přidat artefakt** a v části **typ zdroje** vyberte **úložiště Azure**.
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
1. Vyberte **úloha agenta** a potom vyberte znaménko plus ( **+** ) a přidejte úkol do úlohy agenta.
1. Vyhledejte a vyberte **PowerShell**. Nevybírejte "Azure PowerShell," PowerShell na cílových počítačích "nebo jinou položku prostředí PowerShell.
1. Vyberte nově přidaný úkol **skriptu prostředí PowerShell** .
1. Zadejte následující hodnoty pro úlohu skript prostředí PowerShell:
    * **Verze úlohy**: 2. *
    * **Zobrazovaný název**: název zásady, kterou by měl tento úkol odeslat. Například *B2C_1A_TrustFrameworkBase*.
    * **Typ**: cesta k souboru
    * **Cesta ke skriptu**: vyberte tři tečky (**_..._* _) přejděte do složky _Scripts * a potom vyberte soubor *DeployToB2C.ps1* .
    * **Náhodné**

        Pro **argumenty** zadejte následující hodnoty. Nahraďte `{alias-name}` aliasem, který jste zadali v předchozí části.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Například pokud je alias, který jste zadali, *policyRepo*, řádek argumentu by měl:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Kliknutím na **Uložit** uložte úlohu agenta.

Úloha, kterou jste právě přidali, nahrává *jeden* soubor zásad pro Azure AD B2C. Než budete pokračovat, ručně aktivujte úlohu (**vytvořit vydání**), abyste zajistili, že se úspěšně dokončí před vytvořením dalších úkolů.

Pokud se úloha úspěšně dokončí, přidejte úlohy nasazení provedením předchozích kroků pro všechny soubory vlastních zásad. Upravte `-PolicyId` `-PathToFile` hodnoty argumentů a pro každou zásadu.

`PolicyId`Je hodnota zjištěná na začátku souboru zásad XML v uzlu TrustFrameworkPolicy. Například `PolicyId` v následujících zásadách XML je *B2C_1A_TrustFrameworkBase*:

```xml
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

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Rozhraní identity Experience Framework toto pořadí vynutilo, protože struktura souborů je postavená na hierarchickém řetězu.

## <a name="test-your-pipeline"></a>Test kanálu

Testování kanálu pro vydávání verzí:

1. Vyberte **kanály** a pak **vydané verze**.
1. Vyberte kanál, který jste vytvořili dříve, například *DeployCustomPolicies*.
1. Vyberte **vytvořit vydání** a pak vyberte **vytvořit** a zařadíte do fronty verzi.

Měl by se zobrazit informační zpráva s oznámením, že vydaná verze byla zařazena do fronty. Chcete-li zobrazit jeho stav, vyberte odkaz v oznamovací hlavičce nebo ho vyberte v seznamu na kartě **vydané verze** .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace:

* [Volání služby mezi službami pomocí přihlašovacích údajů klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines

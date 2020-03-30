---
title: Nasazení vlastních zásad pomocí Azure Pipelines
titleSuffix: Azure AD B2C
description: Zjistěte, jak nasadit vlastní zásady Azure AD B2C v kanálu CI/CD pomocí Azure Pipelines ve službách Azure DevOps.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188745"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Nasazení vlastních zásad pomocí Azure Pipelines

Pomocí kanálu průběžné integrace a doručování (CI/CD), který nastavíte v [Azure Pipelines][devops-pipelines], můžete zahrnout vlastní zásady Azure AD B2C do doručování softwaru a automatizace řízení kódu. Při nasazování do různých prostředí Azure AD B2C, například dev, test a produkční, doporučujeme odebrat ruční procesy a provést automatizované testování pomocí Azure Pipelines.

Existují tři primární kroky potřebné pro povolení Azure Pipelines ke správě vlastních zásad v rámci Azure AD B2C:

1. Vytvoření registrace webové aplikace v tenantovi Azure AD B2C
1. Konfigurace azure repo
1. Konfigurace kanálu Azure

> [!IMPORTANT]
> Správa vlastních zásad Azure AD B2C pomocí azure pipeline aktuálně `/beta` používá operace **náhledu** dostupné v koncovém bodě rozhraní API Microsoft Graph. Použití těchto api v produkčních aplikacích není podporováno. Další informace naleznete v [odkazu na beta koncový bod rozhraní API rozhraní Microsoft Graph REST](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Požadavky

* [Tenant Azure AD B2C](tutorial-create-tenant.md)a přihlašovací údaje pro uživatele v adresáři s rolí [správce zásad B2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Vlastní zásady](custom-policy-get-started.md) nahrané do vašeho tenanta
* [Aplikace pro správu](microsoft-graph-get-started.md) registrovaná ve vašem tenantovi pomocí zásad oprávnění rozhraní API microsoft *graphu.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)a přístup k [projektu Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Tok udělení pověření klienta

Scénář popsaný zde využívá volání služby ke službě mezi Azure Pipelines a Azure AD B2C pomocí [toku udělení pověření klienta](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)OAuth 2.0 . Tento tok udělení umožňuje webové službě, jako je Azure Pipelines (důvěrný klient) používat vlastní pověření namísto zosobnění uživatele k ověření při volání jiné webové služby (microsoft graph api, v tomto případě). Azure Pipelines získá token neinteraktivně a pak provede požadavky na rozhraní Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registrace aplikace pro úlohy správy

Jak je uvedeno v [požadavky](#prerequisites), budete potřebovat registraci aplikace, která vaše skripty Prostředí PowerShell -- spouštěné Azure Pipelines -- můžete použít pro přístup k prostředkům ve vašem tenantovi.

Pokud již máte registraci aplikace, kterou používáte pro úlohy automatizace, ujistěte se, že byla udělena **Microsoft Graph** > **Policy.ReadWrite.TrustFramework** oprávnění v rámci **oprávnění rozhraní API** registrace aplikace.**Policy** > 

Pokyny k registraci aplikace pro správu najdete v [tématu Správa Azure AD B2C pomocí Microsoft Graphu](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurace azure repo

S registrovanou aplikací pro správu jste připraveni nakonfigurovat úložiště pro soubory zásad.

1. Přihlaste se ke své organizaci Azure DevOps Services.
1. [Vytvořte nový projekt][devops-create-project] nebo vyberte existující projekt.
1. V projektu přejděte na **Repos** a vyberte stránku **Soubory.** Vyberte existující úložiště nebo jej vytvořte pro toto cvičení.
1. Vytvořte složku s názvem *B2CAssets*. Pojmenujte požadovaný zástupný soubor *README.md* a **potvrdíte** soubor. Pokud chcete, můžete tento soubor později odebrat.
1. Přidejte soubory zásad Azure AD B2C do složky *B2CAssets.* Patří sem *soubor TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*a všechny další zásady, které jste vytvořili. Zaznamenejte název souboru každého souboru zásad Azure AD B2C pro pozdější krok (používají se jako argumenty skriptu PowerShellu).
1. Vytvořte složku s názvem *Skripty* v kořenovém adresáři úložiště, pojmenujte zástupný soubor *DeployToB2c.ps1*. V tomto okamžiku soubor nepozavazuj, uděláte to později.
1. Vložte následující skript Prostředí PowerShell do *souboru DeployToB2c.ps1*a pak soubor **potvrďte.** Skript získá token z Azure AD a volá rozhraní Microsoft Graph API k nahrání zásad ve složce *B2CAssets* do vašeho klienta Azure AD B2C.

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

Když je úložiště inicializováno a naplněno vlastními soubory zásad, můžete začít s kanálem vydání.

### <a name="create-pipeline"></a>Vytvoření kanálu

1. Přihlaste se k organizaci Služby Azure DevOps Services a přejděte k projektu.
1. V projektu vyberte **potrubí** > **zprávy nové** > **potrubí**.
1. V části **Vybrat šablonu**vyberte **Prázdná úloha**.
1. Zadejte **název vymezené plochy**, například *DeployCustomPolicies*, a pak zavřete podokno.
1. Vyberte **Přidat artefakt**a v části **Typ zdroje**vyberte Úložiště **Azure**.
    1. Zvolte zdrojové úložiště obsahující složku *Skripty,* kterou jste naplnili skriptem Prostředí PowerShell.
    1. Zvolte **výchozí větev**. Pokud jste v předchozí části vytvořili nové úložiště, je výchozí větev *hlavní*.
    1. Výchozí **verze** nejnovější ho ponechte *ve výchozí větvi*.
    1. Zadejte **alias Zdroje** pro úložiště. Například *policyRepo*. Do názvu aliasu nezahrnejte žádné mezery.
1. Vyberte **Přidat**.
1. Přejmenujte kanál tak, aby odrážel jeho záměr. Například *nasadit vlastní seznam zásad*.
1. Vyberte **Uložit,** chcete-li uložit konfiguraci kanálu.

### <a name="configure-pipeline-variables"></a>Konfigurace proměnných kanálu

1. Vyberte kartu **Proměnné.**
1. V části Proměnné **kanálu** přidejte následující proměnné a nastavte jejich hodnoty podle specifikace:

    | Name (Název) | Hodnota |
    | ---- | ----- |
    | `clientId` | **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali dříve. |
    | `clientSecret` | Hodnota **tajného klíče klienta,** který jste vytvořili dříve. <br /> Změňte typ proměnné na **tajný (vyberte** ikonu zámku). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, kde *váš klient b2c* je název vašeho klienta Azure AD B2C. |

1. Vyberte **Uložit,** chcete-li proměnné uložit.

### <a name="add-pipeline-tasks"></a>Přidání úkolů kanálu

Dále přidejte úlohu k nasazení souboru zásad.

1. Vyberte kartu **Úkoly.**
1. Vyberte **úlohu agenta**a**+** pak vyberte znaménko plus ( ) pro přidání úkolu do úlohy agenta.
1. Vyhledejte a vyberte **prostředí PowerShell**. Nevybírejte "Azure PowerShell", "PowerShell na cílových počítačích" nebo jinou položku prostředí PowerShell.
1. Vyberte nově přidanou **úlohu skriptu prostředí PowerShell.**
1. Pro úlohu Skript prostředí PowerShell zadejte následující hodnoty:
    * **Verze úkolu**: 2.*
    * **Zobrazovaný název**: Název zásady, kterou by měl tento úkol odeslat. Například *B2C_1A_TrustFrameworkBase*.
    * **Typ**: Cesta k souboru
    * **Cesta skriptu**: Vyberte tři tečky (***...***), přejděte do složky *Skripty* a pak vyberte soubor *DeployToB2C.ps1.*
    * **Argumenty:**

        Zadejte následující hodnoty pro **argumenty**. Nahraďte `{alias-name}` aliasem, který jste zadali v předchozí části.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Pokud je například zadaný alias *policyRepo*, řádek argumentu by měl být:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Vyberte **Uložit,** chcete-li úlohu agenta uložit.

Úloha, kterou jste právě přidali, nahraje *jeden* soubor zásad do Azure AD B2C. Než budete pokračovat, ručně spusťte úlohu (**Vytvořit verzi),** abyste zajistili, že bude úspěšně dokončena před vytvořením dalších úkolů.

Pokud se úloha úspěšně dokončí, přidejte úlohy nasazení provedením předchozích kroků pro každý z vlastních souborů zásad. Upravte `-PolicyId` `-PathToFile` hodnoty argumentů a pro každou zásadu.

Jedná `PolicyId` se o hodnotu nalezenou na začátku souboru zásad XML v uzlu TrustFrameworkPolicy. Například `PolicyId` v následující zásady XML je *B2C_1A_TrustFrameworkBase*:

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

Při spouštění agentů a nahrávání souborů zásad se ujistěte, že jsou nahráni v tomto pořadí:

1. *Soubor TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *Soubor SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Rozhraní Identity Experience Framework vynucuje toto pořadí jako struktura souborů je postavena na hierarchickém řetězci.

## <a name="test-your-pipeline"></a>Test kanálu

Testování kanálu vydání:

1. Vyberte **možnost Kanály** a potom **vydání**.
1. Vyberte dříve vytvořený kanál, například *DeployCustomPolicies*.
1. Vyberte **Vytvořit vydání**, pak vyberte **Vytvořit** pro zařazení do fronty.

Měli byste vidět banner oznámení, který říká, že vydání bylo zařazeno do fronty. Chcete-li zobrazit jeho stav, vyberte odkaz v pruhu oznámení nebo jej vyberte v seznamu na kartě **Zprávy.**

## <a name="next-steps"></a>Další kroky

Další informace:

* [Volání mezi službami pomocí pověření klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines

---
title: Vytvoření prostředku Asistivní čtečky
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak vytvořit nový prostředek moderního čtecího zařízení s vlastní subdoménou a potom nakonfigurovat Azure AD v tenantovi Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: b012da0b2aea4a50002e9adbc0876396ddd4b5e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368725"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Vytvoření prostředku pro moderní čtečku a konfigurace ověřování Azure Active Directory

V tomto článku poskytujeme skript, který vytvoří prostředek pro moderní čtečku a nakonfiguruje ověřování Azure Active Directory (Azure AD). Pokaždé, když se vytvoří prostředek pro moderní čtečku, ať už s tímto skriptem, nebo na portálu, musí se taky nakonfigurovat s oprávněními Azure AD. Tento skript vám to pomůže.

Skript je navržený tak, aby se v jednom kroku vytvořily a nakonfigurovali všechny nezbytné moderní čtečky a prostředky Azure AD. Můžete ale taky nakonfigurovat ověřování Azure AD pro existující prostředek pro moderní čtečku, pokud je třeba, že jste už nějakou vytvořili v Azure Portal.

Pro některé zákazníky může být nutné vytvořit více prostředků moderního čtecího zařízení, pro vývoj vs. produkci, případně pro více různých oblastí, ve kterých je vaše služba nasazená. V takových případech se můžete vrátit zpět a pomocí skriptu několikrát vytvořit různé prostředky pro moderní čtečku a nakonfigurujete je s oprávněními Azure AD.

Skript je navržený tak, aby byl flexibilní. Nejprve bude vyhledán existující moderní čtecí zařízení a prostředky Azure AD ve vašem předplatném a vytvářet je pouze v případě potřeby, pokud ještě neexistují. Pokud vytvoříte prostředek pro moderní čtečku poprvé, bude skript provádět všechno, co potřebujete. Pokud ho chcete použít jenom ke konfiguraci služby Azure AD pro existující prostředek moderního čtecího zařízení, který se vytvořil na portálu, provede se to i vy. Dá se použít i k vytvoření a konfiguraci více prostředků pro moderní čtečku.

## <a name="set-up-powershell-environment"></a>Nastavení prostředí PowerShell

1. Začněte otevřením [Azure Cloud Shell](../../cloud-shell/overview.md). Ujistěte se, že je v rozevíracím seznamu vlevo nahoře nastavená Cloud Shell na PowerShell nebo když zadáte `pwsh` .

1. Zkopírujte a vložte následující fragment kódu do prostředí.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Spusťte funkci `Create-ImmersiveReaderResource` , podle potřeby zadejte parametry.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
      -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    | Parametr | Komentáře |
    | --- | --- |
    | SubscriptionName |Název předplatného Azure, které se má použít pro prostředek pro moderní čtečku Aby bylo možné vytvořit prostředek, musíte mít předplatné. |
    | ResourceName |  Musí být alfanumerické a může obsahovat znak-, pokud znak-není prvním nebo posledním znakem. Délka nesmí překročit 63 znaků.|
    | ResourceSubdomain |Pro prostředek pro moderní čtečku je potřeba vlastní subdoména. Subdoména je používána sadou SDK při volání služby moderního čtecího zařízení za účelem spuštění čtecího modulu. Subdoména musí být globálně jedinečná. Subdoména musí být alfanumerická a může obsahovat znak-, pokud znak-není prvním nebo posledním znakem. Délka nesmí překročit 63 znaků. Tento parametr je nepovinný, pokud prostředek již existuje. |
    | ResourceSKU |Možnosti: `S0` . Další informace o jednotlivých dostupných SKU najdete na naší [stránce s cenami Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) . Tento parametr je nepovinný, pokud prostředek již existuje. |
    | ResourceLocation |Možnosti: `eastus` , `eastus2` , `southcentralus` , `westus` , `westus2` , `australiaeast` , `southeastasia` , `centralindia` , `japaneast` , `northeurope` , `uksouth` , `westeurope` . Tento parametr je nepovinný, pokud prostředek již existuje. |
    | ResourceGroupName |Prostředky se vytvářejí ve skupinách prostředků v rámci předplatných. Zadejte název existující skupiny prostředků. Pokud skupina prostředků ještě neexistuje, vytvoří se nový s tímto názvem. |
    | ResourceGroupLocation |Pokud vaše skupina prostředků neexistuje, je nutné dodat umístění, ve kterém chcete skupinu vytvořit. Chcete-li najít seznam umístění, spusťte příkaz `az account list-locations` . Použijte vlastnost *Name* (bez mezer) vráceného výsledku. Tento parametr je nepovinný, pokud vaše skupina prostředků již existuje. |
    | AADAppDisplayName |Zobrazovaný název aplikace Azure Active Directory. Pokud se nenašla existující aplikace Azure AD, vytvoří se nový s tímto názvem. Tento parametr je nepovinný, pokud už aplikace Azure AD existuje. |
    | AADAppIdentifierUri |Identifikátor URI pro aplikaci Azure AD. Pokud se nenašla existující aplikace služby Azure AD, vytvoří se nový s tímto identifikátorem URI. Například, `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Heslo, které vytvoříte, které se později použije k ověření při získání tokenu pro spuštění moderního čtecího zařízení. Heslo musí mít alespoň 16 znaků, musí obsahovat alespoň 1 speciální znak a musí obsahovat alespoň 1 číselný znak. Pokud chcete spravovat tajné klíče klienta aplikace Azure AD po vytvoření tohoto prostředku, přejděte https://portal.azure.com na stránku domů-> Azure Active Directory – > registrace aplikací – > `[AADAppDisplayName]` – > certifikáty a tajné klíče – > pro klienta (viz obrázek "Správa tajných klíčů aplikace Azure AD" na obrázku níže). |
    | AADAppClientSecretExpiration |Datum nebo datum a čas, po kterém `[AADAppClientSecret]` vyprší platnost (např. "2020-12-31T11:59:59 + 00:00" nebo "2020-12-31"). |

    Správa tajných kódů aplikace Azure AD

    ![Okno certifikátů a tajných klíčů na webu Azure Portal](./media/client-secrets-blade.png)

1. Zkopírujte výstup JSON do textového souboru pro pozdější použití. Výstup by měl vypadat nějak takto.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Další kroky

* Podívejte se na úvodní úvodní [Node.js](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) , abyste viděli, co dalšího můžete dělat se sadou pro moderní čtečku pomocí Node.js
* Podívejte se na [kurz pro Android](./tutorial-android.md) , kde najdete další informace, které můžete dělat v sadě moderní čtečky pomocí Java nebo Kotlin pro Android.
* Podívejte se na [kurz pro iOS](./tutorial-ios.md) , kde najdete další informace, které můžete dělat v sadě pro moderní čtečku pomocí SWIFT pro iOS.
* Podívejte se na [kurz Pythonu](./tutorial-python.md) , abyste viděli, co dalšího můžete dělat se sadou moderní čtečky pomocí Pythonu.
* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)
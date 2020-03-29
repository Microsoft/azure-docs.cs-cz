---
title: Vytvoření prostředku Asistivní čtečky
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak vytvořit nový prostředek Immersive Reader s vlastní subdoménou a pak nakonfigurovat Azure AD ve vašem tenantovi Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330715"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Vytvoření prostředku immersive Readeru a konfigurace ověřování azure active directory

V tomto článku poskytujeme skript, který vytvoří prostředek immersive Reader a nakonfiguruje ověřování Azure Active Directory (Azure AD). Pokaždé, když je vytvořen prostředek Immersive Reader, ať už s tímto skriptem nebo na portálu, musí být také nakonfigurované s oprávněními Azure AD. Tento skript vám pomůže s tím.

Skript je navržen tak, aby vytvořit a nakonfigurovat všechny potřebné Immersive Reader a Azure AD prostředky pro vás všechny v jednom kroku. Můžete však také nakonfigurovat ověřování Azure AD pro existující prostředek Immersive Reader, pokud například jste už vytvořili jeden na webu Azure Portal.

Pro některé zákazníky může být nutné vytvořit více prostředků immersive Reader, pro vývoj vs produkčního prostředí nebo možná pro více různých oblastí, ve kterých je vaše služba nasazena. V těchto případech se můžete vrátit a použít skript vícekrát k vytvoření různých prostředků immersive Reader a získat je nakonfigurované s oprávněními Azure AD.

Skript je navržen tak, aby byl flexibilní. Nejprve vyhledá existující immersive Reader a Azure AD prostředky ve vašem předplatném a vytvořit je pouze podle potřeby, pokud ještě neexistují. Pokud je to váš první čas vytvoření immersive Reader zdroj, skript udělá vše, co potřebujete. Pokud chcete použít pouze ke konfiguraci Azure AD pro existující prostředek Immersive Reader, který byl vytvořen na portálu, bude to udělat taky. Lze jej také použít k vytvoření a konfiguraci více prostředků aplikace Immersive Reader.

## <a name="set-up-powershell-environment"></a>Nastavení prostředí Prostředí PowerShellu

1. Začněte otevřením [prostředí Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ujistěte se, že prostředí cloud je nastavena na PowerShell `pwsh`v levém horním rohu nebo zadáním .

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
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
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
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
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

1. Spusťte `Create-ImmersiveReaderResource`funkci a podle potřeby zadejme parametry.

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
    ```

    | Parametr | Komentáře |
    | --- | --- |
    | SubscriptionName |Název předplatného Azure, které se má použít pro prostředek immersive Reader. Chcete-li vytvořit prostředek, musíte mít předplatné. |
    | ResourceName |  Musí být alfanumerická a může obsahovat '-', pokud '-' není první nebo poslední znak. Délka nesmí překročit 63 znaků.|
    | ZdrojSubdoména |Vlastní subdoména je potřebná pro váš prostředek immersive Reader. Subdoména je používána sadou SDK při volání služby Immersive Reader ke spuštění čtečky. Subdoména musí být globálně jedinečná. Subdoména musí být alfanumerická a může obsahovat '-', pokud '-' není první nebo poslední znak. Délka nesmí překročit 63 znaků. Tento parametr je volitelný, pokud prostředek již existuje. |
    | ResourceSKU |Možnosti: `S0`. Další informace o jednotlivých dostupných skladových položkách najdete na [naší stránce s cenami služeb Cognitive Services.](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) Tento parametr je volitelný, pokud prostředek již existuje. |
    | ResourceLocation |`eastus`Možnosti: `eastus2` `southcentralus`, `westus` `westus2`, `australiaeast` `southeastasia`, `centralindia` `japaneast`, `northeurope` `uksouth`, `westeurope`, , , , , . Tento parametr je volitelný, pokud prostředek již existuje. |
    | ResourceGroupName |Prostředky jsou vytvářeny ve skupinách prostředků v rámci předplatných. Zadej název existující skupiny prostředků. Pokud skupina prostředků ještě neexistuje, bude vytvořena nová skupina s tímto názvem. |
    | ResourceGroupLocation |Pokud vaše skupina prostředků neexistuje, je třeba zadat umístění, ve kterém chcete vytvořit skupinu. Chcete-li najít seznam `az account list-locations`umístění, spusťte . Použijte vlastnost *name* (bez mezer) vráceného výsledku. Tento parametr je volitelný, pokud skupina prostředků již existuje. |
    | AADAppDisplayName |Zobrazovaný název aplikace Azure Active Directory. Pokud není nalezena existující aplikace Azure AD, vytvoří se nová aplikace s tímto názvem. Tento parametr je volitelný, pokud aplikace Azure AD již existuje. |
    | AADAppIdentifieruri |Identifikátor URI pro aplikaci Azure AD. Pokud není nalezena existující aplikace Azure AD, vytvoří se nová aplikace s tímto identifikátorem URI. Například, `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Heslo, které vytvoříte a které bude později použito k ověření při získání tokenu ke spuštění immersive Reader. Heslo musí mít alespoň 16 znaků, musí obsahovat alespoň 1 speciální znak a musí obsahovat alespoň 1 číselný znak. |

1. Zkopírujte výstup JSON do textového souboru pro pozdější použití. Výstup by měl vypadat takto.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Další kroky

* Zobrazení [rychlého startu node.js](./quickstart-nodejs.md) zobrazíte další, co dalšího můžete dělat se sadou Immersive Reader SDK pomocí souboru Node.js
* Podívejte se na [kurz v Pythonu](./tutorial-python.md) a zjistěte, co dalšího můžete dělat s sadou Immersive Reader SDK pomocí Pythonu
* Podívejte se na [výukový program Swift,](./tutorial-ios-picture-immersive-reader.md) abyste zjistili, co dalšího můžete dělat s sadou Immersive Reader SDK pomocí swiftu
* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](./reference.md)





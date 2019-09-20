---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 3a6807cc204a5f8a6957bb03cf4dcbaf3611c17c
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148389"
---
## <a name="authenticate-with-azure-active-directory"></a>Ověřování pomocí Azure Active Directory

> [!IMPORTANT]
> V současné době se ověřování podporuje **pouze** rozhraní API pro počítačové zpracování obrazu, Face API, rozhraní API pro analýzu textu a moderní čtečky pomocí Azure Active Directory (AAD).

V předchozích částech jsme vám ukázali, jak ověřit službu Azure Cognitive Services pomocí klíče předplatného s jednou službou nebo více službami. I když tyto klíče poskytují rychlou a jednoduchou cestu k zahájení vývoje, jsou v složitějších scénářích, které vyžadují řízení přístupu na základě rolí. Pojďme se podívat na to, co je potřeba k ověřování pomocí Azure Active Directory (AAD).

V následujících částech budete pomocí prostředí Azure Cloud Shell nebo Azure CLI vytvářet subdomény, přiřazovat role a získat nosný token pro volání Cognitive Services Azure. Pokud se zablokuje, jsou odkazy v jednotlivých oddílech k dispozici se všemi dostupnými možnostmi pro každý příkaz v Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Vytvoření prostředku s vlastní subdoménou

Prvním krokem je vytvoření vlastní subdomény.

1. Začněte otevřením Azure Cloud Shell. pak [Vyberte předplatné](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. Dále [vytvořte prostředek Cognitive Services](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) s vlastní subdoménou. Název subdomény musí být globálně jedinečný a nesmí obsahovat speciální znaky, například: ".", "!", ",".

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. V případě úspěchu by měl **koncový bod** zobrazit název subdomény jedinečný pro váš prostředek.


### <a name="assign-a-role-to-a-service-principal"></a>Přiřazení role k instančnímu objektu

Teď, když máte přidruženou vlastní subdoménu k vašemu prostředku, budete muset přiřadit roli instančnímu objektu.

> [!NOTE]
> Mějte na paměti, že rozšíření role AAD může trvat až pět minut.

1. Nejdřív si zaregistrujeme [aplikaci AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   V následujícím kroku budete potřebovat **ApplicationId** .

2. Dále musíte [vytvořit instanční objekt](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) pro aplikaci AAD.

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Pokud zaregistrujete aplikaci v Azure Portal, bude tento krok dokončen za vás.

3. Posledním krokem je [přiřazení role "Cognitive Services uživatele"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) k instančnímu objektu (vymezenému pro prostředek). Přiřazením role udělíte instančnímu objektu přístup k tomuto prostředku. Stejnému instančnímu objektu můžete udělit přístup k několika prostředkům v rámci vašeho předplatného.
   >[!NOTE]
   > Je použit identifikátor ObjectId objektu služby, nikoli identifikátor ObjectId pro aplikaci.
   > ACCOUNT_ID bude ID prostředku Azure účtu Cognitive Services, který jste vytvořili. ID prostředku Azure můžete najít z části vlastnosti prostředku v Azure Portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Ukázková žádost

V této ukázce se k ověření instančního objektu používá heslo. Poskytnutý token se pak použije k volání rozhraní API pro počítačové zpracování obrazu.

1. Získejte **TenantId**:
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Získání tokenu:
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Zavolejte rozhraní API pro počítačové zpracování obrazu:
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Případně můžete instanční objekt ověřit pomocí certifikátu. Kromě instančního objektu se taky podporuje i objekt zabezpečení, protože má oprávnění delegovaná přes jinou aplikaci AAD. V tomto případě by se uživatelům při získávání tokenu zobrazila výzva k zadání dvojúrovňového ověřování.

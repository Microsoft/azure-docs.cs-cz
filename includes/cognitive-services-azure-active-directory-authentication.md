---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262446"
---
## <a name="authenticate-with-azure-active-directory"></a>Ověřování pomocí Azure Active Directory

> [!IMPORTANT]
> 1. V současné době **pouze** rozhraní API pro počítačové zpracování obrazu, rozhraní API pro rozpoznávání tváří, rozhraní API pro analýzu textu, immersive Reader, nástroj pro rozpoznávání formulářů, detektor anomálií a všechny služby Bing s výjimkou vlastního vyhledávání Bingu podporují ověřování pomocí služby Azure Active Directory (AAD).
> 2. Ověřování AAD je potřeba vždy použít společně s vlastním názvem subdomény vašeho prostředku Azure. [Místní koncové body](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) nepodporují ověřování AAD.

V předchozích částech jsme vám ukázali, jak se ověřit proti Azure Cognitive Services pomocí klíče předplatného s jednou službou nebo s více službami. Zatímco tyto klíče poskytují rychlou a snadnou cestu ke spuštění vývoje, nedosahují ve složitějších scénářích, které vyžadují ovládací prvky přístupu založené na rolích. Podívejme se na to, co je potřeba k ověření pomocí Služby Azure Active Directory (AAD).

V následujících částech použijete buď prostředí Azure Cloud Shell nebo rozhraní příkazového příkazu k Azure, k vytvoření subdomény, přiřazení rolí a získání tokenu nosiče pro volání služeb Azure Cognitive Services. Pokud se zaseknete, odkazy jsou k dispozici v každé části se všemi dostupnými možnostmi pro každý příkaz v Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Vytvoření prostředku s vlastní subdoménou

Prvním krokem je vytvoření vlastní subdomény. Pokud chcete použít existující prostředek služeb Cognitive Services, který nemá vlastní název subdomény, postupujte podle pokynů v [cognitive services vlastní subdomény](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) povolit vlastní subdomény pro váš prostředek.

1. Začněte otevřením prostředí Azure Cloud Shell. Pak [vyberte předplatné](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Dále [vytvořte prostředek služeb Cognitive Services](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) s vlastní subdoménou. Název subdomény musí být globálně jedinečný a nesmí obsahovat speciální znaky, například: ".", "!", ",".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Pokud je úspěšná, **koncový bod** by měl zobrazit název subdomény jedinečný pro váš prostředek.


### <a name="assign-a-role-to-a-service-principal"></a>Přiřazení role instančnímu objektu

Teď, když máte vlastní subdoménu přidruženou k vašemu prostředku, budete muset přiřadit roli instančnímu objektu.

> [!NOTE]
> Mějte na paměti, že šíření přiřazení rolí AAD může trvat až pět minut.

1. Nejprve zaregistrujme [aplikaci AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Budete potřebovat **ApplicationId** v dalším kroku.

2. Dále je třeba [vytvořit instanční objekt](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) pro aplikaci AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Pokud zaregistrujete aplikaci na webu Azure Portal, tento krok se dokončí za vás.

3. Posledním krokem je [přiřazení role "Uživatel kognitivních služeb" k](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) instančnímu objektu služby (vymezený pro prostředek). Přiřazením role udělujete primárnímu přístupu k tomuto prostředku. Můžete udělit stejný přístup k instančnímu objektu pro více prostředků ve vašem předplatném.
   >[!NOTE]
   > ObjectId instančního objektu se používá, nikoli ObjectId pro aplikaci.
   > ACCOUNT_ID bude ID prostředku Azure účtu služeb Cognitive Services, který jste vytvořili. Id prostředku Azure najdete z "vlastností" prostředku na webu Azure Portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Požadavek na ukázku

V této ukázce se k ověření instančního objektu používá heslo. Zadaný token se pak používá k volání rozhraní API pro počítačové zpracování obrazu.

1. Získejte **id tenanta:**
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Získejte žeton:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Zavolejte rozhraní API pro počítačové zpracování obrazu:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Případně instanční objekt služby lze ověřit pomocí certifikátu. Kromě instančního objektu je uživatelský objekt zabezpečení podporován také tím, že oprávnění deleguje prostřednictvím jiné aplikace AAD. V takovém případě místo hesel nebo certifikátů budou uživatelé při získávání tokenu vyzváni k dvoufaktorovému ověřování.

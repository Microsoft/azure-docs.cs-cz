---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: 1085daca153431a28fdcc2583d0e31308214bf91
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563645"
---
## <a name="authenticate-with-azure-active-directory"></a>Ověřování pomocí Azure Active Directory

> [!IMPORTANT]
> 1. V současné době platí **pouze** rozhraní API pro počítačové zpracování obrazu, Face API, rozhraní API pro analýzu textu, moderní čtečka, funkce pro rozpoznávání formulářů, detektor anomálií a všechny služby Bing s výjimkou vlastní vyhledávání Bingu ověřování pomocí Azure Active Directory (AAD).
> 2. Ověřování AAD se musí vždycky používat společně s názvem vlastní subdomény vašeho prostředku Azure. [Regionální koncové body](../articles/cognitive-services/cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) nepodporují ověřování AAD.

V předchozích částech jsme vám ukázali, jak ověřit službu Azure Cognitive Services pomocí klíče předplatného s jednou službou nebo více službami. I když tyto klíče poskytují rychlou a jednoduchou cestu k zahájení vývoje, jsou v složitějších scénářích, které vyžadují řízení přístupu na základě role v Azure (Azure RBAC). Pojďme se podívat na to, co je potřeba k ověřování pomocí Azure Active Directory (AAD).

V následujících částech budete pomocí prostředí Azure Cloud Shell nebo Azure CLI vytvářet subdomény, přiřazovat role a získat nosný token pro volání Cognitive Services Azure. Pokud se zablokuje, jsou odkazy v jednotlivých oddílech k dispozici se všemi dostupnými možnostmi pro každý příkaz v Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Vytvoření prostředku s vlastní subdoménou

Prvním krokem je vytvoření vlastní subdomény. Pokud chcete použít existující prostředek Cognitive Services, který nemá název vlastní subdomény, postupujte podle pokynů v [Cognitive Services vlastní subdomény](../articles/cognitive-services/cognitive-services-custom-subdomains.md#how-does-this-impact-existing-resources) , abyste pro svůj prostředek povolili vlastní subdoménu.

1. Začněte otevřením Azure Cloud Shell. Pak [Vyberte předplatné](/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Dále [vytvořte prostředek Cognitive Services](/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) s vlastní subdoménou. Název subdomény musí být globálně jedinečný a nesmí obsahovat speciální znaky, například: ".", "!", ",".

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. V případě úspěchu by měl **koncový bod** zobrazit název subdomény jedinečný pro váš prostředek.


### <a name="assign-a-role-to-a-service-principal"></a>Přiřazení role k instančnímu objektu

Teď, když máte přidruženou vlastní subdoménu k vašemu prostředku, budete muset přiřadit roli instančnímu objektu.

> [!NOTE]
> Mějte na paměti, že rozšíření přiřazení rolí Azure může trvat až pět minut.

1. Nejdřív si zaregistrujeme [aplikaci AAD](/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   V následujícím kroku budete potřebovat **ApplicationId** .

2. Dále musíte [vytvořit instanční objekt](/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) pro aplikaci AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Pokud zaregistrujete aplikaci v Azure Portal, bude tento krok dokončen za vás.

3. Posledním krokem je [přiřazení role "Cognitive Services uživatele"](/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) k instančnímu objektu (vymezenému pro prostředek). Přiřazením role udělíte instančnímu objektu přístup k tomuto prostředku. Stejnému instančnímu objektu můžete udělit přístup k několika prostředkům v rámci vašeho předplatného.
   >[!NOTE]
   > Je použit identifikátor ObjectId objektu služby, nikoli identifikátor ObjectId pro aplikaci.
   > ACCOUNT_ID bude ID prostředku Azure účtu Cognitive Services, který jste vytvořili. ID prostředku Azure můžete najít z části vlastnosti prostředku v Azure Portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Ukázková žádost

V této ukázce se k ověření instančního objektu používá heslo. Poskytnutý token se pak použije k volání rozhraní API pro počítačové zpracování obrazu.

1. Získejte **TenantId**:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Získání tokenu:
   > [!NOTE]
   > Pokud používáte Azure Cloud Shell, `SecureClientSecret` třída není k dispozici. 

   #### <a name="powershell"></a>[PowerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. Zavolejte rozhraní API pro počítačové zpracování obrazu:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Případně můžete instanční objekt ověřit pomocí certifikátu. Kromě instančního objektu se taky podporuje i objekt zabezpečení, protože má oprávnění delegovaná přes jinou aplikaci AAD. V tomto případě by se uživatelům při získávání tokenu zobrazila výzva k zadání dvojúrovňového ověřování.

## <a name="authorize-access-to-managed-identities"></a>Autorizovat přístup ke spravovaným identitám
 
Cognitive Services podporuje ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../articles/active-directory/managed-identities-azure-resources/overview.md). Spravované identity pro prostředky Azure můžou autorizovat přístup k prostředkům Cognitive Services pomocí přihlašovacích údajů Azure AD z aplikací běžících na virtuálních počítačích Azure, aplikacích funkcí, škálování virtuálních počítačů a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.  

### <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači

Než budete moct použít spravované identity pro prostředky Azure k autorizaci přístupu k prostředkům Cognitive Services z virtuálního počítače, musíte na VIRTUÁLNÍm počítači povolit spravované identity pro prostředky Azure. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v těchto tématech:

- [Azure Portal](../articles/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../articles/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../articles/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../articles/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../articles/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Další informace o spravovaných identitách najdete v tématu [spravované identity pro prostředky Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).
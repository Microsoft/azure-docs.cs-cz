---
title: Ověřování Azure Active Directory (Azure AD)
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace k sadě pro moderní čtečku
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: de6a29e1c4c102aa7d4038185c1635544ba9dfe2
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688798"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Použití ověřování pomocí Azure Active Directory (Azure AD) se službou moderního čtecího zařízení

V následujících částech použijete prostředí Azure Cloud Shell nebo Azure CLI k vytvoření nového prostředku moderního čtecího zařízení s vlastní subdoménou a potom nakonfigurovat Azure AD ve vašem tenantovi Azure. Po dokončení této počáteční konfigurace budete volat službu Azure AD a získat přístupový token podobný tomu, jak se bude provádět při používání sady moderní čtečky. Pokud se dostanete k zablokování, najdete v každé části všechny dostupné možnosti pro jednotlivé příkazy rozhraní příkazového řádku Azure.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Vytvoření prostředku pro moderní čtečku s vlastní subdoménou

1. Začněte otevřením [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Pak [Vyberte předplatné](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Dále [vytvořte prostředek pro moderní čtečku](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) s vlastní subdoménou.

   >[!NOTE]
   > Název subdomény se používá v sadě moderního čtecího rozhraní SDK při spuštění čtecího modulu s funkcí launchAsync.

   -SkuName může být F0 (úroveň Free) nebo S0 (úroveň Standard, ve verzi Public Preview také zdarma). Úroveň S0 má vyšší limit frekvence volání a žádnou měsíční kvótu pro počet volání.

   -Umístění může být kterékoli z následujících: `eastus`, `westus`, `australiaeast`, `centralindia`, `japaneast`, `northeurope`,`westeurope`

   -CustomSubdomainName musí být globálně jedinečné a nesmí obsahovat speciální znaky, například: ".", "!", ",".


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   V případě úspěchu by měl **koncový bod** prostředku zobrazit název subdomény jedinečný pro váš prostředek.

   Zachytíme nově vytvořený objekt prostředku do proměnné **$Resource** , protože se později použije při udělování přístupu k tomuto prostředku.


   >[!NOTE]
   > Pokud vytvoříte prostředek v Azure Portal, použije se jako vlastní subdoména název prostředku. Název subdomény můžete na portálu ověřit tak, že na něm kliknete na stránku Přehled prostředku a v koncovém bodu tam najdete subdoménu, `https://[SUBDOMAIN].cognitiveservices.azure.com/`například. Pokud potřebujete získat subdoménu pro integraci se sadou SDK, můžete to také provést později.

   Pokud se prostředek na portálu vytvořil, můžete teď [získat existující prostředek](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) .

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Přiřazení role k instančnímu objektu

Teď, když máte přidruženou vlastní subdoménu k vašemu prostředku, musíte k instančnímu objektu přiřadit roli.

1. Nejprve [vytvoříme aplikaci Azure AD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > Heslo, označované také jako "tajný klíč klienta", bude použito při získávání ověřovacích tokenů.

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Do proměnné **$aadApp** pro použití v dalším kroku zachytíme nově vytvořený objekt aplikace Azure AD.

2. Dále musíte [vytvořit instanční objekt](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) pro aplikaci služby Azure AD.

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Zachytíme nově vytvořený objekt instančního objektu do proměnné **$Principal** pro použití v dalším kroku.


3. Posledním krokem je [přiřazení role "Cognitive Services uživatele"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) k instančnímu objektu (vymezenému pro prostředek). Přiřazením role udělíte instančnímu objektu přístup k tomuto prostředku. Stejnému instančnímu objektu můžete udělit přístup k několika prostředkům v rámci vašeho předplatného.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Tento krok je potřeba provést pro každý prostředek pro moderní čtečku, který vytvoříte. Pokud například vytvoříte více prostředků pro různé globální oblasti, pak budete muset tento krok provést pro každý z těchto regionálních prostředků, aby ověřování Azure AD fungovalo pro všechny z nich. Nebo pokud vytvoříte nový prostředek později, budete muset tento krok přiřazení role pro tento nový prostředek provést také.


## <a name="obtain-an-azure-ad-token"></a>Získání tokenu Azure AD

V tomto příkladu se heslo používá k ověření instančního objektu pro získání tokenu služby Azure AD.

1. Získejte **TenantId**:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Získání tokenu:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > Sada moderní čtečka SDK používá vlastnost AccessToken tokenu, například $token. AccessToken. Podrobnosti najdete v [referenčních](reference.md) informacích k sadě SDK a [ukázkách](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) kódu.

Případně můžete instanční objekt ověřit pomocí certifikátu. Kromě instančního objektu jsou taky podporované uživatelské objekty a oprávnění delegovaná přes jinou aplikaci Azure AD. V tomto případě, místo hesel nebo certifikátů, se uživatelům při získávání tokenů zobrazí výzva k zadání dvou faktorů ověřování.

## <a name="next-steps"></a>Další postup

* Podívejte se na [kurz](./tutorial.md) , který vám umožní udělat si další informace, které můžete dělat v sadě moderní čtečky.
* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)
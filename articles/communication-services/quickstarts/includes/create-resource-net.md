---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 9a9f8fdda1bc853057f3eb858e85b938357397cd
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886094"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- Nejnovější verze [klientské knihovny .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém.
- Získejte nejnovější verzi [klientské knihovny .NET identity](/dotnet/api/azure.identity?view=azure-dotnet).
- Získejte nejnovější verzi [klientské knihovny pro správu .NET](../../concepts/sdk-options.md).

## <a name="installing-the-client-library"></a>Instalace klientské knihovny

Nejdřív do projektu C# zahrňte klientskou knihovnu pro správu komunikačních služeb:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>ID předplatného

Budete potřebovat znát ID vašeho předplatného Azure. Dá se získat z portálu:

1.  Přihlaste se k účtu Azure.
2.  Výběr předplatných na levém bočním panelu
3.  Vyberte, co je potřeba pro předplatné.
4.  Klikněte na přehled.
5.  Vyberte ID vašeho předplatného.

V tomto rychlém startu budeme předpokládat, že jste uložili ID předplatného v proměnné prostředí s názvem `AZURE_SUBSCRIPTION_ID` .

## <a name="authentication"></a>Authentication

Abyste mohli komunikovat s komunikačními službami Azure, musíte se nejdřív ověřit v Azure. Obvykle to provedete pomocí identity instančního objektu.

### <a name="option-1-managed-identity"></a>Možnost 1: spravovaná identita

Pokud je váš kód spuštěný jako služba v Azure, nejjednodušší způsob ověřování je získání spravované identity z Azure. Přečtěte si další informace o [spravovaných identitách](../../../active-directory/managed-identities-azure-resources/overview.md).

[Služby Azure, které podporují spravované identity](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[Použití spravovaných identit pro App Service a Azure Functions](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Spravovaná identita přiřazená systémem](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Spravovaná identita přiřazená uživatelem](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

ClientId spravované identity, kterou jste vytvořili, je nutné předat `ManagedIdentityCredential` explicitně.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Možnost 2: instanční objekt

Místo používání spravované identity můžete chtít provést ověření v Azure pomocí instančního objektu, který spravujete sami. Další informace najdete v dokumentaci k [vytváření a správě instančního objektu ve službě Azure Active Directory](../../../active-directory/develop/howto-create-service-principal-portal.md).

Po vytvoření instančního objektu budete muset z Azure Portal shromažďovat následující informace:

- **ID klienta**
- **Tajný kód klienta**
- **ID tenanta**

Tyto hodnoty uložte do proměnných prostředí s názvem `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` a v `AZURE_TENANT_ID` uvedeném pořadí. Pak můžete vytvořit klienta pro správu komunikačních služeb takto:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Možnost 3: identita uživatele

Pokud chcete volat Azure jménem interaktivního uživatele místo používání identity služby, můžete k vytvoření klienta pro správu služby Azure Communication Services použít následující kód. Tím se otevře okno prohlížeče s výzvou k zadání přihlašovacích údajů uživatele MSA nebo Azure AD.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Správa prostředků služby Communications Services

### <a name="interacting-with-azure-resources"></a>Interakce s prostředky Azure

Teď, když jste ověřeni, můžete použít klienta pro správu k volání rozhraní API.

Pro každý z následujících příkladů budeme přiřazovat prostředky služby Communications do existující skupiny prostředků.

Pokud potřebujete vytvořit skupinu prostředků, můžete to udělat pomocí [Azure Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) nebo [Azure Resource Manager klientské knihovny](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### <a name="create-and-manage-a-communication-services-resource"></a>Vytvoření a Správa prostředku komunikačních služeb

Naši instanci klienta klientské knihovny pro správu komunikačních služeb ( ``Azure.ResourceManager.Communication.CommunicationManagementClient`` ) je možné použít k provádění operací s prostředky komunikačních služeb.

#### <a name="create-a-communication-services-resource"></a>Vytvoření prostředku Communication Services

Při vytváření prostředku komunikačních služeb zadáte název skupiny prostředků a název prostředku. Všimněte si, že `Location` vlastnost bude vždycky `global` a během veřejné verze Preview `DataLocation` musí být hodnota `UnitedStates` .

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Aktualizace prostředku komunikačních služeb

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Zobrazit seznam všech prostředků služby Communication Services

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Odstranění prostředku komunikačních služeb

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Správa klíčů a připojovacích řetězců

Každý prostředek komunikačních služeb má dvojici přístupových klíčů a odpovídajících připojovacích řetězců. K těmto klíčům se dá dostat pomocí klientské knihovny pro správu a pak je používá v jiných knihovnách klienta komunikačních služeb k ověřování vůči komunikačním službám Azure.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Získání přístupových klíčů pro prostředek komunikačních služeb

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Opětovné generování přístupového klíče pro prostředek komunikačních služeb

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```
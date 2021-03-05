---
title: Použití spravovaných identit ve komunikačních službách (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Spravované identity umožňují autorizovat přístup ke komunikačním službám Azure z aplikací běžících na virtuálních počítačích Azure, aplikacích Function App a dalších prostředcích.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 9571d13537b504b4d48685e879a379b08df3110d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211477"
---
# <a name="use-managed-identities-net"></a>Použití spravovaných identit (.NET)

Začněte s komunikačními službami Azure pomocí spravovaných identit v .NET. Správa komunikačních služeb a klientské knihovny SMS podporují ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

V tomto rychlém startu se dozvíte, jak autorizovat přístup k klientským knihovnám pro správu a SMS z prostředí Azure, které podporuje spravované identity. Také popisuje, jak testovat kód ve vývojovém prostředí.

## <a name="prerequisites"></a>Požadavky

 - Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
 - Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](./create-communication-resource.md?pivots=platform-azp&tabs=windows).
 -  Spravovaná identita. [Vytvořte spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

## <a name="setting-up"></a>Nastavení

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Povolení spravovaných identit na virtuálním počítači nebo ve službě App Service

U prostředků Azure, které autorizujete, by měly být povolené spravované identity. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Aplikační služby](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Přiřazení rolí Azure k Azure Portal

1. Přejděte na Azure Portal.
1. Přejděte do prostředku služby Azure Communication Service.
1. Nabídka přejít na Access Control (IAM) – > + Add-> přidat přiřazení role.
1. Vyberte roli Přispěvatel (Jedná se o jedinou podporovanou roli).
1. Zvolte položku spravovaná identita přiřazená uživatelem (nebo "spravovaná identita přiřazená systémem") a pak vyberte požadovanou identitu. Uložte výběr.

![Spravovaná role identity](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Přiřazení rolí Azure k PowerShellu

Pokud chcete přiřadit role a oprávnění pomocí PowerShellu, přečtěte si téma [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Přidání spravované identity do řešení komunikačních služeb

### <a name="install-the-client-library-packages"></a>Instalace balíčků klientské knihovny

```console
dotnet add package Azure.Identity
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
```

### <a name="use-the-client-library-packages"></a>Použití balíčků klientské knihovny

Přidejte následující `using` direktivy do kódu, abyste mohli použít klientské knihovny Azure identity a Azure Storage.

```csharp
using Azure;
using Azure.Core;
using Azure.Identity;
using Azure.Communication;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

### <a name="create-an-identity-and-issue-a-token"></a>Vytvoření identity a vydání tokenu

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby s Azure Active Directory tokeny a pak použít klienta k vydání tokenu pro nového uživatele:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEndpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
          var identity = identityResponse.Value;
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Odeslání SMS s tokeny Azure Active Directory

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby s Azure Active Directory tokeny a pak pomocí klienta odeslat zprávu SMS:

```csharp
     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o ověřování](../concepts/authentication.md)

Můžete také chtít:

- [Další informace o řízení přístupu na základě role v Azure](../../../articles/role-based-access-control/index.yml)
- [Další informace o službě Azure identity Library pro .NET](/dotnet/api/overview/azure/identity-readme)
- [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)
- [Odeslání zprávy SMS](../quickstarts/telephony-sms/send.md)
- [Další informace o SMS](../concepts/telephony-sms/concepts.md)

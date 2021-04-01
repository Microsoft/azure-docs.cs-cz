---
title: Knihovny pro správu – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o knihovně, kterou můžete použít ke správě oborů názvů a entit Azure Event Hubs v rozhraní .NET.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 74392fbf0b2c0b81898410af8027a4f13fc52b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89013992"
---
# <a name="event-hubs-management-libraries"></a>Knihovny pro správu Event Hubs

Knihovny pro správu služby Azure Event Hubs můžete použít k dynamickému zajišťování Event Hubs oborů názvů a entit. Tato dynamická povaha umožňuje složitá nasazení a scénáře zasílání zpráv, abyste mohli programově určit, jaké entity se mají zřídit. Tyto knihovny jsou aktuálně k dispozici pro rozhraní .NET.

## <a name="supported-functionality"></a>Podporované funkce

* Vytváření, aktualizace a odstraňování oboru názvů
* Vytvoření, aktualizace, odstranění Event Hubs
* Vytvoření, aktualizace, odstranění skupiny uživatelů

## <a name="prerequisites"></a>Požadavky

Chcete-li začít používat Event Hubs knihovny pro správu, je třeba ověřit pomocí Azure Active Directory (AAD). Služba AAD vyžaduje, abyste se ověřili jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytváření instančního objektu najdete v jednom z těchto článků:  

* [Použití Azure Portal k vytvoření aplikace služby Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)

Tyto kurzy vám poskytnou `AppId` (ID klienta), `TenantId` a `ClientSecret` (ověřovací klíč), které se používají k ověřování pomocí knihoven pro správu. Pro skupinu prostředků, na které chcete spustit, musíte mít oprávnění **vlastníka** .

## <a name="programming-pattern"></a>Programovací model

Vzor pro manipulaci s jakýmkoli Event Hubsm prostředkem se řídí společným protokolem:

1. Získejte token z AAD pomocí `Microsoft.IdentityModel.Clients.ActiveDirectory` knihovny.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Vytvořte `EventHubManagementClient` objekt.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Nastavte `CreateOrUpdate` parametry na zadané hodnoty.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Spusťte volání.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Další kroky
* [Ukázka správy .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Reference k Microsoft. Azure. Management. EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

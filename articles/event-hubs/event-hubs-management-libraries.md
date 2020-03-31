---
title: Knihovny pro správu – Azure Event Hubs| Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o knihovně, kterou můžete použít ke správě oborů názvů a entit centra Centra událostí Azure z rozhraní .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60746654"
---
# <a name="event-hubs-management-libraries"></a>Knihovny pro správu Event Hubs

Knihovny správy Centra událostí Azure můžete použít k dynamickému zřizování oborů názvů a entit Centra událostí. Tato dynamická povaha umožňuje komplexní nasazení a scénáře zasílání zpráv, takže můžete programově určit, jaké entity zřídit. Tyto knihovny jsou aktuálně k dispozici pro rozhraní .NET.

## <a name="supported-functionality"></a>Podporovaná funkce

* Vytvoření oboru názvů, aktualizace, odstranění
* Vytváření, aktualizace, odstranění centra událostí
* Vytvoření, aktualizace, odstranění skupiny spotřebitelů

## <a name="prerequisites"></a>Požadavky

Chcete-li začít používat knihovny správy centra událostí, musíte se ověřit pomocí služby Azure Active Directory (AAD). AAD vyžaduje, abyste se ověřili jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytvoření instančního objektu naleznete v jednom z těchto článků:  

* [Vytvoření instančního objektu aplikace a služby Active Directory, který má přístup k prostředkům, použijte portál Azure.](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Tyto kurzy poskytují `AppId` (ID `TenantId`klienta), `ClientSecret` a (ověřovací klíč), které se používají pro ověřování knihovny správy. Musíte mít oprávnění **vlastníka** pro skupinu prostředků, ve které chcete spustit.

## <a name="programming-pattern"></a>Programovací vzor

Vzor pro manipulaci s jakýmkoli prostředkem Centra událostí se řídí běžným protokolem:

1. Získejte token z AAD pomocí knihovny. `Microsoft.IdentityModel.Clients.ActiveDirectory`
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

1. Proveďte volání.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Další kroky
* [Ukázka správy rozhraní .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referenční příručka Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

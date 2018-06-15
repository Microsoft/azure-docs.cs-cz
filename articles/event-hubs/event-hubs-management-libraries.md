---
title: Správa knihovny Azure Event Hubs | Microsoft Docs
description: Správa oborů názvů Event Hubs a entity z rozhraní .NET
services: event-hubs
cloud: na
documentationcenter: na
author: sethmanheim
manager: timlt
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 2ae2f8f2006507284338fb4fa62e4942476cf2bc
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26780969"
---
# <a name="event-hubs-management-libraries"></a>Knihovny správy centra událostí

Knihovny správy Event Hubs můžete dynamicky zajišťují Event Hubs obory názvů a entity. Tento indikovala umožňuje komplexní nasazení a scénáře zasílání zpráv, tak prostřednictvím kódu programu můžete určit, jaké entity zřídit. Tyto knihovny jsou aktuálně dostupné pro rozhraní .NET.

## <a name="supported-functionality"></a>Podporované funkce

* Namespace vytváření, aktualizaci, odstranění
* Vytvoření centra událostí, aktualizaci, odstranění
* Vytvoření skupiny uživatelů, aktualizaci, odstranění

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít používat knihovny správy Event Hubs, je třeba ověřit s Azure Active Directory (AAD). AAD vyžaduje ověřování jako hlavní název služby, která poskytuje přístup k prostředkům Azure. Informace o vytvoření objektu služby najdete v jednom z těchto článků:  

* [Pomocí portálu Azure k vytvoření aplikace Active Directory a objektu služby, které mají přístup k prostředkům](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Tyto kurzy vám poskytují `AppId` (ID klienta), `TenantId`, a `ClientSecret` (ověřovací klíč), které se používají pro ověřování pomocí knihovny správy. Musíte mít **vlastníka** oprávnění pro skupinu prostředků, na kterém chcete spustit.

## <a name="programming-pattern"></a>Vzor programování

Vzor k manipulaci s jakýmikoli prostředky služby Event Hubs dodržuje společný protokol:

1. Získat token pomocí AAD `Microsoft.IdentityModel.Clients.ActiveDirectory` knihovny.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Vytvořte `EventHubManagementClient` objektu.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Nastavte `CreateOrUpdate` parametry pro zadané hodnoty.
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
* [Ukázka správy rozhraní .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Odkaz na Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

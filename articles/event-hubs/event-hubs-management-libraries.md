---
title: Knihovny správy služby Azure Event Hubs | Dokumentace Microsoftu
description: Spravovat obory názvů služby Event Hubs a entity z .NET
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 06/13/2018
ms.author: shvija
ms.openlocfilehash: 1b158fd3ac3f36b71a76c149e8b6815f576be1c5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006325"
---
# <a name="event-hubs-management-libraries"></a>Knihovny pro správu Event Hubs

Knihovny správy služby Azure Event Hubs můžete dynamicky zřizovat obory názvů služby Event Hubs a entity. Tato Dynamická povaha umožňuje složitá nasazení a scénářů zasílání zpráv, takže můžete prostřednictvím kódu programu určit, jaké entity ke zřízení. Tyto knihovny jsou aktuálně dostupné pro .NET.

## <a name="supported-functionality"></a>Podporované funkce

* Namespace vytvoření, aktualizace, odstranění
* Vytvoření centra událostí, aktualizace, odstranění
* Vytvoření skupiny uživatelů, aktualizace, odstranění

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít používat knihovny správy služby Event Hubs, musí ověřit pomocí Azure Active Directory (AAD). AAD vyžaduje ověřování jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytvoření instančního objektu služby najdete v některém z těchto článků:  

* [Pomocí webu Azure portal k vytvoření aplikace Active Directory a instančního objektu, který má přístup k prostředkům](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Tyto kurzy vám poskytují `AppId` (ID klienta), `TenantId`, a `ClientSecret` (ověřovací klíč), které se používají pro ověřování pomocí knihovny pro správu. Musíte mít **vlastníka** oprávnění pro skupinu prostředků, na kterém chcete spustit.

## <a name="programming-pattern"></a>Model programování

Vzor pro manipulaci s prostředek služby Event Hubs následuje běžný protokol:

1. Získání tokenu pomocí AAD `Microsoft.IdentityModel.Clients.ActiveDirectory` knihovny.
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

## <a name="next-steps"></a>Další postup
* [Ukázka rozhraní .NET pro správu](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Odkaz na Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

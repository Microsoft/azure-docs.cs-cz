---
title: Knihovny správy – Služba Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o knihovně, můžete použít ke správě oborů názvů Azure Event Hubs a entit v rozhraní .NET.
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
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53102707"
---
# <a name="event-hubs-management-libraries"></a>Knihovny pro správu Event Hubs

Knihovny správy služby Azure Event Hubs můžete dynamicky zřizovat obory názvů služby Event Hubs a entity. Tato Dynamická povaha umožňuje složitá nasazení a scénářů zasílání zpráv, takže můžete prostřednictvím kódu programu určit, jaké entity ke zřízení. Tyto knihovny jsou aktuálně dostupné pro .NET.

## <a name="supported-functionality"></a>Podporované funkce

* Namespace vytvoření, aktualizace, odstranění
* Vytvoření centra událostí, aktualizace, odstranění
* Vytvoření skupiny uživatelů, aktualizace, odstranění

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít používat knihovny správy služby Event Hubs, musí ověřit pomocí Azure Active Directory (AAD). AAD vyžaduje ověřování jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytvoření instančního objektu služby najdete v některém z těchto článků:  

* [Pomocí webu Azure portal k vytvoření aplikace Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
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

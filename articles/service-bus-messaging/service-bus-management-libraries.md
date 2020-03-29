---
title: Knihovny pro správu Azure Service Bus| Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak používat knihovny správy Služby Azure Service Bus k dynamickému zřizování oborů názvů a entit Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: d0e90d9278ede97de04ad8efeaa59d94a4567f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756262"
---
# <a name="service-bus-management-libraries"></a>Knihovny pro správu služby Service Bus

Knihovny správy Azure Service Bus mohou dynamicky zřit obory názvů service bus a entity. To umožňuje komplexní nasazení a zasílání zpráv scénáře a umožňuje programově určit, jaké entity zřídit. Tyto knihovny jsou aktuálně k dispozici pro rozhraní .NET.

## <a name="supported-functionality"></a>Podporovaná funkce

* Vytvoření oboru názvů, aktualizace, odstranění
* Vytvoření fronty, aktualizace, odstranění
* Tvorba tématu, aktualizace, odstranění
* Vytvoření předplatného, aktualizace, odstranění

## <a name="prerequisites"></a>Požadavky

Chcete-li začít používat knihovny správy service bus, musíte se ověřit pomocí služby Azure Active Directory (Azure AD). Azure AD vyžaduje ověření jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytvoření instančního objektu naleznete v jednom z těchto článků:  

* [Vytvoření instančního objektu aplikace a služby Active Directory, který má přístup k prostředkům, použijte portál Azure.](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure CLI](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Tyto kurzy poskytují `AppId` (ID `TenantId`klienta), `ClientSecret` a (ověřovací klíč), které se používají pro ověřování knihovny správy. Musíte mít oprávnění **vlastníka** pro skupinu prostředků, ve které chcete spustit.

## <a name="programming-pattern"></a>Programovací vzor

Vzor pro manipulaci s jakýmkoli prostředkem služby Service Bus se řídí běžným protokolem:

1. Získejte token ze služby Azure AD pomocí **knihovny Microsoft.IdentityModel.Clients.ActiveDirectory:**
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Vytvořte `ServiceBusManagementClient` objekt:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Nastavte `CreateOrUpdate` parametry na zadané hodnoty:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Provést volání:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Dokončení kódu pro vytvoření fronty
Zde je kompletní kód pro vytvoření fronty service bus: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Úplný příklad najdete v [ukázce správy rozhraní .NET na GitHubu](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Další kroky
[Odkaz na rozhraní API Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)

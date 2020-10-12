---
title: Knihovny pro správu Azure Service Bus | Microsoft Docs
description: Tento článek vysvětluje, jak pomocí Azure Service Bus knihovny pro správu dynamicky zřizovat Service Bus obory názvů a entity.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 915606bffc2037c8fcd1a7d33218143f40c78f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008042"
---
# <a name="service-bus-management-libraries"></a>Knihovny pro správu služby Service Bus

Knihovny pro správu Azure Service Bus můžou dynamicky zřizovat Service Bus obory názvů a entity. Tato možnost umožňuje složitá nasazení a scénáře zasílání zpráv a umožňuje programově určit, které entity se mají zřídit. Tyto knihovny jsou aktuálně k dispozici pro rozhraní .NET.

## <a name="supported-functionality"></a>Podporované funkce

* Vytváření, aktualizace a odstraňování oboru názvů
* Vytvoření, aktualizace a odstranění fronty
* Vytváření, aktualizace a odstraňování tématu
* Vytvoření, aktualizace, odstranění odběru

## <a name="prerequisites"></a>Požadavky

Pokud chcete začít používat Service Bus knihovny pro správu, musíte se ověřit pomocí služby Azure Active Directory (Azure AD). Azure AD vyžaduje, abyste se ověřili jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytváření instančního objektu najdete v jednom z těchto článků:  

* [Použití Azure Portal k vytvoření aplikace služby Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

Tyto kurzy vám poskytnou `AppId` (ID klienta), `TenantId` a `ClientSecret` (ověřovací klíč), které se používají k ověřování pomocí knihoven pro správu. Pro skupinu prostředků, na které chcete spustit, musíte mít minimálně [**Azure Service Bus oprávnění vlastníka dat**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) nebo [**přispěvatele**](../role-based-access-control/built-in-roles.md#contributor) .

## <a name="programming-pattern"></a>Programovací model

Vzor pro manipulaci s jakýmkoli Service Busm prostředkem se řídí společným protokolem:

1. Získání tokenu z Azure AD pomocí knihovny **Microsoft. IdentityModel. clients. Active Directory** :
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
4. Spustit volání:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Úplný kód pro vytvoření fronty
Tady je kompletní kód pro vytvoření Service Bus fronty: 

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
> Úplný příklad najdete v [ukázce .NET Management na GitHubu](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Další kroky
[Referenční informace k rozhraní API Microsoft. Azure. Management. ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
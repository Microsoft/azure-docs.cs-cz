---
title: Programové vytváření Azure Service Bus entit | Microsoft Docs
description: Tento článek vysvětluje, jak dynamicky nebo programově zřizovat Service Bus obory názvů a entity.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539869"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Dynamické zřizování Service Bus oborů názvů a entit 
Knihovny pro správu Azure Service Bus můžou dynamicky zřizovat Service Bus obory názvů a entity. Tato možnost umožňuje složitá nasazení a scénáře zasílání zpráv a umožňuje programově určit, které entity se mají zřídit. Tyto knihovny jsou aktuálně k dispozici pro rozhraní .NET.

## <a name="overview"></a>Přehled
K dispozici jsou tři knihovny pro správu, které můžete použít k vytvoření a správě Service Bus entit. Jsou to tyto:

- [Azure. Messaging. ServiceBus. Administration](#azuremessagingservicebusadministration)
- [Microsoft. Azure. ServiceBus. Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

Všechny tyto balíčky podporují operace vytvoření, získání, vypsání, odstranění, aktualizace, odstranění a aktualizace ve **frontách, tématech a předplatných**. Ale jenom [Microsoft. Azure. Management. ServiceBus](#microsoftazuremanagementservicebus) podporuje operace vytváření, aktualizace, výpisu a získávání a odstraňování na **oborech názvů**, výpisu a opětovném vygenerování klíčů SAS a další. 

Knihovna Microsoft. Azure. Management. ServiceBus funguje jenom s ověřováním Azure Active Directory (Azure AD) a nepodporuje použití připojovacího řetězce. Zatímco ostatní dvě knihovny (Azure. Messaging. ServiceBus a Microsoft. Azure. ServiceBus) podporují použití připojovacího řetězce pro ověřování ve službě a je snazší je použít. Mezi těmito knihovnami je Azure. Messaging. ServiceBus nejnovější a to je to, co doporučujeme použít.

Následující části obsahují další podrobnosti o těchto knihovnách. 

## <a name="azuremessagingservicebusadministration"></a>Azure. Messaging. ServiceBus. Administration
Pomocí třídy [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) v oboru názvů [Azure. Messaging. ServiceBus. Administrative](/dotnet/api/azure.messaging.servicebus.administration) můžete spravovat obory názvů, fronty, témata a odběry. Zde je ukázkový kód. Úplný příklad naleznete v tématu [příklad CRUD](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft. Azure. ServiceBus. Management 
Pomocí třídy [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) v oboru názvů [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management) můžete spravovat obory názvů, fronty, témata a odběry. Zde je ukázkový kód: 

> [!NOTE]
> Doporučujeme použít `ServiceBusAdministrationClient` třídu z `Azure.Messaging.ServiceBus.Administration` knihovny, která je nejnovější sadou SDK. Podrobnosti najdete v [první části](#azuremessagingservicebusadministration). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Tato knihovna je součástí sady SDK ovládacího prvku na základě Azure Resource Manager. 

### <a name="prerequisites"></a>Předpoklady

Pokud chcete začít s používáním této knihovny, musíte se ověřit pomocí služby Azure Active Directory (Azure AD). Azure AD vyžaduje, abyste se ověřili jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytváření instančního objektu najdete v jednom z těchto článků:  

* [Použití Azure Portal k vytvoření aplikace služby Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)

Tyto kurzy vám poskytnou `AppId` (ID klienta), `TenantId` a `ClientSecret` (ověřovací klíč), které se používají k ověřování pomocí knihoven pro správu. Pro skupinu prostředků, na které chcete spustit, musíte mít minimálně [**Azure Service Bus oprávnění vlastníka dat**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) nebo [**přispěvatele**](../role-based-access-control/built-in-roles.md#contributor) .

### <a name="programming-pattern"></a>Programovací model

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

### <a name="complete-code-to-create-a-queue"></a>Úplný kód pro vytvoření fronty
Zde je ukázkový kód pro vytvoření fronty Service Bus. Úplný příklad najdete v [ukázce .NET Management na GitHubu](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

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

## <a name="fluent-library"></a>Knihovna Fluent
Příklad použití knihovny Fluent ke správě Service Bus entit naleznete v [této ukázce](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus). 

## <a name="next-steps"></a>Další kroky
Podívejte se na následující referenční témata: 

- [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)
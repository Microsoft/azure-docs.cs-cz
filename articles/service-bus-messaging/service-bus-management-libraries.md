---
title: Knihovny správy služby Azure Service Bus | Dokumentace Microsoftu
description: Spravujte obory názvů služby Service Bus a entity z .NET pro zasílání zpráv.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: spelluru
ms.openlocfilehash: a959687fbf6e296cab7e0d8ca49ae97a005622cf
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696571"
---
# <a name="service-bus-management-libraries"></a>Knihovny pro správu služby Service Bus

Knihovny správy služby Azure Service Bus můžete dynamicky zřizovat obory názvů služby Service Bus a entity. To umožňuje složitá nasazení a scénáře zasílání zpráv a díky tomu je možné programově určit, jaké entity ke zřízení. Tyto knihovny jsou aktuálně dostupné pro .NET.

## <a name="supported-functionality"></a>Podporované funkce

* Namespace vytvoření, aktualizace, odstranění
* Vytvoření fronty, aktualizace, odstranění
* Vytvoření tématu, aktualizace, odstranění
* Vytvoření předplatného, aktualizace, odstranění

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít používat knihovny správy služby Service Bus, je třeba ověřit pomocí služby Azure Active Directory (Azure AD). Azure AD vyžaduje ověřování jako instanční objekt, který poskytuje přístup k prostředkům Azure. Informace o vytvoření instančního objektu služby najdete v některém z těchto článků:  

* [Pomocí webu Azure portal k vytvoření aplikace Active Directory a instančního objektu, který má přístup k prostředkům](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí Azure PowerShellu](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Vytvoření instančního objektu pro přístup k prostředkům pomocí rozhraní příkazového řádku Azure](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Tyto kurzy vám poskytují `AppId` (ID klienta), `TenantId`, a `ClientSecret` (ověřovací klíč), které se používají pro ověřování pomocí knihovny pro správu. Musíte mít **vlastníka** oprávnění pro skupinu prostředků, na kterém chcete spustit.

## <a name="programming-pattern"></a>Model programování

Vzor pro manipulaci s všechny prostředky služby Service Bus následuje běžný protokol:

1. Získání tokenu z Azure AD používat **Microsoft.IdentityModel.Clients.ActiveDirectory** knihovny:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Vytvořte `ServiceBusManagementClient` objektu:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Nastavte `CreateOrUpdate` parametrů zadaných hodnot:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Spusťte volání:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>Další postup

* [Ukázka správy .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Odkaz na Microsoft.Azure.Management.ServiceBus rozhraní API](/dotnet/api/Microsoft.Azure.Management.ServiceBus)

---
title: Vytváření prostředí integračních služeb (ISEs) s rozhraním REST API aplikací logiky
description: Vytvoření prostředí integrační ch služeb (ISE) pomocí rozhraní API REST logic apps, abyste měli přístup k virtuálním sítím Azure (VNET) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478828"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Vytvoření prostředí integrační služby (ISE) pomocí rozhraní API REST logic apps

Tento článek ukazuje, jak vytvořit [ *prostředí služby integrace* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) prostřednictvím rozhraní API služby Logic Apps REST pro scénáře, kde vaše aplikace logiky a účty integrace potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md). ISE je izolované prostředí, které používá vyhrazené úložiště a další prostředky, které jsou udržovány odděleně od "globální" služby Logic Apps pro více klientů. Toto oddělení také snižuje jakýkoli dopad, který ostatní tenantové Azure může mít na výkon vašich aplikací. ISE také poskytuje vlastní statické IP adresy. Tyto IP adresy jsou oddělené od statických IP adres, které jsou sdíleny aplikacemi logiky ve veřejné službě s více klienty.

ISE můžete také vytvořit pomocí [ukázkové šablony rychlého startu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) nebo pomocí [portálu Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Aplikace logiky, integrované aktivační události, předdefinované akce a konektory, které běží ve vaší službě ISE, používají cenový plán odlišný od cenového plánu založeného na spotřebě. Informace o tom, jak fungují ceny a fakturace pro ises, najdete v tématu [Cenové homodelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps pricing](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Požadavky

* Stejné požadavky a [požadavky pro povolení přístupu pro ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jako při vytváření [služby](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) ISE na webu Azure Portal

* Nástroj, který můžete použít k vytvoření služby ISE voláním rozhraní API služby REST logic apps s požadavkem HTTPS PUT. Můžete například použít [Postman](https://www.getpostman.com/downloads/)nebo můžete vytvořit aplikaci logiky, která provádí tento úkol.

## <a name="send-the-request"></a>Odeslat žádost

Chcete-li vytvořit službu ISE voláním rozhraní REST API logic apps, proveďte tento požadavek HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 verze vyžaduje, abyste vytvořit vlastní HTTP PUT požadavek pro konektory ISE.

Nasazení obvykle trvá do dvou hodin. V některých případě může nasazení trvat až čtyři hodiny. Chcete-li zkontrolovat stav nasazení, vyberte na webu Azure na [panelu](https://portal.azure.com)nástrojů Azure ikonu oznámení, která otevře podokno oznámení.

> [!NOTE]
> Pokud nasazení selže nebo odstraníte ise, Azure může trvat až hodinu před uvolněním podsítí. Toto zpoždění znamená, že budete muset počkat před opětovnou použitím těchto podsítí v jiné ise.
>
> Pokud odstraníte virtuální síť, Azure obvykle trvá až dvě hodiny před uvolněním podsítí, ale tato operace může trvat déle. 
> Při mazání virtuálních sítí se ujistěte, že nejsou stále připojeny žádné prostředky. 
> Viz [Odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Hlavička požadavku

Do hlavičky požadavku uveďte tyto vlastnosti:

* `Content-type`: Nastavte tuto `application/json`hodnotu vlastnosti na .

* `Authorization`: Nastavte tuto hodnotu vlastnosti na token nosiče pro zákazníka, který má přístup k předplatnému Azure nebo skupině prostředků, které chcete použít.

### <a name="request-body-syntax"></a>Syntaxe textu požadavku

Zde je syntaxe těla požadavku, která popisuje vlastnosti, které se mají použít při vytváření služby ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Příklad tělo požadavku

Toto tělo požadavku příklad zobrazuje ukázkové hodnoty:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integračních služeb](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)


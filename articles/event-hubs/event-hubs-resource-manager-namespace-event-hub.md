---
title: Vytvoření služby Azure Event Hubs oboru názvů a příjemce skupiny pomocí šablony | Dokumentace Microsoftu
description: Vytvořte obor názvů služby Event Hubs pomocí centra událostí a příjemce skupiny pomocí šablon Azure Resource Manageru
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 48d41ef4df986f959dfabe04e07552e287fdfcd0
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457049"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Vytvořte obor názvů služby Event Hubs s centrem a příjemce skupiny událostí pomocí šablony Azure Resource Manageru
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs naleznete v tématu [Přehled služby Event Hubs](event-hubs-about.md) a [funkcích služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte Centrum událostí pomocí šablony Azure Resource Manageru. Použijete šablony Azure Resource Manageru k vytvoření oboru názvů typu [Event Hubs](event-hubs-what-is-event-hubs.md)s jedním centrem událostí a jedné skupiny uživatelů. Tento článek ukazuje, jak definovat prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru][Authoring Azure Resource Manager templates].

Úplnou šablonu najdete v článku [šablony skupiny centra a příjemce události] [ Event Hub and consumer group template] na Githubu.

> [!NOTE]
> Nejnovější šablony můžete zkontrolovat tak, že přejdete do galerie [Šablony Azure pro rychlý start][Azure Quickstart Templates] a vyhledáte Event Hubs.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet] []. než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud používáte **prostředí Azure PowerShell** k nasazení šablony Resource Manageru místně, musíte spustit nejnovější verzi prostředí PowerShell k dokončení tohoto rychlého startu. Pokud PowerShell potřebujete nainstalovat nebo upgradovat, přečtěte si téma [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Pokud se rozhodnete nainstalovat a používat **rozhraní příkazového řádku Azure** k nasazení šablony Resource Manageru místně, tento kurz vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte obor názvů služby Event Hubs pomocí centra událostí a skupiny příjemců.

Automaticky spustit nasazení, vyberte na následující tlačítko:

[![Nasazení do Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="define-parameters"></a>Definovat parametry
Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část `Parameters`, která obsahuje všechny hodnoty parametrů. Byste měli definovat parametr pro hodnoty, které se budou lišit podle projektu, které nasazujete nebo podle prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru v šabloně definuje prostředky, které jsou nasazené.

Šablona definuje následující parametry:

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Název oboru názvů Event Hubs, který se má vytvořit.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Název centra událostí vytvořeného v oboru názvů Event Hubs.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Název skupiny uživatelů vytvořené pro Centrum událostí.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

Verze rozhraní API šablony.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="define-resources-to-deploy"></a>Definujte prostředky k nasazení

Vytvoří obor názvů tohoto typu **EventHubs**s centra událostí a skupiny příjemců:

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="azure-powershell"></a>Azure PowerShell
K nasazení prostředků pomocí Azure Powershellu, spusťte následující příkaz:

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
K nasazení prostředků pomocí Azure Powershellu, spusťte následující příkaz:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

Blahopřejeme! Použijete šablony Azure Resource Manageru k vytvoření oboru názvů Event Hubs a centra událostí v daném oboru názvů.

## <a name="next-steps"></a>Další postup

V tomto článku se obor názvů služby Event Hubs a ukázkové aplikace používá k odesílání a příjmu událostí z centra událostí. Podrobné pokyny týkající se odesílání událostí do (nebo) přijímat události z centra událostí najdete v následujících kurzech: 

- **Odesílání událostí do centra událostí**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js ](event-hubs-node-get-started-send.md), [Přejít](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Příjem událostí z centra událostí**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [ Node.js](event-hubs-node-get-started-receive.md), [Přejít](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

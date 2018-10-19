---
title: Automaticky vertikálně navýšit kapacitu jednotek propustnosti centra událostí Azure | Dokumentace Microsoftu
description: Povolte automatické rozšiřování automaticky vertikálně navýšit kapacitu jednotek propustnosti oboru názvů.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 12d8f3764dc52d8e76c3fb4599f6913fc8791f73
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426127"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automaticky vertikálně navýšit kapacitu jednotek propustnosti centra událostí Azure

Azure Event Hubs je vysoce škálovatelná platforma pro streamování dat. V důsledku toho Služba Event Hubs využití často zvětšuje po začali používat službu. Použití je potřeba zvýšit předem [jednotek propustnosti](event-hubs-features.md#throughput-units) zpracovat větší množství přenesených dat a škálovat služby Event Hubs. **Automatické rozšiřování** funkce Event Hubs se automaticky zvětší zvýšením počtu jednotek propustnosti, které bude vyhovovat požadavkům využití. Zvýšení jednotek propustnosti brání omezení scénáře, ve kterém:

* Sazby za příchozí přenos dat překročí sady jednotek propustnosti.
* Žádost o přenosy dat překročí sady jednotek propustnosti.

Služba Event Hubs se zvyšuje propustnost při zvýšení zátěže ze strany nad prahovou hodnotou minimální, bez jakékoli požadavky služeb při selhání s chybami ServerBusy.

## <a name="how-auto-inflate-works"></a>Jak funguje automatické rozšiřování

Event Hubs provoz se řídí [jednotek propustnosti](event-hubs-features.md#throughput-units). Za druhé příchozího přenosu dat a dvakrát šířka odchozích přenosů dat umožňuje jedna jednotka propustnosti 1 MB. Centra událostí úrovně Standard může mít nakonfigurovanou 1 až 20 jednotek propustnosti. Automatické rozšiřování umožňuje začít v malém s jednotkami minimální požadované propustnosti, které zvolíte. Funkce pak automaticky škáluje do maximálního počtu jednotek propustnosti, které potřebujete, v závislosti na nárůst objemu provozu. Automatické rozšiřování poskytuje následující výhody:

- Efektivní škálování mechanismus začít v malém a vertikálně navyšujte kapacitu podle bude vaše firma růst.
- Automatické škálování na zadané horní mez bez omezení problémy.
- Větší kontrolu nad Škálováním, protože můžete určit, kdy a jak velký bude škálování.

## <a name="enable-auto-inflate-on-a-namespace"></a>Povolit automatické rozšiřování oboru názvů

Můžete povolit nebo zakázat automatické rozšiřování na obor názvů služby Event Hubs pomocí jedné z následujících metod:

- [Webu Azure portal](https://portal.azure.com).
- [Šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Povolit automatické rozšiřování prostřednictvím portálu

Při vytváření oboru názvů Event Hubs můžete povolit funkci automatické rozšiřování:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Tato možnost povolená můžete začít v malém s kapacitu jednotek propustnosti a vertikálně navyšujte kapacitu podle využití musí zvýšení. Horní mez pro inflaci nemá vliv na okamžitě ceny, které závisí na počtu použitých jednotek propustnosti za hodinu.

Můžete také povolit automatické rozšiřování pomocí **škálování** možnost v podokně nastavení na portálu:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)


> [!NOTE]
> Pokud použijete automatické rozšiřování konfigurace pro zvýšení jednotek propustnosti, Služba Event Hubs generuje diagnostické protokoly, které vám poskytnou informace o proč a kdy zvýšit propustnost. 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Povolit automatické rozšiřování pomocí šablony Azure Resource Manageru

Povolit automatické rozšiřování při nasazení šablony Azure Resource Manageru. Například nastavit `isAutoInflateEnabled` vlastnost **true** a nastavte `maximumThroughputUnits` až 10. Příklad:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Úplnou šablonu najdete v článku [oboru názvů Event Hubs vytvořit a povolit rozšiřování](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) šablony na Githubu.


## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)


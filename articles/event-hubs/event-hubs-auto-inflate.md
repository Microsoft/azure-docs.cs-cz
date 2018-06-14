---
title: Automaticky škálovat jednotky propustnosti Azure Event Hubs | Microsoft Docs
description: Povolit automatické zvýšilo u oboru názvů automaticky škálování jednotky propustnosti
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 20ee0e6cff2a07cbd62a79799eada5708c7a0f07
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28018605"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automaticky škálovat jednotky propustnosti Azure Event Hubs

Azure Event Hubs je vysoce škálovatelná data streamování platformy. Použití služby Event Hubs jako takový často zvyšuje po spuštění pomocí služby. Takové využití je potřeba zvýšit propustnost předem určený jednotky škálování služby Event Hubs a zpracování větší množství přenesených dat. *Zvýšilo automaticky* funkce služby Event Hubs automatické škálování počtu jednotek propustnosti podle potřeb využití. Zvýšení jednotky propustnosti brání scénáře, ve kterém omezování:

* Datové sazby příchozího překročit jednotky propustnosti sady.
* Sazby žádost o sazbách za odchozí data překročit jednotky propustnosti sady.

## <a name="how-auto-inflate-works"></a>Jak funguje automatické zvýšilo

Je provoz Event Hubs řízená prostřednictvím jednotek propustnosti. Jedna jednotka propustnosti umožňuje 1 MB za sekundu příjem příchozích dat a dvakrát toto množství odchozí. Standardní služba event hubs se dá nakonfigurovat s 1-20 jednotek propustnosti. Automatické zvýšilo umožňuje začněte v malém rozsahu u jednotek minimální požadované propustnost. Funkci pak škáluje automaticky maximálnímu limitu jednotek propustnosti, které potřebujete, v závislosti na zvýšení provozu. Zvýšilo automaticky poskytuje následující výhody:

- Efektivní škálování mechanismus začněte v malém rozsahu a škálovat postupně.
- Automaticky škálovat zadanou horní limit bez omezení problémy.
- Větší kontrolu nad škálování, jak můžete řídit, kdy a jak mnohem škálování.

## <a name="enable-auto-inflate-on-a-namespace"></a>Povolit automatické rozšířené na obor názvů

Můžete povolit nebo zakázat automatické zvýšilo na na obor názvů služby Event Hubs některou z následujících metod:

1. [Portál Azure](https://portal.azure.com).
2. Šablonu Azure Resource Manager.

### <a name="enable-auto-inflate-through-the-portal"></a>Povolit automatické zvýšilo prostřednictvím portálu

Při vytváření na obor názvů služby Event Hubs můžete povolit funkci zvýšilo automaticky:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Když povolíte tuto možnost můžete začněte v malém rozsahu na vaší jednotky propustnosti a škálovat vaše využití stačit zvýšení. Horní limit pro inflační neovlivňuje okamžitě ceny, které závisí na počtu jednotek propustnosti použít za hodinu.

Můžete také povolit automatické zvýšilo pomocí **škálování** možnost v podokně nastavení na portálu:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Povolit automatické zvýšilo pomocí šablony Azure Resource Manager

Můžete povolit zvýšilo automaticky během nasazení šablony Azure Resource Manager. Například nastavit `isAutoInflateEnabled` vlastnost **true** a nastavte `maximumThroughputUnits` do 10.

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

Úplnou šablonu, najdete v článku [centra událostí vytvořit obor názvů a povolit zvýšilo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) šablony na Githubu.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)


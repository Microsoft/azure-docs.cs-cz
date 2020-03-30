---
title: Automatické škálování jednotek propustností – Centra událostí Azure | Dokumenty společnosti Microsoft
description: Povolte automatické nafouknutí v oboru názvů, abyste mohli automaticky vertikálně navýšit kapacitu jednotek propustností.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: dc6edaebebe89b6d4a35ada58d40795f86a935d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264475"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatické škálování jednotek propustností centra událostí Azure
Azure Event Hubs je vysoce škálovatelná platforma pro streamování dat. Jako takové využití centra událostí se často zvyšuje po zahájení používání služby. Takové využití vyžaduje zvýšení předem určené [propustnosti jednotky](event-hubs-scalability.md#throughput-units) pro škálování centra událostí a zpracování větší přenosové rychlosti. Funkce **automatického nafouknutí** centra událostí se automaticky zvětšuje zvýšením počtu jednotek propustností, aby byly splněny potřeby využití. Zvýšení jednotky propustnost zabraňuje omezení scénáře, ve kterém:

* Rychlost příchozího přenosu dat překračuje jednotky nastavené propustnosti.
* Rychlost požadavků na odchozí přenos dat překračuje jednotky nastavené propustnosti.

Služba Event Hubs zvyšuje propustnost při zvýšení zatížení nad minimální prahovou hodnotu, aniž by došlo k selhání požadavků s chybami ServerBusy.

## <a name="how-auto-inflate-works"></a>Jak auto-nafukování funguje

Provoz v Centru událostí je řízen [jednotkami propustností](event-hubs-scalability.md#throughput-units). Jedna jednotka propustnost umožňuje 1 MB za sekundu příchozího přenosu dat a dvojnásobek této částky odchozího přenosu dat. Standardní centra událostí lze konfigurovat s 1-20 propustnými jednotkami. Automatické nafouknutí umožňuje začít v malém s minimální požadovanou propustností jednotek, které si vyberete. Funkce se pak automaticky škáluje na maximální limit jednotek propustností, které potřebujete, v závislosti na zvýšení provozu. Automatické nafouknutí poskytuje následující výhody:

- Efektivní škálovací mechanismus pro spuštění malých a vertikálně navýšit kapacitu, jak budete růst.
- Automaticky škálovat na zadaný horní limit bez problémů s omezením.
- Větší kontrola nad škálováním, protože řídíte, kdy a kolik měřítka.

## <a name="enable-auto-inflate-on-a-namespace"></a>Povolení automatického nafouknutí v oboru názvů

Můžete povolit nebo zakázat automatické nafouknutí v oboru názvů Centra událostí úrovně Standard pomocí jedné z následujících metod:

- Portál [Azure](https://portal.azure.com).
- [Šablona Správce prostředků Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Obory názvů Centra událostí základní vrstvy nepodporují automatické nafouknutí.

### <a name="enable-auto-inflate-through-the-portal"></a>Povolit automatické nafouknutí přes portál


#### <a name="enable-at-the-time-of-creation"></a>Povolit v době vytvoření 
Funkci automatického nafouknutí můžete povolit **při vytváření oboru názvů Event Hubs**:
 
![Povolení automatického nafouknutí při vytváření centra událostí](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

S touto povolenou možností můžete začít v malém prostředí s jednotkami propustností a navýšit kapacitu podle toho, jak se zvýší vaše potřeba využití. Horní limit inflace nemá okamžitý vliv na ceny, což závisí na počtu jednotek propustností použitých za hodinu.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Povolení automatického nafouknutí pro existující centrum událostí
Funkci automatického nafouknutí můžete také povolit a upravit její nastavení pomocí následujících pokynů: 
 
1. Na stránce **Obor názvů centra eventhubů** vyberte v části **Jednotky automatického nafouknutí propustností**vyberte **Zakázáno** .  

    ![Výběr jednotek propustnostna na stránce oboru názvů Centra událostí](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Na stránce **Nastavení měřítka** zaškrtněte políčko **Povolit** (pokud funkce automatického škálování nebyla povolena).

    ![Vybrat povolit](./media/event-hubs-auto-inflate/scale-settings.png)
3. Zadejte **maximální** počet jednotek propustností nebo nastavte hodnotu pomocí posuvníku. 
4. (nepovinné) Aktualizujte **minimální** počet jednotek propustností v horní části této stránky. 


> [!NOTE]
> Když použijete konfiguraci automatického nafouknutí ke zvýšení propustnost jednotky, služba Event Hubs vydává diagnostické protokoly, které poskytují informace o tom, proč a kdy se propustnost zvýšila. Pokud chcete povolit protokolování diagnostiky pro centrum událostí, vyberte **Nastavení diagnostiky** v levé nabídce na stránce Centrum událostí na webu Azure Portal. Další informace najdete v tématu [Nastavení diagnostických protokolů pro centrum událostí Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Povolení automatického nafouknutí pomocí šablony Azure Resource Manager

Můžete povolit automatické nafouknutí během nasazení šablony Azure Resource Manager. Například nastavte `isAutoInflateEnabled` vlastnost na **hodnotu true** a nastavte na hodnotu `maximumThroughputUnits` 10. Například:

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

Kompletní šablonu najdete v [tématu Vytvoření oboru názvů Centra událostí a povolte](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) šablonu nafouknutí na GitHubu.


## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled centra událostí](event-hubs-what-is-event-hubs.md)


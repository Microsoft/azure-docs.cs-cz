---
title: Automatické škálování jednotek propustnosti – Azure Event Hubs | Microsoft Docs
description: Pokud chcete automaticky škálovat jednotky propustnosti, povolte automatickou škálu oboru názvů.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020802"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatické škálování jednotek propustnosti Azure Event Hubs
Azure Event Hubs je vysoce škálovatelná platforma pro streamování dat. V takovém případě se Event Hubs využití často zvětšuje po zahájení používání služby. Takové použití vyžaduje zvýšení škály předem určených [jednotek propustnosti](event-hubs-scalability.md#throughput-units) pro škálování Event Hubs a zpracování větších přenosových rychlostí. Funkce automaticky **rozšířené** Event Hubs se automaticky škáluje tak, že se zvýší počet jednotek propustnosti, aby se splnily požadavky na využití. Zvýšení jednotek propustnosti zabraňuje scénářům omezování, ve kterých:

* Míry příchozího přenosu dat překračují nastavené jednotky propustnosti.
* Míry požadavků na výstup dat přesahují nastavené jednotky propustnosti.

Služba Event Hubs zvyšuje propustnost, když se zatížení zvýší nad minimální prahovou hodnotu, aniž by došlo k selhání požadavků s ServerBusymi chybami.

## <a name="how-auto-inflate-works"></a>Jak funguje automaticky rozplochý

Event Hubs provoz se řídí podle [jednotek propustnosti](event-hubs-scalability.md#throughput-units). Jedna jednotka propustnosti umožňuje 1 MB příchozího přenosu dat za sekundu a dvojnásobek jejich odchozího přenosu. Standardní centra událostí je možné nakonfigurovat s 1-20 jednotek propustnosti. Automatické vypínání vám umožní začít malou s minimálními požadovanými jednotkami propustnosti, které zvolíte. Tato funkce se pak automaticky škáluje na maximální limit jednotek propustnosti, které potřebujete, v závislosti na nárůstu provozu. Automatické vystavení nabízí následující výhody:

- Účinný mechanizmus pro škálování, který se má začít malým a vertikálně narůstat.
- Automaticky se škáluje podle zadaného horního limitu bez omezení problémů.
- Větší kontrola nad škálováním, protože můžete ovládat, kdy a kolik se má škálovat.

## <a name="enable-auto-inflate-on-a-namespace"></a>Povolit automatické rozploché u oboru názvů

Automatickou možnost můžete povolit nebo zakázat na úrovni Standard Event Hubs oboru názvů pomocí některé z následujících metod:

- [Azure Portal](https://portal.azure.com).
- [Šablona Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Obory názvů Event Hubs úrovně Basic nepodporují automatické rozploché.

### <a name="enable-auto-inflate-through-the-portal"></a>Povolit automatické rozvrhnout přes portál


#### <a name="enable-at-the-time-of-creation"></a>Povolit v době vytváření 
**Při vytváření oboru názvů Event Hubs** můžete povolit funkci automatického rozplochení:
 
![Povolit automatické rozploché v době vytváření centra událostí](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Když je tato možnost povolená, můžete začít malým počtem jednotek propustnosti a navýšit škálování podle toho, jak se vaše nároky na využití zvyšují. Horní limit pro inflace přímo neovlivní ceny, což závisí na počtu použitých jednotek propustnosti za hodinu.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Povolit automatické rozplochení pro existující centrum událostí
Můžete také povolit funkci automatické deflace a změnit její nastavení pomocí následujících pokynů: 
 
1. Na stránce **Event Hubs obor názvů** vyberte **zakázáno** v části **automaticky neploché jednotky propustnosti**.  

    ![Vyberte jednotky propustnosti na stránce Event Hubs oboru názvů.](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Na stránce **Nastavení škálování** zaškrtněte políčko **Povolit** (Pokud není zapnutá funkce automatického škálování).

    ![Vybrat Povolit](./media/event-hubs-auto-inflate/scale-settings.png)
3. Zadejte **maximální** počet jednotek propustnosti nebo nastavte hodnotu pomocí posuvníku. 
4. volitelné Aktualizujte **minimální** počet jednotek propustnosti v horní části této stránky. 


> [!NOTE]
> Použijete-li automatickou konfiguraci pro zvýšení jednotek propustnosti, Služba Event Hubs vygeneruje diagnostické protokoly, které vám poskytnou informace o tom, proč a kdy se propustnost zvýšila. Pokud chcete povolit protokolování diagnostiky pro centrum událostí, vyberte v levé nabídce na stránce centra událostí v Azure Portal možnost **nastavení diagnostiky** . Další informace najdete v tématu [Nastavení diagnostických protokolů pro centrum událostí Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Povolit automatické rozploché pomocí šablony Azure Resource Manager

Automatickou možnost můžete povolit při nasazení šablony Azure Resource Manager. Nastavte například `isAutoInflateEnabled` vlastnost na **true** a nastavte `maximumThroughputUnits` na hodnotu 10. Například:

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

Úplnou šablonu najdete v tématu [vytvoření Event Hubs oboru názvů a povolení neploché](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) šablony na GitHubu.


## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled Event Hubs](./event-hubs-about.md)

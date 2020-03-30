---
title: Struktura řídicích panelů Azure | Dokumenty společnosti Microsoft
description: Projděte si strukturu JSON řídicího panelu Azure pomocí ukázkového řídicího panelu. Obsahuje odkaz na vlastnosti prostředků.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/20/2019
ms.author: mblythe
ms.openlocfilehash: 18125e119e7ffdd2f8fa8ca3c5c1b12c8c9a94e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640359"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura řídicích panelů Azure
Tento dokument prochází strukturou řídicího panelu Azure pomocí následujícího řídicího panelu jako příkladu:

![Ukázkový řídicí panel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Vzhledem k tomu, že sdílené [řídicí panely Azure jsou prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), tento řídicí panel může být reprezentován jako JSON.  Následující JSON představuje řídicí panel vizualizovaný výše.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Běžné vlastnosti prostředků

Pojďme rozebrat příslušné části JSON.  Vlastnosti nejvyšší úrovně, __id__, __název__, __typ__, __umístění__a __značky__ vlastnosti jsou sdíleny ve všech typech prostředků Azure. To znamená, že nemají moc co do činění s obsahem řídicího panelu.

### <a name="the-id-property"></a>Vlastnost ID

ID prostředku Azure, s výhradou [konvencí pojmenování prostředků Azure](/azure/architecture/best-practices/resource-naming). Když portál vytvoří řídicí panel, obvykle zvolí ID ve formě identifikátoru GUID, ale při programovém vytváření můžete použít libovolný platný název. 

### <a name="the-name-property"></a>Vlastnost name
Název je segment ID prostředku, který neobsahuje informace o předplatném, typu prostředku nebo skupině prostředků. V podstatě se jedná o poslední segment ID prostředku.

### <a name="the-type-property"></a>Vlastnost type
Všechny řídicí panely jsou typu __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Vlastnost umístění
Na rozdíl od jiných prostředků řídicí panely nemají součást runtime.  Pro řídicí panely umístění označuje primární geografické umístění, které ukládá reprezentaci JSON řídicího panelu. Hodnota by měla být jedním z kódů lokací, které lze načíst pomocí [rozhraní API umístění na prostředku odběry](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Vlastnost tagy
Značky jsou běžnou funkcí prostředků Azure, které umožňují uspořádat prostředek podle dvojice libovolných hodnot názvu. Pro řídicí panely existuje jedna speciální značka nazvaná __skrytý název__. Pokud má řídicí panel tuto vlastnost naplněnou, použije se jako zobrazovaný název řídicího panelu na portálu. Id prostředků Azure nelze přejmenovat, ale značky mohou. Tato značka umožňuje mít pro řídicí panel renamable zobrazovaný název.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Objekt vlastností
Objekt vlastností obsahuje dvě vlastnosti, __objektivy__ a __metadata__. Vlastnost __objektivy__ obsahuje informace o dlaždicích na řídicím panelu.  Vlastnost __metadata__ je k dispozici pro potenciální budoucí funkce.

### <a name="the-lenses-property"></a>Vlastnost objektivy
Vlastnost __objektivy__ obsahuje řídicí panel. Všimněte si, že objektivy objekt v tomto příkladu obsahuje jednu vlastnost s názvem "0". Objektivy jsou koncept seskupení, který není aktuálně implementován v řídicích panelech. Pro tuto chvíli mají všechny řídicí panely tuto jedinou vlastnost na objektu objektivu, opět nazvanou "0".

### <a name="the-lens-object"></a>Objekt objektivu
Objekt pod "0" obsahuje dvě vlastnosti, __pořadí__ a __části__.  V aktuální verzi řídicích panelů je __pořadí__ vždy 0. Vlastnost __parts__ obsahuje objekt, který definuje jednotlivé části (označované také jako dlaždice) na řídicím panelu.

Objekt __parts__ obsahuje vlastnost pro každou část, kde název vlastnosti je číslo. Toto číslo není významné. 

### <a name="the-part-object"></a>Objekt dílu
Každý jednotlivý objekt dílu má __pozici__a __metadata__.

### <a name="the-position-object"></a>Objekt polohy
Vlastnost __pozice__ obsahuje informace o velikosti a umístění dílu vyjádřené jako __x__, __y__, __rowSpan__a __colSpan__. Hodnoty jsou z hlediska jednotek mřížky. Tyto jednotky mřížky jsou viditelné, když je řídicí panel v režimu přizpůsobení, jak je znázorněno zde. Pokud chcete, aby dlaždice měla šířku dvou jednotek mřížky, výšku jedné jednotky mřížky a umístění v levém horním rohu řídicího panelu, pak objekt pozice vypadá takto:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![mřížkové jednotky](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Objekt metadat
Každá část má vlastnost metadata, objekt má pouze jednu požadovanou vlastnost nazvanou __type__. Tento řetězec informuje portál, kterou dlaždici chcete zobrazit. Náš příklad řídicího panelu používá tyto typy dlaždic:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`– Používá se k zobrazení metrik monitorování
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`- Používá se k zobrazení s textem nebo obrázky se základním formátováním pro seznamy, odkazy, atd.
1. `Extension[azure]/HubsExtension/PartType/VideoPart`– Používá se k zobrazení videí z YouTube, Channel9 a jakéhokoli jiného typu videa, které funguje ve značce videa HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`– Slouží k zobrazení názvu a stavu virtuálního počítače Azure.

Každý typ dílu má svou vlastní konfiguraci. Možné vlastnosti konfigurace se nazývají __vstupy__, __nastavení__a __datový zdroj__. 

### <a name="the-inputs-object"></a>Objekt inputs
Objekt inputs obvykle obsahuje informace, které svážují dlaždici s instancí prostředku.  Část virtuálního počítače v našem ukázkovém řídicím panelu obsahuje jediný vstup, který používá ID prostředku Azure k vyjádření vazby.  Tento formát ID prostředků je konzistentní ve všech prostředcích Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Část grafu metriky obsahuje jeden vstup, který vyjadřuje prostředek, na který se má vážit, a také informace o zobrazených metrikách. Tady je vstup pro dlaždici, která zobrazuje metriky Network In a Network Out.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>Objekt nastavení
Objekt nastavení obsahuje konfigurovatelné prvky dílu.  V našem ukázkovém řídicím panelu používá část Markdown nastavení pro uložení vlastního obsahu markdownu a konfigurovatelný název a titulky.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Podobně má dlaždice videa vlastní nastavení, která obsahují ukazatel na video, které chcete přehrát, nastavení automatického přehrávání a volitelné informace o nadpisu.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Objekt datového zdroje
Dlaždice, které jsou vázány na prvotřídní spravovatelné objekty portálu (nazývané datové zdroje), mají tento vztah vyjádřený prostřednictvím objektu asset.  V našem příkladu řídicího panelu obsahuje dlaždice virtuálního počítače tento popis datového zdroje.  Vlastnost __idInputName__ informuje portál, že vstup ID obsahuje jedinečný identifikátor pro datový zdroj, v tomto případě ID prostředku. Většina typů prostředků Azure má prostředky definované na portálu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`

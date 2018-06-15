---
title: Struktura řídicí panely Azure | Microsoft Docs
description: Tento článek vysvětluje strukturu JSON řídicího panelu Azure
services: azure-portal
documentationcenter: ''
author: adamab
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: fa50b31a0c8e9077658106039c4fbd2eef8b4367
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165175"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura Azure řídicí panely
Tento dokument vás provede strukturu Azure řídicí panel jako příklad použijeme řídicím panelu následující:

![ukázkový řídicí panel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Vzhledem k tomu, že sdílené [Azure řídicí panely jsou prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), tento řídicí panel může být reprezentován jako JSON.  Následujícím kódu JSON představuje řídicím panelu vizualizována výše.

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

## <a name="common-resource-properties"></a>Běžné vlastnosti prostředku

Umožňuje rozdělení v příslušných částech formátu JSON.  Nejvyšší úrovně vlastnosti __id__, __název__, __typ__, __umístění__, a __značky__ vlastnosti sdílet mezi všechny typy prostředků Azure. To znamená nemají mnohem dělat s obsah řídicího panelu.

### <a name="the-id-property"></a>Vlastnost id

Id prostředku Azure, předmět k [prostředků Azure konvence vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Když portál vytvoří řídicí panel obecně zvolí id ve formátu guid, ale můžete použít libovolný platný název při jejich vytváření prostřednictvím kódu programu. 

### <a name="the-name-property"></a>Vlastnost názvu
Název je segmentu prostředku Id, která nezahrnuje předplatné, typ prostředku nebo informace o skupině prostředků. Je v podstatě poslední segment ID prostředku.

### <a name="the-type-property"></a>Vlastnost type
Všechny řídicí panely jsou typu __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Vlastnosti umístění
Na rozdíl od jiných prostředků nemáte řídicí panely komponenty modulu runtime.  Pro řídicí panely Určuje umístění primárního zeměpisného umístění, která ukládá reprezentace JSON řídicího panelu. Hodnota musí být jeden z umístění kódy, které můžete načíst pomocí [umístění rozhraní API na prostředku odběry](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Vlastnost značky
Značky jsou běžnou funkcí prostředků Azure, které umožňují uspořádat podle páry hodnota libovolný název prostředku. Pro řídicí panely, je jednu s názvem speciální značky __skryté název__. Pokud váš řídicí panel je tato vlastnost vyplněný, je použít jako zobrazovaný název pro tento řídicí panel portálu. Azure ID prostředku nelze přejmenovat, ale můžete značky. Tato značka poskytuje způsob, jak mít renamable zobrazovaný název pro tento řídicí panel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Objekt vlastností
Objekt vlastnosti obsahuje dvě vlastnosti __přehledů__ a __metadata__. __Přehledů__ vlastnost obsahuje informace o dlaždice (také známa jako části) na řídicím panelu.  __Metadata__ vlastnost existuje pro potenciální budoucí funkce.

### <a name="the-lenses-property"></a>Vlastnost přehledů
__Přehledů__ vlastnost obsahuje řídicí panel. Všimněte si, že přehledů objekt v tomto příkladu obsahuje jednu vlastnost s názvem "0". Přehledů jsou konceptu seskupení, které v současnosti není implementovaná v řídicí panely. Prozatím se všechny řídicí panely mají tato vlastnost v objektu přehledu, znovu, názvem "0".

### <a name="the-lens-object"></a>Objekt přehledu
Objekt pod "0" obsahuje dvě vlastnosti __pořadí__ a __částí__.  V aktuální verzi řídicí panely __pořadí__ je vždy 0. __Částí__ vlastnost obsahuje objekt, který definuje jednotlivé součásti (také známa jako dlaždice) na řídicím panelu.

__Částí__ objekt obsahuje vlastnost pro každou část, kde název vlastnosti je číslo. Toto číslo není důležité. 

### <a name="the-part-object"></a>Část objektu
Každý objekt jednotlivých součástí má __pozice__, a __metadata__.

### <a name="the-position-object"></a>Objekt pozice
__Pozice__ vlastnost obsahuje informace o velikosti a umístění pro část vyjádřený jako __x__, __y__, __rowSpan__a __colSpan__. Hodnoty jsou z hlediska jednotky mřížky. Tyto jednotky mřížky jsou viditelné, když řídicího panelu je v režimu přizpůsobení, jak je vidět tady. Pokud chcete na dlaždici tak, aby měl šířku dvě jednotky mřížky, výška jeden mřížky jednotky a umístění v horní části levého horního rohu řídicího panelu a pozice obejct vypadá takto:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![jednotky mřížky](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Objekt metadat
Každá část má vlastnost metadata, objekt je jenom jeden požadovaná vlastnost s názvem __typu__. Tento řetězec informuje portálu které dlaždice zobrazíte. Řídicí panel náš příklad používá tyto typy dlaždice:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Používá k zobrazení, sledování metrik
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Používá k označení pomocí textu nebo obrázků s základní formátování pro seznamy, odkazy, atd.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Umožňuje zobrazit videa z YouTube, Channel9 a žádný jiný druh video, které lze použít v video značky jazyka html.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Umožňuje zobrazují název a stav virtuálního počítače Azure.

Každý typ části má svou vlastní konfiguraci. Vlastnosti možné konfigurace se označují jako __vstupy__, __nastavení__, a __asset__. 

### <a name="the-inputs-object"></a>Objekt vstupy
Objekt vstupy obvykle obsahuje informace, která se sváže s instance prostředku na dlaždici.  Virtuální počítač součástí naší ukázkový řídicí panel obsahuje jeden vstup, který používá id prostředku Azure express vazby.  Tento formát id prostředku je konzistentní napříč všechny prostředky Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Část metriky graf má jeden vstup, která vyjadřuje prostředků k vytvoření vazby, a také informace o metric(s) zobrazení. Zde je vstupem pro dlaždici, která se zobrazuje metriky sítě a sítě výstupy.

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
Objekt nastavení obsahuje konfigurovat elementy součástí.  V našem ukázkový řídicí panel část Markdownu používá nastavení pro ukládání obsahu a také konfigurovat nadpis a podnadpis vlastní markdownu.

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

Podobně dlaždici video má vlastní nastavení, které obsahují ukazatel na video k přehrání, automatické přehrávání disků nastavení a volitelný název informace.

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

### <a name="the-asset-object"></a>Objekt asset
Dlaždice, které jsou vázány na první spravovatelné objekty portálu (nazývané prostředků) mají tuto relaci, vyjádřené prostřednictvím objektu asset.  V našem příkladu řídicího panelu dlaždici virtuální počítač obsahuje popis tohoto prostředku.  __IdInputName__ vlastnost informuje portálu, že id vstup obsahuje jedinečný identifikátor pro prostředek, v tomto případě je id prostředku. Většina Azure typy prostředků mít prostředky, které jsou definovány v portálu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
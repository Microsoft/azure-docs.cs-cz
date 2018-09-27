---
title: Struktura řídicích panelů Azure | Dokumentace Microsoftu
description: Tento článek vysvětluje strukturu JSON řídicím panelu Azure
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: cwatson
ms.openlocfilehash: 405e0d5184880a00c07de55bd968210fa28e45fc
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393073"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura řídicích panelů Azure
Tento dokument vás provede struktura řídicí panel Azure, jako příklad použijeme následující řídicí panel:

![ukázkový řídicí panel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Protože sdílené [řídicích panelů Azure jsou prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), tento řídicí panel můžou být vyjádřeny jako JSON.  Následující kód JSON představuje řídicí panel vizualizovat výše.

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

## <a name="common-resource-properties"></a>Společné vlastnosti prostředku

Podívejme se na dolů na relevantní části kódu JSON.  Vlastnosti nejvyšší úrovně __id__, __název__, __typ__, __umístění__, a __značky__ jsou vlastnosti sdílet napříč všemi typy prostředků Azure. To znamená že nemají dělat s obsahem řídicí panel v podstatě.

### <a name="the-id-property"></a>Vlastnost id

Id prostředku Azure, může [názvů prostředků Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Když portál vytvoří řídicí panel obecně zvolí id ve formě identifikátoru guid, ale můžete libovolně udělovat použít libovolný platný název při vytváření je prostřednictvím kódu programu. 

### <a name="the-name-property"></a>Název vlastnosti
Název je segment prostředku Id, která nezahrnuje předplatné, typ prostředku nebo informace o skupině prostředků. Je v podstatě poslední segment id prostředku.

### <a name="the-type-property"></a>Vlastnost type
Všechny řídicí panely jsou typu __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Vlastnost location
Na rozdíl od jiných prostředků nemáte řídicí panely součásti modulu runtime.  Pro řídicí panely Určuje umístění primárního zeměpisného umístění, která ukládá reprezentaci JSON řídicí panel. Hodnota musí být jeden z umístění kódů, které můžete načíst pomocí [umístění rozhraní API pro prostředek předplatná](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Vlastnost tags
Značky jsou to běžná funkce prostředky Azure, které umožňují uspořádat vaše prostředky pomocí dvojice libovolný název. Pro řídicí panely, je speciální značka volá __skrytý název__. Pokud má tato vlastnost naplní řídicí panel, použije se jako název zobrazení řídicího panelu na portálu. Nelze přejmenovat ID prostředků Azure, ale můžete značky. Tato značka poskytuje způsob, jak renamable zobrazovaný název pro tento řídicí panel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Objekt vlastností
Objekt vlastností obsahuje dvě vlastnosti __přehledů__ a __metadat__. __Přehledů__ vlastnost obsahuje informace o dlaždice (označovaný také jako na řídicím panelu částí).  __Metadat__ vlastnost existuje pro potenciální budoucí funkce.

### <a name="the-lenses-property"></a>Vlastnost přehledů
__Přehledů__ vlastnost obsahuje řídicí panel. Všimněte si, že procházení objektů v tomto příkladu obsahuje jednu vlastnost s názvem "0". Procházení jsou koncept seskupení, které v současnosti není implementovaná v řídicích panelech. Prozatím se všechny řídicí panely mají této jedné vlastnosti oddílu objektu znovu, "0".

### <a name="the-lens-object"></a>Objekt lens
Objekt pod ní "0" obsahuje dvě vlastnosti __pořadí__ a __částí__.  V aktuální verzi řídicích panelů __pořadí__ je vždy 0. __Částí__ vlastnost obsahuje objekt, který definuje jednotlivé součásti (označovaný také jako dlaždice) na řídicím panelu.

__Částí__ objekt obsahuje vlastnosti pro každou část, kde název vlastnosti je číslo. Toto číslo není důležité. 

### <a name="the-part-object"></a>Část objektu
Každý objekt jednotlivé části má __pozice__, a __metadat__.

### <a name="the-position-object"></a>Pozice objektu
__Pozice__ vlastnost obsahuje informace o velikost a umístění pro části vyjádřený jako __x__, __y__, __rowSpan__a __colSpan__. Hodnoty jsou z hlediska jednotky mřížky. Tyto jednotky mřížky jsou viditelná, když řídicí panel je v režimu přizpůsobení, jak je znázorněno zde. Pokud chcete dlaždici mít šířku dvě jednotky mřížky, výška jedné mřížce jednotky a umístění v horní části levém horním rohu řídicího panelu potom obejct pozice vypadá takto:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![jednotky mřížky](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Objekt metadat
Každá část má vlastnost metadat, objekt obsahuje pouze jeden požadovaná vlastnost s názvem __typ__. Tento řetězec říká portálu dlaždice k zobrazení. Náš příklad řídicího panelu používá tyto typy dlaždic:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Slouží k zobrazení monitorování metrik
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Slouží k zobrazení textu nebo obrázků pomocí základní formátování pro seznamy, odkazy, atd.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Používá k zobrazení videa z YouTube, webu Channel 9 a jakýkoli jiný typ video, které funguje ve videu značky jazyka html.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Umožňuje zobrazit název a stav virtuálního počítače Azure.

Každý typ část má svůj vlastní konfigurace. Vlastnosti konfigurace je to možné, se nazývají __vstupy__, __nastavení__, a __asset__. 

### <a name="the-inputs-object"></a>Objekt vstupy
Objekt vstupy obvykle obsahuje informace, které sváže dlaždice instance prostředku.  Část virtuálních počítačů v našem ukázkový řídicí panel obsahuje jeden vstup, který používá id prostředku Azure vyjádřit vazby.  Tento formát id prostředků je konzistentní napříč všechny prostředky Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Část grafu metrik obsahuje jeden vstup, který vyjadřuje prostředek, který chcete přiřadit, a také informace o počet metrik: zobrazení. Tady je vstup pro dlaždici, která se zobrazuje metriky příchozí a odchozí síťový provoz.

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
Objekt nastavení obsahuje prvky konfigurovatelné části.  V našem řídicím panelu ukázka Markdownu část používá nastavení k ukládání obsahu a také konfigurovat nadpis a podnadpis vlastní markdown.

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

Podobně dlaždici s videem má svůj vlastní nastavení, které obsahují ukazatele na začne přehrávat příslušné video, nastavení automatického přehrávání a volitelný popisek informace.

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

### <a name="the-asset-object"></a>Objekt prostředku
Dlaždice, které jsou vázány na prvotřídní spravovatelné objekty portálu (označované jako prostředky) mají tuto relaci vyjádřen prostřednictvím objektu asset.  V náš příklad řídicího panelu dlaždice virtuálního počítače obsahuje popis tohoto prostředku.  __IdInputName__ vlastnost říká na portálu, že id vstup obsahuje jedinečný identifikátor pro prostředek v tomto případě je id prostředku. Většinu služeb Azure typy prostředků máte prostředky, které jsou definované na portálu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
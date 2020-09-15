---
title: Struktura řídicích panelů Azure | Microsoft Docs
description: Projděte si strukturu JSON řídicího panelu Azure pomocí ukázkového řídicího panelu. Obsahuje odkaz na vlastnosti prostředku.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/20/2019
ms.author: mblythe
ms.openlocfilehash: b77c9cfd6e4d1721839acb9db5469b5f0ac73a48
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561597"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura řídicích panelů Azure
Tento dokument projde strukturu řídicího panelu Azure pomocí následujícího řídicího panelu jako příklad:

![Ukázkový řídicí panel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Vzhledem k tomu, že sdílené [řídicí panely Azure jsou prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), tento řídicí panel je možné ZNÁZORNIT jako JSON.  Následující kód JSON reprezentuje řídicí panel, který je uveden výše.

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

## <a name="common-resource-properties"></a>Obecné vlastnosti prostředku

Pojďme rozdělit relevantní části JSON.  Vlastnosti nejvyšší úrovně, __ID__, __název__, __typ__, __umístění__a __značky__ se sdílejí mezi všemi typy prostředků Azure. To znamená, že se s obsahem řídicího panelu nemusejí dělat mnohem.

### <a name="the-id-property"></a>Vlastnost ID

ID prostředku Azure, se kterým se vztahují zásady [vytváření názvů prostředků Azure](/azure/architecture/best-practices/resource-naming). Když portál vytvoří řídicí panel, obvykle zvolí ID ve formě identifikátoru GUID, ale při jejich vytváření programově můžete použít libovolný platný název. 

### <a name="the-name-property"></a>Vlastnost Name
Název je segmentem ID prostředku, které neobsahuje informace o předplatném, typu prostředku ani skupině prostředků. V podstatě je to poslední segment ID prostředku.

### <a name="the-type-property"></a>Vlastnost Type
Všechny řídicí panely jsou typu __Microsoft. Portal/řídicích panelů__.

### <a name="the-location-property"></a>Vlastnost Location
Na rozdíl od jiných prostředků řídicí panely nemají komponentu modulu runtime.  V případě řídicích panelů označuje umístění hlavní geografické umístění, ve kterém je uložený reprezentace JSON řídicího panelu. Hodnota by měla být jeden z kódů umístění, které je možné načíst pomocí [rozhraní API umístění v prostředku předplatných](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Vlastnost značky
Značky jsou běžnou funkcí prostředků Azure, které umožňují organizovat prostředky pomocí dvojic libovolných hodnot názvů. U řídicích panelů existuje jedna speciální značka s názvem __skrytý – název__. Pokud je tato vlastnost nastavená na řídicím panelu, použije se jako zobrazovaný název řídicího panelu na portálu. ID prostředků Azure nejde přejmenovat, ale značky můžou. Tato značka poskytuje způsob, jak mít renamable zobrazovaný název pro řídicí panel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Objekt Properties
Objekt Properties obsahuje dvě vlastnosti, __rozptylová skla__ a __metadata__. Vlastnost __čočky__ obsahuje informace o dlaždicích na řídicím panelu.  Vlastnost __metadata__ je pro možné budoucí funkce.

### <a name="the-lenses-property"></a>Vlastnost čočky
Vlastnost __čočky__ obsahuje řídicí panel. Všimněte si, že objekt čočky v tomto příkladu obsahuje jednu vlastnost s názvem "0". Rozptylová skla jsou koncept seskupení, který není v současnosti implementován v řídicích panelech. V současné době všechny řídicí panely mají tuto jedinou vlastnost objektu objektivu (s názvem "0").

### <a name="the-lens-object"></a>Objekt čočky
Objekt pod "0" obsahuje dvě vlastnosti, __pořadí__ a __části__.  V aktuální verzi řídicích panelů je __objednávka__ vždycky 0. Vlastnost __Parts__ obsahuje objekt, který definuje jednotlivé části (také označované jako dlaždice) na řídicím panelu.

Objekt __Parts__ obsahuje vlastnost pro každou část, kde název vlastnosti je číslo. Toto číslo není významné. 

### <a name="the-part-object"></a>Objekt součásti
Každý objekt jednotlivé součásti má __pozici__a __metadata__.

### <a name="the-position-object"></a>Objekt pozice
Vlastnost __Position__ obsahuje informace o velikosti a umístění pro část vyjádřené jako __x__, __y__, __RowSpan__a __ColSpan__. Hodnoty jsou v jednotkách mřížky. Tyto jednotky mřížky se zobrazí, když je řídicí panel v režimu přizpůsobení, jak je znázorněno zde. Chcete-li, aby dlaždice měla šířku dvou jednotek mřížky, výšku jedné jednotky mřížky a umístění v levém horním rohu řídicího panelu, bude objekt pozice vypadat takto:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![Snímek obrazovky ukazuje zavření mřížky, kde je zvýrazněna jedna Čtvercová mřížka.](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Objekt metadat
Každá část má vlastnost metadat, objekt má pouze jednu povinnou vlastnost nazvanou __typ__. Tento řetězec instruuje portál, který má zobrazit dlaždici. Náš ukázkový řídicí panel používá tyto typy dlaždic:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Používá se k zobrazení metrik monitorování.
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Slouží k zobrazení textu nebo obrázků se základním formátováním pro seznamy, odkazy atd.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Používá se k zobrazení videí z YouTube, channel9 a jakéhokoli jiného typu videa, které funguje ve značce HTML video.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Slouží k zobrazení názvu a stavu virtuálního počítače Azure.

Každý typ součásti má svou vlastní konfiguraci. Možné vlastnosti konfigurace se nazývají __vstupy__, __Nastavení__a __assety__. 

### <a name="the-inputs-object"></a>Objekt Inputs
Objekt Inputs obecně obsahuje informace, které vážou dlaždici s instancí prostředků.  Část virtuálního počítače na našem řídicím panelu ukázky obsahuje jeden vstup, který k vyjádření vazby používá ID prostředku Azure.  Tento formát ID prostředku je konzistentní napříč všemi prostředky Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Část grafu metriky obsahuje jeden vstup, který vyjadřuje prostředek, ke kterému se má vytvořit vazba, a informace o metrikách, které se zobrazují. Tady je vstup pro dlaždici, na které se zobrazuje síť a metrika sítě.

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
Objekt nastavení obsahuje konfigurovatelné prvky součásti.  Na našem ukázkovém řídicím panelu používá část Markdownu nastavení k uložení vlastního obsahu Markdownu a také konfigurovatelného názvu a podnadpisu.

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

Podobně dlaždice video má vlastní nastavení, které obsahuje ukazatel na video, které se má přehrát, nastavení automatického přehrávání a volitelné informace o názvu.

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

### <a name="the-asset-object"></a>Objekt Asset
Dlaždice, které jsou vázány na první třídu spravovatelné objekty portálu (nazývané assety), mají tento vztah vyjádřen prostřednictvím objektu Asset.  Na našem řídicím panelu obsahuje dlaždice virtuálního počítače tento popis prostředku.  Vlastnost __idInputName__ přikáže portálu, že vstup ID obsahuje jedinečný identifikátor pro Asset, v tomto případě ID prostředku. Většina typů prostředků Azure má prostředky definované na portálu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`

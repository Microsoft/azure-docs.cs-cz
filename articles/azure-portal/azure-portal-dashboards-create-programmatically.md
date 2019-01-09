---
title: Vytváření řídicích panelů Azure prostřednictvím kódu programu | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak vytváření řídicích panelů Azure prostřednictvím kódu programu.
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
ms.openlocfilehash: ed3737b2f64fb4aad3f1418f08e909bf720b42f3
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103838"
---
# <a name="programmatically-create-azure-dashboards"></a>Vytváření řídicích panelů Azure prostřednictvím kódu programu

Tento dokument vás provede procesem programové vytváření a publikování řídicích panelů Azure. Řídicí panel najdete níž je odkazováno v celém dokumentu.

![ukázkový řídicí panel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Přehled

Sdílené řídicí panely v Azure jsou [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) stejně, jako jsou virtuální počítače a účty úložiště.  Proto, že je možné spravovat prostřednictvím kódu programu přes [rozhraní REST API Azure Resource Manageru](/rest/api/), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure), [příkazy prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0)a mnoho [ Azure portal](https://portal.azure.com) funkce postaveny tato rozhraní API pro usnadnění správy prostředků.  

Každá z těchto rozhraní API a nástroje nabízí způsoby, jak vytvořit seznam, načíst, upravit a odstranit prostředky.  Vzhledem k tomu, že řídicí panely jsou prostředky, můžete vybrat vaše oblíbená rozhraní API / nástroj použít.

Bez ohledu na to, který nástroj použijete budete muset vytvořit reprezentaci JSON objektu řídicí panel, než bude možné volat jakékoli vytvoření prostředku rozhraní API. Tento objekt obsahuje informace o součástech (označovaný také jako dlaždice) na řídicím panelu. Zahrnuje velikost, umístění, prostředky, které jsou vázány na a jakýmikoli uživatelskými úpravami.

Nejvhodnější způsob, jak vytvořit tento dokument JSON je použití [portálu](https://portal.azure.com/) interaktivně přidávat a umístění dlaždice. Pak je exportovat ve formátu JSON. Nakonec vytvořte šablonu z výsledku pro pozdější použití ve skriptech, programů a nástroje pro nasazení.

## <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Pokud chcete vytvořit nový řídicí panel, použijte příkaz nový řídicí panel na hlavní obrazovce portálu společnosti.

![nový řídicí panel – příkaz](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Galerie dlaždic můžete vyhledat a přidat dlaždice. Dlaždice se přidal přetažením. Některé dlaždice podporovalo změnu velikosti prostřednictvím úchyt pro přetažení, zatímco jiné, podporu opraví velikostí, které si můžete prohlédnout ve své místní nabídky.

### <a name="drag-handle"></a>Úchyt pro přetažení
![Úchyt pro přetažení](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Pevné velikosti přes místní nabídku
![místní nabídka velikosti](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Sdílení řídicího panelu

Po nakonfigurování řídicí panel podle vašich představ, které jsou tyto další kroky k publikování řídicího panelu (pomocí příkazu Share) a následné použití Průzkumníka prostředků k načtení ve formátu JSON.

![příkaz pro sdílení](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Příkaz sdílené složky se zobrazí dialogové okno s dotazem, můžete zvolit, jaké předplatné a skupinu prostředků k publikování do. Mějte na paměti, která [musí mít oprávnění k zápisu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) k předplatné a skupinu prostředků, kterou zvolíte.

![sdílení a přístup](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Načíst reprezentaci JSON řídicího panelu

Publikování zabere jenom pár sekund.  Po dokončení, dalším krokem je použít [Průzkumníka prostředků](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) načíst ve formátu JSON.

![Procházet Průzkumníka prostředků](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

V Průzkumníku prostředků přejděte předplatné a skupinu prostředků, kterou jste zvolili. Pak klikněte na nově publikovaný řídicí panel prostředek, který chcete zobrazit ve formátu JSON.

![Resource explorer json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Vytvoření šablony z JSON

Dalším krokem je vytvoření šablony z tohoto formátu JSON, takže ho můžete znovu použít prostřednictvím kódu programu s odpovídající prostředek správy rozhraní API, nástrojů příkazového řádku, nebo na portálu.

Není nutné, abyste úplně pochopili strukturu JSON řídicí panel k vytvoření šablony. Ve většině případů budete chtít zachovat strukturu a konfiguraci každého bloku a potom parametrizovat prostředky Azure, které se odkazuje na sadu. Podívejte se na řídicím panelu exportované JSON a vyhledejte všechny výskyty ID prostředků Azure. Náš příklad řídicí panel obsahuje dlaždice více odkazujících na jeden virtuální počítač Azure. Důvodem je, náš řídicí panel jenom zjistí tento jeden prostředek. Pokud hledáte ukázková json (je součástí na konec dokumentu) "/ předplatná", hledání několika výskytů číslem ID této.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Publikování tohoto řídicího panelu pro jakýkoli virtuální počítač je v budoucnu potřeba parametrizovat všechny výskyty tohoto řetězce v kódu JSON. 

Existují dva typy rozhraní API, která vytváření prostředků v Azure. [Imperativní API](https://docs.microsoft.com/rest/api/resources/resources) , který současně, vytvořit jeden prostředek a [založené na šablonách nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) systém, který můžete orchestrovat vytváření více závislých prostředků pomocí jediného volání rozhraní API. Druhá možnost nativně podporuje Parametrizace a šablonování proto používáme v našem příkladu.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Prostřednictvím kódu programu vytvořte řídicí panel ze šablony pomocí šablony nasazení

Azure nabízí možnost můžete orchestrovat nasazení více prostředků. Můžete vytvořit šablonu nasazení, která vyjadřuje sadu prostředků pro nasazení a také vztahy mezi nimi.  Formát JSON každého prostředku je stejný, jako kdybyste vytvářeli je jeden po druhém. Rozdíl je, že [jazyk šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) přidá několik konceptů, jako jsou proměnné, parametry, základní funkce a další. To rozšířené syntaxe je podporován pouze v rámci nasazování šablony a nefunguje, pokud je použita s imperativní rozhraní API už jsme probírali výše.

Pokud budete tuto trasu, pak by mělo být provedeno Parametrizace pomocí syntaxe pro parametr šablony.  Můžete nahradit všechny výskyty id prostředku, které jsme zjistili dříve, jak je znázorněno zde.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Příklad JSON vlastnost s pevně zakódované Id prostředku
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Příklad JSON vlastnost převést na parametrizovaný verzi na základě parametrů šablony

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Také musíte deklarovat některá metadata požadovanou šablonu a parametry v horní části šablony json takto:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Zobrazí se kompletní, funkční šablonu na konci tohoto dokumentu.__

Jakmile máte vytvořený šablony můžete nasadit pomocí [rozhraní REST API](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create), nebo [stránku portálu šablony nasazení ](https://portal.azure.com/#create/Microsoft.Template).

Tady jsou dvě verze náš příklad řídicího panelu JSON. První je verze, která jsme exportovat z portálu, který byl již vázána na prostředek. Druhým je verze šablony, která mohou být prostřednictvím kódu programu vázány na všech virtuálních počítačů a nasazení pomocí Azure Resource Manageru.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentace JSON náš příklad řídicího panelu (před šablonování)

To je, co můžete očekávat zobrazíte, pokud budete postupovat podle starší pokynů k načtení reprezentaci JSON řídicí panel, který už je nasazená. Poznámka: identifikátory prostředků pevně zakódované, které ukazují, že tento řídicí panel odkazuje na konkrétní virtuální počítač Azure.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>Šablona reprezentace náš příklad řídicího panelu

Verze šablony řídicího panelu definoval tři parametry, které volá __virtualMachineName__, __virtualMachineResourceGroup__, a __dashboardName__.  Parametry umožňují pokaždé, když nasazujete bod tento řídicí panel v různých virtuálních počítačů Azure. Parametrizované ID jsou zvýrazněny zobrazíte, že tento řídicí panel můžete prostřednictvím kódu programu nakonfigurovat a nasadit tak, aby odkazoval do libovolného virtuálního počítače Azure. Nejjednodušší způsob, jak testovat tuto funkci má následující šablony zkopírujte a vložte ho do [stránku nasazení šablony webu Azure portal](https://portal.azure.com/#create/Microsoft.Template). 

Tento příklad nasadí řídicí panel samostatně, ale jazyk šablony umožňuje nasazovat více zdrojů a zabalí jeden nebo několik řídicích panelů po straně je. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```

---
title: Vytváření řídicích panelů Azure prostřednictvím kódu programu
description: Použijte řídicí panel na webu Azure Portal jako šablonu k programovému vytváření řídicích panelů Azure. Zahrnuje odkaz JSON.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 9ec9a4daad139a4930174ba9e3445e1cda1f8c54
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461306"
---
# <a name="programmatically-create-azure-dashboards"></a>Vytváření řídicích panelů Azure prostřednictvím kódu programu

Tento článek vás provede procesem programového vytváření a publikování řídicích panelů Azure. Řídicí panel uvedený níže je odkazován v celém dokumentu.

![Ukázkový řídicí panel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Přehled

Sdílené řídicí panely na [webu Azure Portal](https://portal.azure.com) jsou [prostředky,](../azure-resource-manager/management/overview.md) jako jsou virtuální počítače a účty úložiště. Prostředky můžete spravovat programově pomocí [api REST Azure Resource Manager](/rest/api/), azure [CLI](/cli/azure)a [Azure PowerShell příkazy](/powershell/azure/get-started-azureps).

Mnoho funkcí staví na těchto api, aby se usnadnila správa prostředků. Každý z těchto api a nástroje nabízí způsoby, jak vytvořit, seznam, načíst, upravit a odstranit prostředky. Vzhledem k tomu, že řídicí panely jsou prostředky, můžete si vybrat své oblíbené rozhraní API nebo nástroj, který chcete použít.

Bez ohledu na to, které nástroje použijete, k vytvoření řídicího panelu programově vytvoříte reprezentaci JSON objektu řídicího panelu. Tento objekt obsahuje informace o dlaždicích na řídicím panelu. Zahrnuje velikosti, pozice, prostředky, ke kterým jsou vázáni, a veškeré uživatelské úpravy.

Nejpraktičtější způsob, jak vytvořit tento dokument JSON je použití portálu Azure. Dlaždice můžete interaktivně přidávat a umísťovat. Potom exportujte JSON a vytvořte šablonu z výsledku pro pozdější použití ve skriptech, programech a nástrojích pro nasazení.

## <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Pokud chcete vytvořit řídicí panel, vyberte **Řídicí panel** z nabídky [Portál Azure](https://portal.azure.com) a pak vyberte Nový **řídicí panel**.

![nový řídicí panel, příkaz](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Pomocí galerie dlaždic můžete najít a přidat dlaždice. Dlaždice se přidávají jejich přetažením. Některé dlaždice podporují změna velikosti pomocí táhla pro přetažení.

![přetažením pro změnu velikosti](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Jiní mají pevné velikosti na výběr v jejich kontextové nabídce.

![velikosti kontextové nabídky pro změnu velikosti](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Sdílení řídicího panelu

Po konfiguraci řídicího panelu je dalším krokem publikování řídicího panelu pomocí příkazu **Sdílet.**

![sdílení řídicího panelu](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Výběrem **možnosti Sdílet** se zobrazí výzva k výběru předplatného a skupiny prostředků, do kterých chcete publikovat. Musíte mít přístup pro zápis do odběru a skupiny prostředků, které zvolíte. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a portálu Azure](../role-based-access-control/role-assignments-portal.md).

![provádět změny ve sdílení a přístupu](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Načtení reprezentace JSON řídicího panelu

Publikování trvá pouze několik sekund. Když je to hotovo, dalším krokem je načíst JSON pomocí **příkazu Stáhnout.**

![stáhnout reprezentaci JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Vytvoření šablony z JSON

Dalším krokem je vytvoření šablony z tohoto JSON. Tuto šablonu používejte programově s příslušnými api pro správu prostředků, nástroji příkazového řádku nebo v rámci portálu.

K vytvoření šablony není třeba plně porozumět struktuře JSON řídicího panelu. Ve většině případů chcete zachovat strukturu a konfiguraci každé dlaždice. Pak parametrizujte sadu prostředků Azure, na které dlaždice odkazují. Podívejte se na exportovaný řídicí panel JSON a najděte všechny výskyty ID prostředků Azure. Náš ukázkový řídicí panel obsahuje několik dlaždic, které všechny ukazují na jeden virtuální počítač Azure. Je to proto, že náš řídicí panel se dívá pouze na tento jediný prostředek. Pokud hledáte ukázku JSON, zahrnuty na konci dokumentu pro "/odběry", najdete několik výskytů tohoto ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Chcete-li publikovat tento řídicí panel pro jakýkoli virtuální počítač v budoucnu, parametrizovat každý výskyt tohoto řetězce v rámci JSON.

Existují dva přístupy pro api, které vytvářejí prostředky v Azure:

* Imperativní api vytvořit jeden prostředek najednou. Další informace naleznete v tématu [Resources](/rest/api/resources/resources).
* Systém nasazení založený na šabloně, který vytváří více závislých prostředků s jedním voláním rozhraní API. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md).

Nasazení založené na šabloně podporuje parametrizaci a šablonování. Tento přístup používáme v tomto článku.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programově vytvoření řídicího panelu ze šablony pomocí nasazení šablony

Azure nabízí možnost organizovat nasazení více prostředků. Vytvoříte šablonu nasazení, která vyjadřuje sadu prostředků k nasazení a vztahy mezi nimi.  Formát JSON každého prostředku je stejný, jako kdybyste je vytvářeli jeden po druhém. Rozdíl je v tom, že jazyk šablony přidá několik konceptů, jako jsou proměnné, parametry, základní funkce a další. Tato rozšířená syntaxe je podporována pouze v kontextu nasazení šablony. Nefunguje, pokud se používá s imperativní api diskutovali dříve. Další informace najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Parametrizace by měla být provedena pomocí syntaxe parametru šablony.  Nahradíte všechny instance ID prostředku, které jsme našli dříve, jak je znázorněno zde.

Příklad vlastnosti JSON s pevně zakódovaným ID prostředku:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Příklad vlastnosti JSON převedené na parametrizovanou verzi založenou na parametrech šablony

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarujte požadovaná metadata šablony a parametry v horní části šablony JSON takto:

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
Jakmile šablonu nakonfigurujete, nasaďte ji pomocí některé z následujících metod:

* [Rozhraní REST API](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Stránka nasazení šablony portálu Azure](https://portal.azure.com/#create/Microsoft.Template)

Dále uvidíte dvě verze našeho příkladu palubní desky JSON. První je verze, kterou jsme exportovali z portálu, který byl již vázán na prostředek. Druhá je verze šablony, která může být programově vázána na libovolný virtuální počítač a nasazená pomocí Správce prostředků Azure.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON reprezentace našeho příkladu palubní desky před templating

Tento příklad ukazuje, co můžete očekávat, pokud jste postupovali spolu s tímto článkem. Pokyny exportovaly reprezentaci JSON řídicího panelu, který je již nasazen. Pevně zakódované identifikátory prostředků ukazují, že tento řídicí panel ukazuje na konkrétní virtuální počítač Azure.

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

### <a name="template-representation-of-our-example-dashboard"></a>Šablona reprezentace našeho příkladu řídicího panelu

Verze šablony řídicího panelu definovala `virtualMachineName` `virtualMachineResourceGroup`tři `dashboardName`parametry nazývané , a .  Parametry umožňují nasměrovat tento řídicí panel na jiný virtuální počítač Azure při každém nasazení. Tento řídicí panel lze programově nakonfigurovat a nasadit tak, aby ukazoval na libovolný virtuální počítač Azure. Chcete-li tuto funkci otestovat, zkopírujte následující šablonu a vložte ji na [stránku nasazení šablony portálu Azure](https://portal.azure.com/#create/Microsoft.Template).

Tento příklad nasazuje řídicí panel sám, ale jazyk šablony umožňuje nasadit více prostředků a sdružovat jeden nebo více řídicích panelů s nimi.

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

Teď, když jste viděli příklad použití parametrizované šablony k nasazení řídicího panelu, můžete zkusit nasadit šablonu pomocí [rozhraní API REST Azure Resource Manager](/rest/api/), příkazů Azure [CLI](/cli/azure)nebo [Azure PowerShell](/powershell/azure/get-started-azureps).

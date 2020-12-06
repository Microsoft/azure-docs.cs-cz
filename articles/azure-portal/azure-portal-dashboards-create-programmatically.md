---
title: Vytváření řídicích panelů Azure prostřednictvím kódu programu
description: K programovému vytváření řídicích panelů Azure použijte řídicí panel v Azure Portal jako šablonu. Obsahuje odkaz JSON.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: e69d3f3cea0ff63f94e797047eb10b9583678b1b
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745804"
---
# <a name="programmatically-create-azure-dashboards"></a>Vytváření řídicích panelů Azure prostřednictvím kódu programu

Tento článek vás provede procesem vytváření a publikování řídicích panelů Azure prostřednictvím kódu programu. Na řídicím panelu uvedeném níže je odkazováno v celém dokumentu.

![Ukázkový řídicí panel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Přehled

Sdílené řídicí panely v [Azure Portal](https://portal.azure.com) jsou [prostředky](../azure-resource-manager/management/overview.md) stejně jako virtuální počítače a účty úložiště. Prostředky můžete spravovat programově pomocí [Azure Resource Manager rozhraní REST API](/rest/api/), příkazů [Azure CLI](/cli/azure)a [Azure PowerShell](/powershell/azure/get-started-azureps).

Mnoho funkcí, které na těchto rozhraních API sestavuje, usnadňuje správu prostředků. Každé z těchto rozhraní API a nástrojů nabízí způsoby, jak vytvářet, vypisovat, načítat, upravovat a odstraňovat prostředky. Vzhledem k tomu, že řídicí panely jsou prostředky, můžete si vybrat oblíbené rozhraní API nebo nástroj, který chcete použít.

Bez ohledu na to, jaké nástroje použijete, můžete vytvořit řídicí panel pomocí kódu programu v kódu řídicího panelu. Tento objekt obsahuje informace o dlaždicích na řídicím panelu. Zahrnuje velikosti, pozice, prostředky, na které jsou svázané, a libovolné vlastní uživatelské nastavení.

Nejpohodlnější způsob, jak sestavit tento dokument JSON, je použití Azure Portal. Dlaždice můžete interaktivně přidat a umístit. Pak exportujte JSON a vytvořte šablonu z výsledku pro pozdější použití ve skriptech, programech a nástrojích pro nasazení.

## <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Řídicí panel vytvoříte tak, že v nabídce [Azure Portal](https://portal.azure.com) vyberete **řídicí panel** a pak vyberete **nový řídicí panel**.

![nový řídicí panel – příkaz](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Pomocí Galerie dlaždic můžete najít a přidat dlaždice. Dlaždice jsou přidány přetažením a přetažením. Některé dlaždice podporují změnu velikosti pomocí úchytu pro přetahování.

![Přetažením úchytu změníte velikost](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Ostatní mají pevně zvolené velikosti pro výběr z místní nabídky.

![místní nabídka velikosti pro změnu velikosti](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Sdílet řídicí panel

Po nakonfigurování řídicího panelu je dalším krokem publikování řídicího panelu pomocí příkazu **share** .

![sdílení řídicího panelu](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Po výběru možnosti **sdílet** budete vyzváni k výběru předplatného a skupiny prostředků, do které chcete publikovat. Musíte mít oprávnění k zápisu do předplatného a skupiny prostředků, kterou si zvolíte. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí webu Azure Portal](../role-based-access-control/role-assignments-portal.md).

![provedení změn pro sdílení a přístup](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Načtení reprezentace z řídicího panelu ve formátu JSON

Publikování trvá jenom několik sekund. Až to bude hotové, další krok je načíst JSON pomocí příkazu **ke stažení** .

![Stáhnout reprezentace JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Vytvoření šablony z formátu JSON

Dalším krokem je vytvoření šablony z tohoto formátu JSON. Tuto šablonu můžete používat programově s příslušnými rozhraními API pro správu prostředků, nástroji příkazového řádku nebo v rámci portálu.

Pro vytvoření šablony není nutné plně porozumět struktuře JSON řídicího panelu. Ve většině případů chcete zachovat strukturu a konfiguraci každé dlaždice. Potom parametrizovat sadu prostředků Azure, na které dlaždice odkazují. Podívejte se na exportovaný řídicí panel JSON a vyhledejte všechny výskyty ID prostředků Azure. Náš ukázkový řídicí panel obsahuje několik dlaždic, které všechny odkazují na jeden virtuální počítač Azure. To je proto, že náš řídicí panel prohlíží jenom tento jediný prostředek. Pokud vyhledáte vzorový kód JSON, který je zahrnutý na konci dokumentu, pro text "/Subscriptions" zjistíte několik výskytů tohoto ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Pokud chcete tento řídicí panel publikovat pro libovolný virtuální počítač v budoucnu, parametrizovat všechny výskyty tohoto řetězce ve formátu JSON.

Existují dva přístupy k rozhraním API, která vytvářejí prostředky v Azure:

* Imperativní rozhraní API vytvářejí jeden prostředek najednou. Další informace najdete v tématu [prostředky](/rest/api/resources/resources).
* Systém nasazení založený na šablonách, který vytváří více závislých prostředků s jedním voláním rozhraní API. Další informace najdete v tématu  [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

Nasazení založené na šablonách podporuje Parametrizace a šablonování. Tento postup používáme v tomto článku.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programové vytvoření řídicího panelu ze šablony pomocí nasazení šablony

Azure nabízí možnost orchestrovat nasazení více prostředků. Vytvoříte šablonu nasazení, která vyjadřuje sadu prostředků pro nasazení a vztahy mezi nimi.  Formát JSON každého prostředku je stejný, jako kdybyste ho vytvořili jeden po jednom. Rozdílem je, že jazyk šablony přidává několik konceptů, jako jsou proměnné, parametry, základní funkce a další. Tato rozšířená syntaxe je podporována pouze v kontextu nasazení šablony. Nefunguje, pokud se používá s imperativními rozhraními API, která jsou popsaná výše. Další informace najdete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Parametrizace by mělo být provedeno pomocí syntaxe parametru šablony.  Nahradíte všechny výskyty ID prostředku, které jsme dříve našli, jak je znázorněno zde.

Příklad vlastnosti JSON s pevně zakódovaným ID prostředku:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Ukázková vlastnost JSON převedená na parametrizovanou verzi založenou na parametrech šablony

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklaruje požadovaná metadata šablony a parametry v horní části šablony JSON takto:

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
Jakmile nakonfigurujete šablonu, nasaďte ji pomocí kterékoli z následujících metod:

* [Rozhraní REST API](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Stránka Azure Portalho nasazení šablony](https://portal.azure.com/#create/Microsoft.Template)

Dále uvidíte dvě verze našeho ukázkového řídicího panelu JSON. První je verze, kterou jsme exportovali z portálu, který už je vázaný na prostředek. Druhá je verze šablony, která se dá programově svázat s jakýmkoli virtuálním počítačem a nasadit pomocí Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Ukázka řídicího panelu v kódu JSON před šablonování

V tomto příkladu se dozvíte, co můžete očekávat, pokud jste spolu s tímto článkem postupovali. Pokyny exportují reprezentaci JSON řídicího panelu, který je už nasazený. Pevně zakódované identifikátory prostředků ukazují, že tento řídicí panel ukazuje na konkrétní virtuální počítač Azure.

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

### <a name="template-representation-of-our-example-dashboard"></a>Reprezentace šablony našeho ukázkového řídicího panelu

Verze šablony řídicího panelu definovala tři parametry s názvem `virtualMachineName` , `virtualMachineResourceGroup` a `dashboardName` .  Parametry umožňují Ukázat tento řídicí panel na jiném virtuálním počítači Azure při každém nasazení. Tento řídicí panel se dá programově nakonfigurovat a nasadit tak, aby odkazoval na libovolný virtuální počítač Azure. Chcete-li otestovat tuto funkci, zkopírujte následující šablonu a vložte ji do [stránky Azure Portalho nasazení šablony](https://portal.azure.com/#create/Microsoft.Template).

Tento příklad nasadí řídicí panel sám o sobě, ale jazyk šablony vám umožní nasadit více prostředků a seskupit jeden nebo více řídicích panelů, které jsou na nich umístěné.

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

Teď, když jste viděli příklad použití parametrizované šablony pro nasazení řídicího panelu, můžete zkusit nasadit šablonu pomocí [Azure Resource Manager rozhraní REST API](/rest/api/), rozhraní [příkazového](/powershell/azure/get-started-azureps) [řádku Azure](/cli/azure)nebo Azure PowerShell.

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Programové vytvoření řídicího panelu pomocí Azure CLI

Připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tyto příklady používají následující řídicí panel: [portal-dashboard-template-testvm.jsna](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Nahraďte obsah v lomených závorkách hodnotami.

Spuštěním příkazu [AZ Portal Dashboard Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) vytvořte řídicí panel:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Řídicí panel můžete aktualizovat pomocí příkazu [AZ Portal Dashboard Update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) :

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Podrobnosti řídicího panelu zobrazíte spuštěním příkazu [AZ Portal Dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) :

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Pokud chcete zobrazit všechny řídicí panely aktuálního předplatného, použijte příkaz [AZ Portal Dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Můžete také zobrazit všechny řídicí panely pro skupinu prostředků:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o stolních počítačích najdete v tématu [Správa nastavení a předvoleb Azure Portal](set-preferences.md).

Další informace o podpoře Azure CLI pro řídicí panely najdete v tématu [AZ Portal Dashboard](/cli/azure/ext/portal/portal/dashboard).

---
title: Vytváření řídicích panelů Azure prostřednictvím kódu programu | Microsoft Docs
description: Tento článek vysvětluje, jak programově vytváření řídicích panelů Azure.
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
ms.openlocfilehash: 8670d25e10b58c40b9d0807de1db88c3296b193d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164379"
---
# <a name="programmatically-create-azure-dashboards"></a>Vytváření řídicích panelů Azure prostřednictvím kódu programu

Tento dokument vás provede procesem prostřednictvím kódu programu vytváření a publikování v Azure řídicí panely. Řídicí panel, viz následující obrázek je odkazováno v celém dokumentu.

![ukázkový řídicí panel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Přehled

Sdílet řídicí panely v Azure jsou [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) stejně jako virtuální počítače a účty úložiště.  Proto je můžete spravovat prostřednictvím kódu programu prostřednictvím [rozhraní API REST Azure Resource Manager](/rest/api/), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure), [příkazy prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0)a mnoho [ Portál Azure](https://portal.azure.com) funkce sestavení nad tato rozhraní API v zájmu snazší správy prostředků.  

Každý z těchto rozhraní API a nástroje nabízí způsoby vytváření, výpisu, načtení, upravit a odstranit prostředky.  Vzhledem k tomu, že řídicí panely jsou prostředky, můžete vybrat vaše oblíbená rozhraní API a nástroj použít.

Bez ohledu na to, který nástroj použijete budete muset vytvořit reprezentaci JSON objektu řídicí panel, než bude možné volat jakékoli vytvoření prostředku rozhraní API. Tento objekt obsahuje informace o části (také známa jako dlaždice) na řídicím panelu. Obsahuje velikosti, pozice, prostředků, které jsou vázány na a nastavení uživatele.

Nejvhodnější způsob, jak můžete vytvořit až tento dokument JSON má používat [portálu](https://portal.azure.com/) interaktivně přidávat a umístit je dlaždice. Pak můžete exportovat ve formátu JSON. Nakonec vytvořte šablonu z výsledku pro pozdější použití ve skriptech, programů a nástroje pro nasazení.

## <a name="create-a-dashboard"></a>Vytvořit řídicí panel

Pokud chcete vytvořit nový řídicí panel, použijte příkaz nového řídicího panelu na hlavní obrazovky na portálu.

![nový příkaz řídicí panel](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Potom můžete galerii dlaždice můžete najít a přidat dlaždice. Dlaždice se přidají pomocí přetahování a vkládání je. Některé dlaždice podporovat změnu velikosti prostřednictvím popisovač přetažení, zatímco jiné, podporu opraví velikosti, které si můžete prohlédnout ve své místní nabídce.

### <a name="drag-handle"></a>Přetažením úchytu
![Přetažením úchytu](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Pevné velikosti prostřednictvím místní nabídky
![velikosti kontextové nabídky](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Sdílejte řídicí panel

Po nakonfigurování řídicího panelu, aby přesně další kroky jsou publikovat řídicí panel (pomocí příkazu sdílené složky) a potom pomocí Průzkumníka prostředků k načtení JSON.

![příkaz sdílené složky](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Kliknutím na příkaz sdílené složky zobrazí dialogové okno s dotazem, abyste si zvolili které předplatném nebo skupině prostředků pro publikování do. Mějte na paměti, [musí mít oprávnění k zápisu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) ke skupině předplatného a prostředků, který zvolíte.

![sdílení a přístup](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Načtení reprezentace JSON řídicího panelu

Publikování trvá jenom pár sekund.  Až skončíte, dalším krokem je přejít na [Průzkumníka prostředků](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) načíst JSON.

![Procházet Průzkumník prostředků](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

V Průzkumníku prostředků přejděte na skupině předplatného a prostředků, který jste si zvolili. Potom klikněte na tento řídicí panel Přehled nově publikovaných prostředek odhalit JSON.

![json Průzkumník prostředků](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Vytvoření šablony z formátu JSON

Dalším krokem je vytvoření šablony z tohoto formátu JSON, tak, aby ho lze opětovně použít prostřednictvím kódu programu se příslušném prostředku rozhraní API pro správu nástroje příkazového řádku, nebo v rámci portálu.

Není nutné, abyste plně porozuměli tomu struktuře JSON řídicího panelu vytvořit šablonu. Ve většině případů budete chtít zachovat strukturu a konfiguraci každou dlaždici a potom Parametrizace sadu prostředků Azure, které jste odkazující na. Podívejte se na řídicím panelu exportovaný JSON a najít všechny výskyty Azure ID prostředku. Náš příklad řídicí panel obsahuje více dlaždice všech bodů na jeden virtuální počítač Azure. Je to způsobeno naše řídicí panel vypadá pouze na tento jeden prostředek. Pokud hledáte ukázková json (zahrnutá na konci tohoto dokumentu) "/ odběry", najít několik výskyty číslem ID této.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Pokud chcete publikovat tento řídicí panel pro jakýkoli virtuální počítač je třeba v budoucnu Parametrizace všechny výskyty řetězce tento řetězec ve formátu JSON. 

Existují dva typy rozhraní API, která vytváření prostředků v Azure. [Imperativní rozhraní API](https://docs.microsoft.com/rest/api/resources/resources) současně, který vytvořit jeden prostředek a [nasazení na základě šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) systém, který můžete orchestraci vytvoření více prostředků závislých na základě jednoho volání rozhraní API. K tomu nativně podporuje Parametrizace a ukázka tak používáme pro náš příklad.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Řídicí panel ze šablony pomocí šablony nasazení vytváření prostřednictvím kódu programu

Azure nabízí možnost orchestraci nasazení více prostředků. Můžete vytvořit šablonu nasazení, která vyjadřuje sadu prostředků pro nasazení také vztahy mezi nimi.  Formát JSON každého prostředku je stejný, jako kdyby jejich byly vytváření po jednom. Rozdíl je, že [jazyk šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) přidá s několika koncepty jako proměnné, parametry, základní funkce a další. Tuto rozšířenou syntaxe je podporován pouze v kontextu šablony nasazení a nefunguje, pokud se používá s imperativní rozhraní API už jsme probírali výše.

Pokud budete tuto trasu, pak by mělo být provedeno Parametrizace pomocí syntaxe parametru šablony.  Můžete nahradit všechny instance, které jsme našli dříve, jak je vidět tady ID prostředku.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Příklad JSON vlastnost s Id pevně prostředku
`id: “/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1”`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Příklad JSON vlastnost převedena na parametrizované verzi na základě parametrů šablony

`id: "[resourceId(parameters('virtualMachineResourceGroup'), ‘Microsoft.Compute/virtualMachines’, parameters('virtualMachineName'))]"`

Také musíte deklarovat některá metadata požadovanou šablonu a parametry v horní části šablony json takto:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

__Uvidíte kompletní, funkční šablonou na konci tohoto dokumentu.__

Jakmile máte vytvořené šablony můžete nasadit pomocí [rozhraní REST API](https://docs.microsoft.com/rest/api/resources/deployments), [prostředí PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/group/deployment#az_group_deployment_create), nebo [stránku nasazení šablony portálu ](https://portal.azure.com/#create/Microsoft.Template).

Tady jsou jsou dvě verze náš příklad řídicího panelu JSON. První je verze, který jsme exportovali z portálu, který byl již vázána na prostředek. Druhá je verzi šablony, které mohou být prostřednictvím kódu programu vázány na žádné virtuální počítače a nasazují pomocí Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentace JSON náš příklad řídicího panelu (před ukázka)

Je to, co můžete očekávat chcete zobrazit, pokud jste postupujte podle pokynů starší a načíst reprezentaci JSON řídicí panel, který je už nasazená. Poznámka: identifikátory pevně prostředků, které ukazují, že tento řídicí panel je jednotka ukazovat na konkrétní virtuální počítač Azure.

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

### <a name="template-representation-of-our-example-dashboard"></a>Reprezentace šablony náš příklad řídicího panelu

Šablony verze řídicího panelu nemá definovánu tři parametry s názvem __virtualMachineName__, __virtualMachineResourceGroup__, a __dashboardName__.  Parametry umožňují pokaždé, když nasazujete bodu tento řídicí panel na jiný virtuální počítač Azure. Parametrizované ID se zvýrazněnou zobrazit, že tento řídicí panel můžete prostřednictvím kódu programu nakonfigurovat a nasadit tak, aby odkazoval na všechny virtuální počítač Azure. Nejjednodušší způsob, jak otestovat tato funkce má následující šablony zkopírujte a vložte ji do [stránku nasazení šablony Azure portal](https://portal.azure.com/#create/Microsoft.Template). 

Tento příklad nasadí řídicí panel sám o sobě, ale jazyk šablony umožňuje nasadit několik prostředků a sady jeden nebo více řídicí panely na straně je. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
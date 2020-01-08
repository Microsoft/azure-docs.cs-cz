---
title: Vytváření řídicích panelů Azure prostřednictvím kódu programu | Microsoft Docs
description: Řídicí panel můžete v Azure Portal použít jako šablonu pro programové vytváření řídicích panelů Azure. Obsahuje odkaz JSON.
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
ms.date: 09/01/2017
ms.author: mblythe
ms.openlocfilehash: 498e0255cfa289f7d8ccb93040980c362cf510a0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640342"
---
# <a name="programmatically-create-azure-dashboards"></a>Vytváření řídicích panelů Azure prostřednictvím kódu programu

Tento dokument vás provede procesem programového vytváření a publikování řídicích panelů Azure. Na řídicím panelu uvedeném níže je odkazováno v celém dokumentu.

![Ukázkový řídicí panel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Přehled

Sdílené řídicí panely v Azure jsou [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) stejně jako virtuální počítače a účty úložiště.  Proto je možné je spravovat programově prostřednictvím [rozhraní REST api Azure Resource Manager](/rest/api/), příkazů [Azure CLI](https://docs.microsoft.com/cli/azure), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)a mnoha [Azure Portalch](https://portal.azure.com) funkcí sestavování nad těmito rozhraními API, aby bylo snazší správa prostředků.  

Každé z těchto rozhraní API a nástrojů nabízí způsoby, jak vytvářet, vypisovat, načítat, upravovat a odstraňovat prostředky.  Vzhledem k tomu, že řídicí panely jsou prostředky, můžete si vybrat oblíbené rozhraní API nebo nástroj, které chcete použít.

Bez ohledu na to, který nástroj používáte, musíte vytvořit reprezentaci JSON objektu řídicího panelu, abyste mohli volat libovolné rozhraní API pro vytváření prostředků. Tento objekt obsahuje informace o částech (označuje se také jako dlaždice) na řídicím panelu. Zahrnuje velikosti, pozice, prostředky, ke kterým jsou svázané, a libovolné vlastní uživatelské nastavení.

Nejpohodlnější způsob, jak vytvořit tento dokument JSON, je použít [portál](https://portal.azure.com/) k interaktivnímu přidávání a umísťování dlaždic. Pak exportujte JSON. Nakonec vytvoříte šablonu z výsledku pro pozdější použití ve skriptech, programech a nástrojích pro nasazení.

## <a name="create-a-dashboard"></a>Vytvoření řídicího panelu

Nový řídicí panel vytvoříte pomocí příkazu nový řídicí panel na hlavní obrazovce portálu.

![nový řídicí panel – příkaz](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Potom můžete pomocí Galerie dlaždic najít a přidat dlaždice. Dlaždice jsou přidány přetažením a přetažením. Některé dlaždice podporují změnu velikosti prostřednictvím úchytu přetažení, zatímco ostatní podporují velikosti, které lze zobrazit v místní nabídce.

### <a name="drag-handle"></a>Úchyt pro přetažení
![Přetáhněte popisovač](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Pevné velikosti prostřednictvím kontextové nabídky
![místní nabídka velikostí](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Sdílet řídicí panel

Až nakonfigurujete řídicí panel na míru, další postup je publikovat řídicí panel (pomocí příkazu share) a potom pomocí Průzkumníka prostředků načíst JSON.

![sdílet – příkaz](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Kliknutím na příkaz sdílet se zobrazí dialogové okno s výzvou k výběru předplatného a skupiny prostředků, do které se má publikovat. Mějte na paměti, že [musíte mít oprávnění k zápisu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do předplatného a skupiny prostředků, kterou si zvolíte.

![sdílení a přístup](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Načtení reprezentace z řídicího panelu ve formátu JSON

Publikování trvá jenom několik sekund.  Až to bude hotové, další krok je přejít na [Průzkumník prostředků](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) a načíst JSON.

![Procházet Průzkumníka prostředků](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

V Průzkumníku prostředků přejděte do předplatného a skupiny prostředků, kterou jste zvolili. V dalším kroku klikněte na prostředek nově publikovaného řídicího panelu, aby se kód JSON vykryl.

![JSON pro Resource Explorer](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Vytvoření šablony z formátu JSON

Dalším krokem je vytvoření šablony z tohoto formátu JSON, aby ji bylo možné znovu použít prostřednictvím odpovídajících rozhraní API pro správu prostředků, nástrojů příkazového řádku nebo na portálu.

Pro vytvoření šablony není nutné plně porozumět struktuře JSON řídicího panelu. Ve většině případů chcete zachovat strukturu a konfiguraci každé dlaždice a pak parametrizovat sadu prostředků Azure, na které odkazují. Podívejte se na exportovaný řídicí panel JSON a vyhledejte všechny výskyty ID prostředků Azure. Náš ukázkový řídicí panel obsahuje několik dlaždic, které všechny odkazují na jeden virtuální počítač Azure. To je proto, že náš řídicí panel prohlíží jenom tento jediný prostředek. Pokud vyhledáte ukázkový kód JSON (který je součástí konce dokumentu) pro "/Subscriptions", zjistíte několik výskytů tohoto ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Pokud chcete tento řídicí panel publikovat pro libovolný virtuální počítač v budoucnu, musíte v rámci formátu JSON parametrizovat všechny výskyty tohoto řetězce. 

Existují dva typy rozhraní API, které vytvářejí prostředky v Azure. [Imperativní rozhraní API](https://docs.microsoft.com/rest/api/resources/resources) , která současně vytvářejí jeden prostředek, a systém [nasazení založený na šablonách](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) , který dokáže orchestrovat vytvoření více závislých prostředků s jedním voláním rozhraní API. Druhá nativně podporuje Parametrizace a šablonování, takže ji používáme pro náš příklad.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programové vytvoření řídicího panelu ze šablony pomocí nasazení šablony

Azure nabízí možnost orchestrovat nasazení více prostředků. Vytvoříte šablonu nasazení, která vyjadřuje sadu prostředků k nasazení a také vztahy mezi nimi.  Formát JSON každého prostředku je stejný, jako kdybyste ho vytvořili jeden po jednom. Rozdílem je, že [jazyk šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) přidává několik konceptů, jako jsou proměnné, parametry, základní funkce a další. Tato rozšířená syntaxe je podporována pouze v kontextu nasazení šablony a nefunguje, pokud je použita s imperativními rozhraními API, která jsou popsána výše.

Pokud se chystáte o tuto trasu, parametrizace by se mělo provést pomocí syntaxe parametru šablony.  Nahradíte všechny výskyty ID prostředku, které jsme dříve našli, jak je znázorněno zde.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Příklad vlastnosti JSON s pevně zakódovaným ID prostředku
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Ukázková vlastnost JSON převedená na parametrizovanou verzi založenou na parametrech šablony

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Musíte taky deklarovat některá požadovaná metadata šablony a parametry v horní části šablony JSON, jako je tato:

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

__Na konci tohoto dokumentu můžete zobrazit úplnou pracovní šablonu.__

Po vyřazení šablony ji můžete nasadit pomocí [rozhraní REST API](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create)nebo [stránky nasazení šablony portálu](https://portal.azure.com/#create/Microsoft.Template).

Tady jsou dvě verze našeho ukázkového kódu JSON řídicího panelu. První je verze, kterou jsme exportovali z portálu, který už je vázaný na prostředek. Druhá je verze šablony, která se dá programově svázat s jakýmkoli virtuálním počítačem a nasadit pomocí Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentace formátu JSON našeho ukázkového řídicího panelu (před šablonování)

To je to, co můžete očekávat, pokud budete postupovat podle předchozích pokynů k načtení reprezentace JSON řídicího panelu, který je již nasazený. Poznamenejte si pevně kódované identifikátory prostředků, které ukazují, že tento řídicí panel odkazuje na konkrétní virtuální počítač Azure.

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

Verze šablony řídicího panelu definovala tři parametry s názvem __virtualMachineName__, __virtualMachineResourceGroup__a název __řídicího panelu__.  Parametry umožňují Ukázat tento řídicí panel na jiném virtuálním počítači Azure při každém nasazení. Parametrizovaná ID jsou zvýrazněná a ukazují, že tento řídicí panel je možné programově nakonfigurovat a nasadit tak, aby odkazoval na libovolný virtuální počítač Azure. Nejjednodušší způsob, jak otestovat tuto funkci, je zkopírovat následující šablonu a vložit ji do [stránky nasazení šablony Azure Portal](https://portal.azure.com/#create/Microsoft.Template). 

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

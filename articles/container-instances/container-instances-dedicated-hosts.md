---
title: Nasazení na vyhrazeném hostiteli
description: Použití vyhrazeného hostitele k dosažení skutečné izolace na úrovni hostitele pro úlohy azure kontejnerů
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025029"
---
# <a name="deploy-on-dedicated-hosts"></a>Nasazování na vyhrazené hostitele

"Vyhrazené" je sku instance kontejnerů Azure (ACI), která poskytuje izolované a vyhrazené výpočetní prostředí pro bezpečně spuštěné kontejnery. Pomocí vyhrazené sku výsledky v každé skupině kontejnerů, které mají vyhrazený fyzický server v datovém centru Azure, zajištění úplné izolace pracovního vytížení, které vám pomohou splnit požadavky vaší organizace na zabezpečení a dodržování předpisů. 

Vyhrazená sku je vhodná pro úlohy kontejneru, které vyžadují izolaci pracovního vytížení z hlediska fyzického serveru.

## <a name="prerequisites"></a>Požadované součásti

* Výchozí limit pro jakékoli předplatné používat vyhrazené sku je 0. Pokud chcete použít tuto sku pro nasazení produkčního kontejneru, vytvořte [žádost o podporu Azure][azure-support] pro zvýšení limitu.

## <a name="use-the-dedicated-sku"></a>Použijte vyhrazenou sku

> [!IMPORTANT]
> Použití vyhrazené sku je k dispozici pouze v nejnovější verzi rozhraní API (2019-12-01), která je aktuálně zaváděcí. Tuto verzi rozhraní API zadejte v šabloně nasazení.
>

Počínaje verzí rozhraní API 2019-12-01 `sku` je vlastnost v části vlastnosti skupiny kontejnerů šablony nasazení, která je vyžadována pro nasazení ACI. V současné době můžete tuto vlastnost použít jako součást šablony nasazení Azure Resource Manager pro ACI. Další informace o nasazení prostředků ACI se šablonou v [kurzu: Nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Vlastnost `sku` může mít jednu z následujících hodnot:
* `Standard`- standardní volba nasazení ACI, která stále zaručuje bezpečnost na úrovni hypervisoru 
* `Dedicated`- používá se pro izolaci na úrovni pracovního vytížení s vyhrazenými fyzickými hostiteli pro skupinu kontejnerů

## <a name="modify-your-json-deployment-template"></a>Úprava šablony nasazení JSON

V šabloně nasazení upravte nebo přidejte následující vlastnosti:
* V `resources`části `apiVersion` `2019-12-01`najetna na .
* Pod vlastnostmi skupiny `sku` kontejnerů `Dedicated`přidejte vlastnost s hodnotou .

Tady je ukázkový úryvek pro část prostředků šablony nasazení skupiny kontejnerů, která používá vyhrazenou sku:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Následuje úplná šablona, která nasazuje skupinu ukázkových kontejnerů, která používá jednu instanci kontejneru:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Nasazení skupiny kontejnerů

Pokud jste vytvořili a upravili soubor šablony nasazení na ploše, můžete ho nahrát do adresáře Cloud Shell přetažením souboru do něj. 

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasaďte šablonu pomocí příkazu [vytvořit nasazení skupiny az.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Během několika sekund by se měla zobrazit první odezva z Azure. Úspěšné nasazení probíhá na vyhrazeném hostiteli.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

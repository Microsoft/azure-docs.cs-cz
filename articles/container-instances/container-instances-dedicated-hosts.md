---
title: Nasadit na vyhrazeném hostiteli
description: Použití vyhrazeného hostitele k dosažení skutečné izolace na úrovni hostitele pro vaše Azure Container Instances úlohy
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 68b9b31cdfb55e8150b05e3efd35389320905cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034267"
---
# <a name="deploy-on-dedicated-hosts"></a>Nasazování na vyhrazené hostitele

"Vyhrazeným" je SKU Azure Container Instances (ACI), které poskytuje izolované a vyhrazené výpočetní prostředí pro bezpečné spouštění kontejnerů. Výsledkem použití vyhrazené skladové položky v každé skupině kontejnerů je vyhrazený fyzický server v datovém centru Azure, které zajišťuje úplnou izolaci úloh, aby bylo možné lépe vyhovovat požadavkům vaší organizace na zabezpečení a dodržování předpisů. 

Vyhrazená SKU je vhodná pro úlohy kontejneru, které vyžadují izolaci úloh z pohledu fyzického serveru.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Kvůli některým aktuálním omezením není zaručeno schválení všech žádostí o zvýšení limitu.

* Výchozí omezení pro všechna předplatná, která používají vyhrazené SKU, je 0. Pokud chcete tuto sku použít pro nasazení produkčního kontejneru, vytvořte [support Request Azure][azure-support] pro zvýšení limitu.

## <a name="use-the-dedicated-sku"></a>Použití vyhrazené SKU

> [!IMPORTANT]
> Použití vyhrazené SKU je dostupné jenom v nejnovější verzi rozhraní API (2019-12-01), která se v tuto chvíli zavádí. Tuto verzi rozhraní API zadejte v šabloně nasazení.
>

Počínaje rozhraním API verze 2019-12-01 je `sku` v části vlastnosti skupiny kontejnerů v šabloně nasazení požadovaná vlastnost, která se vyžaduje pro nasazení ACI. V současné době můžete tuto vlastnost použít jako součást šablony nasazení Azure Resource Manager pro ACI. Další informace o nasazení prostředků ACI pomocí šablony v tomto [kurzu: nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](./container-instances-multi-container-group.md). 

`sku`Vlastnost může mít jednu z následujících hodnot:
* `Standard` – standardní volba nasazení ACI, která stále garantuje zabezpečení na úrovni hypervisoru 
* `Dedicated` – používá se pro izolaci na úrovni pracovního vytížení s vyhrazenými fyzickými hostiteli pro skupinu kontejnerů.

## <a name="modify-your-json-deployment-template"></a>Úprava šablony nasazení JSON

V šabloně nasazení upravte nebo přidejte následující vlastnosti:
* V části `resources` nastavte `apiVersion` na `2019-12-01` .
* V části vlastnosti skupiny kontejnerů přidejte `sku` vlastnost s hodnotou `Dedicated` .

Tady je příklad fragmentu pro oddíl Resources v šabloně nasazení skupiny kontejnerů, která používá vyhrazenou SKU:

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

Následuje úplná šablona, která nasadí ukázkovou skupinu kontejnerů se spuštěnou instancí jedné instance kontejneru:

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

Nasaďte šablonu pomocí příkazu [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Během několika sekund by se měla zobrazit první odezva z Azure. Úspěšné nasazení se provádí na vyhrazeném hostiteli.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

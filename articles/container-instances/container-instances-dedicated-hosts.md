---
title: Nasazení na vyhrazené hostitele
description: Použití vyhrazených hostitelů k dosažení skutečné izolace na úrovni hostitele pro vaše úlohy
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903753"
---
# <a name="deploy-on-dedicated-hosts"></a>Nasazení na vyhrazené hostitele

"Vyhrazeným" je SKU Azure Container Instances (ACI), které poskytuje izolované a vyhrazené výpočetní prostředí pro bezpečné spouštění kontejnerů. Výsledkem použití vyhrazené skladové položky v každé skupině kontejnerů je vyhrazený fyzický server v datovém centru Azure, které zajišťuje úplnou izolaci úloh, aby bylo možné lépe vyhovovat požadavkům vaší organizace na zabezpečení a dodržování předpisů. 

Vyhrazená SKU je vhodná pro úlohy kontejneru, které vyžadují izolaci úloh z pohledu fyzického serveru.

## <a name="using-the-dedicated-sku"></a>Použití vyhrazené SKU

> [!IMPORTANT]
> Použití vyhrazené SKU je dostupné jenom v nejnovější verzi rozhraní API (2019-12-01), která se v tuto chvíli zavádí. Tuto verzi rozhraní API zadejte v šabloně nasazení. Kromě toho je výchozí omezení pro všechna předplatná, která používají vyhrazené SKU, 0. Pokud chcete tuto sku použít pro nasazení produkčního kontejneru, vytvořte prosím [support Request Azure][azure-support] .

Počínaje rozhraním API verze 2019-12-01 existuje vlastnost SKU v části vlastností skupiny kontejnerů v šabloně nasazení, která je nutná pro nasazení ACI. V současné době můžete tuto vlastnost použít jako součást šablony nasazení Azure Resource Manager pro ACI. Další informace o nasazení prostředků ACI pomocí šablony v tomto [kurzu: nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Vlastnost SKU může mít jednu z následujících hodnot:
* Standard – volba standardního nasazení ACI, která stále garantuje zabezpečení na úrovni hypervisoru 
* Vyhrazené – používá se pro izolaci na úrovni pracovního vytížení s vyhrazenými fyzickými hostiteli pro skupinu kontejnerů.

## <a name="modify-your-json-deployment-template"></a>Úprava šablony nasazení JSON

V šabloně nasazení, kde je určen prostředek skupiny kontejnerů, zajistěte, aby `"apiVersion": "2019-12-01",`. V části vlastnosti prostředku skupiny kontejnerů nastavte `"sku": "Dedicated",`.

Tady je příklad fragmentu pro oddíl Resources v šabloně nasazení skupiny kontejnerů, která používá vyhrazenou SKU:

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
                "osType": "Linux",
            },
            "location": "eastus2euap",
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

Šablonu nasaďte pomocí příkazu [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Během několika sekund by se měla zobrazit první odezva z Azure. Až se nasazení dokončí, všechna data související s tím, která jsou trvale zachovaná službou ACI, se zašifrují pomocí zadaného klíče.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

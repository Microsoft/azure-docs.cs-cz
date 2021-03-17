---
title: Kurz – nasazení skupiny více kontejnerů – šablona
description: V tomto kurzu se naučíte, jak nasadit skupinu kontejnerů s více kontejnery v Azure Container Instances pomocí Azure Resource Manager šablony pomocí Azure CLI.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bc956bed8324398c2d60f4641cd0bcb821fb51c2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091340"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Kurz: nasazení skupiny s více kontejnery pomocí šablony Správce prostředků

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances podporuje nasazení více kontejnerů do jednoho hostitele pomocí [skupiny kontejnerů](container-instances-container-groups.md). Skupina kontejnerů je užitečná při sestavování postranního vozíku aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci, kde služba potřebuje druhý připojený proces.

V tomto kurzu budete postupovat podle kroků pro spuštění jednoduché konfigurace na dvou kontejnerech, a to nasazením šablony Azure Resource Manager pomocí Azure CLI. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace více kontejnerových šablon skupiny
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

Šablonu Správce prostředků lze snadno přizpůsobit pro scénáře, pokud potřebujete nasadit další prostředky služby Azure (například sdílenou složku Azure Files nebo virtuální síť) se skupinou kontejnerů. 

> [!NOTE]
> Skupiny více kontejnerů jsou aktuálně omezené na kontejnery Linux. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>Konfigurace šablony

Začněte tím, že zkopírujete následující JSON do nového souboru s názvem `azuredeploy.json` . V Azure Cloud Shell můžete pomocí Visual Studio Code vytvořit soubor v pracovním adresáři:

```
code azuredeploy.json
```

Tato šablona Správce prostředků definuje skupinu kontejnerů se dvěma kontejnery, veřejnou IP adresou a dvěma vystavenými porty. První kontejner ve skupině spouští internetovou webovou aplikaci. Druhý kontejner, postranní vozík, provede požadavek HTTP hlavní webové aplikaci prostřednictvím místní sítě skupiny.

```JSON
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
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

K použití privátního registru imagí kontejneru přidejte objekt do dokumentu JSON s následujícím formátem. Ukázkovou implementaci této konfigurace najdete v referenční dokumentaci k [ACI správce prostředků Template][template-reference] .

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Nasazení šablony

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasaďte šablonu pomocí příkazu [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

Během několika sekund by se měla zobrazit první odezva z Azure.

## <a name="view-deployment-state"></a>Zobrazit stav nasazení

Chcete-li zobrazit stav nasazení, použijte následující příkaz [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Pokud chcete zobrazit spuštěnou aplikaci, přejděte v prohlížeči na jeho IP adresu. Například IP adresa je `52.168.26.124` v tomto příkladu výstupu:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Zobrazte výstup protokolu kontejneru pomocí příkazu [AZ Container logs][az-container-logs] . `--container-name`Argument určuje kontejner, ze kterého mají být vyžádané protokoly. V tomto příkladu `aci-tutorial-app` je určen kontejner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Výstup:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Chcete-li zobrazit protokoly pro kontejner vozíku, spusťte podobný příkaz určující `aci-tutorial-sidecar` kontejner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Výstup:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Jak vidíte, postranní vozík pravidelně vytváří požadavek HTTP do hlavní webové aplikace prostřednictvím místní sítě skupiny, aby bylo zajištěno, že je spuštěný. Tento příklad postranního vozíku se dá rozšířit tak, aby aktivoval výstrahu, pokud obdržel jiný kód odpovědi HTTP než `200 OK` .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili šablonu Azure Resource Manager k nasazení skupiny více kontejnerů v Azure Container Instances. Naučili jste se:

> [!div class="checklist"]
> * Konfigurace více kontejnerových šablon skupiny
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

Další ukázky šablon najdete v tématu [šablony Azure Resource Manager pro Azure Container Instances](container-instances-samples-rm.md).

Skupinu s více kontejnery můžete zadat také pomocí [souboru YAML](container-instances-multi-container-yaml.md). Vzhledem k výstižnější povaze formátu YAML je nasazení se souborem YAML vhodné, pokud vaše nasazení zahrnuje jenom instance kontejnerů.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups

---
title: Kurz – nasazení skupiny s více kontejnery – šablona
description: V tomto kurzu se dozvíte, jak nasadit skupinu kontejnerů s více kontejnery v instanci kontejneru Azure pomocí šablony Azure Resource Manager s Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533624"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Kurz: Nasazení skupiny s více kontejnery pomocí šablony Správce prostředků

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances podporuje nasazení více kontejnerů na jednoho hostitele pomocí [skupiny kontejnerů](container-instances-container-groups.md). Skupina kontejnerů je užitečná při vytváření postranního varu aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci, kde služba potřebuje druhý připojený proces.

V tomto kurzu postupujte podle kroků ke spuštění jednoduché konfigurace sajdcovna dvou kontejnerů nasazením šablony Azure Resource Manager pomocí azure cli. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace šablony skupiny s více kontejnery
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

Šablonu Správce prostředků lze snadno přizpůsobit pro scénáře, kdy potřebujete nasadit další prostředky služby Azure (například sdílené složky Azure Files nebo virtuální síť) se skupinou kontejnerů. 

> [!NOTE]
> Skupiny s více kontejnery jsou aktuálně omezeny na kontejnery Linuxu. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Konfigurace šablony

Začněte zkopírováním následujícího jsonu `azuredeploy.json`do nového souboru s názvem . V Prostředí Azure Cloud Shell můžete použít visual studio kód k vytvoření souboru ve vašem pracovním adresáři:

```
code azuredeploy.json
```

Tato šablona Správce prostředků definuje skupinu kontejnerů se dvěma kontejnery, veřejnou IP adresou a dvěma exponovanými porty. První kontejner ve skupině spouští internetovou webovou aplikaci. Druhý kontejner, sajdkaři, vytvoří požadavek HTTP na hlavní webovou aplikaci prostřednictvím místní sítě skupiny.

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
      "apiVersion": "2018-10-01",
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
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
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

Chcete-li použít registr bitových obrázků soukromého kontejneru, přidejte objekt do dokumentu JSON v následujícím formátu. Příklad implementace této konfigurace naleznete v referenční dokumentaci [k šabloně Správce prostředků ACI.][template-reference]

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

Nasaďte šablonu pomocí příkazu [vytvořit nasazení skupiny az.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Během několika sekund by se měla zobrazit první odezva z Azure.

## <a name="view-deployment-state"></a>Zobrazit stav nasazení

Chcete-li zobrazit stav nasazení, použijte následující příkaz [az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Pokud chcete zobrazit spuštěnou aplikaci, přejděte v prohlížeči na její IP adresu. Například IP je `52.168.26.124` v tomto příkladu výstupu:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Zobrazení výstupu protokolu kontejneru pomocí příkazu [az container logs.][az-container-logs] Argument `--container-name` určuje kontejner, ze kterého chcete vyžádat protokoly. V tomto příkladu `aci-tutorial-app` je určen kontejner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Výstup:

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Chcete-li zobrazit protokoly pro kontejner sajdkár, spusťte podobný příkaz určující `aci-tutorial-sidecar` kontejner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Výstup:

```bash
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

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
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Jak můžete vidět, sajdkár pravidelně provádí požadavek HTTP na hlavní webovou aplikaci prostřednictvím místní sítě skupiny, aby bylo zajištěno, že je spuštěn. Tento příklad postranního sajdkáře by mohl být `200 OK`rozšířen tak, aby aktivoval výstrahu, pokud obdržel jiný kód odpovědi HTTP než .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili šablonu Azure Resource Manager k nasazení skupiny více kontejnerů v instanci kontejneru Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurace šablony skupiny s více kontejnery
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

Další ukázky šablon najdete v [tématu šablony Azure Resource Manager pro instance kontejnerů Azure](container-instances-samples-rm.md).

Můžete také určit skupinu více kontejnerů pomocí [souboru YAML](container-instances-multi-container-yaml.md). Vzhledem k stručnější povaze formátu YAML je nasazení se souborem YAML dobrou volbou, když vaše nasazení obsahuje pouze instance kontejnerů.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

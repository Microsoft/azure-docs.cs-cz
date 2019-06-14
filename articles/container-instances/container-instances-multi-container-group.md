---
title: Kurz – nasazení skupiny více kontejnerů ve službě Azure Container Instances – šablony
description: V tomto kurzu se dozvíte, jak nasadit skupinu kontejnerů s několika kontejnerů ve službě Azure Container Instances pomocí šablony Azure Resource Manageru pomocí rozhraní příkazového řádku Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f769beda1654dc9f58ecff733741fb1ab9118031
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152301"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Kurz: Nasazení skupiny více kontejnerů pomocí šablony Resource Manageru

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Služba Azure Container Instances podporuje nasazení několika kontejnerů na jednom hostiteli pomocí [skupinu kontejnerů](container-instances-container-groups.md). Skupiny kontejnerů je užitečné při vytváření sajdkára aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci Pokud služba potřebuje druhý připojený proces.

V tomto kurzu postupujte podle kroků pro spuštění konfigurace jednoduché dvě kontejner sajdkára nasazením šablony Azure Resource Manageru pomocí rozhraní příkazového řádku Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace skupiny vícekontejnerové šablony
> * Nasazení skupiny kontejnerů
> * Zobrazení protokolů z kontejnerů

Šablony Resource Manageru můžete snadno upravit pro scénáře, když budete chtít nasadit prostředky dalších služeb Azure (třeba sdílenou složku služby soubory Azure nebo virtuální sítě) s skupiny kontejnerů. 

> [!NOTE]
> Skupiny vícekontejnerové jsou momentálně omezené jenom na Linuxové kontejnery. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Konfigurace šablony

Začněte tak, že zkopírujete následující kód JSON do nového souboru s názvem `azuredeploy.json`. Ve službě Azure Cloud Shell můžete použít Visual Studio Code k vytvoření souboru ve svém pracovním adresáři:

```
code azuredeploy.json
```

Tuto šablonu Resource Manageru definuje skupinu kontejnerů se dvěma kontejnery, veřejnou IP adresu a dva vystavené porty. První kontejner ve skupině spustí webovou aplikaci přístupem k Internetu. Druhý kontejner sajdkára, odešle požadavek HTTP hlavní webové aplikace prostřednictvím místní sítě skupiny.

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

Pokud chcete použít image privátní registr, přidání objektu do dokumentu JSON v následujícím formátu. Příklad implementace této konfigurace, najdete v článku [referenčními informacemi k šablonám Resource Manageru ACI] [ template-reference] dokumentaci.

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

Nasazení šablony s [vytvořit nasazení skupiny pro az] [ az-group-deployment-create] příkazu.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Během několika sekund by se měla zobrazit první odezva z Azure.

## <a name="view-deployment-state"></a>Zobrazení stavu nasazení

Chcete-li zobrazit stav nasazení, použijte následující [az container show] [ az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Pokud chcete zobrazit spuštěnou aplikaci, přejděte na jeho IP adresa v prohlížeči. Například IP adresa je `52.168.26.124` tento ukázkový výstup:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Zobrazit výstup protokolu kontejneru pomocí [protokoly kontejneru az] [ az-container-logs] příkazu. `--container-name` Argument určuje kontejner, ze kterého chcete vyžádat protokoly. V tomto příkladu `aci-tutorial-app` určen kontejner.

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

Pokud chcete zobrazit protokoly pro kontejner sajdkára, spusťte podobný příkaz zadání `aci-tutorial-sidecar` kontejneru.

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

Jak je vidět, sajdkára pravidelně provádí požadavek HTTP do hlavní webové aplikace prostřednictvím místní sítě skupiny k zajištění, že je spuštěná. V tomto příkladu sajdkára může rozšířit tak, aby aktivuje výstrahu, pokud ho kód odpovědi HTTP přijaté jiné než `200 OK`.

## <a name="next-steps"></a>Další postup

V tomto kurzu pomocí šablony Azure Resource Manageru k nasazení skupiny více kontejnerů ve službě Azure Container Instances. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurace skupiny vícekontejnerové šablony
> * Nasazení skupiny kontejnerů
> * Zobrazení protokolů z kontejnerů

Další šablony ukázky najdete v tématu [šablony Azure Resource Manageru pro Azure Container Instances](container-instances-samples-rm.md).

Můžete také určit skupiny vícekontejnerové pomocí [soubor YAML](container-instances-multi-container-yaml.md). Z důvodu stručnější povahy formátu YAML nasazení pomocí souboru YAML je dobrou volbou, pokud vaše nasazení obsahuje pouze instance kontejneru.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

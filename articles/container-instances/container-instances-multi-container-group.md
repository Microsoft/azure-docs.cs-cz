---
title: Nasazení skupin více kontejnerů ve službě Azure Container Instances
description: Zjistěte, jak nasadit skupinu kontejnerů s několika kontejnerů ve službě Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: adb284772291dc901dd5302124982948c1f37eea
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856467"
---
# <a name="deploy-a-container-group"></a>Nasazení skupiny kontejnerů

Služba Azure Container Instances podporuje nasazení několika kontejnerů na jednom hostiteli pomocí [skupinu kontejnerů](container-instances-container-groups.md). To je užitečné při vytváření sajdkára aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci Pokud služba potřebuje druhý připojený proces.

Existují dvě metody pro nasazení skupin více kontejnerů pomocí Azure CLI:

* Nasazení šablony Resource Manageru (Tento článek)
* [Nasazení souboru YAML](container-instances-multi-container-yaml.md)

Nasazení pomocí šablony Resource Manageru se doporučuje, pokud potřebujete nasadit prostředky dalších služeb Azure (například do soubory Azure sdílené složky) v době nasazování instancí kontejnerů. Protože potřebujeme stručnější formátu YAML, se doporučuje nasazení pomocí souboru YAML, pokud vaše nasazení obsahuje *pouze* container instances.

> [!NOTE]
> Skupiny vícekontejnerové jsou momentálně omezené jenom na Linuxové kontejnery. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-template"></a>Konfigurace šablony

Části v tomto článku vás provedou spuštěním jednoduché sajdkára vícekontejnerová konfigurace nasazení šablony Azure Resource Manageru.

Začněte vytvořením souboru s názvem `azuredeploy.json`, pak zkopírujte do něj následující kód JSON.

Tuto šablonu Resource Manageru definuje skupinu kontejnerů se dvěma kontejnery, veřejnou IP adresu a dva vystavené porty. První kontejner ve skupině spustí aplikaci přístupem k Internetu. Druhý kontejner sajdkára, odešle požadavek HTTP hlavní webové aplikace prostřednictvím místní sítě skupiny.

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
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
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
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Zobrazení protokolů

Zobrazit výstup protokolu kontejneru pomocí [protokoly kontejneru az] [ az-container-logs] příkazu. `--container-name` Argument určuje kontejner, ze kterého chcete vyžádat protokoly. V tomto příkladu je zadán první kontejner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Výstup:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Pokud chcete zobrazit protokoly pro kontejner postranním, spusťte stejný příkaz druhý název kontejneru.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Výstup:

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

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
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Jak je vidět, sajdkára pravidelně provádí požadavek HTTP do hlavní webové aplikace prostřednictvím místní sítě skupiny k zajištění, že je spuštěná. V tomto příkladu sajdkára může rozšířit aktivuje výstrahu, pokud kód odpovědi HTTP než 200 OK dostali.

## <a name="next-steps"></a>Další postup

Tento článek popisuje kroky potřebné pro nasazení více kontejnerů Azure container instance. Prostředí Azure Container Instances začátku do konce najdete v kurzu Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

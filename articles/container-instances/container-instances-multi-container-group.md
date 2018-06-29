---
title: Nasazení skupiny více kontejnerů v Azure kontejner instancí
description: Postup nasazení kontejneru skupiny pomocí několika kontejnerů v Azure kontejner instancí.
services: container-instances
author: iainfoulds
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6d337c9ed23ac9af884f4113b046a8e9756fd441
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097100"
---
# <a name="deploy-a-container-group"></a>Nasazení kontejneru skupiny

Podporuje nasazení několika kontejnerů do jednoho hostitele pomocí Azure instancí kontejnerů [skupina kontejneru](container-instances-container-groups.md). To je užitečné při sestavování něho aplikace pro protokolování, sledování nebo jakoukoli jinou konfiguraci, které služba potřebuje druhý připojené procesu.

Existují dvě metody pro nasazení pomocí rozhraní příkazového řádku Azure skupiny více kontejnerů:

* Nasazení šablony Resource Manageru (v tomto článku)
* [Nasazení YAML souborů](container-instances-multi-container-yaml.md)

Nasazení pomocí šablony Resource Manageru se doporučuje, když potřebujete nasadit další služby Azure prostředky (například sdílení souborů Azure) v době nasazení instance kontejneru. Z důvodu YAML formátu přesnější povahy, se doporučuje nasazení se souborem YAML Pokud nasazení obsahuje *pouze* instancí kontejnerů.

> [!NOTE]
> Více kontejner skupiny jsou aktuálně omezeno na kontejnery Linux. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-template"></a>Konfigurace šablony

Části v tomto článku vás provede procesem službou Jednoduché něho více kontejneru konfigurace a nasazení šablonu Azure Resource Manager.

Začněte vytvořením soubor s názvem `azuredeploy.json`, zkopírujte do ní následující kód JSON.

Tato šablona Resource Manager definuje skupinu kontejner s dvěma kontejnery, veřejnou IP adresu a dva porty zveřejněné. První kontejner ve skupině běží Internetové aplikace. Druhý kontejneru něho, zašle požadavek HTTP do hlavní webové aplikace prostřednictvím místní sítě skupiny.

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

Použít privátní kontejneru registru bitovou kopii, přidáte objekt v dokumentu JSON v následujícím formátu. Příklad implementace této konfigurace, najdete v článku [odkaz na šablonu ACI Resource Manager] [ template-reference] dokumentaci.

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

Nasazení šablony s [vytvořit nasazení skupiny az] [ az-group-deployment-create] příkaz.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Během několika sekund by se měla zobrazit první odezva z Azure.

## <a name="view-deployment-state"></a>Zobrazení stavu nasazení

Chcete-li zobrazit stav nasazení, použijte následující [az kontejneru zobrazit] [ az-container-show] příkaz:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Pokud chcete zobrazit běžící aplikaci, přejděte na jeho IP adresu v prohlížeči. Například IP adresa je `52.168.26.124` v tomto příkladu výstupu:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Zobrazení protokolů

Zobrazit výstup protokolu kontejneru pomocí [az kontejneru protokoly] [ az-container-logs] příkaz. `--container-name` Argument určuje kontejner, ze kterého protokoly pro vyžádání obsahu. V tomto příkladu je zadána první kontejneru.

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

Pokud chcete zobrazit protokoly pro kontejner straně car, spusťte stejný příkaz zadání druhý název kontejneru.

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

Jak můžete vidět, je něho pravidelně zajistit požadavek HTTP je hlavní webové aplikace prostřednictvím místní sítě skupiny zkontrolujte, zda je spuštěn. Tento příklad něho může rozšířit, aby spuštění výstrahy v případě obdržel kódu odpovědi HTTP než 200 OK.

## <a name="next-steps"></a>Další postup

Tento článek popsané kroky potřebné pro nasazení s více kontejnerů Azure container instance. Prostředí Azure kontejner instancí začátku do konce naleznete v kurzu instancí kontejnerů Azure.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

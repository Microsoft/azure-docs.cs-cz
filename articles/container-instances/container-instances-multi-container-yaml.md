---
title: Nasazení skupin více kontejnerů ve službě Azure Container Instances pomocí rozhraní příkazového řádku Azure a YAML
description: Zjistěte, jak nasadit skupinu kontejnerů s několika kontejnerů ve službě Azure Container Instances pomocí rozhraní příkazového řádku Azure a soubor YAML.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/17/2018
ms.author: marsma
ms.openlocfilehash: 1d1885112b8e7f7b1e187073c86d561eb57fd23f
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114459"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Nasazení skupiny vícekontejnerové kontejneru s YAML

Služba Azure Container Instances podporuje nasazení několika kontejnerů na jednom hostiteli s použitím [skupinu kontejnerů](container-instances-container-groups.md). Skupiny vícekontejnerové kontejnerů jsou užitečné při vytváření sajdkára aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci Pokud služba potřebuje druhý připojený proces.

Existují dvě metody pro nasazení skupin více kontejnerů pomocí Azure CLI:

* Nasazení souboru YAML (Tento článek)
* [Nasazení šablony Resource Manageru](container-instances-multi-container-group.md)

Protože potřebujeme stručnější formátu YAML, se doporučuje nasazení pomocí souboru YAML, pokud vaše nasazení obsahuje *pouze* container instances. Pokud potřebujete nasadit prostředky dalších služeb Azure (například do soubory Azure sdílené složky) v době nasazování instancí kontejnerů, doporučuje se nasazení šablony Resource Manageru.

> [!NOTE]
> Skupiny vícekontejnerové jsou momentálně omezené jenom na Linuxové kontejnery. Pracujeme na všechny funkce byly do kontejnerů Windows, můžete najít aktuální rozdíly pro tyto platformy v [kvóty a dostupnost oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Konfigurovat soubor YAML

Nasazení skupiny vícekontejnerové kontejnerů s [az container vytvořit] [ az-container-create] příkaz v rozhraní příkazového řádku Azure, musíte zadat konfigurace kontejneru skupiny v souboru YAML a předat jako soubor YAML parametr k příkazu.

Začněte tak, že zkopírujete následující kód YAML do nového souboru s názvem **nasazení aci.yaml**.

Tento soubor YAML definuje skupinu kontejnerů s názvem "myContainerGroup" se dvěma kontejnery, veřejnou IP adresu a dva vystavené porty. První kontejner ve skupině spustí webovou aplikaci přístupem k Internetu. Druhý kontejner sajdkára, pravidelně vytváří požadavky HTTP k webové aplikaci spuštěné v kontejneru první prostřednictvím místní sítě skupinu kontejnerů.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>Nasazení skupiny kontejnerů

Vytvořte skupinu prostředků pomocí [vytvořit skupiny az] [ az-group-create] příkaz:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasadit skupinu kontejnerů s [az container vytvořit] [ az-container-create] předáním jako argument soubor YAML, příkaz:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Zobrazení protokolů

Zobrazit výstup protokolu kontejneru pomocí [protokoly kontejneru az] [ az-container-logs] příkazu. `--container-name` Argument určuje kontejner, ze kterého chcete vyžádat protokoly. V tomto příkladu je zadán první kontejner.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Výstup:

```console
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

```console
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

## <a name="deploy-from-private-registry"></a>Nasazení z privátního registru

Chcete-li použít obraz privátní registr, patří následující kód YAML hodnotami upravit pro vaše prostředí:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Například následující kód YAML nasadí skupinu kontejnerů s jedním kontejnerem, jehož bitové kopie se načítají z privátního registru kontejneru Azure s názvem "myregistry":

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Export skupiny kontejnerů YAML

Konfigurace stávající skupině kontejnerů můžete exportovat do souboru YAML pomocí příkazového řádku Azure [az container export][az-container-export].

Užitečné pro zachování konfigurace skupiny kontejnerů, export umožňuje ukládat vaše konfigurace skupiny kontejnerů ve správě verzí pro "konfigurace jako kódu." Nebo můžete použít exportovaný soubor jako výchozí bod při vývoji nové konfigurace v YAML.

Exportovat konfiguraci pro skupinu kontejnerů, který jste vytvořili dříve, pomocí následujících [az container export] [ az-container-export] příkaz:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Pokud příkaz byl úspěšný, ale můžete zobrazit obsah souboru k zobrazení výsledku, nezobrazí se žádný výstup. Například několik prvních řádků s `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
```

## <a name="next-steps"></a>Další postup

Tento článek popisuje kroky potřebné pro nasazení více kontejnerů Azure container instance. Prostředí Azure Container Instances začátku do konce, včetně použití privátní registr kontejnerů Azure najdete v kurzu Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

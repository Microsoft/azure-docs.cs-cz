---
title: Kurz – nasazení skupiny více kontejnerů – YAML
description: V tomto kurzu se naučíte, jak nasadit skupinu kontejnerů s více kontejnery v Azure Container Instances pomocí souboru YAML pomocí Azure CLI.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 6f9dda7735587dfee1dde86c85375efcf057daa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605157"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Kurz: nasazení skupiny s více kontejnery pomocí souboru YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances podporuje nasazení více kontejnerů do jednoho hostitele pomocí [skupiny kontejnerů](container-instances-container-groups.md). Skupina kontejnerů je užitečná při sestavování postranního vozíku aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci, kde služba potřebuje druhý připojený proces.

V tomto kurzu budete postupovat podle kroků pro spuštění jednoduché konfigurace na dvou kontejnerech, a to nasazením [souboru YAML](container-instances-reference-yaml.md) pomocí Azure CLI. Soubor YAML poskytuje stručný formát pro zadání nastavení instance. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace souboru YAML
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

> [!NOTE]
> Skupiny více kontejnerů jsou aktuálně omezené na kontejnery Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Konfigurace souboru YAML

Pokud chcete nasadit skupinu s více kontejnery pomocí příkazu [AZ Container Create][az-container-create] v rozhraní příkazového řádku Azure, musíte v souboru YAML zadat konfiguraci skupiny kontejnerů. Pak předejte soubor YAML jako parametr do příkazu.

Začněte zkopírováním následujících YAML do nového souboru nazvaného **Deploy-ACI. yaml**. V Azure Cloud Shell můžete pomocí Visual Studio Code vytvořit soubor v pracovním adresáři:

```
code deploy-aci.yaml
```

Tento soubor YAML definuje skupinu kontejnerů s názvem "myContainerGroup" se dvěma kontejnery, veřejnou IP adresou a dvěma vystavenými porty. Kontejnery se nasazují z veřejných imagí Microsoft. První kontejner ve skupině spouští internetovou webovou aplikaci. Druhý kontejner, postranní vozík, pravidelně vytváří požadavky HTTP na webovou aplikaci spuštěnou v prvním kontejneru prostřednictvím místní sítě skupiny kontejnerů.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Pro použití privátního registru image kontejneru přidejte `imageRegistryCredentials` vlastnost do skupiny kontejnerů s hodnotami upravenými pro vaše prostředí:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Nasazení skupiny kontejnerů

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasaďte skupinu kontejnerů pomocí příkazu [AZ Container Create][az-container-create] a předáním souboru YAML jako argumentu:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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

V tomto kurzu jste použili soubor YAML k nasazení skupiny více kontejnerů v Azure Container Instances. Naučili jste se:

> [!div class="checklist"]
> * Konfigurace souboru YAML pro skupinu s více kontejnery
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

Skupinu s více kontejnery můžete zadat také pomocí [šablony Správce prostředků](container-instances-multi-container-group.md). Šablonu Správce prostředků lze snadno přizpůsobit pro scénáře, pokud potřebujete nasadit další prostředky služby Azure ve skupině kontejnerů.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups

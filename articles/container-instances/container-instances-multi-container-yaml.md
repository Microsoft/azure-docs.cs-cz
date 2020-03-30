---
title: Kurz – nasazení skupiny s více kontejnery – YAML
description: V tomto kurzu se dozvíte, jak nasadit skupinu kontejnerů s více kontejnery v instanci kontejneru Azure pomocí souboru YAML s Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533597"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Kurz: Nasazení skupiny s více kontejnery pomocí souboru YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances podporuje nasazení více kontejnerů na jednoho hostitele pomocí [skupiny kontejnerů](container-instances-container-groups.md). Skupina kontejnerů je užitečná při vytváření postranního varu aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci, kde služba potřebuje druhý připojený proces.

V tomto kurzu postupujte podle kroků ke spuštění jednoduché konfigurace sajdcovna se dvěma kontejnery nasazením [souboru YAML](container-instances-reference-yaml.md) pomocí azure CLI. Soubor YAML poskytuje stručný formát pro určení nastavení instance. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace souboru YAML
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

> [!NOTE]
> Skupiny s více kontejnery jsou aktuálně omezeny na kontejnery Linuxu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Konfigurace souboru YAML

Chcete-li nasadit skupinu více kontejnerů s příkazem [az kontejner u vytvoření][az-container-create] v příkazovém příkazu Konto Azure, musíte zadat konfiguraci skupiny kontejnerů v souboru YAML. Potom předaj soubor YAML jako parametr příkazu.

Začněte zkopírováním následujícího souboru YAML do nového souboru s názvem **deploy-aci.yaml**. V Prostředí Azure Cloud Shell můžete použít visual studio kód k vytvoření souboru ve vašem pracovním adresáři:

```
code deploy-aci.yaml
```

Tento soubor YAML definuje skupinu kontejnerů s názvem "myContainerGroup" se dvěma kontejnery, veřejnou IP adresou a dvěma exponovanými porty. Kontejnery jsou nasazeny z veřejných iimages Microsoft. První kontejner ve skupině spouští internetovou webovou aplikaci. Druhý kontejner, sajdkárna, pravidelně provádí požadavky HTTP pro webovou aplikaci spuštěnou v prvním kontejneru prostřednictvím místní sítě skupiny kontejnerů.

```YAML
apiVersion: 2018-10-01
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
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Chcete-li použít registr bitových `imageRegistryCredentials` bitových obrázků soukromého kontejneru, přidejte vlastnost do skupiny kontejnerů s hodnotami upravenými pro vaše prostředí:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Nasazení skupiny kontejnerů

Vytvořte skupinu prostředků pomocí příkazu [vytvořit skupinu az:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasazení skupiny kontejnerů pomocí příkazu [az container create][az-container-create] a předání souboru YAML jako argumentu:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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

```console
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

```console
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

V tomto kurzu jste použili soubor YAML k nasazení skupiny více kontejnerů v azure container instances. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurace souboru YAML pro skupinu s více kontejnery
> * Nasazení skupiny kontejnerů
> * Zobrazit protokoly kontejnerů

Skupinu s více kontejnery můžete také určit pomocí [šablony Správce prostředků](container-instances-multi-container-group.md). Šablonu Správce prostředků lze snadno přizpůsobit pro scénáře, když potřebujete nasadit další prostředky služby Azure se skupinou kontejnerů.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

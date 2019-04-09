---
title: Kurz – nasazení skupiny více kontejnerů ve službě Azure Container Instances – YAML
description: V tomto kurzu se dozvíte, jak nasadit skupinu kontejnerů s několika kontejnerů ve službě Azure Container Instances pomocí souboru YAML pomocí Azure CLI.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a0a91ece4f219cf822673cd457c064c326b89478
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006187"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Kurz: Nasazení skupiny více kontejnerů pomocí souboru YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Služba Azure Container Instances podporuje nasazení několika kontejnerů na jednom hostiteli pomocí [skupinu kontejnerů](container-instances-container-groups.md). Skupiny kontejnerů je užitečné při vytváření sajdkára aplikace pro protokolování, monitorování nebo jakoukoli jinou konfiguraci Pokud služba potřebuje druhý připojený proces.

V tomto kurzu postupujte podle kroků pro spuštění konfigurace jednoduché dvě kontejner sajdkára nasazením soubor YAML pomocí Azure CLI. Soubor YAML poskytuje stručný formát pro zadání nastavení instance. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurovat soubor YAML
> * Nasazení skupiny kontejnerů
> * Zobrazení protokolů z kontejnerů

> [!NOTE]
> Skupiny vícekontejnerové jsou momentálně omezené jenom na Linuxové kontejnery.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Konfigurovat soubor YAML

Nasazení skupiny více kontejnerů s [az container vytvořit] [ az-container-create] příkaz v rozhraní příkazového řádku Azure, je nutné zadat konfigurace kontejneru skupiny v souboru YAML. Potom tento soubor YAML předejte jako parametr k příkazu.

Začněte tak, že zkopírujete následující kód YAML do nového souboru s názvem **nasazení aci.yaml**. Ve službě Azure Cloud Shell můžete použít Visual Studio Code k vytvoření souboru ve svém pracovním adresáři:

```
code deploy-aci.yaml
```

Tento soubor YAML definuje skupinu kontejnerů s názvem "myContainerGroup" se dvěma kontejnery, veřejnou IP adresu a dva vystavené porty. Kontejnery se nasazují z veřejné Image Microsoft. První kontejner ve skupině spustí webovou aplikaci přístupem k Internetu. Druhý kontejner sajdkára, pravidelně vytváří požadavky HTTP k webové aplikaci spuštěné v kontejneru první prostřednictvím místní sítě skupinu kontejnerů.

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

Chcete-li použít obraz privátní registr, přidejte `imageRegistryCredentials` vlastnost skupina kontejnerů s hodnotami upravit pro vaše prostředí:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
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

```console
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

Jak je vidět, sajdkára pravidelně provádí požadavek HTTP do hlavní webové aplikace prostřednictvím místní sítě skupiny k zajištění, že je spuštěná. V tomto příkladu sajdkára může rozšířit tak, aby aktivuje výstrahu, pokud ho kód odpovědi HTTP přijaté jiné než `200 OK`.

## <a name="next-steps"></a>Další postup

V tomto kurzu se používá soubor YAML pro nasazení skupiny více kontejnerů ve službě Azure Container Instances. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurovat soubor YAML pro skupiny více kontejnerů
> * Nasazení skupiny kontejnerů
> * Zobrazení protokolů z kontejnerů

Můžete také určit skupiny vícekontejnerové pomocí [šablony Resource Manageru](container-instances-multi-container-group.md). Šablony Resource Manageru můžete snadno upravit pro scénáře, když budete chtít nasadit prostředky dalších služeb Azure s skupiny kontejnerů.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

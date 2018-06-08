---
title: Nasazení skupiny více kontejnerů v Azure instancí kontejnerů pomocí rozhraní příkazového řádku Azure a YAML
description: Informace o nasazení pomocí rozhraní příkazového řádku Azure a soubor YAML skupina kontejneru s několika kontejnerů v Azure kontejner instancí.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: marsma
ms.openlocfilehash: 5dfee15e978d2dba0f50d1dc4b78953698389950
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851240"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Nasazení několika kontejneru kontejner skupiny pomocí YAML

Azure instancí kontejnerů podporuje nasazení několika kontejnerů na jednom hostiteli pomocí [skupina kontejneru](container-instances-container-groups.md). Kontejner s více kontejner skupiny jsou užitečné při sestavování něho aplikace pro protokolování, sledování nebo jakoukoli jinou konfiguraci, které služba potřebuje druhý připojené procesu.

Existují dvě metody pro nasazení pomocí rozhraní příkazového řádku Azure skupiny více kontejnerů:

* Nasazení YAML souborů (v tomto článku)
* [Nasazení šablony Resource Manageru](container-instances-multi-container-group.md)

Z důvodu YAML formátu přesnější povahy, se doporučuje nasazení se souborem YAML Pokud nasazení obsahuje *pouze* instancí kontejnerů. Pokud potřebujete nasadit další služby Azure prostředky (například sdílení souborů Azure) v době nasazení instance kontejneru, doporučuje se nasazení šablony Resource Manageru.

> [!NOTE]
> Více kontejner skupiny jsou aktuálně omezeno na kontejnery Linux. Při pracujeme na uvede všechny funkce Windows kontejnery, můžete najít aktuální platformy rozdíly v [kvóty a dostupnost v oblastech Azure kontejner instancí](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Konfigurace souboru YAML

K nasazení kontejneru více kontejner skupiny pomocí [vytvořit kontejner az] [ az-container-create] příkaz v Azure CLI, je nutné zadat skupinu konfiguraci kontejneru YAML souboru a potom předat YAML soubor jako parametr k příkazu.

Začněte tím, že kopírování následující YAML do nového souboru s názvem **nasazení aci.yaml**.

Tento soubor YAML definuje skupinu kontejner s dvěma kontejnery, veřejnou IP adresu a dva porty zveřejněné. První kontejner ve skupině spustí webovou aplikaci internetové brány. Druhý kontejneru něho pravidelně odešle požadavky HTTP webové aplikace běžící v první kontejneru prostřednictvím místní sítě skupina kontejneru.

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

## <a name="deploy-the-container-group"></a>Nasazení kontejneru skupiny

Vytvořte skupinu prostředků s [vytvořit skupinu az] [ az-group-create] příkaz:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasazení kontejneru skupiny pomocí [vytvořit kontejner az] [ az-container-create] příkazu předávání soubor YAML jako argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --name myContainerGroup -f deploy-aci.yaml
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
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Zobrazení protokolů

Zobrazit výstup protokolu kontejneru pomocí [az kontejneru protokoly] [ az-container-logs] příkaz. `--container-name` Argument určuje kontejner, ze kterého protokoly pro vyžádání obsahu. V tomto příkladu je zadána první kontejneru.

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

Pokud chcete zobrazit protokoly pro kontejner straně car, spusťte stejný příkaz zadání druhý název kontejneru.

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

Jak můžete vidět, je něho pravidelně zajistit požadavek HTTP je hlavní webové aplikace prostřednictvím místní sítě skupiny zkontrolujte, zda je spuštěn. Tento příklad něho může rozšířit, aby spuštění výstrahy v případě obdržel kódu odpovědi HTTP než 200 OK.

## <a name="deploy-from-private-registry"></a>Nasazení z privátní registru

Použít bitovou kopii registru privátní kontejneru, patří následující YAML s hodnotami upravit pro vaše prostředí:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Například následující YAML nasadí kontejner skupiny s jedním kontejnerem, jejichž image pocházejí z privátní registru kontejner Azure s názvem "myregistry":

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

## <a name="export-container-group-to-yaml"></a>Exportovat do YAML skupina kontejneru

Konfigurace existující kontejner skupiny můžete exportovat do souboru YAML pomocí příkazu příkazového řádku Azure CLI [export kontejneru az][az-container-export].

Užitečná k zachování skupina kontejneru konfigurace, export umožňuje ukládat vaše konfigurace skupiny kontejner ve správě verzí pro "konfigurace jako kód." Nebo použít exportovaný soubor jako výchozí bod při vývoji v YAML novou konfiguraci.

Export konfigurace pro kontejner skupinu, kterou jste vytvořili dříve vydáním následujících [export kontejneru az] [ az-container-export] příkaz:

```azurecli-interactive
az container export --resource-group rg604 --name myContainerGroup --file deployed-aci.yaml
```

Pokud úspěšně, ale můžete zobrazit obsah souboru zobrazíte výsledek, zobrazí se žádný výstup. Například několik prvních řádků s `head`:

```console
$ head deployed-aci.yaml
apiVersion: 2018-02-01-preview
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports:
```

> [!NOTE]
> Pro Azure CLI verze 2.0.34, je [známý problém] [ cli-issue-6525] které exportovali kontejner skupiny zadejte starší verze rozhraní API **2018-02-01-preview** (zobrazená v předchozí Příklad výstupu JSON). Pokud chcete znovu nasadit pomocí exportovaný soubor YAML, můžete bezpečně aktualizovat `apiVersion` hodnotu v exportovaném souboru YAML k **2018-06-01**.

## <a name="next-steps"></a>Další postup

Tento článek popsané kroky potřebné pro nasazení s více kontejnerů Azure container instance. Prostředí Azure kontejner instancí začátku do konce, včetně použití privátního kontejner Azure registr najdete v kurzu instancí kontejnerů Azure.

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

---
title: Připojení tajného svazku do skupiny kontejnerů
description: Zjistěte, jak připojit tajný svazek pro ukládání citlivých informací pro přístup k instancí kontejneru
ms.topic: article
ms.date: 07/19/2018
ms.openlocfilehash: 913e3d147519bc73c3c57b8da383f9d373f3666d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249941"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Připojení tajného svazku v instancích kontejnerů Azure

Pomocí *tajného* svazku dodávat citlivé informace do kontejnerů ve skupině kontejnerů. Tajný *secret* svazek ukládá tajné klíče v souborech v rámci svazku, přístupné kontejnery ve skupině kontejnerů. Uložením tajných kódů v *tajném* svazku se můžete vyhnout přidávání citlivých dat, jako jsou klíče SSH nebo pověření databáze, do kódu aplikace.

Všechny *tajné* svazky jsou zálohovány [tmpfs][tmpfs], souborový systém podporovaný ram; jejich obsah se nikdy nezapisuje do stálého úložiště.

> [!NOTE]
> *Tajné* svazky jsou v současné době omezeny na linuxové kontejnery. Zjistěte, jak předat proměnné zabezpečeného prostředí pro kontejnery Windows i Linux v [proměnných prostředí Set](container-instances-environment-variables.md). Zatímco pracujeme na tom, aby všechny funkce do kontejnerů Windows, můžete najít aktuální rozdíly platformy v [přehledu](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Připojit tajný svazek – Azure CLI

Chcete-li nasadit kontejner s jedním nebo více tajnými kódy pomocí rozhraní příkazového řádku Azure, zahrňte parametry `--secrets` a `--secrets-mount-path` v příkazu [az container create.][az-container-create] Tento příklad připojí *tajný* svazek skládající se ze dvou tajných kódů, `/mnt/secrets`"mysecret1" a "mysecret2", na adrese :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Následující výstup [az kontejneru exec][az-container-exec] ukazuje otevření prostředí v běžícím kontejneru, výpis souborů v tajném svazku a poté zobrazení jejich obsahu:

```azurecli
az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Připojit tajný svazek - YAML

Skupiny kontejnerů můžete také nasadit pomocí příkazového příkazu Konto Azure a [šablony YAML](container-instances-multi-container-yaml.md). Nasazení šablonou YAML je upřednostňovanou metodou při nasazování skupin kontejnerů skládajících se z více kontejnerů.

Při nasazení se šablonou YAML musí být tajné hodnoty **v šabloně kódovány jako Base64.** Tajné hodnoty se však zobrazí ve formátu prostého textu v souborech v kontejneru.

Následující šablona YAML definuje skupinu kontejnerů s *secret* jedním `/mnt/secrets`kontejnerem, který připojí tajný svazek na . Tajný svazek má dvě tajemství, "mysecret1" a "mysecret2."

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Chcete-li nasadit pomocí šablony YAML, uložte předchozí `deploy-aci.yaml`hodnotu YAML do souboru s názvem , poté spusťte příkaz [az container create][az-container-create] s parametrem: `--file`

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Připojit tajný svazek – Správce prostředků

Kromě nasazení cli a YAML můžete nasadit skupinu kontejnerů pomocí šablony Azure [Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve naplňte `volumes` pole v `properties` části skupiny kontejnerů šablony. Při nasazení pomocí šablony Správce prostředků musí být tajné hodnoty **v šabloně kódovány jako Base64.** Tajné hodnoty se však zobrazí ve formátu prostého textu v souborech v kontejneru.

Dále pro každý kontejner ve skupině kontejnerů, ve *secret* kterém chcete připojit `volumeMounts` tajný `properties` svazek, naplňte pole v části definice kontejneru.

Následující šablona Správce prostředků definuje skupinu kontejnerů *secret* s jedním `/mnt/secrets`kontejnerem, který připojí tajný svazek na adrese . Tajný svazek má dvě tajemství, "mysecret1" a "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Chcete-li nasadit pomocí šablony Správce prostředků, uložte `deploy-aci.json`předchozí json do souboru s `--template-file` názvem , pak spusťte příkaz create nasazení [skupiny az][az-group-deployment-create] s parametrem:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Další kroky

### <a name="volumes"></a>Svazky

Zjistěte, jak připojit další typy svazků v instanci kontejneru Azure:

* [Připojení sdílené složky ve službě Azure Container Instances](container-instances-volume-azure-files.md)
* [Připojení svazku emptyDir v instancích kontejnerů Azure](container-instances-volume-emptydir.md)
* [Připojení svazku gitRepo v instanci kontejneru Azure](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Proměnné bezpečného prostředí

Další metodou pro poskytování citlivých informací kontejnerům (včetně kontejnerů systému Windows) je použití [proměnných zabezpečeného prostředí](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

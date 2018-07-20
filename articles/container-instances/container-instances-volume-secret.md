---
title: Připojit tajný svazek ve službě Azure Container Instances
description: Zjistěte, jak připojit tajný svazek pro ukládání citlivých informací pro přístup instancí kontejneru
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: marsma
ms.openlocfilehash: 572e6701bbe69bbb07c76d468a309030fc37d984
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159885"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Připojit tajný svazek ve službě Azure Container Instances

Použití *tajný kód* svazek k poskytování citlivé informace do kontejnerů ve skupině kontejnerů. *Tajný kód* svazku uloží vaše tajné kódy soubory na svazku, dostupné kontejnery ve skupině kontejnerů. Ukládáním tajných kódů v *tajný kód* svazku, se můžete vyhnout přidávání citlivá data, jako jsou klíče SSH nebo přihlašovací údaje databáze do kódu aplikace.

Všechny *tajný kód* svazky se zálohují na [tmpfs][tmpfs], systém souborů zajišťuje RAM; jejich obsah se nikdy zapisují na stálé úložiště.

> [!NOTE]
> *Tajný kód* jsou momentálně omezené jenom na Linuxové kontejnery svazků. Zjistěte, jak předat proměnné zabezpečené prostředí pro kontejnery Windows i Linuxu v [nastavit proměnné prostředí](container-instances-environment-variables.md). Pracujeme na všechny funkce byly do kontejnerů Windows, můžete najít aktuální rozdíly pro tyto platformy v [kvóty a dostupnost oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Připojit tajný svazek – rozhraní příkazového řádku Azure

Nasazení kontejneru s tajnými kódy jeden nebo více pomocí rozhraní příkazového řádku Azure, patří `--secrets` a `--secrets-mount-path` parametry v [vytvořit az container] [ az-container-create] příkazu. Tento příklad připojí *tajný kód* skládající se z dva tajné kódy, "mysecret1" a "mysecret2," na svazku `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image microsoft/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Následující [az container exec] [ az-container-exec] výstup ukazuje otevření prostředí v spuštěný kontejner, vypisování souborů v rámci tajný svazek a zobrazení jejich obsahu:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
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

## <a name="mount-secret-volume---yaml"></a>Tajný svazek - YAML připojit

Můžete taky nasadit skupiny kontejnerů pomocí Azure CLI a [šablon YAML](container-instances-multi-container-yaml.md). Nasazení pomocí šablon YAML je upřednostňovanou metodou při nasazování skupiny kontejnerů, který se skládá z několika kontejnerů.

Při nasazení pomocí šablony YAML, musí být hodnoty tajných kódů **s kódováním Base64** v šabloně. Hodnoty tajných kódů se však zobrazí ve formátu prostého textu v rámci souborů v kontejneru.

Následující šablony YAML definuje skupinu kontejnerů s jeden kontejner, který připojí *tajný kód* svazek v `/mnt/secrets`. Tajný svazek má dva tajné kódy, "mysecret1" a "mysecret2."

```yaml
apiVersion: '2018-06-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
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

K nasazení pomocí šablony YAML, uložit do souboru s názvem předchozí YAML `deploy-aci.yaml`, provádějí [az container vytvořit] [ az-container-create] příkazů `--file` parametr:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Připojit tajný svazek - Resource Manageru

Kromě rozhraní příkazového řádku a YAML nasazení, můžete nasadit skupinu kontejnerů pomocí Azure [šablony Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups).

Nejprve naplnit `volumes` pole ve skupině kontejnerů `properties` část šablony. Při nasazení pomocí šablony Resource Manageru, musí být hodnoty tajných kódů **s kódováním Base64** v šabloně. Hodnoty tajných kódů se však zobrazí ve formátu prostého textu v rámci souborů v kontejneru.

Dále pro každý kontejner ve skupině kontejnerů, ve kterém byste chtěli připojit *tajný kód* svazku, naplnění `volumeMounts` obsahuje pole `properties` část definice kontejneru.

Následující šablony Resource Manageru definuje skupinu kontejnerů s jeden kontejner, který připojí *tajný kód* svazek v `/mnt/secrets`. Tajný svazek má dva tajné kódy, "mysecret1" a "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json --> [!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

K nasazení pomocí šablony Resource Manageru uložit do souboru s názvem výše uvedeného kódu JSON `deploy-aci.json`, provádějí [vytvořit nasazení skupiny pro az] [ az-group-deployment-create] příkazů `--template-file` parametr:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Další postup

### <a name="volumes"></a>Svazky

Zjistěte, jak připojit další typy svazku ve službě Azure Container Instances:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojit svazek gitRepo ve službě Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Zabezpečení proměnné prostředí

Další metodou pro citlivé informace, které kontejnery (včetně kontejnerů Windows), je prostřednictvím [zabezpečení proměnné prostředí](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

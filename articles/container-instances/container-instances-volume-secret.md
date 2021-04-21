---
title: Připojit tajný svazek ke skupině kontejnerů
description: Naučte se připojit tajný svazek pro ukládání citlivých informací pro přístup k vašim instancím kontejnerů.
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: cd8bd4d59b5e53a0db2455bdfbaf56c05c93d65f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770994"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Připojit tajný svazek v Azure Container Instances

Použijte *tajný* svazek k poskytnutí citlivých informací do kontejnerů ve skupině kontejnerů. *Tajný* svazek ukládá vaše tajné kódy do souborů ve svazku, které jsou přístupné pro kontejnery ve skupině kontejnerů. Uložením tajných kódů do *tajného* svazku se můžete vyhnout přidávání citlivých dat, jako jsou klíče SSH nebo pověření databáze, do kódu aplikace.

* Po nasazení s tajnými klíči ve skupině kontejnerů je tajný svazek *jen pro čtení*.
* Všechny tajné svazky jsou založené na [tmpfs][tmpfs], který je zálohovaný systémem souborů RAM. jejich obsah se nikdy nezapisuje do úložiště, které není volatile.

> [!NOTE]
> *Tajné* svazky jsou aktuálně omezené na kontejnery Linux. Přečtěte si, jak předat bezpečnostní proměnné prostředí pro kontejnery Windows i Linux v tématu [nastavení proměnných prostředí](container-instances-environment-variables.md). Pracujeme na tom, abychom do kontejnerů Windows přenesli všechny funkce. aktuální rozdíly na platformách najdete v [přehledu](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Připojit tajný svazek – Azure CLI

Pokud chcete nasadit kontejner s jedním nebo více tajnými klíči pomocí rozhraní příkazového řádku Azure, zahrňte `--secrets` `--secrets-mount-path` parametry a do příkazu [AZ Container Create][az-container-create] . Tento příklad připojí *tajný* svazek skládající se ze dvou souborů obsahujících tajné klíče, "mysecret1" a "mysecret2" na `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Následující příkaz [AZ Container exec][az-container-exec] Output zobrazuje otevření prostředí v běžícím kontejneru, výpis souborů v rámci tajného svazku a zobrazování jejich obsahu:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Připojit tajný svazek – YAML

Skupiny kontejnerů můžete nasadit taky pomocí Azure CLI a [šablony YAML](container-instances-multi-container-yaml.md). Nasazení šablonou YAML je upřednostňovanou metodou při nasazování skupin kontejnerů, které se skládají z více kontejnerů.

Při nasazení se šablonou YAML musí být tajné hodnoty v šabloně **zakódované v kódování Base64** . Tajné hodnoty se ale v rámci souborů v kontejneru zobrazí ve formátu prostého textu.

Následující šablona YAML definuje skupinu kontejnerů s jedním kontejnerem, který připojuje *tajný* svazek na `/mnt/secrets` . Tajný svazek obsahuje dva soubory s tajnými klíči, "mysecret1" a "mysecret2".

```yaml
apiVersion: '2019-12-01'
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

K nasazení se šablonou YAML uložte předchozí YAML do souboru s názvem `deploy-aci.yaml` a potom spusťte příkaz [AZ Container Create][az-container-create] s `--file` parametrem:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Připojit ke svazku tajného kódu – Správce prostředků

Kromě nasazení CLI a YAML můžete nasadit skupinu kontejnerů pomocí [šablony Azure správce prostředků](/azure/templates/microsoft.containerinstance/containergroups).

Nejdřív naplňte `volumes` pole do části Skupina kontejnerů v `properties` šabloně. Při nasazení pomocí šablony Správce prostředků musí být tajné hodnoty v šabloně **zakódované v kódování Base64** . Tajné hodnoty se ale v rámci souborů v kontejneru zobrazí ve formátu prostého textu.

Potom pro každý kontejner ve skupině kontejnerů, do kterého chcete připojit *tajný* svazek, naplňte `volumeMounts` pole v `properties` části definice kontejneru.

Následující šablona Správce prostředků definuje skupinu kontejnerů s jedním kontejnerem, který připojuje *tajný* svazek na `/mnt/secrets` . Tajný svazek má dva tajné klíče, "mysecret1" a "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Pokud chcete nasadit šablonu Správce prostředků, uložte předchozí JSON do souboru s názvem `deploy-aci.json` a potom spusťte příkaz [AZ Deployment Group Create][az-deployment-group-create] s `--template-file` parametrem:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Další kroky

### <a name="volumes"></a>Svazky

Naučte se připojit další typy svazků v Azure Container Instances:

* [Připojení sdílené složky ve službě Azure Container Instances](container-instances-volume-azure-files.md)
* [Připojení svazku emptyDir v Azure Container Instances](container-instances-volume-emptydir.md)
* [Připojení svazku Gitrepo nepodporují v Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Proměnné prostředí zabezpečení

Další metodou pro poskytování citlivých informací do kontejnerů (včetně kontejnerů Windows) je použití [proměnných zabezpečeného prostředí](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

---
title: Připojení svazku gitRepo instancí kontejnerů Azure
description: Zjistěte, jak připojit svazek gitRepo klonovat úložiště Git do vaší instance kontejneru
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166775"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Připojení svazku gitRepo v Azure kontejner instancí

Zjistěte, jak se připojit *gitRepo* svazku klonovat úložiště Git do vaší instancí kontejnerů.

> [!NOTE]
> Připojení *gitRepo* svazek je aktuálně omezeno na kontejnery Linux. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>gitRepo svazku

*GitRepo* svazek připojí adresáře a provede klonování zadaný úložiště Git do ní při spuštění kontejneru. Pomocí *gitRepo* svazku v vaše instance kontejneru, se můžete vyhnout přidání kódu, jak to udělat ve svých aplikacích.

Když připojíte *gitRepo* svazku, můžete nastavit tři vlastnosti pro konfiguraci svazku:

| Vlastnost | Požaduje se | Popis |
| -------- | -------- | ----------- |
| `repository` | Ano | Úplnou adresu URL, včetně `http://` nebo `https://`, klonovat úložiště Git.|
| `directory` | Ne | Adresář, do kterého by měl být klonovat úložiště. Cesta nesmí obsahovat ani začínat "`..`".  Pokud zadáte "`.`", je do adresáře svazku klonovat úložiště. Jinak je do podadresáři se zadaným názvem v adresáři svazku klonovat úložiště Git. |
| `revision` | Ne | Hodnota hash potvrzení revize ke klonování. Pokud tento parametr zadán, `HEAD` naklonována revize. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo připojení svazku: rozhraní příkazového řádku Azure

Připojení svazku gitRepo při nasazování kontejner instancí s [rozhraní příkazového řádku Azure](/cli/azure), dodávky `--gitrepo-url` a `--gitrepo-mount-path` parametry, které [vytvořit kontejner az] [ az-container-create] příkaz. Volitelně můžete zadat adresář v rámci svazku klonovat do (`--gitrepo-dir`) a hodnotu hash potvrzení revize ke klonování (`--gitrepo-revision`).

Tento ukázkový příkaz provede klonování [aci helloworld] [ aci-helloworld] ukázkové aplikace do `/mnt/aci-helloworld` v instanci kontejneru:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Pokud chcete ověřit, byla připojena gitRepo svazku, spusťte prostředí v kontejneru s [az kontejneru exec] [ az-container-exec] seznam adresáři a:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>GitRepo připojení svazku: Resource Manager

Připojení svazku gitRepo při nasazování kontejner instancí s [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups), nejprve naplnit `volumes` pole ve skupině kontejneru `properties` část šablony. Potom pro každý kontejner ve skupině kontejner, ve kterém byste chtěli připojit *gitRepo* svazku, naplnit `volumeMounts` pole v `properties` části definici kontejneru.

Například následující šablony Resource Manageru vytvoří skupinu kontejneru, který se skládá z jednoho kontejneru. Kontejner provede klonování dvě úložišť GitHub určeného *gitRepo* blocích svazku. Druhý svazek zahrnuje určení adresáře klonovat na další vlastnosti a hodnotu hash potvrzení konkrétní revizi klonovat.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Výsledná struktura adresářů dvě klonovaný úložiště, které jsou definované v šabloně předchozí je:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Příklad nasazení kontejneru instance pomocí šablony Azure Resource Manager, najdete v sekci [nasazení skupiny více kontejnerů v Azure kontejner instancí](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit jiné typy svazku v Azure kontejner instancí:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
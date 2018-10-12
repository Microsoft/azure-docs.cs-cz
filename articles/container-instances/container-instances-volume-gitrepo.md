---
title: Připojit svazek gitRepo Azure Container Instances
description: Zjistěte, jak připojit svazek gitRepo klonování úložiště Git do instancí kontejneru
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: 1df251522c6ca14c5eb8967771fd609c04ffe2ea
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114939"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Připojit svazek gitRepo ve službě Azure Container Instances

Zjistěte, jak připojit *gitRepo* svazku naklonujte úložiště Git do instancí kontejneru.

> [!NOTE]
> Připojení *gitRepo* svazek je momentálně omezené jenom na Linuxové kontejnery. Pracujeme na tom, aby všechny funkce byly dostupné i pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>gitRepo svazku

*GitRepo* svazek připojí adresář a duplicity zadanému úložišti Git do něj při spuštění kontejneru. Pomocí *gitRepo* svazku ve vaší službě container instances, se můžete vyhnout přidávání kódu to udělat ve svých aplikacích.

Když připojíte *gitRepo* svazku, můžete nastavit tři vlastnosti konfigurace svazku:

| Vlastnost | Požaduje se | Popis |
| -------- | -------- | ----------- |
| `repository` | Ano | Úplnou adresu URL, včetně `http://` nebo `https://`, ke klonování úložiště Git.|
| `directory` | Ne | Adresář, do kterého by měl klonovat úložiště. Cesta nesmí obsahovat ani začínat "`..`".  Pokud zadáte "`.`", úložiště se naklonovalo do adresáře svazku. V opačném případě naklonování úložiště Git do podadresáře v adresáři svazek se zadaným názvem. |
| `revision` | Ne | Hodnota hash zápisu revize ke klonování. Pokud tento parametr zadán, `HEAD` klonovat revize. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo připojení svazku: rozhraní příkazového řádku Azure

Připojení svazku gitRepo při nasazování instancí kontejnerů s [rozhraní příkazového řádku Azure](/cli/azure), poskytují `--gitrepo-url` a `--gitrepo-mount-path` parametrů [az container vytvořit] [ az-container-create] příkazu. Volitelně můžete zadat adresář na svazku naklonovat do (`--gitrepo-dir`) a hodnota hash zápisu revize ke klonování (`--gitrepo-revision`).

Příkaz v tomto příkladu duplicity [aci-helloworld] [ aci-helloworld] ukázkové aplikace do `/mnt/aci-helloworld` v instanci kontejneru:

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

Pokud chcete ověřit, gitRepo svazek byl připojen, spusťte prostředí v kontejneru s [az container exec] [ az-container-exec] a výpis adresáře:

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

Připojení svazku gitRepo při nasazování instancí kontejnerů pomocí [šablony Azure Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups), nejprve naplnit `volumes` pole ve skupině kontejnerů `properties` část šablony. Pak pro každý kontejner ve skupině kontejnerů, ve kterém byste chtěli připojit *gitRepo* svazku, naplnění `volumeMounts` obsahuje pole `properties` část definice kontejneru.

Například následující šablony Resource Manageru vytvoří skupinu kontejnerů, který se skládá z jednoho kontejneru. Kontejner duplicity dvou úložišť GitHub, které jsou určené *gitRepo* blocích svazku. Druhý svazek zahrnuje určení adresáře naklonujete další vlastnosti a hodnotu hash potvrzení konkrétní revizi klonování.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json --> [!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Výsledný struktura adresářů dvě klonovaných úložištích definované v předchozí šablonu postupem je:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Příklad nasazením instance kontejneru pomocí šablony Azure Resource Manageru najdete v tématu [nasazení skupin více kontejnerů ve službě Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Ověřování Git privátní úložiště

Připojit svazek gitRepo pro privátní úložiště Git, zadejte přihlašovací údaje v adrese URL úložiště. Obvykle přihlašovací údaje jsou ve tvaru uživatelské jméno a obor osobní přístupový token PAT, který uděluje přístup k úložišti.

Příklad rozhraní příkazového řádku Azure `--gitrepo-url` parametr pro privátní úložiště GitHub by vypadat podobně jako následující příkaz (kde je uživatelské jméno Githubu "gituser" a "abcdef1234fdsa4321abcdef" osobní přístupový token uživatele):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Pro úložiště Git v úložišti Azure zadejte libovolné uživatelské jméno (můžete použít "azurereposuser" jako v následujícím příkladu) v kombinaci s platný token PAT:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Další informace o osobní přístupové tokeny Githubu a úložiště Azure naleznete v následujících tématech:

GitHub: [vytvoření osobního přístupového tokenu pro příkazový řádek][pat-github]

Úložiště Azure: [vytvářet osobní přístupové tokeny k ověření přístupu][pat-repos]

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit další typy svazku ve službě Azure Container Instances:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec

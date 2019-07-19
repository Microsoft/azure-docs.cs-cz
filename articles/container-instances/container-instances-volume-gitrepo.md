---
title: Připojit Azure Container Instances svazku Gitrepo nepodporují
description: Naučte se připojit svazek Gitrepo nepodporují, abyste mohli klonovat úložiště Git do svých instancí kontejnerů.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: e8afa9e14941920cdcfb984e6660bdc666240716
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325438"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Připojení svazku Gitrepo nepodporují v Azure Container Instances

Naučte se připojit svazek *gitrepo nepodporují* , abyste mohli klonovat úložiště Git do svých instancí kontejnerů.

> [!NOTE]
> Připojení svazku *gitrepo nepodporují* je aktuálně omezené na kontejnery Linux. Pracujeme na tom, abychom do kontejnerů Windows přenesli všechny funkce. aktuální rozdíly na platformách najdete v [přehledu](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>Gitrepo nepodporují svazek

Svazek *gitrepo nepodporují* připojí adresář a naklonuje zadané úložiště Git do něj při spuštění kontejneru. Pomocí svazku *gitrepo nepodporují* ve svých instancích kontejnerů se můžete vyhnout přidání kódu k tomu, abyste ho mohli použít ve svých aplikacích.

Když připojíte svazek *gitrepo nepodporují* , můžete nastavit tři vlastnosti pro konfiguraci svazku:

| Vlastnost | Požadováno | Popis |
| -------- | -------- | ----------- |
| `repository` | Ano | Úplná adresa URL, včetně `http://` nebo `https://`, úložiště Git, které se má klonovat.|
| `directory` | Ne | Adresář, do kterého má být úložiště klonováno. Cesta nesmí obsahovat ani začínat znakem`..`"".  Pokud zadáte "`.`", úložiště bude naklonováno do adresáře svazku. V opačném případě je úložiště Git naklonované do podadresáře daného názvu v rámci adresáře svazků. |
| `revision` | Ne | Hodnota hash potvrzení revizí, která se má klonovat Pokud tento `HEAD` parametr nezadáte, je revize naklonována. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Připojit svazek Gitrepo nepodporují: Azure CLI

Pokud chcete při nasazování instancí kontejneru pomocí [Azure CLI](/cli/azure)připojit svazek gitrepo nepodporují, zadejte `--gitrepo-url` parametry a `--gitrepo-mount-path` do příkazu [AZ Container Create][az-container-create] . Volitelně můžete zadat adresář v rámci svazku, do kterého se má klonovat`--gitrepo-dir`(), a hodnotu hash potvrzení revize, která se má klonovat`--gitrepo-revision`().

Tento ukázkový příkaz naklonuje ukázkovou aplikaci Microsoft [ACI-HelloWorld][aci-helloworld] do `/mnt/aci-helloworld` v instanci kontejneru:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Pokud chcete ověřit, že se svazek Gitrepo nepodporují připojil, spusťte prostředí v kontejneru pomocí [AZ Container exec][az-container-exec] a uveďte adresář:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Připojit svazek Gitrepo nepodporují: Resource Manager

Chcete-li při nasazování instancí kontejneru pomocí [šablony Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups)připojit svazek gitrepo nepodporují, nejprve `volumes` pole vyplňte v části skupina `properties` kontejnerů v šabloně. Pak u každého kontejneru ve skupině kontejnerů, do kterého chcete připojit svazek *gitrepo nepodporují* , `volumeMounts` vyplňte pole v `properties` části definice kontejneru.

Například následující šablona Správce prostředků vytvoří skupinu kontejnerů skládající se z jednoho kontejneru. Kontejner klonuje dvě úložiště GitHubu zadaná v blocích svazku *gitrepo nepodporují* . Druhý svazek obsahuje další vlastnosti určující adresář, do kterého se má klonovat, a hodnotu hash potvrzení konkrétní revize, která se má klonovat.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Výsledná adresářová struktura dvou klonovaných úložišť definovaných v předchozí šabloně je:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Příklad nasazení instance kontejneru s Azure Resource Manager šablonou najdete v tématu [nasazení skupin více kontejnerů v Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Soukromé ověření úložiště Git

Pokud chcete připojit svazek Gitrepo nepodporují pro privátní úložiště Git, zadejte přihlašovací údaje v adrese URL úložiště. Přihlašovací údaje jsou obvykle ve formě uživatelského jména a osobního přístupového tokenu (PAT), který uděluje oborový přístup k úložišti.

Například parametr Azure CLI `--gitrepo-url` pro privátní úložiště GitHub by se měl podobat následujícímu (kde "gituser" je uživatelské jméno GitHubu a "abcdef1234fdsa4321abcdef" je osobní přístupový token uživatele):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

V případě Azure Repos úložiště Git zadejte libovolné uživatelské jméno (jako v následujícím příkladu můžete použít "azurereposuser") v kombinaci s platnou PAT:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Další informace o tokenech osobních přístupů pro GitHub a Azure Repos najdete v následujících tématech:

GitHubu [Vytvoření osobního přístupového tokenu pro příkazový řádek][pat-github]

Azure Repos: [Vytvořit osobní přístupové tokeny pro ověření přístupu][pat-repos]

## <a name="next-steps"></a>Další kroky

Naučte se připojit další typy svazků v Azure Container Instances:

* [Připojit sdílenou složku Azure v Azure kontejner instancí](container-instances-volume-azure-files.md)
* [Připojit emptyDir svazek v Azure kontejner instancí](container-instances-volume-emptydir.md)
* [Připojení tajný svazku v Azure kontejner instancí](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec

---
title: Připojení svazku gitRepo do skupiny kontejnerů
description: Přečtěte si, jak připojit svazek gitRepo pro klonování úložiště Git do instancí kontejneru
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 405cacd7a1649f95640a8dabf476729e101d03f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252091"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Připojení svazku gitRepo v instanci kontejneru Azure

Přečtěte si, jak připojit svazek *gitRepo* ke klonování úložiště Git do instancí kontejneru.

> [!NOTE]
> Připojení svazku *gitRepo* je v současné době omezeno na kontejnery Linuxu. Zatímco pracujeme na tom, aby všechny funkce do kontejnerů Systému Windows, můžete najít aktuální rozdíly platformy v [přehledu](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>gitRepo svazek

Svazek *gitRepo* připojí adresář a naklonuje do něj zadané úložiště Git při spuštění kontejneru. Pomocí *gitRepo* svazek v instancích kontejneru, můžete se vyhnout přidání kódu pro to ve vašich aplikacích.

Při připojení svazku *gitRepo* můžete nastavit tři vlastnosti pro konfiguraci svazku:

| Vlastnost | Požaduje se | Popis |
| -------- | -------- | ----------- |
| `repository` | Ano | Úplná adresa `http://` URL, včetně úložiště Git, které má být klonováno, včetně `https://`úložiště Git nebo jeho.|
| `directory` | Ne | Adresář, do kterého by mělo být úložiště klonováno. Cesta nesmí obsahovat ani`..`začínat " ".  Pokud zadáte`.`" ", úložiště je klonováno do adresáře svazku. V opačném případě je úložiště Git naklonováno do podadresáře daného názvu v adresáři svazku. |
| `revision` | Ne | Hash revize revize, která má být klonována. Pokud není zadán, `HEAD` revize je klonován. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Připojení svazku gitRepo: Rozhraní příkazového příkazu Azure

Chcete-li připojit svazek gitRepo při nasazování instancí `--gitrepo-mount-path` kontejneru pomocí [rozhraní příkazu Azure CLI](/cli/azure), zadejte `--gitrepo-url` parametry a příkaz u vytvoření [kontejneru az.][az-container-create] Volitelně můžete zadat adresář v rámci svazku, do který chcete klonovat ,`--gitrepo-dir`a`--gitrepo-revision`hodnotu hash revize, která má být klonována , ( ).

Tento příklad příkazklony Microsoft [aci-helloworld][aci-helloworld] ukázkové aplikace do `/mnt/aci-helloworld` v instanci kontejneru:

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

Chcete-li ověřit, zda byl svazek gitRepo připojen, spusťte prostředí v kontejneru s [az kontejnerem exec][az-container-exec] a uveďte adresář:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Připojit gitRepo svazek: Správce prostředků

Chcete-li připojit svazek gitRepo při nasazení instancí kontejneru `volumes` pomocí šablony `properties` Správce [prostředků Azure](/azure/templates/microsoft.containerinstance/containergroups), nejprve naplňte pole v části skupiny kontejnerů šablony. Potom pro každý kontejner ve skupině kontejnerů, ve kterém chcete připojit svazek *gitRepo,* naplňte `volumeMounts` pole v `properties` části definice kontejneru.

Například následující šablona Správce prostředků vytvoří skupinu kontejnerů skládající se z jednoho kontejneru. Kontejner klonuje dvě úložiště GitHub určená bloky svazků *gitRepo.* Druhý svazek obsahuje další vlastnosti určující adresář, do který má být klonován, a hodnotu hash potvrzení konkrétní revize klonování.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Výsledná adresářová struktura dvou klonovaných repo polí definovaných v předchozí šabloně je:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Příklad nasazení instancí kontejneru pomocí šablony Azure Resource Manageru najdete v tématu [Nasazení skupin s více kontejnery v instanci kontejnerů Azure](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Privátní ověřování úložiště Git

Chcete-li připojit svazek gitRepo pro soukromé úložiště Git, zadejte pověření v adrese URL úložiště. Pověření jsou obvykle ve formě uživatelského jména a osobní přístupový token (PAT), který uděluje přístup s vymezeným rozsahem do úložiště.

Například parametr Azure `--gitrepo-url` CLI pro privátní úložiště GitHub se zobrazí podobně jako následující (kde "gituser" je uživatelské jméno GitHub a "abcdef1234fdsa4321abcdef" je osobní přístupový token uživatele):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Pro úložiště Azure Repos Git zadejte libovolné uživatelské jméno (můžete použít "azurereposuser" jako v následujícím příkladu) v kombinaci s platným PAT:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Další informace o tokenech osobního přístupu pro GitHub a Azure Repos najdete v následujících tématech:

GitHub: [Vytvoření osobního přístupového tokenu pro příkazový řádek][pat-github]

Azure Repos: [Vytvoření osobních přístupových tokenů pro ověření přístupu][pat-repos]

## <a name="next-steps"></a>Další kroky

Zjistěte, jak připojit další typy svazků v instanci kontejneru Azure:

* [Připojení sdílené složky ve službě Azure Container Instances](container-instances-volume-azure-files.md)
* [Připojení svazku emptyDir v instancích kontejnerů Azure](container-instances-volume-emptydir.md)
* [Připojení tajného svazku v instancích kontejnerů Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec

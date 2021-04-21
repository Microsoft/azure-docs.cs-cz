---
title: Uzamknutí imagí
description: Nastavte atributy pro Image kontejneru nebo úložiště, aby se nemohlo odstranit ani přepsat v registru kontejnerů Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 340beb1bb6666ddf0de7de38adee6be71f5f52bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772338"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Uzamknutí image kontejneru ve službě Azure Container Registry

Ve službě Azure Container Registry můžete zamknout verzi Image nebo úložiště, aby se nemohlo odstranit ani aktualizovat. Pokud chcete zamknout Image nebo úložiště, aktualizujte jeho atributy pomocí příkazu Azure CLI [AZ ACR úložiště Update][az-acr-repository-update]. 

Tento článek vyžaduje, abyste spouštěli Azure CLI v Azure Cloud Shell nebo lokálně (doporučuje se verze 2.0.55 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

> [!IMPORTANT]
> Tento článek se nevztahuje na uzamykání celého registru, například pomocí **nastavení > zámky** v Azure Portal nebo `az lock` v příkazech Azure CLI. Uzamykání prostředku registru nebrání v vytváření, aktualizaci nebo odstraňování dat v úložištích. Uzamknutí registru má vliv jenom na operace správy, jako je přidání nebo odstranění replikace nebo odstranění samotného registru. Další informace o [uzamčení prostředků, aby se zabránilo neočekávaným změnám](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scénáře

Ve výchozím nastavení je označený obrázek v Azure Container Registry *proměnlivý*, takže s příslušnými oprávněními můžete opakovaně aktualizovat a nasdílet image se stejnou značkou do registru. V případě potřeby můžete také [Odstranit](container-registry-delete.md) image kontejneru. Toto chování je užitečné při vývoji imagí a potřebě udržovat velikost registru.

Pokud však nasadíte image kontejneru do produkčního prostředí, budete možná potřebovat *neměnné* image kontejneru. Neproměnlivá Image je taková, kterou nemůžete omylem odstranit ani přepsat.

V tématu [doporučení pro označování a image kontejnerů](container-registry-image-tag-version.md) , které se týkají označení a verze imagí v registru, najdete v tématu doporučení.

Pomocí příkazu [AZ ACR úložiště Update][az-acr-repository-update] nastavte atributy úložiště, abyste mohli:

* Uzamčení verze bitové kopie nebo celého úložiště

* Ochrana verze bitové kopie nebo úložiště před odstraněním, ale povolení aktualizací

* Zabránit operacím čtení (přijetí) v imagi verze nebo v celém úložišti

Příklady najdete v následujících oddílech. 

## <a name="lock-an-image-or-repository"></a>Uzamknutí obrázku nebo úložiště 

### <a name="show-the-current-repository-attributes"></a>Zobrazit aktuální atributy úložiště
Pokud chcete zobrazit aktuální atributy úložiště, spusťte následující příkaz [AZ ACR úložiště show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Zobrazit aktuální atributy obrázku
Chcete-li zobrazit aktuální atributy značky, spusťte následující příkaz [AZ ACR úložiště show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Uzamknutí obrázku podle značky

Chcete-li zamknout *myrepo/MyImage: image značky* v *myregistry*, spusťte následující příkaz [AZ ACR úložiště Update][az-acr-repository-update] :

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Uzamknutí obrázku pomocí výtahu manifestu

K uzamknutí image *myrepo/MyImage* identifikované hodnotou Digest MANIFESTU (SHA-256 hash, reprezentovaná jako `sha256:...` ) spusťte následující příkaz. (Chcete-li zjistit, který výtah manifestu je přidružen k jedné nebo více značkám obrázku, spusťte příkaz [AZ ACR úložištì show-Manifests][az-acr-repository-show-manifests] .)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Uzamčení úložiště

Chcete-li uzamknout úložiště *myrepo/MyImage* a všechny bitové kopie, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Ochrana Image nebo úložiště před odstraněním

### <a name="protect-an-image-from-deletion"></a>Ochrana obrázku před odstraněním

Pokud chcete, aby se obrázek *značky myrepo/MyImage:* aktualizoval, ale neodstranil, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Ochrana úložiště před odstraněním

Následující příkaz nastaví úložiště *myrepo/MyImage* tak, aby se nemohlo odstranit. Jednotlivé image se pořád dají aktualizovat nebo odstranit.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Zabránit operacím čtení na obrázku nebo v úložišti

Chcete-li zabránit operacím čtení (pull) na obrázku *myrepo/MyImage: tag* , spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Pokud chcete zabránit operacím čtení na všech obrázcích v úložišti *myrepo/MyImage* , spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Odemčení Image nebo úložiště

Chcete-li obnovit výchozí chování obrázku *značky myrepo/MyImage:* , aby jej bylo možné odstranit a aktualizovat, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Chcete-li obnovit výchozí chování úložiště *myrepo/MyImage* a všech imagí, aby bylo možné je odstranit a aktualizovat, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o použití příkazu [AZ ACR úložištì Update][az-acr-repository-update] , který zabraňuje odstranění nebo aktualizaci verzí imagí v úložišti. Informace o nastavení dalších atributů najdete v referenčních informacích k příkazu [AZ ACR úložiště Update][az-acr-repository-update] .

Pokud chcete zobrazit atributy nastavené pro verzi Image nebo úložiště, použijte příkaz [AZ ACR úložištì show][az-acr-repository-show] .

Podrobnosti o operacích odstranění najdete [v tématu Odstranění imagí kontejneru v Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

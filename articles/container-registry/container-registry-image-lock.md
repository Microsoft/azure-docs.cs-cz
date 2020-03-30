---
title: Uzamknutí imagí
description: Nastavte atributy pro image kontejneru nebo úložiště tak, aby ji nelze odstranit nebo přepsat v registru kontejneru Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659692"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Uzamčení image kontejneru v registru kontejneru Azure

V registru kontejneru Azure můžete uzamknout verzi image nebo úložiště, aby ji nebylo možné odstranit nebo aktualizovat. Chcete-li zamknout bitovou kopii nebo úložiště, aktualizujte její atributy pomocí [aktualizace úložiště AZ az][az-acr-repository-update]příkazu Azure CLI . 

Tento článek vyžaduje spuštění příkazového příkazu k onomu Azure v prostředí Azure Cloud Shell nebo místně (verze 2.0.55 nebo novější doporučeno). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

> [!IMPORTANT]
> Tento článek se nevztahuje na uzamčení celého registru, například pomocí `az lock` nastavení > **zámky** na webu Azure portal nebo příkazy v nastavení příkazu k onobího kvitu Azure. Uzamčení prostředku registru nezabrání vytváření, aktualizaci nebo odstranění dat v úložištích. Uzamčení registru ovlivní pouze operace správy, jako je například přidání nebo odstranění replikací nebo odstranění samotného registru. Další informace v [lock prostředky, aby se zabránilo neočekávaným změnám](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scénáře

Ve výchozím nastavení je tagovaná bitová kopie v registru kontejnerů Azure *proměnlivá*, takže s příslušnými oprávněními můžete opakovaně aktualizovat a vsušovat bitovou kopii se stejnou značkou do registru. Obrázky kontejnerů lze také [odstranit](container-registry-delete.md) podle potřeby. Toto chování je užitečné při vývoji bitových kopií a je třeba zachovat velikost registru.

Však při nasazení image kontejneru do produkčního prostředí, budete potřebovat *neměnné* image kontejneru. Neměnný obrázek je obrázek, který nelze omylem odstranit nebo přepsat.

V [tématu Doporučení pro označování a správu verzí imitací kontejnerů](container-registry-image-tag-version.md) pro strategie pro označení a verzi iobrazů v registru.

Pomocí příkazu [az acr repository update][az-acr-repository-update] nastavte atributy úložiště, abyste mohli:

* Uzamčení verze obrázku nebo celého úložiště

* Ochrana verze bitové kopie nebo úložiště před odstraněním, ale povolit aktualizace

* Zabránit čtení (vyžádat) operace na verzi obrázku nebo celé úložiště

Příklady naleznete v následujících částech. 

## <a name="lock-an-image-or-repository"></a>Uzamčení obrázku nebo úložiště 

### <a name="show-the-current-repository-attributes"></a>Zobrazit aktuální atributy úložiště
Chcete-li zobrazit aktuální atributy úložiště, spusťte následující příkaz [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Zobrazit aktuální atributy obrázku
Chcete-li zobrazit aktuální atributy značky, spusťte následující příkaz [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Uzamčení obrázku značkou

Chcete-li zamknout *image myrepo/myimage:tag* v *myregistry*, spusťte následující příkaz [aktualizace úložiště az acr:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Uzamčení obrazu podle přehledu manifestu

Chcete-li zamknout bitovou kopii *myrepo/myimage* označenou manifest `sha256:...`digest (SHA-256 hash, reprezentovaná jako ), spusťte následující příkaz. (Chcete-li najít přehled manifestu přidružený k jedné nebo více značkám obrázků, spusťte příkaz [az acr repository show-manifests.)][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Uzamčení úložiště

Chcete-li uzamknout úložiště *myrepo/myimage* a všechny obrázky v něm, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Ochrana obrázku nebo úložiště před odstraněním

### <a name="protect-an-image-from-deletion"></a>Ochrana obrázku před odstraněním

Chcete-li povolit aktualizaci obrázku *myrepo/myimage:tag,* ale ne odstraněnou, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Ochrana úložiště před odstraněním

Následující příkaz nastaví úložiště *myrepo/myimage* tak, aby jej nebylo možné odstranit. Jednotlivé obrázky lze stále aktualizovat nebo odstranit.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Zabránění operacím čtení v bitové kopii nebo úložišti

Chcete-li zabránit operacím čtení (vyžádat) na bitové kopii *myrepo/myimage:tag,* spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Chcete-li zabránit operacím čtení na všech obrázcích v úložišti *myrepo/myimage,* spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Odemknutí obrázku nebo úložiště

Chcete-li obnovit výchozí chování obrázku *myrepo/myimage:tag,* aby jej bylo možné odstranit a aktualizovat, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Chcete-li obnovit výchozí chování úložiště *myrepo/myimage* a všech obrázků, aby je bylo možné odstranit a aktualizovat, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o použití příkazu [aktualizace úložiště az acr,][az-acr-repository-update] abyste zabránili odstranění nebo aktualizaci verzí bitové kopie v úložišti. Další atributy nastavíte na odkaz na [příkaz aktualizace úložiště az acr.][az-acr-repository-update]

Chcete-li zobrazit atributy nastavené pro verzi obrázku nebo úložiště, použijte příkaz [az acr repository show.][az-acr-repository-show]

Podrobnosti o operacích odstranění najdete [v tématu Odstranění ibi kontejnerů v registru kontejnerů Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md


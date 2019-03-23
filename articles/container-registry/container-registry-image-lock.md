---
title: Zamknout image v Azure Container Registry
description: Nastavení atributů pro image kontejneru nebo úložiště, nejde odstranit ani přepsán ve službě Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361281"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Zamknout image kontejneru do služby Azure container registry

Ve službě Azure container registry můžete uzamknout image verze nebo úložiště tak, aby nelze odstranit nebo aktualizovat. Zamknout bitovou kopii nebo úložiště, aktualizovat jeho atributy, pomocí příkazu Azure CLI [aktualizace úložiště az acr][az-acr-repository-update]. 

Tento článek vyžaduje, abyste spustili Azure CLI ve službě Azure Cloud Shell nebo místně (verze 2.0.55 nebo později doporučené). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="scenarios"></a>Scénáře

Ve výchozím nastavení, tagged image ve službě Azure Container Registry je *proměnlivé*, takže s příslušnými oprávněními můžete opakovaně aktualizovat a nahrajete do registru image se stejnou značkou. Image kontejnerů může být také [odstranit](container-registry-delete.md) podle potřeby. Toto chování je užitečné při vývoji imagí a nemusíte udržovat velikost svého registru.

Ale při nasazení image kontejneru do produkčního prostředí, může být nutné *neměnné* image kontejneru. Neměnné image je ten, který nelze omylem odstraníte nebo přepsat. Použití [aktualizace úložiště az acr] [ az-acr-repository-update] příkaz pro nastavení úložiště atributů, což vám umožní:

* Zamknout image verze nebo celé úložiště

* Ochrana před odstraněním verzi image nebo úložiště, ale povolit aktualizace

* Zakázat operace čtení (pull) ve verzi image, nebo celé úložiště

Viz příklady v následujících částech.

## <a name="lock-an-image-or-repository"></a>Zamknout obrázek nebo úložiště 

### <a name="show-the-current-repository-attributes"></a>Zobrazit aktuální úložiště atributů
Pokud chcete zobrazit aktuální atributy z úložiště, spusťte následující příkaz [az acr úložiště zobrazit] [ az-acr-repository-show] příkaz:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Zobrazit aktuální atributy obrázku
Pokud chcete zobrazit aktuální atributy značky, spusťte následující příkaz [az acr úložiště zobrazit] [ az-acr-repository-show] příkaz:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Zamknout bitovou kopii podle značky

Pokud chcete nastavit poměr *myrepo / myimage:tag* obrázků v *myregistry*, spusťte následující příkaz [aktualizace úložiště az acr] [ az-acr-repository-update] příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Zamknout bitovou kopii podle manifestu ověřování algoritmem digest

Pokud chcete nastavit poměr *myrepo/myimage* bitovou kopii určenou v manifestu digest (hodnoty hash SHA-256, vyjádřené `sha256:...`), spusťte následující příkaz. (Pokud chcete najít manifestu digest přidružené k jedné nebo více značek bitové kopie, spusťte [az acr úložiště show manifesty] [ az-acr-repository-show-manifests] příkazu.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Zamknout úložiště

Pokud chcete nastavit poměr *myrepo/myimage* úložiště a všechny Image v něm, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Ochrana před odstraněním obrazu nebo úložiště

### <a name="protect-an-image-from-deletion"></a>Ochrana před odstraněním bitovou kopii

Chcete-li povolit *myrepo / myimage:tag* obrázek, který se aktualizuje, ale nebyl odstraněn, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Ochrana před odstraněním úložiště

Nastaví zadáním následujícího příkazu *myrepo/myimage* úložiště, takže se nedá odstranit. Jednotlivé Image můžete dál aktualizovat ani odstranit.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Zabránit operací čtení na obrázek nebo úložiště

Aby se zabránilo na operace čtení (pull) *myrepo / myimage:tag* bitové kopie, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Aby se zabránilo operací čtení na všechny Image v *myrepo/myimage* úložiště, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Odemknout obrázek nebo úložiště

Chcete-li obnovit výchozí chování *myrepo / myimage:tag* obrázku tak, aby ho odstranit a aktualizovat, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Chcete-li obnovit výchozí chování *myrepo/myimage* úložiště a všechny Image tak, že budou odstraněny a aktualizovat, spusťte následující příkaz:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o použití [aktualizace úložiště az acr] [ az-acr-repository-update] příkaz, abyste zabránili odstranění nebo aktualizaci z verzí bitové kopie v úložišti. Pokud chcete nastavit další atributy, najdete v článku [aktualizace úložiště az acr] [ az-acr-repository-update] referenčních příkazu.

Pokud chcete zobrazit atributy nastavené pro verze image nebo úložiště, použijte [az acr úložiště zobrazit] [ az-acr-repository-show] příkazu.

Podrobnosti o operace odstranění najdete v tématu [odstranit Image kontejnerů ve službě Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md


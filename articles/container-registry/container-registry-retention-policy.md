---
title: Zásady pro zachování netagovaných manifestů v Azure Container Registry
description: Naučte se, jak ve službě Azure Container Registry povolit zásady uchovávání informací pro automatické odstranění netagovaných manifestů po definovaném období.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306000"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Nastavit zásady uchovávání informací pro netagované manifesty

Azure Container Registry vám dává možnost nastavit *zásady uchovávání informací* pro manifesty uložených imagí, které nemají žádné přidružené značky (*netagované manifesty*). Pokud jsou povoleny zásady uchovávání informací, odtagované manifesty v registru budou automaticky odstraněny po nastaveném počtu dní. Tato funkce zabrání registru v naplnění artefaktů, které nepotřebujete, a pomůže vám ušetřit náklady na úložiště. Pokud je `false`atributem netagovaného manifestu nastavený na, manifest se nedá odstranit a zásady uchovávání se nevztahují. `delete-enabled`

Příklady příkazů v tomto článku můžete spustit pomocí Azure Cloud Shell nebo místní instalace rozhraní příkazového řádku Azure CLI. Pokud ho chcete používat místně, je potřeba verze 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

> [!WARNING]
> Nastavte zásady uchovávání informací se zapnutou opatrností. data bitové kopie je možné obnovit. Pokud máte systémy, které vyžádají image podle výtahu manifestu (na rozdíl od názvu image), neměli byste nastavit zásady uchovávání informací pro netagované manifesty. Odstraněním netagovaných imagí znemožníte těmto systémům navrácení imagí z registru. Místo toho, aby se vybral manifest, zvažte přijetí jedinečného schématu *označování* , což je doporučený osvědčený [postup](container-registry-image-tag-version.md).

Pokud chcete odstranit jednotlivé značky obrázků nebo manifesty pomocí příkazů rozhraní příkazového řádku Azure, přečtěte si téma [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="preview-limitations"></a>Omezení verze Preview

* Pomocí zásad uchovávání informací se dá nakonfigurovat jenom registr kontejnerů **Premium** . Informace o úrovních služby registru najdete v tématu [Azure Container Registry SKU](container-registry-skus.md).
* Zásady uchovávání informací lze nastavit pouze pro netagované manifesty.

## <a name="set-a-retention-policy---cli"></a>Nastavení zásad uchovávání informací – CLI

Následující příklad ukazuje, jak pomocí rozhraní příkazového řádku Azure nastavit zásady uchovávání informací pro netagované manifesty v registru.

### <a name="enable-a-retention-policy"></a>Povolit zásady uchovávání informací

Ve výchozím nastavení nejsou v registru kontejneru nastavené žádné zásady uchovávání informací. Pokud chcete nastavit nebo aktualizovat zásady uchovávání informací, spusťte v Azure CLI příkaz [AZ ACR config][az-acr-config-retention-update] re Update. Pro uchování netagovaných manifestů můžete zadat počet dní mezi 0 a 365. Pokud nezadáte počet dnů, příkaz nastaví výchozí hodnotu 7 dní. Po dobu uchování se všechny neoznačené manifesty v registru automaticky odstraní.

Následující příklad nastaví zásady uchovávání informací po dobu 30 dnů pro netagované manifesty v registru *myregistry*:

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

Následující příklad nastaví zásadu pro odstranění všech manifestů v registru, jakmile není označena. Vytvořit tuto zásadu nastavením doby uchování 0 dnů:

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>Zakázat zásady uchovávání informací

Pokud chcete zobrazit sadu zásad uchovávání dat v registru, spusťte příkaz [AZ ACR config diskazuje show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --name myregistry
```

Chcete-li zakázat zásady uchovávání informací v registru, spusťte příkaz [AZ ACR config uchování aktualizace][az-acr-config-retention-update] a `--status disabled`nastavte:

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>Nastavení zásad uchovávání informací – portál

V [Azure Portal](https://portal.azure.com)můžete také nastavit zásady uchovávání registru. Následující příklad ukazuje, jak pomocí portálu nastavit zásady uchovávání informací pro netagované manifesty v registru.

### <a name="enable-a-retention-policy"></a>Povolit zásady uchovávání informací

1. Přejděte do služby Azure Container Registry. V části **zásady**vyberte **uchování** (Preview).
1. V **stav**vyberte **povoleno**.
1. Vyberte počet dní mezi 0 a 365 a zachovejte netagované manifesty. Vyberte **Uložit**.

![Povolit zásady uchovávání informací v Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Zakázat zásady uchovávání informací

1. Přejděte do služby Azure Container Registry. V části **zásady**vyberte **uchování** (Preview).
1. V **stav**vyberte **zakázáno**. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* Další informace o možnostech [odstraňování imagí a úložišť](container-registry-delete.md) v Azure Container Registry

* Zjistěte, jak [automaticky vyprázdnit](container-registry-auto-purge.md) vybrané image a manifesty z registru.

* Další informace o možnostech pro [uzamknutí obrázků a manifestů](container-registry-image-lock.md) v registru

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show

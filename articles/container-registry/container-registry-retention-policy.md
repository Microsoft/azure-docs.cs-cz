---
title: Zásady pro zachování netagovaných manifestů
description: Naučte se, jak ve službě Azure Container Registry povolit zásady uchovávání informací pro automatické odstranění netagovaných manifestů po definovaném období.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83683456"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Nastavit zásady uchovávání informací pro netagované manifesty

Azure Container Registry vám dává možnost nastavit *zásady uchovávání informací* pro manifesty uložených imagí, které nemají žádné přidružené značky (*netagované manifesty*). Pokud jsou povoleny zásady uchovávání informací, odtagované manifesty v registru budou automaticky odstraněny po nastaveném počtu dní. Tato funkce zabrání registru v naplnění artefaktů, které nepotřebujete, a pomůže vám ušetřit náklady na úložiště. Pokud `delete-enabled` je atributem netagovaného manifestu nastavený na `false` , manifest se nedá odstranit a zásady uchovávání se nevztahují.

Příklady příkazů v tomto článku můžete spustit pomocí Azure Cloud Shell nebo místní instalace rozhraní příkazového řádku Azure CLI. Pokud ho chcete používat místně, je potřeba verze 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Zásady uchovávání informací jsou funkcí pro Registry kontejnerů úrovně **Premium** . Informace o úrovních služby registru najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

> [!WARNING]
> Nastavte zásady uchovávání informací se zapnutou opatrností. data bitové kopie je možné obnovit. Pokud máte systémy, které vyžádají image podle výtahu manifestu (na rozdíl od názvu image), neměli byste nastavit zásady uchovávání informací pro netagované manifesty. Odstraněním netagovaných imagí znemožníte těmto systémům navrácení imagí z registru. Místo toho, aby se vybral manifest, zvažte přijetí *jedinečného schématu označování* , což je [doporučený osvědčený postup](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Omezení verze Preview

* Zásady uchovávání informací lze nastavit pouze pro netagované manifesty.
* Zásady uchovávání informací se aktuálně vztahují jenom na manifesty, které jsou *po* povolení zásady neoznačené. Existující netagované manifesty v registru nepodléhají zásadám. Chcete-li odstranit existující netagované manifesty, přečtěte si příklady v tématu [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Informace o zásadách uchovávání informací

Azure Container Registry provádí počítání odkazů pro manifesty v registru. Pokud je manifest bez příznaku označený, zkontroluje zásady uchovávání informací. Pokud je povolená zásada uchovávání informací, je operace odstranění manifestu zařazená do fronty s konkrétním datem podle počtu dní nastavených v zásadě.

Samostatná úloha správy fronty neustále zpracovává zprávy a mění velikost podle potřeby. Předpokládejme například, že jste neoznačili dva manifesty, a to v registru se zásadami uchovávání informací po dobu 30 dnů. Dvě zprávy by byly zařazeny do fronty. Po uplynutí 30 dnů později přibližně 1 hodinu od sebe se zprávy načtou z fronty a zpracovaná za předpokladu, že zásada byla stále platná.

## <a name="set-a-retention-policy---cli"></a>Nastavení zásad uchovávání informací – CLI

Následující příklad ukazuje, jak pomocí rozhraní příkazového řádku Azure nastavit zásady uchovávání informací pro netagované manifesty v registru.

### <a name="enable-a-retention-policy"></a>Povolit zásady uchovávání informací

Ve výchozím nastavení nejsou v registru kontejneru nastavené žádné zásady uchovávání informací. Pokud chcete nastavit nebo aktualizovat zásady uchovávání informací, spusťte v Azure CLI příkaz [AZ ACR config][az-acr-config-retention-update] re Update. Pro uchování netagovaných manifestů můžete zadat počet dní mezi 0 a 365. Pokud nezadáte počet dnů, příkaz nastaví výchozí hodnotu 7 dní. Po dobu uchování se všechny neoznačené manifesty v registru automaticky odstraní.

Následující příklad nastaví zásady uchovávání informací po dobu 30 dnů pro netagované manifesty v registru *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

Následující příklad nastaví zásadu pro odstranění všech manifestů v registru, jakmile není označena. Vytvořte tuto zásadu nastavením doby uchování 0 dnů. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Ověření zásady uchovávání informací

Pokud povolíte předchozí zásadu s dobou uchování 0 dnů, můžete rychle ověřit, jestli jsou netagované manifesty smazány:

1. Nahrajte image obrazu testu `hello-world:latest` do registru nebo nahraďte další testovací image podle vašeho výběru.
1. Zrušit označení `hello-world:latest` Image například pomocí příkazu [AZ ACR úložiště zrušit označení][az-acr-repository-untag] . Netagovaný manifest zůstane v registru.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Během několika sekund se netagovaný manifest odstraní. Odstraněním výpisů v úložišti můžete ověřit zadáním manifestů, například pomocí příkazu [AZ ACR úložiště show-Manifests][az-acr-repository-show-manifests] . Pokud byl test image jediným z nich v úložišti, odstraní se samotné úložiště.

### <a name="disable-a-retention-policy"></a>Zakázat zásady uchovávání informací

Pokud chcete zobrazit sadu zásad uchovávání dat v registru, spusťte příkaz [AZ ACR config diskazuje show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --registry myregistry
```

Chcete-li zakázat zásady uchovávání informací v registru, spusťte příkaz [AZ ACR config uchování aktualizace][az-acr-config-retention-update] a nastavte `--status disabled` :

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
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
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests

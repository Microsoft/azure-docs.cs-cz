---
title: Zásady pro zachování neoznačených manifestů
description: Zjistěte, jak povolit zásady uchovávání informací v registru kontejnerů Azure pro automatické odstranění neoznačených manifestů po definovaném období.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454812"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Nastavení zásad uchovávání informací pro netagované manifesty

Azure Container Registry vám dává možnost nastavit *zásady uchovávání informací* pro uložené image manifesty, které nemají žádné přidružené značky *(netagované manifesty*). Pokud je povolena zásada uchovávání informací, netagované manifesty v registru jsou automaticky odstraněny po několika dnech, které jste nastavili. Tato funkce zabraňuje zaplnění registru artefakty, které nejsou potřeba, a pomáhá ušetřit náklady na úložiště. Pokud `delete-enabled` je atribut netagovaného manifestu nastaven na `false`, manifest nelze odstranit a zásady uchovávání informací se nepoužijí.

Azure Cloud Shell nebo místní instalace Azure CLI spustit příklady příkazů v tomto článku. Pokud jej chcete používat místně, je vyžadována verze 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a platí určitá [omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

> [!WARNING]
> Nastavte zásady uchovávání informací opatrně – odstraněná obrazová data jsou nezdolná. Pokud máte systémy, které vytahují obrázky podle manifestu digest (na rozdíl od názvu obrázku), neměli byste nastavit zásady uchovávání informací pro netagované manifesty. Odstranění neoznačených obrázků zabrání těmto systémům v vytažení bitových kopií z registru. Místo toho, aby tahání podle manifestu, zvažte přijetí jedinečné schéma *označování,* [doporučený osvědčený postup](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Omezení náhledu

* Pouze registr kontejneru **Premium** lze nakonfigurovat pomocí zásad uchovávání informací. Informace o vrstvách služby registru naleznete [v tématu Azure Container Registry SKU](container-registry-skus.md).
* Zásady uchovávání informací lze nastavit pouze pro netagované manifesty.
* Zásady uchovávání informací se aktuálně vztahují pouze na manifesty, které jsou *po* povolení zásady neoznačeny. Existující netagované manifesty v registru nepodléhají zásadám. Pokud chcete odstranit existující netagované manifesty, podívejte se na příklady v [delete image kontejneru v Registru kontejnerů Azure](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>O zásadách uchovávání informací

Azure Container Registry provádí počítání odkazů pro manifesty v registru. Pokud manifest je netagovaný, zkontroluje zásady uchovávání informací. Pokud je povolena zásada uchovávání informací, je operace odstranění manifestu zařazena do fronty s určitým datem podle počtu dní nastavených v zásadě.

Samostatná úloha správy fronty neustále zpracovává zprávy a podle potřeby se škáluje. Jako příklad předpokládejme, že jste neoznačili dva manifesty, 1 hodinu od sebe, v registru se zásadami uchovávání informací 30 dnů. Dvě zprávy budou zařazeny do fronty. Potom, 30 dní později, přibližně 1 hodinu od sebe, zprávy by být načteny z fronty a zpracovány, za předpokladu, že zásady byly stále v platnosti.

## <a name="set-a-retention-policy---cli"></a>Nastavení zásad uchovávání informací – cli

Následující příklad ukazuje, jak pomocí příkazového příkazu Azure nastavit zásady uchovávání informací pro netagované manifesty v registru.

### <a name="enable-a-retention-policy"></a>Povolení zásad uchovávání informací

Ve výchozím nastavení nejsou v registru kontejneru nastaveny žádné zásady uchovávání informací. Chcete-li nastavit nebo aktualizovat zásady uchovávání informací, spusťte příkaz [aktualizace aktualizace retence konfigurace az acr v][az-acr-config-retention-update] příkazovém příkazovém příkazu Azure CLI. Můžete zadat počet dní mezi 0 a 365 zachovat netagované manifesty. Pokud nezadáte počet dní, příkaz nastaví výchozí 7 dní. Po uplynutí doby uchovávání jsou automaticky odstraněny všechny netagované manifesty v registru.

Následující příklad nastaví zásady uchovávání informací 30 dnů pro netagované manifesty v registru *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

Následující příklad nastaví zásadu pro odstranění všech manifestů v registru, jakmile je neoznačený. Vytvořte tuto zásadu nastavením období uchování 0 dní. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Ověření zásad uchovávání informací

Pokud povolíte předchozí zásady s dobou uchování 0 dní, můžete rychle ověřit, že netagované manifesty jsou odstraněny:

1. Zatlačte `hello-world:latest` bitovou kopii testovací bitové kopie do registru nebo nahraďte jinou zkušební bitovou kopii podle vašeho výběru.
1. Odtaguj obrázek, `hello-world:latest` například pomocí příkazu [az acr repozitáře untag.][az-acr-repository-untag] Netagovaný manifest zůstane v registru.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Během několika sekund se odstraní netagovaný manifest. Odstranění můžete ověřit výpisem manifestů v úložišti, například pomocí příkazu [az acr repository show-manifests.][az-acr-repository-show-manifests] Pokud testovací bitová kopie byla jediná v úložišti, samotné úložiště se odstraní.

### <a name="disable-a-retention-policy"></a>Zakázání zásad uchovávání informací

Chcete-li zobrazit zásady uchovávání informací nastavené v registru, spusťte příkaz [az acr config retention show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Chcete-li zakázat zásady uchovávání informací v registru, spusťte `--status disabled`příkaz [az acr config retence a][az-acr-config-retention-update] nastavte :

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Nastavení zásad uchovávání informací – portál

Zásady uchovávání informací registru můžete nastavit také na [webu Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak pomocí portálu nastavit zásady uchovávání informací pro netagované manifesty v registru.

### <a name="enable-a-retention-policy"></a>Povolení zásad uchovávání informací

1. Přejděte do registru kontejnerů Azure. V části **Zásady**vyberte **Možnost uchovávání informací** (náhled).
1. V **pole Stav**vyberte Možnost **Povoleno**.
1. Vyberte počet dní mezi 0 a 365 zachovat neoznačené manifesty. Vyberte **Uložit**.

![Povolení zásad uchovávání informací na webu Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Zakázání zásad uchovávání informací

1. Přejděte do registru kontejnerů Azure. V části **Zásady**vyberte **Možnost uchovávání informací** (náhled).
1. V **poučita**vyberte **Zakázáno**. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* Další informace o možnostech [odstranění bitových kopií a úložišť](container-registry-delete.md) v registru kontejnerů Azure

* Zjistěte, jak [automaticky vymazat](container-registry-auto-purge.md) vybrané obrázky a manifesty z registru

* Další informace o možnostech [uzamčení obrázků a manifestů](container-registry-image-lock.md) v registru

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests

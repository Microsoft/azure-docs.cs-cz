---
title: Kontejner Azure registru SKU
description: Porovnejte různých úrovních služby dostupné v registru kontejner Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: a8dcc6fc60b80a19c4edebd57fdad5bb10cfdd0b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165210"
---
# <a name="azure-container-registry-skus"></a>Kontejner Azure registru SKU

Azure kontejneru registru (ACR) je k dispozici ve více úrovní služeb, označuje jako SKU. Tyto identifikátory SKU zadejte předvídatelná cena a několik možností pro zarovnání na způsoby kapacitu a využití vaší privátní registru Docker v Azure.

| Skladová jednotka (SKU) | Spravované | Popis |
| --- | :-------: | ----------- |
| **Basic** | Ano | Náklady na optimalizované vstupní bod pro vývojáře, získávání informací o registru kontejner Azure. Základní registrech mít stejné programové funkce jako Standard a Premium (Integrace ověřování služby Azure Active Directory, odstranění bitové kopie a webové háky), ale existují omezení velikosti a využití. |
| **Standard** | Ano | Standardní registrech nabízí stejné funkce jako základní s limity vyšší úložiště a propustnost bitové kopie. Standardní registrech by měl vyhovovat potřebám většině produkčních scénářích. |
| **Premium** | Ano | Premium registrech poskytují vyšší limity na omezení, jako je například úložiště a souběžných operací, povolení vysoký počet scénáře. Kromě vyšší kapacity propustnosti bitové kopie, Premium přidává funkce, jako jsou [geografická replikace] [ container-registry-geo-replication] pro správu jednoho registru v několika oblastech, zachování registru sítě zavřít všechny nasazení. |
| Classic | Ne | Skladová položka registru Classic povoleno počáteční verzi služby Azure kontejneru registru v Azure. Účet úložiště, který vytvoří Azure v rámci vašeho předplatného, což omezí možnosti pro ACR zajistit vyšší úrovně funkcí, jako je vyšší propustnost a geografická replikace jsou zajišťované Classic registrech. Z důvodu jeho omezené možnosti plánujeme Classic SKU přestat používat v budoucnosti. |

Při výběru že vyšší úrovně SKU poskytuje další výkonu a možností škálování, ale všech skladových položek spravované poskytovat stejné programové funkce. S více úrovní služeb můžete začít pracovat s Basic pak převést jako vaše zvýšení využití registru Standard a Premium.

> [!NOTE]
> Z důvodu plánované vyřazení registru Classic SKU doporučujeme používat Basic, Standard nebo Premium pro všechny nové registrech. Informace o převodu vaší existující klasické registru najdete v tématu [Upgrade Classic registru][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Spravovaných a nespravovaných

Basic, Standard a Premium SKU se souhrnně označují jako *spravované* registrech a Classic registrech jako *nespravované*. Základní rozdíl mezi nimi je, jak jsou uloženy vaše Image kontejneru.

### <a name="managed-basic-standard-premium"></a>Spravované (Basic, Standard, Premium)

Spravovat registrech benefit z úložiště image kompletně spravované službou Azure. To znamená účet úložiště, který ukládá obrázků nezobrazí v rámci vašeho předplatného Azure. Existuje více výhod získaných pomocí jedné z registru spravované SKU, popsané podrobný v [kontejneru úložiště bitové kopie v registru kontejner Azure][container-registry-storage]. Tento článek se zaměřuje na spravované registru SKU a jejich možnosti.

### <a name="unmanaged-classic"></a>Nespravované (klasické)

Classic registrech jsou "nespravované" v tom smyslu, že účet úložiště, který zálohuje Classic registru se nachází v rámci *vaše* předplatného Azure. Jako takový jste zodpovědní za správu účtu úložiště, ve kterém jsou uloženy vaše Image kontejneru. S nespravované registrech, nelze přepnout mezi SKU podle potřeby (jiné než [upgrade] [ container-registry-upgrade] spravované registru), a několik funkce spravované registrech nejsou k dispozici (například) Odstranění obrázku kontejneru, [geografická replikace][container-registry-geo-replication], a [webhooky][container-registry-webhook]).

Další informace o upgradu na jednu z spravované SKU Classic registru najdete v tématu [Upgrade Classic registru][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Matice funkce SKU

V následující tabulce jsou funkcí a omezení úrovně služeb Basic, Standard a Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Změna SKU

Soubor registru SKU pomocí rozhraní příkazového řádku Azure nebo na portálu Azure, můžete změnit. Můžete můžete volně přesouvat mezi spravované SKU dokud SKU přepínáte k dispozici požadované maximální úložnou kapacitu. Pokud přejdete na jednu z spravované SKU z klasického, nemůžete přesunout zpět do Classic – je jednosměrný převod.

### <a name="azure-cli"></a>Azure CLI

Chcete-li přesunout mezi SKU v Azure CLI, použijte [az acr aktualizace] [ az-acr-update] příkaz. Chcete-li například přepnout na Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

V registru kontejneru **přehled** na portálu Azure vyberte **aktualizace**, pak vyberte nový **SKU** z rozevíracího seznamu SKU.

![Aktualizace registru kontejneru SKU na portálu Azure][update-registry-sku]

Pokud máte registru Classic, nelze vybrat spravované SKU v rámci portálu Azure. Místo toho je nutné nejprve [upgrade] [ container-registry-upgrade] spravované registru (najdete v části [změna z klasického](#changing-from-classic)).

## <a name="changing-from-classic"></a>Změna z Classic

Existují další aspekty vzít v úvahu při migraci nespravované Classic registru na jednu z spravované Basic, Standard nebo Premium SKU. Pokud vaše Classic registru obsahuje velký počet obrázků a je mnoho gigabajtů velikost, proces migrace může chvíli trvat. Kromě toho `docker push` operace jsou zakázány, dokud se nedokončí migrace.

Podrobnosti týkající se upgradu vaší Classic registru na jednu z spravované SKU najdete v tématu [Upgrade kontejneru registru Classic][container-registry-upgrade].

## <a name="pricing"></a>Ceny

Informace o cenách ve všech SKU registru kontejner Azure, najdete v části [ceny kontejneru registru][container-registry-pricing].

## <a name="next-steps"></a>Další postup

**Kontejner Azure registru plán**

Přejděte [ACR plán] [ acr-roadmap] na Githubu se najít informace o chystaných funkcí ve službě.

**Kontejner Azure registru UserVoice**

Odeslání a hlasovat o nové funkce návrhů v [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md

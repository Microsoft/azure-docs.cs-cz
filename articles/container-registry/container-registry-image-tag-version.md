---
title: Osvědčené postupy značek obrázků
description: Osvědčené postupy pro označování a nastavování imagí kontejnerů Docker při vkládání imagí do a načítání imagí z služby Azure Container Registry
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: 9dfd29f2265e599f3cc7c412b81c3b7b93af40db
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109709"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Doporučení pro označování a naznačení verzí imagí kontejneru

Když zadáváte image kontejneru do registru kontejneru a pak je nasadíte, budete potřebovat strategii pro označování imagí a správu verzí. Tento článek popisuje dva přístupy a jejich umístění do životního cyklu kontejneru:

* **Stabilní značky značek** , které můžete opakovaně používat, například k označení hlavní nebo dílčí verze, jako je například *mycontainerimage: 1.0*.
* **Jedinečné značky** – pro každý obrázek, který zadáváte do registru, jako je například *mycontainerimage: abc123*, se jedná o jinou značku.

## <a name="stable-tags"></a>Stabilní značky

**Doporučení**: použití stabilních značek k údržbě **základních imagí** pro sestavení kontejnerů. Vyhněte se nasazení s stabilními značkami, protože tyto značky nadále získávají aktualizace a můžou v produkčních prostředích zavádět nekonzistence.

*Stabilní značky* znamenají, že vývojář nebo systém sestavení může pokračovat v vyžádání konkrétní značky, která bude nadále získávat aktualizace. Stabilita znamená, že obsah je zmrazený. Místo toho předpokládá, že by image měla být stabilní pro záměr této verze. Aby zůstala stálá, mohla by se provozovat na použití oprav zabezpečení nebo aktualizací rozhraní.

### <a name="example"></a>Příklad

Tým rozhraní dodává verzi 1,0. Ví, že budou dodávat aktualizace, včetně menších aktualizací. Pro podporu stabilních značek pro danou hlavní a dílčí verzi mají dvě sady stabilních značek.

* `:1` – stabilní značka pro hlavní verzi. `1` představuje nejnovější nebo nejnovější verzi 1. *.
* `:1.0`– stabilní značka pro verzi 1,0, která vývojářům umožňuje vytvořit vazby na aktualizace 1,0 a nebude se předávat do 1,1 po vydání.

Tým také používá `:latest` značku, která odkazuje na nejnovější stabilní značku bez ohledu na to, co je aktuální hlavní verze.

Pokud jsou k dispozici základní aktualizace obrázků nebo jakýkoli typ servisního vydání rozhraní, bitové kopie s stabilními značkami se aktualizují na nejnovější výtah, který představuje nejaktuálnější verzi této verze.

V tomto případě se průběžně obsluhují hlavní i vedlejší značky. V případě základní Image to umožňuje vlastníkovi obrázku poskytovat obsluhované bitové kopie.

### <a name="delete-untagged-manifests"></a>Odstranit netagované manifesty

Pokud je obrázek s stabilní značkou aktualizován, dříve označený obrázek není označený, výsledkem bude osamocený obrázek. Manifest předchozí image a jedinečná data vrstvy zůstávají v registru. Chcete-li zachovat velikost registru, můžete pravidelně odstraňovat netagované manifesty, které jsou způsobeny stabilními aktualizacemi imagí. Například [Automatické vymazání](container-registry-auto-purge.md) netagovaných manifestů, které jsou starší než zadaná doba trvání, nebo nastavte [zásady uchovávání informací](container-registry-retention-policy.md) pro netagované manifesty.

## <a name="unique-tags"></a>Jedinečné značky

**Doporučení**: Používejte jedinečné značky pro **nasazení**, zejména v prostředí, které by mohlo škálovat na více uzlech. Pravděpodobně budete chtít záměrné nasazení konzistentní verze komponent. Pokud se Váš kontejner restartuje nebo Orchestrator navýší více instancí, nebudou Vaši hostitelé omylem vyčítat novější verzi, která není konzistentní s ostatními uzly.

Jedinečné označení jednoduše znamená, že každý obrázek, který byl vložen do registru, má jedinečnou značku. Značky se znovu nepoužívají. Existuje několik vzorů, které můžete použít ke generování jedinečných značek, včetně:

* **Časové razítko** – tento přístup je poměrně společný, protože můžete jasně určit, kdy se obrázek sestavil. Ale jak ho korelovat zpátky do vašeho sestavovacího systému? Je nutné najít sestavení, které bylo dokončeno ve stejnou dobu? V jakém časovém pásmu jste? Jsou všechny vaše systémy sestavení kalibrovány do standardu UTC?
* **Git Commit**  – tento přístup funguje, dokud nezačnete podporovat základní aktualizace imagí. Pokud dojde k aktualizaci základní image, systém sestavení se zahájí se stejným potvrzením Git jako předchozí sestavení. Základní image ale obsahuje nový obsah. Obecně platí, že Git potvrzení poskytuje *částečně* stabilní značku.
* **Výtah manifestu** – každá image kontejneru vložená do registru kontejneru je přidružená k manifestu, který je identifikovaný jedinečnou hodnotou hash SHA-256 nebo hodnotou Digest. I když je jedinečný, je výtah dlouhý, obtížně čitelný a nekoreluje s vaším prostředím sestavení.
* **ID buildu** – Tato možnost může být nejlepší, protože je pravděpodobně přírůstková a umožňuje provést korelaci zpět k určitému sestavení a vyhledat všechny artefakty a protokoly. Podobně jako u výtahu manifestu ale může být obtížné číst člověka.

  Pokud má vaše organizace několik systémů sestavení, Předpona značky s názvem systému sestavení je variací této možnosti: `<build-system>-<build-id>` . Můžete například odlišit buildy ze systému sestavení Jenkinse týmu rozhraní API a webového týmu Azure Pipelines systém sestavení.

### <a name="lock-deployed-image-tags"></a>Zamknout značky nasazené bitové kopie

Jako osvědčený postup doporučujeme, abyste zavedli [uzamknutí](container-registry-image-lock.md) všech nasazených značek bitové kopie nastavením jejího `write-enabled` atributu na `false` . Tímto postupem zabráníte nechtěnému odebrání image z registru a případnému přerušení nasazení. Krok zamykání můžete zahrnout do svého kanálu pro vydávání verzí.

Uzamykání nasazené image vám pořád umožňuje odebrat jiné, nenasazené image z registru, a to pomocí funkcí Azure Container Registry k údržbě registru. Můžete například [automaticky vyprázdnit](container-registry-auto-purge.md) netagované manifesty nebo odemknuté obrázky starší než zadaná doba trvání nebo nastavit [zásady uchovávání informací](container-registry-retention-policy.md) pro netagované manifesty.

## <a name="next-steps"></a>Další kroky

Podrobnější diskuzi o konceptech v tomto článku najdete v blogovém příspěvku [označení Docker: osvědčené postupy pro označování a správu verzí imagí Docker](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

V zájmu maximalizace výkonu a nákladově efektivního využívání služby Azure Container Registry si přečtěte téma [osvědčené postupy pro Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->


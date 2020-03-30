---
title: Doporučené postupy pro označení obrázku
description: Doporučené postupy pro označování a správu verzí ibi kontejnerů Dockeru při odesílání ibi do a vytahování ibi z registru kontejnerů Azure
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445740"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Doporučení pro označování a správu verzí iontů kontejnerů

Při odesílání nasazení inamh kontejneru do registru kontejneru a jejich následné nasazení potřebujete strategii pro označování bitových kopií a správu verzí. Tento článek popisuje dva přístupy a kde se každý vejde během životního cyklu kontejneru:

* **Stabilní značky** – značky, které znovu použijete, například k označení hlavní nebo dílčí verze, například *mycontainerimage:1.0*.
* **Jedinečné značky** – pro každou bitovou kopii, kterou předáte do registru, jiná značka, například *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabilní značky

**Doporučení:** Pomocí stabilních značek můžete udržovat **základní image** pro sestavení kontejnerů. Vyhněte se nasazení se stabilními značkami, protože tyto značky nadále dostávají aktualizace a mohou zavádět nekonzistence v produkčním prostředí.

*Stabilní značky* znamenají, že vývojář nebo systém sestavení může pokračovat v vytahování konkrétní značky, která pokračuje v aktualizaci. Stabilní neznamená, že obsah je zmrazen. Spíše stabilní znamená, že obraz by měl být stabilní pro záměr této verze. Chcete-li zůstat "stabilní", může být obsluhována použít opravy zabezpečení nebo aktualizace architektury.

### <a name="example"></a>Příklad

Rámcový tým dodává verze 1.0. Vědí, že budou doručovávat aktualizace, včetně menších aktualizací. Pro podporu stabilních značek pro danou hlavní a dílčí verzi mají dvě sady stabilních značek.

* `:1`– stabilní značka pro hlavní verzi. `1`představuje "nejnovější" nebo "nejnovější" verzi 1.*.
* `:1.0`- stabilní značka pro verzi 1.0, která umožňuje vývojáři vázat na aktualizace 1.0 a nesmí být vrácena dopředu na 1.1, když je vydána.

Tým také používá `:latest` značku, která odkazuje na nejnovější stabilní značku, bez ohledu na aktuální hlavní verzi.

Pokud jsou k dispozici aktualizace základní bitové kopie nebo jakýkoli typ údržby verze rozhraní, bitové kopie se stabilní značky jsou aktualizovány na nejnovější digest, který představuje nejnovější stabilní verze této verze.

V tomto případě jsou hlavní i vedlejší značky neustále servisovány. Ze scénáře základní bitové kopie to umožňuje vlastníkovi bitové kopie poskytovat obsluhované bitové kopie.

### <a name="delete-untagged-manifests"></a>Odstranění netagovaných manifestů

Pokud je obraz se stabilní značkou aktualizován, dříve tagovaný obraz je netagovaný, což vede k osamocený obrázek. Data manifestu a jedinečné vrstvy předchozí bitové kopie zůstanou v registru. Chcete-li zachovat velikost registru, můžete pravidelně odstraňovat netagované manifesty vyplývající ze stabilních aktualizací obrázků. Například [automatické proplachování](container-registry-auto-purge.md) netagovaných manifestů starších než zadaná doba trvání nebo nastavení [zásad uchovávání informací](container-registry-retention-policy.md) pro netagované manifesty.

## <a name="unique-tags"></a>Jedinečné značky

**Doporučení:** Používejte jedinečné **značky**pro nasazení , zejména v prostředí, které by mohlo škálovat na více uzlech. Pravděpodobně budete chtít záměrné nasazení konzistentní verze součástí. Pokud se váš kontejner restartuje nebo orchestrátor škáluje více instancí, hostitelé nebudou omylem vytáhnout novější verzi, což není konzistentní s ostatními uzly.

Jedinečné označování jednoduše znamená, že každá bitová kopie zasazená do registru má jedinečnou značku. Značky nejsou znovu použity. Existuje několik vzorů, které můžete sledovat, abyste vytvořili jedinečné značky, včetně:

* **Datum-čas razítko** - Tento přístup je poměrně časté, protože můžete jasně říct, kdy byl obraz postaven. Ale jak korelovat zpět do vašeho systému sestavení? Potřebujete najít sestavení, které bylo dokončeno ve stejnou dobu? V jakém jste časovém pásmu? Jsou všechny vaše systémy sestavení kalibrovány na UTC?
* **Potvrzení Git** – Tento přístup funguje, dokud nezačnete podporovat aktualizace základní image. Pokud dojde k aktualizaci základní bitové kopie, systém sestavení se spustí se stejným potvrzením Gitu jako předchozí sestavení. Základní obrázek má však nový obsah. Obecně platí, že potvrzení Git poskytuje *polostabilní*značku.
* **Manifest digest** - Každá bitová kopie kontejneru posunutá do registru kontejneru je přidružena k manifestu identifikovaného jedinečným haštovací sadou SHA-256 nebo digest. Zatímco jedinečný, digest je dlouhý, obtížně čitelný a nekorelované s prostředím sestavení.
* **ID sestavení** – Tato možnost může být nejlepší, protože je pravděpodobně přírůstkové a umožňuje korelovat zpět na konkrétní sestavení najít všechny artefakty a protokoly. Nicméně, stejně jako zjevně digest, to může být obtížné pro člověka číst.

  Pokud má vaše organizace několik systémů sestavení, předpona značky s `<build-system>-<build-id>`názvem systému sestavení je variantou této možnosti: . Můžete například odlišit sestavení od systému sestavení Jenkinstýmu rozhraní API a systému sestavení Azure Pipelines webového týmu.

### <a name="lock-deployed-image-tags"></a>Zamknout nasazené značky bitových obrázků

Jako osvědčený postup doporučujeme [zamknout](container-registry-image-lock.md) všechny nasazené image `write-enabled` tag, `false`nastavením atributu . Tento postup zabraňuje nechtěnému odebrání bitové kopie z registru a případnému narušení nasazení. Krok uzamčení můžete zahrnout do kanálu vydání.

Uzamčení nasazené bitové kopie stále umožňuje odebrat další nenasazené bitové kopie z registru pomocí funkcí registru kontejnerů Azure k údržbě registru. Například [automatické pročištění](container-registry-auto-purge.md) netagovaných manifestů nebo odemčených obrázků starších než zadaná doba trvání nebo nastavení [zásad uchovávání informací](container-registry-retention-policy.md) pro netagované manifesty.

## <a name="next-steps"></a>Další kroky

Podrobnější informace o konceptech v tomto článku najdete v příspěvku blogu [Docker Tagging: Doporučené postupy pro označování a správu verzí imitací dockeru](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Pokud chcete maximalizovat výkon a nákladově efektivní využití registru kontejnerů Azure, přečtěte si [informace o doporučených postupech pro Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->


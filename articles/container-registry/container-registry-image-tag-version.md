---
title: Značky a verze imagí ve službě Azure Container Registry
description: Osvědčené postupy pro označování a správy verzí Docker imagí kontejneru
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: steve.lasker
ms.openlocfilehash: bd00fd4f8dd247c766eb34849ecf9de603c5171b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800390"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Doporučení pro označování a správy verzí Image kontejneru

Při nahrávání nasazování imagí kontejneru do registru kontejneru a jejich nasazení, potřebují strategii pro označování image a správy verzí. Tento článek popisuje dva přístupy a kde každý vyhovuje během životního cyklu kontejneru:

* **Stabilní značky** – značky, které můžete znovu použít, například znamenat velkou nebo vedlejší číslo verze, jako *mycontainerimage:1.0*.
* **Jedinečné značky** -jinou značku pro každý obrázek nahrajete do registru, jako například *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabilní značky

**Doporučení**: Použití značek stabilní udržovat **základní image** pro sestavení kontejneru. Nasazení s stabilní značky se vyhněte, protože tyto visačky nadále získávat aktualizace a můžete zavést nekonzistence v produkčním prostředí.

*Stabilní značky* znamenat vývojář nebo systém sestavení, můžete nadále o přijetí změn s konkrétní značkou nadále získávat aktualizace. Stabilní, neznamená, že jsou zmražená obsah. Místo toho znamená stabilní verze image by měla být stabilní pro záměr tuto verzi. Zůstat "stálé", může být obsluhovány použití opravy zabezpečení nebo aktualizace rozhraní framework.

### <a name="example"></a>Příklad

Tým rozhraní framework dodává verze 1.0. Ví, že se budete vydávat, včetně dílčí aktualizace. Pro podporu stabilní značky pro danou hlavní a dílčí verze, mají dvě sady stabilní značky.

* `:1` – stabilní značky pro hlavní verzi. `1` představuje verzi 1.* "nejnovější" nebo "posledního".
* `:1.0`-stabilní značky pro verzi 1.0, což vývojář, abyste mohli vytvořit vazbu k aktualizacím 1.0 a nesmí být posunut dopředu na hodnotu 1.1 po vydání.

Tým také používá `:latest` značku, která odkazuje na nejnovější stabilní značka, bez ohledu na to, jaký je aktuální hlavní verze.

Když jsou k dispozici aktualizace základní image nebo libovolný typ obsluhy verzi rozhraní framework imagí pomocí stabilní značky se aktualizují na nejnovější ověřování algoritmem digest, který představuje nejnovější stabilní verze tuto verzi.

V tomto případě jsou neustále údržba hlavních a vedlejších značek. Ze základní image scénáře díky tomu vlastníka bitové kopie poskytnout obsluhované imagí.

## <a name="unique-tags"></a>Jedinečné značky

**Doporučení**: Použijte jedinečný značky pro **nasazení**, obzvláště v prostředí, které se může škálovat na více uzlech. Pravděpodobně budete chtít rozhodnout vědomě a záměrně nasazení konzistentní verzi komponenty. Pokud restartování kontejneru nebo orchestrátor horizontálně navýší kapacitu instancí, vaši hostitelé nebudou o přijetí změn omylem novější verze, konzistentní se do dalších uzlů.

Jedinečné tagování jednoduše znamená, že každý obrázek nahrány do registru má značku jedinečný. Znovu použít značky. Existují některé postupy, které můžete provést, abyste generovat jedinečný značky, včetně:

* **Časové razítko** -tento přístup je poměrně běžný, protože jasně poznáte, kdy byla vytvořena image. Ale jak porovnat zpět do vašeho systému sestavení? Je nutné nalézt sestavení, který byl dokončen ve stejnou dobu? Časové pásmo se nacházíte? Jsou všechny systémy sestavení nastaveny na čas UTC?
* **Potvrzení změn Git** – tento postup funguje, dokud nespustíte podporuje aktualizace základní image. Pokud se stane aktualizací základních imagí, systém sestavení zahajuje s potvrzení Git stejný jako předchozí sestavení. Základní image má však nový obsah. Obecně platí, poskytuje potvrzení změn Git *částečně*-stabilní značky.
* **Ověřování algoritmem digest manifestu** – každá image kontejneru do registru kontejneru je spojen s manifestem, identifikovat jedinečné hodnoty hash SHA-256, nebo ověřování algoritmem digest. Jedinečné, algoritmus digest je dlouhý, mohou ztížit čtení a bez korelace nejsou s vaším prostředím sestavení.
* **ID sestavení** – tato možnost může být nejvhodnější, protože bude pravděpodobně přírůstkové, a umožňuje korelovat zpět na konkrétní sestavení odeslat najít všechny artefakty a protokoly. Však stejně jako manifestu hodnotu hash, může být obtížné pro lidské ke čtení.

  Pokud má vaše organizace několik systémů buildů, předpony značky s názvem systému sestavení je podobný tuto možnost: `<build-system>-<build-id>`. Například může odlišíte sestavení z týmu rozhraní API systému sestavení Jenkinse a kanály pro Azure web týmu sestavovací systém.

## <a name="next-steps"></a>Další postup

Podrobnější popis konceptů v tomto článku najdete v příspěvku blogu [označování Dockeru: Osvědčené postupy pro označování a správy verzí Image dockeru](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Umožní vám maximalizovat výkon a nákladově efektivní používání svého registru kontejneru Azure, najdete v článku [osvědčené postupy pro službu Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->


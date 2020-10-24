---
title: Celkové náklady na vlastnictví (vlastnictví) s Azure Cosmos DB
description: Tento článek porovnává celkové náklady na vlastnictví Azure Cosmos DB s IaaS a místními databázemi.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: a058e7a23c36dcaca16c3cce1be1ca826d472bc0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476990"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Celkové náklady na vlastnictví (vlastnictví) s Azure Cosmos DB

Součástí návrhu služby Azure Cosmos DB jsou podrobné zásady správného řízení víceklientské architektury a prostředků. Tento návrh umožňuje provoz služby Azure Cosmos DB s výrazně nižšími náklady a pomáhá uživatelům ušetřit. Služba Azure Cosmos DB v současné době podporuje více než 280 zákaznických úloh na jednom počítači (tato hustota se neustále zvyšuje) a tisíce zákaznických úloh v rámci jednoho clusteru. Zajišťuje vyrovnávání zatížení replik zákaznických úloh napříč různými počítači v clusteru a napříč několika clustery v rámci datacentra. Další informace najdete v tématu [Azure Cosmos DB: vložení hranice globálně distribuovaných databází](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Z důvodu zásad správného řízení prostředků, víceklientské architektury a nativní integrace se zbytkem infrastruktury Azure je Azure Cosmos DB v průměru 4 až 6 časů levnějších než MongoDB, Cassandra nebo jiná OSS NoSQL spuštěná v IaaS a až 10krát levnější než databázové moduly běžící místně. Podívejte se na dokument s [celkovými náklady na vlastnictví cloudové služby databáze NoSQL (bez nich)](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

Databázová řešení OSS NoSQL, jako je Apache Cassandra, MongoDB, HBA, jsou navržená místně. Když se tato služba nabídne jako spravovaná, je rovnocenná šabloně Správce prostředků s databází tenanta pro správu zřízené clusterů a monitorování podpory. Architektury OSS NoSQL vyžadují významnou provozní režii a odbornost můžou být obtížné najít. Na druhé straně Azure Cosmos DB je plně spravovaná cloudová služba, která vývojářům umožňuje soustředit se na firemní inovace, a ne na správu a údržbu infrastruktury databáze.

Na rozdíl od cloudové databázové služby Azure Cosmos DB nevytvořili a sestavili databázové stroje OSS NoSQL pomocí zásad správného řízení prostředků nebo jemně odstupňované víceklientské architektury jako základní principy architektury. Databázové moduly OSS NoSQL, jako je Cassandra a MongoDB, vycházejí z předpokladu, že všechny prostředky virtuálního počítače, na kterých jsou spuštěné, jsou k dispozici pro jejich použití. Mnohé z těchto databázových strojů nemůžou fungovat, pokud množství prostředků klesne pod určitou prahovou hodnotu. Například u malých instancí virtuálních počítačů, které jsou k dispozici s konfiguracemi doporučenými pro dodavatele naznačující obvykle velké virtuální počítače s vyššími náklady. Proto není možné hostovat NoSQL OSS ani jiný místní databázový stroj a zpřístupnit ho pomocí modelu zpoplatnění založeného na spotřebě, jako jsou požadavky za sekundu nebo spotřebované úložiště.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Celkové náklady na vlastnictví Azure Cosmos DB

Model zřizování Azure Cosmos DB bez serveru eliminuje nutnost nadměrného poskytování infrastruktury databáze. Azure Cosmos DB prostředky se poskytují bez nutnosti specializované konfigurace nebo licencování. V důsledku toho se Azure Cosmos DBé aplikace, které jsou v porovnání s databázemi OSS NoSQL, dají spouštět s využitím až 70 procent celkových nákladů na úspory vlastnictví. Příklady některých příkladů v reálném čase najdete v tématu [použití zákazníka – případy](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Mezi další výhody Azure Cosmos DB cenového modelu patří:

* **Skvělé hodnota ceny:** Poskytovatelé trhu, zákazníci a partneři potvrdili větší hodnotu všech funkcí, které Azure Cosmos DB nabízí mnohem nižší cenu v porovnání s tím, co můžou zákazníci získat při implementaci těchto řešení na svých vlastních nebo jiných dodavatelích. Tato databáze zahrnuje globální distribuci, zápisy ve více oblastech, jasně definované a intuitivní modely konzistence. automatické indexování se výrazně zjednodušují s Azure Cosmos DB bez jakýchkoli složitosti, režie nebo výpadků.

* **Není nutná žádná Správa NoSQL DevOps:** Když Azure Cosmos DB nikdo nepotřebuje ke správě nasazení používat DevOps, proveďte údržbu, škálování nebo opravu. Můžete spouštět všechny úlohy, které byste měli udělat s hostovaným clusterem OSS NoSQL, který je hostovaný místně nebo v cloudové infrastruktuře.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Azure Cosmos DB náklady na vlastnictví" border="false":::

* **Možnost elastického škálování:** Propustnost Azure Cosmos DB lze škálovat nahoru a dolů, což vám umožní snížit náklady na vlastnictví během období mimo špičku. Clustery OSS NoSQL nasazené v infrastruktuře cloudu nabízejí omezené pružnost a místní nasazení nejsou elastická podle definice. Pokud ve službě Azure Cosmos DB zřizujete větší propustnost, je zaručená propustnost lineárně škálovat. Tato záruka zálohuje finanční SLA a 99 percentil v jakémkoli měřítku.

* **Úspory rozsahu:** Spravovaná služba, například Azure Cosmos DB pracuje s velkým počtem uzlů integrovaných v nativním prostředí s využitím sítě, úložiště a výpočetních prostředků. Vzhledem k velké škále Azure Cosmos DB se standardně můžete ušetřit náklady.

* **Optimalizováno pro Cloud:** Azure Cosmos DB je navržená z provozu s jemnou izolací více tenantů a výkonu. To umožňuje optimální umístění, spouštění a vyrovnávání tisíců klientů a jejich úloh napříč clustery a datovými centry. Na rozdíl od aktuální generace databází OSS NoSQL pracuje místně s celým virtuálním počítačem, který předpokládá spuštění úlohy jednoho tenanta. Tyto databáze nejsou také navržené k využití infrastruktury a hardwaru poskytovatele cloudu v plném rozsahu. Například databázový stroj OSS NoSQL neví o rozdílech mezi virtuálním počítačem, který se rozpíná oproti běžnému upgradu obrazu, nebo s tím, že je už na disku úrovně 3-Way replikace. Nedokáže využít výhod těchto výhod a předávat zákazníkům výhody a úspory.

* **Platíte za hodinu:** U rozsáhlých úloh, které se potřebují škálovat kdykoli v čase, se účtují jenom hodiny. Zatížení aplikace se obvykle liší v době v roce a podle dat, která se dotazují. Díky Azure Cosmos DB můžete škálovat nahoru nebo dolů podle potřeby a platit jenom za to, co potřebujete. U místních nebo IaaSch systémů se nemůžete shodovat s tímto modelem, protože neexistuje způsob, jak vyřadit z provozu hardware každou hodinu. V takových případech můžete v průměru s Azure Cosmos DB ušetřit od 10 do 14 krát.

* **Získáte mnoho funkcí zdarma:** V Azure Cosmos DB jsou úlohy zápisu podstatně levnější v porovnání s alternativním databázovými službami. Azure Cosmos DB navíc nabízí funkce, jako je například [automatické indexování](index-policy.md), [hodnota TTL (Time to Live)](time-to-live.md), [Změna kanálu](change-feed.md) a jiné bez jakýchkoli dalších poplatků, což je obvykle nákladná na jinou službu databáze.

* **Používá jednotnou měnu pro různé úlohy:** Na rozdíl od alternativních nabídek v Azure Cosmos DB nemusíte segmentovat úlohy, například na čtení a zápis. Nebo zřízení propustnosti u jednotlivých typů úloh, které jsou propustnost čtení a propustnost zápisu. V Azure Cosmos DB je zajištěná propustnost vyhrazena pomocí sjednocené a normalizované měny z hlediska jednotek požadavků nebo RU/s. Azure Cosmos DB nenutí, abyste přiřadili k vašim úlohám prioritu, prováděli plánování kapacity nebo platíte za každý typ kapacity samostatně. Takový přístup umožňuje snadno zamezit stejné RU/s mezi různými typy operací a úloh.

* **Nevyžaduje zřizování virtuálních počítačů:** Většina provozních databází vyžaduje, abyste přešli na velké virtuální počítače, abyste se vyhnuli hlučným sousedům a pro nenáročné řízení prostředků, pokud chcete škálovat. To přináší zatížení a předem provázání nákladů na zákazníky. Díky Azure Cosmos DB můžete začít malým a růstem velkých objemů úloh ve velkém rozsahu, a to bez výpadků nebo dopadu na dostupnost dat.

* **Můžete využít zřízenou propustnost do maximálního limitu:** Díky podzákladnímu multiplexování v Azure Cosmos DB můžete zvýšit výkon zajištěné propustnosti v širším rozsahu než IaaS hostované možnosti nebo nabídky třetích stran. Tato metoda uloží spoustu více než alternativních řešení.

* **Rozsáhlá Integrace Azure Cosmos DB s dalšími službami Azure.** Azure Cosmos DB má nativní integraci se sítěmi, výpočetními Azure Functions (bez serveru), Azure IoT a dalšími službami Azure. Díky této integraci získáte nejlepší výkon a rychlost replikace dat po celém světě díky robustním zárukám. Řešení třetích stran se nemůžou shodovat nebo by obvykle mělo za to, že by tyto funkce nabízela cena.

* **Automaticky získáte vysokou dostupnost s minimálně 10-20 doménami selhání ve výchozím nastavení:** Azure Cosmos DB podporuje distribuci úloh napříč doménami selhání, což je funkce, která je důležitá pro vysokou dostupnost. Nabízí 99,999 vysokou dostupnost pro čtení a zápisy na 99 percentilu napříč kdekoli na světě. Náklady na implementaci něco podobného vašemu nebo prostřednictvím řešení třetí strany by byly vysoké.

* **Automaticky získáte všechny podnikové funkce bez dalších poplatků.** Azure Cosmos DB nabízí nejkomplexnější sadu certifikace dodržování předpisů, zabezpečení a šifrování v klidovém provozu a pohyb bez dalších nákladů (ve srovnání s naší soutěží). Automaticky získáte regionální dostupnost kdekoli na světě. Svou databázi můžete rozložit napříč libovolným počtem oblastí Azure a kdykoli je přidat nebo odebrat.

* **Můžete ušetřit až 65% nákladů s rezervovanou kapacitou:** Azure Cosmos DB [Rezervovaná kapacita](cosmos-db-reserved-capacity.md) vám pomůže ušetřit peníze tím, že se předem platíte za prostředky Azure Cosmos DB po dobu jednoho roku nebo tří let. V porovnání s běžnými cenami můžete významně snížit náklady za jednoleté nebo tříleté závazky a ušetřit 20-65% slev. Na vašich důležitých úlohách můžete získat lepší SLA z hlediska kapacity zřizování.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [tom, jak je cenový model Azure Cosmos DB pro zákazníky nákladově efektivní](total-cost-ownership.md) .
* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](./optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Cosmos s více oblastmi](optimize-cost-regions.md)
* Přečtěte si další informace o [celkovém vlastnictví cloudové služby databáze NoSQL (bez nich)](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf) .

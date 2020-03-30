---
title: Celkové náklady na vlastnictví (TCO) s Azure Cosmos DB
description: Tento článek porovnává celkové náklady na vlastnictví Azure Cosmos DB s IaaS a místní databáze
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754792"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Celkové náklady na vlastnictví (TCO) s Azure Cosmos DB

Součástí návrhu služby Azure Cosmos DB jsou podrobné zásady správného řízení víceklientské architektury a prostředků. Tento návrh umožňuje provoz služby Azure Cosmos DB s výrazně nižšími náklady a pomáhá uživatelům ušetřit. Služba Azure Cosmos DB v současné době podporuje více než 280 zákaznických úloh na jednom počítači (tato hustota se neustále zvyšuje) a tisíce zákaznických úloh v rámci jednoho clusteru. Zajišťuje vyrovnávání zatížení replik zákaznických úloh napříč různými počítači v clusteru a napříč několika clustery v rámci datacentra. Další informace najdete v [tématu Azure Cosmos DB: Posouvání hranice globálně distribuovaných databází](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Díky řízení prostředků, víceklientské a nativní integraci se zbytkem infrastruktury Azure je Azure Cosmos DB v průměru 4 až 6krát levnější než MongoDB, Cassandra nebo jiné OSS NoSQL spuštěné na IaaS a až 10krát levnější než databáze motory běžící v místním prostředí. Podívejte se na článek [O celkových nákladech (mimo) vlastnictví cloudové služby databáze NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

Databázová řešení OSS NoSQL, jako jsou Apache Cassandra, MongoDB, HBase, motory byly navrženy pro místní. Pokud jsou nabízeny jako spravovaná služba, jsou ekvivalentní šabloně Správce prostředků s databází klienta pro správu zřízených clusterů a podporu monitorování. Architektury OSS NoSQL vyžadují značné provozní režie a odborné znalosti mohou být obtížné a nákladné najít. Na druhou stranu Azure Cosmos DB je plně spravovaná cloudová služba, která vývojářům umožňuje soustředit se na obchodní inovace, nikoli na správu a údržbu databázové infrastruktury. 

Na rozdíl od databázové služby Azure Cosmos DB nativní pro cloud, databázové moduly OSS NoSQL nebyly navrženy a vytvořeny se zásadou zásad správného řízení prostředků nebo jemně odstupňovanou multitenancy jako základní principy architektury. Databázové stroje OSS NoSQL jako Cassandra a MongoDB představují základní předpoklad, že všechny prostředky virtuálního počítače, na kterém jsou spuštěny, jsou k dispozici pro jejich použití. Mnoho z těchto databázových strojů nemůže fungovat, pokud množství prostředků klesne pod určitou prahovou hodnotu. Například pro malé instance virtuálních počítačů a jsou k dispozici s konfiguracemi doporučenými dodavatelem, které navrhují obvykle velké virtuální počítače s vyššími náklady. Takže není možné hostovat OSS NoSQL nebo jiný místní databázový stroj a zpřístupnit ho pomocí modelu nabíjení založeného na spotřebě, jako jsou požadavky za sekundu nebo spotřebované úložiště.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Celkové náklady na vlastnictví Azure Cosmos DB 

Model zřizování bez serveru azure cosmos db eliminuje potřebu nadměrného zřizování databázové infrastruktury. Prostředky Azure Cosmos DB jsou k dispozici bez nutnosti specializované konfigurace nebo licencování. V důsledku toho azure cosmos DB-couval aplikace můžete spustit s až 70 procent celkové náklady na úspory vlastnictví ve srovnání s Databází OSS NoSQL. Některé příklady v reálném čase naleznete [v tématu případy použití zákazníka](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Mezi další výhody cenového modelu Azure Cosmos DB patří:

* **Skvělá hodnota za cenu:** Analytici trhu, zákazníci a partneři potvrdili větší hodnotu všech funkcí, které Azure Cosmos DB nabízí za mnohem nižší cenu ve srovnání s tím, co mohou zákazníci získat při implementaci těchto řešení sami nebo prostřednictvím jiných dodavatelů. Databáze obsahuje takové globální distribuce, multi-master, dobře definované a intuitivní modely konzistence, automatické indexování jsou výrazně zjednodušeny s Azure Cosmos DB bez jakékoli složitosti, režie nebo prostoje.

* **Není vyžadována žádná správa NoSQL DevOps:** S Azure Cosmos DB není potřeba používat DevOps ke správě nasazení, provádění údržby, škálování nebo opravy. Můžete spustit všechny úlohy, které byste dělat s ClusterU NoSQL OSS Hostované místně nebo na cloudové infrastruktuře.

![Náklady na vlastnictví azure cosmos db](./media/total-cost-ownership/tco.png)

* **Schopnost elasticky škálovat:** Propustnost Azure Cosmos DB lze škálovat nahoru a dolů, což vám umožní snížit náklady na vlastnictví v době mimo špičku. Clustery OSS NoSQL nasazené v cloudové infrastruktuře nabízejí omezenou elasticitu a místní nasazení nejsou podle definice elastická. V Azure Cosmos DB, pokud zřídíte větší propustnost, vaše propustnost je zaručeno škálování lineárně. Tato záruka je zálohována finančními sla a v 99.

* **Úspory z rozsahu:** Spravovaná služba, jako je Azure Cosmos DB, pracuje s velkým počtem uzlů, které jsou nativně integrované se sítí, úložištěm a výpočetními prostředky. Díky rozsáhlému rozsahu Azure Cosmos DB můžete standardizaci ušetřit náklady.

* **Optimalizováno pro cloud:** Azure Cosmos DB je navržen od základu s jemnozrnné víceklientské a izolace výkonu. To umožňuje optimálně umísťovat, provádět a vyvažovat tisíce klientů a jejich úloh napříč clustery a datovými centry. Naproti tomu aktuální generace databází OSS NoSQL funguje místně s celým virtuálním počítačem, o kterém se předpokládá, že spouštějí úlohy jednoho klienta. Tyto databáze také nejsou navrženy tak, aby využívaly infrastrukturu a hardware poskytovatele cloudu v plném rozsahu. Například databázový stroj OSS NoSQL si není vědom rozdílů mezi virtuálním počítačem, který je mimo uživatele, což je rutinní upgrade bitové kopie, nebo skutečností, že disk premium je již třícestný replikovaný. Nemůže využít těchto výhod a přenést výhody a úspory na zákazníky.

* **Platíte od hodiny:** U rozsáhlých úloh, které je třeba škálovat v libovolném okamžiku, se účtuje pouze hodina. Úlohy v aplikaci se obvykle liší v různých obdobích roku a podle dat, která je dotazován. S Azure Cosmos DB můžete vertikálně navýšit nebo snížit kapacitu podle potřeby a platit jenom za to, co potřebujete. S místními systémy nebo systémy hostovanými v IaaS nelze tento model shodovat, protože neexistuje způsob, jak každý hodinový hardware vyřadit z provozu. V takových případech můžete potenciálně uložit 10 až 14 krát v průměru s Azure Cosmos DB.

* **Získáte mnoho funkcí zdarma:** V Azure Cosmos DB úlohy zápisu jsou podstatně levnější ve srovnání s alternativní databázové služby. Kromě toho Azure Cosmos DB nabízí funkce, jako je například [automatické indexování](indexing-policies.md), [Time to Live (TTL)](time-to-live.md), [Change Feed](change-feed.md) a další bez dalších poplatků, něco, co ostatní databázové služby obvykle účtují.

* **Používá jednotnou měnu pro různé úlohy:** Na rozdíl od alternativních nabídek v Azure Cosmos DB, není nutné segmentovat úlohy, například do čtení a zápisy. Nebo zřízení propustnost na typ pracovního vytížení, který je propustnost čtení vs propustnost zápisu. V Azure Cosmos DB zřízená propustnost je vyhrazena pomocí jednotné a normalizované měny z hlediska jednotek požadavků nebo RU/s. Azure Cosmos DB nenutí přiřadit prioritu vašim úlohám, provádět plánování kapacity nebo platit za každý typ kapacity zvlášť. Takový přístup umožňuje snadno vyměnit stejné RU/s mezi různými operacemi a typy úloh.

* **Nevyžaduje zřizování virtuálních můrek pro škálování:** Většina provozních databází vyžaduje, abyste se vyhnuli velkým virtuálním počítačům, abyste se vyhnuli hlučným sousedům a pro řízení volných prostředků, pokud chcete škálovat. To klade zátěž a počáteční závazek nákladů na zákazníky. S Azure Cosmos DB můžete začít malé a růst do velikosti úloh ve velkém měřítku bez problémů a bez prostojů nebo dopad na dostupnost dat.

* **Zřízenou propustnost můžete využít na maximální limit:** Na základě sub-core multiplexing u Azure Cosmos DB, můžete nasytit zřízená propustnost ve větší míře než hostované možnosti IaaS nebo nabídky třetích stran. Tato metoda šetří mnohem více než alternativní řešení.

* **Hluboká integrace Azure Cosmos DB s dalšími službami Azure.** Azure Cosmos DB má nativní integraci se sítěmi, výpočetními prostředky, funkcemi Azure (bez serveru), Azure IoT a dalšími službami Azure. Díky této integraci získáte nejlepší výkon a rychlost replikace dat po celém světě s robustními zárukami. Řešení třetích stran nebudou moci odpovídat nebo by obvykle účtovat prémii nabízet takové funkce.

* **Automaticky získáte vysokou dostupnost s nejméně 10-20 doménami selhání ve výchozím nastavení:** Azure Cosmos DB podporuje distribuci úloh mezi doménami selhání, což je funkce, která je důležitá pro vysokou dostupnost. Nabízí 99.999 vysokou dostupnost pro čtení a zápisy na 99 percentilu kdekoli na světě. Náklady na implementaci něco takového na vlastní pěst nebo prostřednictvím řešení třetí strany, by bylo vysoké.

* **Automaticky získáte všechny podnikové funkce bez dalších nákladů.** Azure Cosmos DB nabízí nejkomplexnější sadu certifikací dodržování předpisů, zabezpečení a šifrování v klidovém stavu a v pohybu bez dalších nákladů (ve srovnání s naší konkurencí). Regionální dostupnost automaticky získáte kdekoli na světě. Databázi můžete prosáknout libovolným počtem oblastí Azure a v libovolném okamžiku přidat nebo odebrat oblasti.

* **Můžete ušetřit až 65 % nákladů s rezervovanou kapacitou:** [Rezervovaná kapacita](cosmos-db-reserved-capacity.md) Azure Cosmos DB vám pomůže ušetřit peníze tak, že předplatíte prostředky Azure Cosmos DB na jeden rok nebo tři roky. Můžete výrazně snížit své náklady s jednoletými nebo tříletými počátečními závazky a ušetřit 20-65% slevy ve srovnání s běžnými cenami. Na vaše kritické úlohy můžete získat lepší sla, pokud jde o kapacitu zřizování.

## <a name="next-steps"></a>Další kroky

* Další informace o [tom, jak je cenový model Azure Cosmos DB pro zákazníky nákladově efektivní](total-cost-ownership.md)
* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Cosmos s více oblastmi](optimize-cost-regions.md)
* Další informace o [celkových nákladech (ne) vlastnictví cloudové služby NoSQL Database](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)

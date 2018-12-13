---
title: Celkové náklady na Ownership(TCO) pomocí služby Azure Cosmos DB
description: Tento článek porovnává celkové náklady na vlastnictví služby Azure Cosmos DB s modelem IaaS a místních databází
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: a15a704cfba9260daac0cd5edfdf8b4566348628
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263962"
---
# <a name="total-cost-of-ownershiptco-with-azure-cosmos-db"></a>Celkové náklady na Ownership(TCO) pomocí služby Azure Cosmos DB

Azure Cosmos DB je navržená v Pokud potřebujete jemněji odstupňované řízení více tenantů a prostředků. Toto řešení umožňuje službě Azure Cosmos DB moci pracovat s výrazně nižšími náklady a pomáhají tak uživatelům uložit. Azure Cosmos DB aktuálně podporuje více než 280 úloh zákazníka na jednom počítači se průběžně zvýšení hustoty a tisíce úloh zákazníka v rámci clusteru. Se vyrovnává zatížení repliky zákazníkům úloh na různých počítačích v clusteru a napříč více clusterů v datovém centru. Další informace najdete v tématu [služby Azure Cosmos DB: Vkládání hranici globálně distribuované databáze](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Z důvodu zásad správného řízení prostředků, více tenantů a nativní integraci se zbytkem infrastrukturu Azure Azure Cosmos DB je v průměru 4 až 6 násobek levnější než MongoDB, Cassandra nebo jiných OSS NoSQL s na IaaS a až 10krát levnější než databáze moduly spuštěná v místním prostředí. Zobrazit na papír [celkové náklady na vlastnictví (ne) cloudové služby databáze NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

Řešení databáze OSS NoSQL, jako je například Apache Cassandra, MongoDB, HBase, moduly byly navrženy pro místní. Když se nabízí jako spravovaná služba, že jsou ekvivalentní šablonu Resource Manageru s databází tenantů pro správu clusterů zřízené a sledování podpory. OSS NoSQL architektury vyžadují značné provozní náklady a své znalosti může být těžké a drahé najít. Na druhé straně služby Azure Cosmos DB je plně spravovaná Cloudová služba, která umožňuje vývojářům zaměřit na obchodní inovace a nikoli na správu a údržbu infrastruktury databáze. 

Na rozdíl od nativní cloudovou databázovou službu Azure Cosmos DB nebyly OSS NoSQL databázových strojů navržen a sestaven s zásady správného řízení prostředků nebo podrobných víceklientské architektury jako základní architektonických principů. OSS NoSQL databázových strojů, jako jsou Cassandra a MongoDB provést základní předpoklad, že všechny prostředky virtuálního počítače, na kterém jsou spuštěné jsou k dispozici pro jejich použití. Mnohé z těchto databázových strojů nemůže fungovat, pokud objem prostředků klesne pod určitou prahovou hodnotu. Například pro malý virtuální počítač instance a že jsou k dispozici dodavatele doporučené konfigurace navrhuje obvykle ve velkém měřítku virtuální počítače s vyšší náklady. Proto není možné k hostování NoSQL OSS nebo jakékoli jiné místní databázový stroj a zpřístupnit pomocí založenou na skutečné spotřebě zpoplatnění modelu jako požadavků za sekundu nebo spotřebované úložiště.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Celkové náklady na vlastnictví služby Azure Cosmos DB 

Bez serveru modelu zřizování služby Azure Cosmos DB eliminuje potřebu zřizovat databázové infrastruktury. Prostředky Azure Cosmos DB jsou zadaná bez nutnosti specializované konfigurace nebo správy licencí. V důsledku toho aplikace s podporou služby Azure Cosmos DB můžete spustit s až 70 procent celkových nákladů na vlastnictví úsporám ve srovnání s databází OSS NoSQL. Příklady v reálném čase najdete v části [případy použití zákazníka](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Další výhody cenový model Azure Cosmos DB patří:

* **Větší hodnotu za cenu:** Analytici na trhu, zákazníky a partnery potvrdily větší hodnotu všech funkcí, které služby Azure Cosmos DB nabízí za mnohem nižší cenu ve srovnání s co zákazníci mohou získat při implementaci těchto řešení, samostatně nebo prostřednictvím jiných dodavatelů. Databáze obsahuje tyto globální distribuci, modely konzistence s několika hlavními databázemi, dobře definovaný a intuitivní, automatické indexování se značně zjednodušená pomocí služby Azure Cosmos DB bez jakékoli režie, složitost nebo výpadek.

* **Žádnou správu NoSQL devops je potřeba:** Pomocí služby Azure Cosmos DB jeden není potřeba využívat devops pro správu nasazení, provádění údržby, škálování nebo opravy. Můžete spustit všechny úlohy, které byste udělali s OSS NoSQL hostovaný cluster místní nebo cloudové infrastruktury.

![Azure Cosmos DB náklady na vlastnictví](./media/total-cost-ownership/tco.png)

* **Schopností pružného škálování:** Azure Cosmos DB propustnost je možné škálovat nahoru a dolů umožňuje snížit náklady na vlastnictví v obdobích mimo špičku. OSS NoSQL clusterech nasazených v infrastruktuře cloudu nabízí omezené pružnost a nejsou elastické podle definice v místním nasazení. Ve službě Azure Cosmos DB Pokud zřídíte větší propustnost, propustnost je zaručeno se škálují lineárně. Záruka je zálohován finanční smlouvy o úrovni služeb a na 99. percentilu v libovolném měřítku.

* **Cenové výhody:** Spravované služby, jako je Azure Cosmos DB funguje s velkým počtem uzlů, integrovaná nativně sítě, úložiště a výpočetní prostředí. Z důvodu služby Azure Cosmos DB velkém měřítku, normalizaci dá ušetřit.

* **Optimalizováno pro cloudové prostředí:** Azure Cosmos DB je navržená od základů s izolací podrobných více tenantů a výkonu. To umožňuje optimální umístění, provádění a vyrovnávání tisíce tenantů a jejich pracovní vytížení clusterů a datových center. Naproti tomu aktuální generace databáze OSS NoSQL použít místní celý virtuální počítač, který předpokládá, že ke spuštění úlohy jednoho tenanta. Tyto databáze nejsou navržený tak, aby využití poskytovatele cloudové infrastruktury a hardwaru v plném rozsahu. Například upgradovat rutinní image NoSQL OSS databázový stroj není zohledňovat rozdíly mezi virtuálním počítači se dolů Vs, nebo skutečnost, tento disk úrovně premium je už trojcestných replikovat. Nemůže využít tyto výhody a předat jí výhody a úspory pro zákazníky.

* **Platíte podle počtu hodin:** Pro rozsáhlé úlohy, které je potřeba škálovat na libovolné bodu v čase se účtují po hodinách. Úlohy v aplikaci obvykle lišit mezi časy roku a data, která, která je dotazována. Pomocí služby Azure Cosmos DB můžete škálovat směrem nahoru nebo dolů podle potřeby a Plaťte jenom za co potřebujete. U systémů, místní nebo hostovaný IaaS nemůžete přiřadit tento model, protože není k dispozici způsob, jak vyřadit z provozu hardware každou hodinu. V takových případech můžete potenciálně ušetřit mezi do 14 10krát na průměr pomocí služby Azure Cosmos DB.

* **Získat mnoho funkcí zdarma:** Ve službě Azure Cosmos DB zápisu úlohy jsou výrazně levnější porovnání s alternativní databázové služby. Kromě toho Azure Cosmos DB nabízí funkce, jako například [automatického indexování](indexing-policies.md), [čas to Live (TTL)](time-to-live.md), [Change Feed](change-feed.md) a dalších bez jakýchkoli dalších poplatků něco, co se obvykle účtuje jiné databázové služby.

* **Používá jednotné měnu pro různé úlohy.** Na rozdíl od alternativních nabídek ve službě Azure Cosmos DB nepotřebujete k segmentu úlohy, například do operací čtení a zápisu. Nebo zřídit propustnost jeden typ úlohy, která je načtena srovnání propustnosti zápisu propustnosti. Ve službě Azure Cosmos DB zřízená propustnost je vyhrazená pomocí měny jednotné a normalizovaných jednotkách žádosti nebo služby Azure Cosmos DB RU/s. nenutí přiřadit prioritu pro vaše úlohy, plánování kapacit nebo platit pro každý typ kapacity samostatně. Takový přístup umožňuje snadno interchange stejné RU/s mezi různými typy úloh a operace.

* **Nevyžaduje, aby zřizování virtuálních počítačů škálovat:** Většinu provozních databází vyžadují, abyste přejít pomocí velkých virtuálních počítačů, aby se zabránilo "hlučným sousedům" a zásady správného řízení prostředků dojde ke ztrátě, pokud chcete, aby škálování. To umístí zatížení a závazků předem nákladů na zákazníky. Pomocí služby Azure Cosmos DB můžete začít v malém a postupně rozrůstat na velikosti pracovního vytížení velkého rozsahu hladce a bez jakýchkoli výpadků nebo dopad na dostupnost dat.

* **Můžete využít zřízená propustnost pro zajištění maximální limit:** Důsledku z multiplexing dílčí core ve službě Azure Cosmos DB můžete saturate zřízenou propustnost na větší rozsah než IaaS hostované možnosti nebo nabídky třetí strany. Tato metoda šetří mnoho, více než alternativní řešení.

* **Těsná integrace služby Azure Cosmos DB s dalšími službami Azure.** Azure Cosmos DB nabízí nativní integraci s sítě, výpočetních, Azure Functions (bez serveru), Azure IoT a dalších služeb Azure. Tato integrace získáte nejlepší výkon, rychlost replikace dat po celém světě s robustní zárukami. Řešení třetí strany nebudou moci odpovídat nebo by obvykle účtuje na úrovni premium nabízí tyto funkce.

* **Automaticky získáte vysokou dostupnost s doménami selhání alespoň 10-20 ve výchozím nastavení:** Azure Cosmos DB podporuje distribuci zatížení napříč doménami selhání, funkce, která je velmi důležité pro zajištění vysoké dostupnosti. Nabízí 99.999 vysokou dostupnost pro čtení a zápisy na 99. percentilu napříč kdekoli na světě. Náklady na implementaci asi takhle nějak. sami nebo prostřednictvím řešení třetí strany, bude vysoký.

* **Všechny funkce enterprise, se automaticky získáte bez dalších poplatků.** Azure Cosmos DB nabízí nejkomplexnější sadu certifikace dodržování předpisů, zabezpečení a šifrování v klidovém stavu a přenášená data bez dalších poplatků (ve srovnání se naši konkurenci). Automaticky získáte regionální dostupnost kdekoli na světě. Může zahrnovat databáze do libovolného počtu oblastí Azure a přidání nebo odebrání oblastí v libovolném bodě.

* **Vyhrazené kapacitě můžete uložit až o 65 % nákladů:** Azure Cosmos DB [rezervované kapacity](cosmos-db-reserved-capacity.md) pomáhá Šetřete peníze optimalizací pro jeden rok nebo tři roky předem platíte za prostředky Azure Cosmos DB. Můžete výrazně snížit náklady s využitím jeden rok nebo tři roky závazek využití objednané a uložit mezi 20 – 65 % slevu oproti běžné ceny. Na klíčové úlohy můžete získat lepší smlouvy o úrovni služeb z hlediska zřizování kapacity.

## <a name="next-steps"></a>Další postup

* Další informace o [jak služby Azure Cosmos DB cenový model je nákladově efektivní pro zákazníky](total-cost-ownership.md)
* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizovat náklady na účty Cosmos ve více oblastech](optimize-cost-regions.md)
* Další informace o [The celkové náklady na vlastnictví (jiné) cloudové služby databáze NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)

---
title: Případy použití analýzy v reálném čase s odkazem na Azure synapse pro Azure Cosmos DB
description: Přečtěte si, jak se Azure Cosmos DB odkaz na službu Azure synapse používá při analýze dodavatelských řetězců, předpovědi, vytváření sestav, přizpůsobení v reálném čase a prediktivní údržbě IOT.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: 2f7ef27ea5cc19c60e05fddfe4d00cd4ff234bc2
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568042"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Synapse Link pro Azure Cosmos DB: Případy použití pro analýzu téměř v reálném čase

[Odkaz na Azure synapse](synapse-link.md) pro Azure Cosmos DB je cloudová funkce hybridního transakčního a analytického zpracování (HTAP), která umožňuje spouštět analýzy prakticky v reálném čase nad provozními daty. Synapse Link vytvoří úzkou plynulou integraci mezi Azure Cosmos DB a Azure synapse Analytics.

Můžete být zajímái, abyste pochopili, jaké případy použití v oboru můžou využít tuto cloudovou nativní schopnost HTAP pro analýzy téměř v reálném čase nad provozními daty. Tady jsou tři běžné případy použití pro Azure synapse Link pro Azure Cosmos DB:

* Analýzy dodavatelských řetězců, prognózy & vytváření sestav
* Přizpůsobení v reálném čase
* Prediktivní údržba, detekce anomálií ve scénářích IOT

> [!NOTE]
> Odkaz na Azure synapse pro Azure Cosmos DB cílí na scénář, ve kterém mají podnikové týmy běžet s analýzou v reálném čase. Tyto analýzy se spouštějí bez ETL, než jsou provozní data vygenerovaná v rámci transakčních aplikací postavených na Azure Cosmos DB. To nenahrazuje potřebu samostatného datového skladu, pokud existují tradiční požadavky na datový sklad, jako je například Správa úloh, vysoké souběžnosti, agregace trvalosti napříč více zdroji dat.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Analýzy dodavatelských řetězců, prognózy & vytváření sestav

Výzkumné studie ukazují, že vkládání analýz velkých objemů dat do operací dodavatelských řetězců vede k vylepšením v době doručování a efektivitě dodavatelských řetězců.

Výrobci se přihlásili k technologiím nativním pro Cloud, aby bylo možné přerušit omezení starších systémů pro plánování podnikových zdrojů (ERP) a správy dodavatelských řetězců (SCM). Pomocí dodavatelských řetězců generujících rostoucí objemy provozních dat každou minutu (objednávka, dodávka, transakční data) potřebují výrobci provozní databázi. Tato provozní databáze by se měla škálovat tak, aby zpracovávala datové svazky i analytickou platformu, aby se dosáhlo úrovně kontextových informací v reálném čase, které by měly zůstat před křivkou.

Následující architektura ukazuje sílu využití Azure Cosmos DB jako provozní databázi cloudu a synapse propojení v rámci analýzy dodavatelských řetězců:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Odkaz na Azure synapse pro Azure Cosmos DB v analýze dodavatelských řetězců " border="false":::

Na základě předchozí architektury můžete dosáhnout následujících případů použití s odkazem na synapse pro Azure Cosmos DB:

* **Příprava & prognózování výukového kanálu:** Pomocí strojového učení se naučíte vytvářet přehledy přes provozní data napříč dodavatelským řetězcem. Tímto způsobem můžete snížit množství inventáře, provozní náklady a zkrátit dobu doručení pro zákazníky.

  Synapse Link umožňuje analyzovat měnící se provozní data v Azure Cosmos DB bez manuálních procesů ETL. Ušetří vám z dalších nákladů, latence a provozní složitosti. Synapse Link umožňuje datovým inženýrům a odborníkům na data vytvářet robustní prediktivní kanály:

  * Dotazy na provozní data z Azure Cosmos DB analytického úložiště využitím nativní integrace s Apache Spark fondy ve službě Azure synapse Analytics. Můžete zadávat dotazy na data v interaktivním poznámkovém bloku nebo naplánované vzdálené úlohy bez komplexního inženýrství dat.

  * Sestavujte modely Machine Learning (ML) s algoritmy Spark ML a integrací Azure ML v Azure synapse Analytics.

  * Zapište zpátky výsledky po odvození modelu do Azure Cosmos DB pro operační bodování v reálném čase.

* **Provozní sestavy:** Týmy dodavatelského řetězce potřebují flexibilní a vlastní sestavy v reálném čase přesná provozní data. Tyto sestavy jsou potřebné k získání zobrazení snímku efektivity dodavatelských řetězců, ziskovosti a produktivity. Umožňuje analytikům dat a dalším klíčovým účastníkům trvale znovu vyhodnotit firmu a identifikovat oblasti, které je potřeba upravit, aby se snížily provozní náklady. 

  Synapse odkaz pro Azure Cosmos DB povoluje/Reporting scénáře s bohatou business intelligence (BI):

  * Dotazy na provozní data z Azure Cosmos DB analytického úložiště pomocí nativní integrace s synapse bez SQL serveru a plnou expresivity jazyka T-SQL.

  * Modelujte a publikujte automatické aktualizace řídicích panelů BI přes Azure Cosmos DB prostřednictvím synapse podpory bez SQL serveru pro známé nástroje BI. Například Azure Analysis Services, Power BI Premium atd.

Následuje několik pokynů pro integraci dat pro dávkové & streamování dat do Azure Cosmos DB:

* **Orchestrace & integrace dat Batch:** Pomocí dodavatelských řetězců získáte složitější datové platformy dodavatelských řetězců, které je potřeba integrovat s nejrůznějšími zdroji a formáty dat. Azure synapse nabízí integrovaný modul pro integraci dat a prostředí, jako Azure Data Factory. Tato integrace umožňuje datovým technikům vytvářet bohatě propojené datové kanály bez samostatného modulu orchestrace:

  * Přesunutím dat z 85 a podporovaných zdrojů dat můžete [Azure Cosmos DB pomocí Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Vytvářejte kanály ETL bez kódu pro Azure Cosmos DB, včetně [relačních a hierarchických mapování s použitím mapování toků dat](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Zpracování & pro integraci dat do datových proudů:** S růstem prostředků v průmyslu IoT (snímače sledování prostředků z "podlah-Store", propojených logistických loďstev atd.) je rozpad dat v reálném čase generovaných ve streamování, které se musí integrovat s tradičním pomalým pohybem dat pro generování přehledů. Azure Stream Analytics je doporučená služba pro streamování ETL a zpracování v Azure s využitím [široké škály scénářů](../stream-analytics/streaming-technologies.md). Azure Stream Analytics podporuje [Azure Cosmos DB jako nativní datovou jímku](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Přizpůsobení v reálném čase

Prodejci dnes musí vytvářet bezpečná a škálovatelná řešení elektronického obchodování, která splňují požadavky zákazníků i firmy. Tato řešení elektronického obchodování potřebují zákazníkům zapojit prostřednictvím přizpůsobených produktů a nabídek, rychle a bezpečně zpracovávat transakce a soustředit se na plnění a poskytování služeb zákazníkům. Azure Cosmos DB spolu s nejnovějším odkazem na synapse pro Azure Cosmos DB umožňuje maloobchodníkům generovat pro zákazníky individuální doporučení v reálném čase. Pro okamžité přehledy používají nastavení konzistence s nízkou latencí a přizpůsobitelné, jak je znázorněno v následující architektuře:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Odkaz na Azure synapse pro Azure Cosmos DB v analýze dodavatelských řetězců " border="false":::

Synapse odkaz pro případ použití Azure Cosmos DB:

* **Příprava & prognózování výukového kanálu:** Můžete vygenerovat přehledy pro provozní data napříč vašimi obchodními jednotkami nebo zákaznickými segmenty pomocí modelů synapse Spark a Machine Learning. To se týká individuálního doručování do cílových zákaznických segmentů, prediktivního prostředí pro koncové uživatele a cílené marketingu, podle požadavků koncových uživatelů.

## <a name="iot-predictive-maintenance"></a>Prediktivní údržba IOT

Inovace průmyslových IOT se významně snížily na výpadky strojového stroje a zvyšují celkovou efektivitu napříč všemi oblastmi. Jednou z těchto inovací je analýza prediktivní údržby pro strojní zařízení na hranici cloudu.

Následuje architektura, která využívá možnosti cloudového nativního HTAPu služby Azure synapse Link pro Azure Cosmos DB v prediktivní údržbě IoT:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Odkaz na Azure synapse pro Azure Cosmos DB v analýze dodavatelských řetězců " border="false" :::

Synapse odkaz pro případy použití Azure Cosmos DB:

* **Příprava & prognózování výukového kanálu:** Historická provozní data ze senzorů zařízení IoT je možné využít ke školení prediktivních modelů, jako jsou detektory anomálií. Tyto detektory anomálií se pak zpětně nasadí na hranici kvůli monitorování v reálném čase. Tato smyčka virtuous umožňuje nepřetržité přeškolení prediktivních modelů.

* **Provozní sestavy:** Díky nárůstu digitálních vlákenných iniciativ společnosti shromažďují velké množství provozních dat od velkého počtu senzorů a vytvářejí tak digitální kopii každého počítače. Tyto datové pravomoci musí aplikace BI pochopit trendy v historických datech kromě nedávných aktivních dat v aplikacích v reálném čase.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Ukázkový scénář: HTAP pro Azure Cosmos DB

Skoro pro desetiletí Azure Cosmos DB používali tisíce zákazníků pro kritické aplikace, které vyžadují Elastické škálování, klíč globální distribuci, replikaci s více oblastmi pro nízkou latenci a vysokou dostupnost obou čtení & zápisu ve svých transakčních úlohách.
 
Následující seznam obsahuje přehled různých vzorů úloh, které jsou podporované provozními daty pomocí Azure Cosmos DB:

* Aplikace & služby v reálném čase
* Zpracování streamu událostí.
* Řídicí panely BI
* Analýzy velkých objemů dat
* Strojové učení

Připojení Azure synapse Azure Cosmos DB umožňuje, aby neprováděly pouze úlohy s daty z provozu, ale také k vytváření analytických úloh prakticky v reálném čase nad historickými provozními daty. Dochází k tomu bez požadavků na ETL a zaručuje izolaci výkonu z transakčních úloh.

Následující obrázek ukazuje vzory úloh pomocí Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Odkaz na Azure synapse pro Azure Cosmos DB v analýze dodavatelských řetězců " border="false":::

Podíváme se na příklad společnosti elektronického obchodování CompanyXYZ s globálními operacemi v 20 zemích nebo oblastech, abyste se vyhlédli výhodami výběru Azure Cosmos DB jako jedna databáze v reálném čase jak v rámci transakčního, tak i analytického požadavku platformy pro správu inventáře.

* Základní firmy CompanyXYZ závisí na systému správy inventáře, takže dostupnost & spolehlivosti jsou požadavky na základní pilíř. Výhody použití Azure Cosmos DB:

  * Díky důkladné integraci s infrastrukturou Azure a transparentním zápisům na více oblastí, globální replikace Azure Cosmos DB poskytuje špičkovou [99,999% vysokou dostupnost](high-availability.md) před místními výpadky.

* Partneři dodavatelského dodavatelských CompanyXYZ můžou být v různých geografických umístěních, ale můžou si Zobrazit jediné zobrazení inventáře produktů na celém světě, aby bylo možné podporovat jejich místní operace. To zahrnuje nutnost číst aktualizace provedené jinými partnery dodavatelských řetězců v reálném čase. I schopnost dělat aktualizace, aniž byste se museli starat o konflikty s ostatními partnery při vysoké propustnosti. Výhody použití Azure Cosmos DB:

  * S jeho jedinečnou mezioblastí zapisuje protokol replikace a transakční úložiště optimalizované pro zápis bez zámků, Azure Cosmos DB garantuje méně než 10 MS latence pro indexované čtení i zápisy v 99 percentilu globálně.

  * Ingestování s vysokou propustností datových kanálů Batch & streamování v [reálném čase](index-policy.md) v transakčním úložišti.

  * Azure Cosmos DB transakční úložiště poskytuje tři další možnosti než dvě extrémní hodnoty silné a konečné úrovně konzistence, aby dosáhly [kompromisů pro dostupnost a výkon](consistency-levels-tradeoffs.md) , které jsou nejblíže potřebám podniku.

* Partneři dodavatelských dodavatelských společností mají vysoce výkyvy ve vzorech přenosů v rozsahu od stovek po miliony požadavků, a proto musí platforma pro správu inventáře zabývat se neočekávaným burstiness v provozu.  Výhody použití Azure Cosmos DB:

  * Transakční úložiště Azure Cosmos DB podporuje elastickou škálovatelnost úložiště a propustnosti pomocí horizontálního dělení. Kontejnery a databáze nakonfigurované v režimu autopilotu můžou automaticky a okamžitě škálovat zřízenou propustnost na základě potřeb aplikací, aniž by to mělo vliv na dostupnost, latenci, propustnost nebo výkon pro globální úlohy.

* CompanyXYZ musí navázat zabezpečenou analytickou platformu pro vytváření historických dat inventáře v celém systému a umožnit tak analýzy a přehledy napříč partnerem dodavatelských řetězců, obchodními jednotkami a funkcemi. Analytická platforma musí umožňovat spolupráci v rámci systému, tradiční případy použití v BI a vytváření sestav, pokročilé analytické případy a prediktivní inteligentní řešení nad daty provozního inventáře. Výhody použití synapse odkazů pro Azure Cosmos DB:

  * Když použijete [Azure Cosmos DB analytické úložiště](analytical-store-introduction.md), plně izolované úložiště sloupců, odkaz synapse ve [službě Azure synapse Analytics](../synapse-analytics/overview-what-is.md) neumožňuje používat při škálování globálně distribuovaná provozní data žádné analýzy extrakce a transformace (ETL).  Obchodní analytiky, datové inženýri a odborníci přes data teď můžou používat synapse Spark nebo synapse SQL, aby běžely v reálném čase business intelligence, analýze a kanálech strojového učení, aniž by to ovlivnilo výkon transakčních úloh Azure Cosmos DB. Další podrobnosti najdete v tématu věnovaném [výhodám synapse s odkazem v Azure Cosmos DB](synapse-link.md) .

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících dokumentech:

* [Odkaz na Azure synapse pro Azure Cosmos DB](synapse-link.md) 

* [Analytické úložiště Azure Cosmos DB](analytical-store-introduction.md)

* [Práce s odkazem na Azure synapse pro Azure Cosmos DB](configure-synapse-link.md)

* [Nejčastější dotazy k Azure Synapse Linku pro Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Apache Spark ve službě Azure synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Podpora běhového prostředí bez SQL serveru ve službě Azure synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)

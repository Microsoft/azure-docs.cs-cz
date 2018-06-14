---
title: Modelování víceklientská ve službě Azure Search | Microsoft Docs
description: Další informace o běžných návrhová schémata pro víceklientské aplikace SaaS při používání služby Azure Search.
manager: jlembicz
author: ashmaka
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: ashmaka
ms.openlocfilehash: 765f9c4600f762efdd7d57681529751e99c13894
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797171"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Vzory pro víceklientské aplikace SaaS a Azure Search návrhu
Víceklientské aplikace je ten, který poskytuje stejné funkce a služby pro libovolný počet klientů, kteří se nedají zobrazit ani sdílet data žádným jiným klientem. Tento dokument popisuje strategie izolace klienta pro víceklientské aplikace sestavené s Azure Search.

## <a name="azure-search-concepts"></a>Koncepty Azure Search
Jako řešení vyhledávání jako služby Azure Search umožňuje vývojářům přidat bohaté vyhledávání prostředí do aplikace bez správu jakékoliv infrastruktury, nebo aby se aktivovala odborník v načítání informací o. Data se odešlou do služby a pak uloženy v cloudu. Pomocí jednoduchých požadavků na rozhraní API služby Azure Search, data pak se dají upravit a vyhledávat. Přehled služby najdete v [v tomto článku](http://aka.ms/whatisazsearch). Před hovoříte o vzory návrhu, je důležité si uvědomit, některé pojmy ve službě Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Vyhledávací služby, indexy, pole a dokumentů
Při používání služby Azure Search, jeden přihlásí k odběru *služby vyhledávání*. Protože data jsou odeslána do služby Azure Search, uloží se do *index* v rámci služby vyhledávání. Může být počet indexů v rámci jedné služby. Pokud chcete použít koncepty seznámit databází, lze funkci hledání služby připodobnit k databázi při indexy v rámci služby lze připodobnit do tabulek v databázi.

Každý index v rámci služby vyhledávání má svou vlastní schéma, který je definovaný počet přizpůsobitelné *pole*. Přidání dat do indexu Azure Search ve formě individuální *dokumenty*. Každý dokument musí být nahrán do konkrétního indexu a schéma této indexu se musí vejít. Při hledání dat pomocí Azure Search, jsou v dotazech fulltextového vyhledávání vystavený v konkrétním indexem.  Chcete-li porovnat tyto koncepty na ty, databáze, pole lze připodobnit ke sloupců v tabulce a dokumenty lze připodobnit na řádky.

### <a name="scalability"></a>Škálovatelnost
Všechny služby Azure Search v standardní [cenová úroveň](https://azure.microsoft.com/pricing/details/search/) můžete škálovat v dvěma rozměry: úložiště a dostupnost.

* *Oddíly* mohou být přidány do zvýšit velikost úložiště služby vyhledávání.
* *Repliky* mohou být přidány do služby, pokud chcete zvýšit propustnost žádostí, které může zpracovat vyhledávací službu.

Přidávání a odebírání oddílů a repliky ve vám umožní kapacitu služby vyhledávání růst s množství dat a provoz aplikace požaduje. V pořadí pro službu vyhledávání k dosažení čtení [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), vyžaduje dvě repliky. V pořadí pro službu k dosažení pro čtení a zápis [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), vyžaduje tři repliky.

### <a name="service-and-index-limits-in-azure-search"></a>Limity služby a index ve službě Azure Search
Existuje několik různých [cenové úrovně](https://azure.microsoft.com/pricing/details/search/) ve službě Azure Search, každý z vrstvy má jiný [omezení a kvóty](search-limits-quotas-capacity.md). Některé z těchto omezení jsou na úrovni služby, některé jsou na úrovni index a některé jsou na úrovni oddílu.

|  | Basic | Standard1 | Standardní2 | Standardní3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximální replik pro službu |3 |12 |12 |12 |12 |
| Maximální oddílů pro službu |1 |12 |12 |12 |3 |
| Maximální vyhledávání jednotky (repliky * oddíly) pro službu |3 |36 |36 |36 |36 (max 3 oddíly) |
| Maximální dokumenty pro službu |1 milion |180 milionů |720 milionů |1.4 miliardy |600 milionů |
| Maximální velikost úložiště pro službu |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximální dokumenty na oddíly |1 milion |15 milionů |60 milionů |120 milionů |200 milionů |
| Maximální velikost úložiště na jeden oddíl |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximální indexů pro službu |5 |50 |200 |200 |3000 (max 1000 indexy nebo oddíl) |

#### <a name="s3-high-density"></a>S3 s vysokou hustotou.
Ve službě Azure Search S3 cenovou úroveň je možnost pro vysokou hustotou (HD) režim určený speciálně pro víceklientské scénáře. V mnoha případech je potřeba podporovat velký počet menší klientům v rámci jedné služby v využívat výhody technologie jednoduchost a náklady na efektivitu.

S3 HD umožňuje mnoho malých indexy balit v části Správa služby hledání jednoduchého podle obchodování schopnost škálovat indexy používající oddíly pro možnost k hostování více indexů v jedné službě.

Služby S3 namítají, může mít mezi 1 a 200 indexy, které společně by mohl být hostitelem až miliardy 1.4 dokumenty. S3 HD na druhé straně by umožnilo jednotlivé indexy pouze přejít až 1 milionů dokumentů, ale ji může zpracovat až 1 000 indexů pro oddíl (až 3000 pro službu) se dokument celkový počet 200 milionů na oddíl (až 600 milionů za služby).

## <a name="considerations-for-multitenant-applications"></a>Důležité informace pro víceklientské aplikace
Víceklientské aplikace musí efektivní distribuci prostředků mezi klienty při zachování některé úrovně ochrany osobních údajů mezi různými klienty. Existuje několik důležité informace při návrhu architektury pro takové aplikace:

* *Izolaci klientů:* vývojáři aplikace potřeba podniknout potřebné kroky, aby žádné klienti mají Neautorizováno nebo nežádoucí přístup k datům z jiných klientů. Nad rámec hlediska ochrany osobních údajů vyžadují strategie izolace klienta efektivní správu sdílených prostředků a ochranu před aktivní okolí.
* *Náklady na prostředek v cloudu:* jako s žádnou jinou aplikaci, musí zůstat řešení softwaru náklady konkurenční jako součást víceklientské aplikace.
* *Snadné operací:* při vývoji víceklientské architektury, dopad na operace a složitost aplikace je důležitý faktor. Má služba Azure Search [SLA 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globální nároky:* víceklientské aplikace je nutné efektivně obsluhovat klienty, které jsou rozmístěny v celém světě.
* *Škálovatelnost:* vývojáři aplikace je potřeba zvážit, jak se odsouhlasení mezi zachování dostatečně nízkou úroveň aplikace složitost a návrhu aplikace ke škálování se počet klientů a velikost dat klientů a zatížení.

Služba Azure Search nabízí několik hranice, které lze použít k izolování pracovního vytížení a dat klientů.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelování víceklientskou architekturu s Azure Search
V případě scénáři víceklientské vývojář aplikace využívá jednu nebo více služeb vyhledávání a dělit svým klientům mezi služby, indexy nebo obojí. Vyhledávání systému Azure má několik běžných vzorů při modelování víceklientské scénář:

1. *Index každého klienta:* každý klient má vlastní index v rámci služby vyhledávání, která je sdílena s jinými klienty.
2. *Služba každého klienta:* má každý klient svůj vlastní vyhrazený služby Azure Search nabízí nejvyšší úroveň oddělení dat a zatížení.
3. *Kombinace obou:* větší, další aktivní klienty přiřazené vyhrazené služby, zatímco menší klienty přiřazené jednotlivé indexy v rámci sdílených služeb.

## <a name="1-index-per-tenant"></a>1. Index každého klienta
![Portrétu modelu index za klienta](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Ve model index za klienta více klientů zabírají jedné služby Azure Search, kde každý klient má své vlastní index.

Klienti dosáhnout izolace dat, protože všechny vyhledávání, požadavky a operace dokumentu vydávají na úrovni indexu ve službě Azure Search. V aplikační vrstvu je nutné sledování do směrovat provoz s různými klienty na správné indexy při také správu prostředků na úrovni služby napříč všichni klienti.

Klíčový atribut index za klienta modelu je schopnost přidělit nadměrnému počtu procesů kapacitu vyhledávací službu mezi klienty aplikace vývojáři aplikace. Pokud klienti nerovnoměrné rozložení zatížení, optimální kombinaci klientů, které mohou být distribuovány na službu vyhledávání indexy zohlednit počet klientů vysoce aktivní, prostředky a současně současné obsluhování dlouho tail méně aktivních klientů. O kompromisu je nemožnost modelu pro řešení situací, kdy je každý klient souběžně vysoce aktivní.

Index za klienta modelu poskytuje základ pro model náklady na proměnnou, kde je zakoupili počáteční celou službu Azure Search a následně vyplněno s klienty. To umožňuje nevyužité kapacity označené pro zkušební verze a bezplatné účty.

Pro aplikace s globální nároky nemusí být nejúčinnější modelu index za klienta. V případě klientů aplikace jsou distribuovány po celém světě, samostatná služba může být nutné pro každou oblast, která může duplikujte náklady na každý z nich.

Služba Azure Search umožňuje škále jednotlivé indexy a celkový počet indexů zvětšovat. Pokud příslušné ceny je zvoleno vrstvy, oddíly a repliky lze přidat na celý vyhledávací službu při jednotlivých index v rámci služby příliš naroste z hlediska úložiště nebo provoz.

Pokud pro jednu službu příliš naroste celkový počet indexů, jiné služby musí být zřízená uložení nové klienty. Pokud indexy přesunování mezi vyhledávací služby, když jsou přidávány nové služby, musí ručně zkopírovat z jeden index na druhý jako Azure Search není povolena pro index, se přesune data z indexu.

## <a name="2-service-per-tenant"></a>2. Služba každého klienta
![Portrétu na klienta služby modelu](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

V architektuře služby za klienta má každý klient vlastní službu vyhledávání.

V tomto modelu aplikace, která patří dosahuje maximální úroveň izolace pro své klienty. Každá služba má vyhrazený úložiště a propustnost pro zpracování požadavku hledání a také různé klíče rozhraní API.

Aplikace, kde každý klient má velké nároky nebo zatížení variabilitu z klienta na klienta je na klienta služby model efektivní volba jako prostředky nejsou sdílená mezi různými klienty úlohy.

Služby za klienta modelu také nabízí výhodu model předvídatelný, pevné náklady. Není žádné počáteční investici v službě celý vyhledávání, dokud je klient k vyplnění, ale náklady za klienta je vyšší než model index za klienta.

Model na klienta služby je pro aplikace s globální nároky na efektivní volbu. S mnoha místech pracujícími klienty je snadné tak, aby měl každý klient služby v příslušné oblasti.

Problémů v škálování tento vzor vynoří během jednotlivých klientů odrůst své služby. Vyhledávání systému Azure aktuálně nepodporuje upgrade cenovou úroveň služby vyhledávání, takže všechna data by musíte ručně zkopírovat na novou službu.

## <a name="3-mixing-both-models"></a>3. Kombinací obou modelů
Jiné vzor pro modelování víceklientská je kombinací strategie index za klienta a služby za klienta.

Pomocí kombinace těchto dvou vzorech, zabírat největší klienty aplikace vyhrazené služby při dlouho tail míň aktivní, menší klientů zabírat indexy ve sdílených služeb. Tento model zajistí, že klienti největší konzistentně vysoký výkon ze služby, a pomáhá chránit menší klienty z žádné aktivní okolí.

Implementace této strategie však využívá předvídání předpovídat, které klienti budou vyžadovat vyhrazené service versus indexu sdílené služby. Složitost aplikace se zvyšuje s potřeba spravovat oba tyto modely víceklientské architektury.

## <a name="achieving-even-finer-granularity"></a>Dosažení i jemnějšího členitosti
Výše uvedené vzory návrhu pro modelování víceklientské scénáře ve službě Azure Search předpokládají uniform oboru, kde je každý klient celou instanci aplikace. Aplikace však může zpracovávat někdy mnoho menší obory.

Pokud na klienta služby a index za klienta modely nejsou dostatečně malé obory, je možné modelu index k dosažení i podrobnějšího členitosti.

Tak, aby měl jeden index chovat jinak pro koncové body jiného klienta, můžete přidat pole na index, který vytváří určitou hodnotu u jednotlivých možných klientů. Pokaždé, když klient zavolá Azure Search dotazu nebo upravit index, určuje kód z klientské aplikace odpovídající hodnotu pro toto pole používání služby Azure Search [filtru](https://msdn.microsoft.com/library/azure/dn798921.aspx) schopností v době dotazu.

Tato metoda umožňuje dosáhnout funkcionality samostatné uživatelské účty, úrovně samostatné oprávnění a i zcela samostatné aplikace.

> [!NOTE]
> Pomocí metody popsané výše nakonfigurovat jeden index obsluhovat několik klientů ovlivňuje relevance výsledků vyhledávání. Skóre vyhledávání významu se vypočítávají v indexu úrovni oboru, není obor úrovni klienta, aby všichni klienti dat je obsažena v relevance skóre základní statistické údaje, třeba termín frekvence.
> 
> 

## <a name="next-steps"></a>Další postup
Služba Azure Search je poutavé volbou pro mnoho aplikací [Další informace o robustní možnosti služby](http://aka.ms/whatisazsearch). Při vyhodnocování různé návrhová schémata pro víceklientské aplikace, vezměte v úvahu [různých cenových úrovní](https://azure.microsoft.com/pricing/details/search/) a [omezení služby](search-limits-quotas-capacity.md) nejlépe přizpůsobit Azure Search, aby odpovídaly úlohy aplikací a architektury všech velikostí.

Jakékoli dotazy týkající se Azure Search a víceklientské scénáře může přesměrovat k azuresearch_contact@microsoft.com.


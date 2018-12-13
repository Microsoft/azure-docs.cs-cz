---
title: Modelování víceklientskou architekturu ve službě Azure Search | Dokumentace Microsoftu
description: Další informace o běžných vzorech návrhu pro víceklientské aplikace SaaS při používání služby Azure Search.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 1da9756df4fa05b367665a5fe024528939f22578
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313033"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Modely návrhu pro víceklientské aplikace SaaS a Azure Search
Víceklientské aplikace je ten, který poskytuje stejné funkce a služby do libovolného počtu klientů, kteří nemohou zobrazovat ani sdílet data žádným jiným tenantem. Tento dokument popisuje strategie izolace tenanta pro víceklientské aplikace integrované s Azure Search.

## <a name="azure-search-concepts"></a>Koncepty služby Azure Search
Jako řešení typu hledání jako služba Azure Search umožňuje vývojářům přidat do aplikací bohaté vyhledávací prostředí bez správu jakékoliv infrastruktury, nebo studovat načítání informací. Data se odešlou do služby a uloží se do cloudu. Pomocí jednoduchých požadavků na rozhraní API Azure Search, data pak se dají upravit a prohledávat. Přehled služby najdete v [v tomto článku](https://aka.ms/whatisazsearch). Před diskuze o způsobech návrhu, je důležité pochopit některé koncepty ve službě Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Vyhledávací služby, indexy, polí a dokumenty
Při použití Azure Search, jeden přihlásí k odběru *služba search*. Do služby Azure Search je nahrávaných dat, je uložena v *index* v rámci služby search. Může existovat několik indexů v rámci jedné služby. Používat známé koncepty databáze, lze vyhledávací službu připodobnit do databáze a indexy, které v rámci služby lze připodobnit do tabulky v databázi.

Každý index v rámci služby search obsahuje vlastní schéma, které jsou definovány pomocí řadu přizpůsobitelných *pole*. Data je přidána do indexu Azure Search ve formuláři jednotlivých *dokumenty*. Každý dokument musí být odeslán do konkrétního indexu a musí vyhovovat schéma tohoto indexu. Při vyhledávání dat pomocí služby Azure Search se dotazy fulltextového vyhledávání vydaný pro konkrétní index.  Pokud chcete porovnat tyto koncepty u těch, které databáze, pole může být připodobnit na sloupce v tabulce a dokumenty lze připodobnit pro řádky.

### <a name="scalability"></a>Škálovatelnost
Libovolnou službu Azure Search ve standardu [cenovou úroveň](https://azure.microsoft.com/pricing/details/search/) lze škálovat ve dvou dimenzích: úložiště a skupiny dostupnosti.

* *Oddíly* lze přidat do úložiště search service zvýšit.
* *Repliky* lze přidat do služby pro zvýšení prostupnosti požadavků, které dokáže zpracovat vyhledávací službu.

Přidávání a odebírání oddílů a replik v vám umožní kapacita služby search růst s množství dat a provoz poptávky aplikací. V pořadí pro službu search k dosažení čtení [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), vyžaduje dvě repliky. V pořadí pro službu k dosažení čtení a zápis [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), vyžaduje tři repliky.

### <a name="service-and-index-limits-in-azure-search"></a>Omezení služby a indexu ve službě Azure Search
Existuje několik různých [cenové úrovně](https://azure.microsoft.com/pricing/details/search/) ve službě Azure Search úrovně obsahují různé [omezení a kvóty](search-limits-quotas-capacity.md). Některé z těchto omezení jsou na úrovni služby, některé jsou na úrovni index a některé jsou na úrovni oddílu.

|  | Basic | Standard1 | Standardní2 | Standardní3 | HD, High Density úrovni standardní3 |
| --- | --- | --- | --- | --- | --- |
| Maximální repliky na službu |3 |12 |12 |12 |12 |
| Maximální oddíly na službu |1 |12 |12 |12 |3 |
| Maximální vyhledávacích jednotek (repliky * oddíly) na službu |3 |36 |36 |36 |36 (maximální počet 3 oddíly) |
| Maximální velikost úložiště na službu |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximální velikost úložiště na oddíl |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximální počet indexů na službu |5 |50 |200 |200 |3000 (maximálně 1000 indexů na oddíl) |

#### <a name="s3-high-density"></a>S3 High Density.
V Azure Search S3 cenovou úroveň je možnost režimu vysoké hustoty (HD, High Density) určený speciálně pro scénářích s více tenanty. V mnoha případech je nutné podporovat velký počet menší tenantů v jedné službě dosáhnout výhod jednoduchosti a nákladové efektivity.

S3 HD, High Density umožňuje mnoho malé indexů, který se má zabalit pod správu nástroje service hledání jednoduchého podle obchodních možnost pro horizontální navýšení kapacity indexy používající možnost oddílů pro schopnost hostovat více indexů v jedné službě.

Služby S3 namítají, může mít mezi 1 a 200 indexy, které společně by mohl být hostitelem až 1,4 miliardy dokumentů. Na druhé straně S3 HD by umožnilo jednotlivých indexů pouze přejít až 1 milion dokumentů, ale dokáže zpracovat až 1000 indexů na oddíl (až 3 000 na službu) a celkový počet dokumentů počet 200 milionů na oddíl (až 600 milionů na službu).

## <a name="considerations-for-multitenant-applications"></a>Důležité informace pro víceklientské aplikace
Víceklientské aplikace, musí efektivní distribuci prostředků mezi různými klienty při zachování určité úrovně ochrany osobních údajů mezi různými tenanty. Při návrhu architektury pro takové aplikace existuje několik důležitých informací:

* *Izolaci klientů:* Vývojáři aplikací nutné podniknout potřebné kroky k zajištění, že se žádní tenanti Neautorizováno nebo nežádoucí přístup k datům z jiných tenantů. Nad rámec hlediska ochrany osobních údajů vyžadují strategie izolace tenanta efektivní správu sdílených prostředků a ochranu před "hlučným sousedům".
* *Náklady na cloud prostředků:* Stejně jako u jakékoli jiné aplikace, musí zůstat softwarová řešení náklady konkurenční jako součást víceklientské aplikace.
* *Snadné operací:* Při vývoji víceklientskou architekturu, dopad na operace a složité aplikace je důležitým aspektem. Azure Search má [99,9 % podle smlouvy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globální stopu:* Víceklientské aplikace může třeba efektivně obsluhovat klienty, které jsou distribuovány na celém světě.
* *Škálovatelnost:* Vývojáři aplikací je nutné vzít v úvahu, jak sjednotit mezi údržbu dostatečně nízkou úroveň zvýšení složitosti aplikace a navrhování škálování s počtem klientů a velikosti dat a úlohy klientů aplikace.

Azure Search nabízí několik hranice, které můžete použít k izolaci úloh a dat klientů.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelování víceklientské architektury s Azure Search
V případě víceklientské scénář vývojář aplikace využívá jednu nebo víc služeb search a rozdělit mezi, indexy a/nebo služby svým klientům. Azure Search má několik běžných vzorech při modelování víceklientské scénář:

1. *Index na tenanta:* Každý tenant má vlastní index v rámci služby vyhledávání, který je sdílen s jinými tenanty.
2. *Služba na tenanta:* Každý tenant má vlastní vyhrazená služba Azure Search nabízí nejvyšší úroveň oddělení dat a úloh.
3. *Kombinace obou:* Větší úložiště, jsou aktivní více tenantů jsou přiřazeny vyhrazené služby, zatímco menší tenanty přidělují jednotlivých indexů v rámci sdílené služby.

## <a name="1-index-per-tenant"></a>1. Index na tenanta
![Portrétu modelu index na tenanta](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

V modelu index na tenanta více tenantů zabírat jedinou službou Azure Search, kde každý tenant má vlastní index.

Tenanti dosáhnout izolace dat, protože všechny požadavky na vyhledávání a vydává operace na úrovni indexu ve službě Azure Search. V aplikační vrstvě je nutné sledování pro směrování provozu různých tenantů do správné indexy ale současná Správa prostředky na úrovni služby ve všech tenantech.

Klíčový atribut index na tenanta modelu je schopnost přidělit nadměrnému počtu procesů kapacitu služby search mezi klienty aplikace vývojář aplikace. Pokud klienti k nerovnoměrné distribuci úloh, ideální kombinaci tenantů mohou být distribuovány na indexy vyhledávání služby tak, aby vyhovovaly několik tenantů vysoce aktivní, náročný a současně současné obsluhování dlouhé konec menší aktivní klienti. Nutný kompromis je nemožnost modelu pro řešení situací, ve kterém je každý tenant souběžně vysoce aktivní.

Index na tenanta model poskytuje základ pro modelu nákladů proměnnou, kterého je zakoupili přímých celou službu Azure Search a následně vyplněné s klienty. To umožňuje nevyužité kapacity pro určené pro zkušební verze a bezplatné účty.

U aplikací s globální stopu nemusí být nejúčinnější modelu index na tenanta. Pokud aplikace tenantů se distribuují po celém světě, samostatná služba může být nutné pro každou oblast, která může duplicitní náklady na každé z nich.

Služba Azure Search umožňuje škálování jednotlivých indexů a celkový počet indexů na růst. Je-li odpovídající ceny je vybrán vrstvy, oddílů a replik lze přidat ke službě celé hledání při jednotlivé indexu v rámci služby příliš naroste z hlediska úložiště nebo provoz.

Pokud celkový počet indexů příliš naroste pro jedinou službou, jiné služby se musí zřídit tak, aby vyhovovaly novým tenantům. Pokud indexy musí přesunout mezi vyhledávací služby, protože se přidají nové služby, musí ručně zkopírovat z jeden index na druhý jako Azure Search se nepovoluje pro index přesunout data z indexu.

## <a name="2-service-per-tenant"></a>2. Služeb na jednoho tenanta
![Portrétu modelu služby na tenanta](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

V architektuře služby na tenanta každý tenant má vlastní vyhledávací službu.

V tomto modelu aplikace dosáhne maximální úroveň izolace pro své tenanty. Každá služba má vyhrazené úložiště a propustnosti pro zpracování požadavku hledání, jakož i různé klíče rozhraní API.

Pro aplikace, kde každý tenant má velké nároky nebo zatížení variabilitu tenanta z tenanta je model služby na tenanta efektivní volba jako prostředky se nesdílejí napříč různými tenanty úlohami.

Služba za tenanta model nabízí taky výhody modelu náklady na předvídatelné, oprava. Neexistuje žádné počáteční investici v rámci celé hledání služby, dokud nedojde k tenantovi tak, aby vyplnil, ale náklady za klienta je vyšší než model index na tenanta.

Model služby na tenanta je efektivní volba pro aplikace s globální stopu. S geograficky distribuovanými tenantů je snadné pro každého tenanta služby v příslušné oblasti.

Výzev škálování tento model vznikají při jednotlivých tenantů velký růst svých služeb. Služba Azure Search aktuálně nepodporuje upgrade cenovou úroveň služby vyhledávání, takže všechna data byste museli ručně zkopírovat do nové služby.

## <a name="3-mixing-both-models"></a>3. Kombinace obou modelů
Jiný způsob modelování víceklientskou architekturu je kombinace strategií pro index na tenanta a na tenanta služby.

Pomocí kombinace dva způsoby, největší klienty aplikace může zabírat vyhrazené služby při dlouhé tail míň aktivní, menší tenantů mohou zaujímat indexů ve sdílené službě. Tento model zajišťuje největší tenantů trvale vysoký výkon ze služby zároveň chránit menší klienty z jakékoli "hlučným sousedům".

Implementaci této strategie však využívá předvídání předpovídat, které klienti budou vyžadovat vyhrazená služba versus indexu ve sdílené službě. Zvýšení složitosti aplikace se zvyšuje s nutnost spravovat oba tyto modely víceklientské architektury.

## <a name="achieving-even-finer-granularity"></a>Dosažení i na nižší členitosti
Výše uvedené vzory návrhu pro modelování scénářích s více tenanty ve službě Azure Search předpokládají jednotného oboru, kde každý tenant je celý instance aplikace. Ale aplikace zvládne někdy menší celou řadu oborů.

Pokud služby za tenanta a index na tenanta modely nejsou dostatečně malé obory, je možné model indexu dosáhnout i na jemnější stupeň členitosti.

Pokud chcete, aby jeden index chovat jinak pro koncové body jiného klienta, můžete přidat pole do indexu, který označuje určitou hodnotu pro každého klienta je to možné. Pokaždé, když klient volá Azure Search k dotazování nebo změně indexu, určuje kód z klientské aplikace má hodnotu vhodnou pro toto pole pomocí služby Azure Search [filtr](https://msdn.microsoft.com/library/azure/dn798921.aspx) funkce v době zpracování dotazu.

Tato metoda je možné dosáhnout funkcionality samostatné uživatelské účty, úrovně samostatné oprávnění a dokonce i úplně jiných aplikací.

> [!NOTE]
> Pomocí postupu popsaného výše nakonfigurovat jeden index k poskytování více tenantů ovlivňuje relevance výsledků hledání. Skóre relevance vyhledávání se vypočítávají v indexu úrovni oboru, ne obor úrovni tenanta, takže za všechny tenanty dat je součástí skóre relevance základní statistiky, jako je frekvence termín.
> 
> 

## <a name="next-steps"></a>Další postup
Azure Search je atraktivní volba pro mnoho aplikací [Další informace o robustní možnosti služby](https://aka.ms/whatisazsearch). Při vyhodnocování různé způsoby návrhu pro víceklientské aplikace, vezměte v úvahu [různých cenových úrovní](https://azure.microsoft.com/pricing/details/search/) a funkcím [omezení služby](search-limits-quotas-capacity.md) nejlépe přizpůsobit podle aplikací Azure Search úlohy a architektury všech velikostí.

Mohou být jakékoliv otázky týkající se Azure Search a scénářích s více tenanty přesměrováni na azuresearch_contact@microsoft.com.


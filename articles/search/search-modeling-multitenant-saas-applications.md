---
title: Vícenásobná ochrana a izolace obsahu
titleSuffix: Azure Cognitive Search
description: Další informace o běžných návrhových vzorech pro víceklientské aplikace SaaS při používání Azure Cognitive Search.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d8e453336005f3389f67e9571fac438bfc340c1b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549014"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Návrhové vzory pro víceklientské aplikace SaaS a Azure Cognitive Search
Víceklientská aplikace je aplikace, která poskytuje stejné služby a možnosti libovolnému počtu klientů, kteří nemohou zobrazit nebo sdílet data jiného klienta. Tento dokument popisuje strategie izolace tenanta pro víceklientské aplikace vytvořené pomocí Azure Cognitive Search.

## <a name="azure-cognitive-search-concepts"></a>Koncepty azure kognitivního vyhledávání
Jako řešení vyhledávání jako služba azure cognitive search umožňuje vývojářům přidávat bohaté možnosti vyhledávání do aplikací bez správy jakékoli infrastruktury nebo se stává odborníkem na načítání informací. Data se nahrají do služby a poté se uloží do cloudu. Pomocí jednoduchých požadavků na rozhraní Azure Cognitive Search API, data pak můžete upravit a prohledat. Přehled služby naleznete v [tomto článku](https://aka.ms/whatisazsearch). Před diskusí návrhové vzory, je důležité pochopit některé koncepty v Azure Cognitive Search.

### <a name="search-services-indexes-fields-and-documents"></a>Vyhledávací služby, indexy, pole a dokumenty
Při použití Azure Cognitive Search se člověk přihlásí k *odběru vyhledávací služby*. Když se data nahrávají do Azure Cognitive Search, jsou uložena v *indexu* v rámci vyhledávací služby. Může existovat počet indexů v rámci jedné služby. Chcete-li použít známé koncepty databází, vyhledávací služby lze přirovnat k databázi, zatímco indexy v rámci služby lze přirovnat k tabulkám v databázi.

Každý index v rámci vyhledávací služby má své vlastní schéma, které je definováno řadou přizpůsobitelných *polí*. Data se přidávají do indexu Azure Cognitive Search ve formě jednotlivých *dokumentů*. Každý dokument musí být odeslán do určitého indexu a musí odpovídat schématu indexu. Při vyhledávání dat pomocí Azure Cognitive Search, fulltextové vyhledávací dotazy jsou vydávány proti určitému indexu.  Chcete-li porovnat tyto koncepty s koncepty databáze, lze pole přirovnat ke sloupcům v tabulce a dokumenty lze přirovnat k řádkům.

### <a name="scalability"></a>Škálovatelnost
Každá služba Azure Cognitive Search v [cenové úrovni](https://azure.microsoft.com/pricing/details/search/) Standard může škálovat ve dvou dimenzích: úložiště a dostupnost.

* *Oddíly* mohou být přidány ke zvýšení úložiště vyhledávací služby.
* *Repliky* lze přidat do služby pro zvýšení propustnost požadavků, které může vyhledávací služba zpracovat.

Přidání a odebrání oddílů a replik na umožní kapacitu vyhledávací služby růst s množstvím dat a provoz aplikace vyžaduje. Aby vyhledávací služba dosáhla čtení [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), vyžaduje dvě repliky. Aby služba mohla dosáhnout [sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/)pro čtení a zápis , vyžaduje tři repliky.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Limity služeb a indexů v Azure Cognitive Search
Existuje několik různých [cenových úrovní](https://azure.microsoft.com/pricing/details/search/) v Azure Cognitive Search, každá z vrstev má různá [omezení a kvóty](search-limits-quotas-capacity.md). Některé z těchto omezení jsou na úrovni služby, některé jsou na úrovni indexu a některé jsou na úrovni oddílu.

|  | Základní | Standard1 | Standardní2 | Standardní3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximální počet replik na službu |3 |12 |12 |12 |12 |
| Maximální počet oddílů na službu |1 |12 |12 |12 |3 |
| Maximální počet vyhledávacích jednotek (repliky*oddíly) na službu |3 |36 |36 |36 |36 (maximálně 3 oddíly) |
| Maximální úložiště na službu |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximální úložiště na oddíl |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximální počet indexů na službu |5 |50 |200 |200 |3000 (maximálně 1000 indexů/oddílů) |

#### <a name="s3-high-density"></a>S3 vysoká hustota"
V cenové úrovni Azure Cognitive Search S3 je možnost pro režim vysoké hustoty (HD) navržené speciálně pro víceklientské scénáře. V mnoha případech je nutné podporovat velký počet menších nájemců v rámci jedné služby, aby bylo dosaženo výhod jednoduchosti a nákladové efektivity.

S3 HD umožňuje mnoho malých indexů, které mají být zabaleny pod správou jedné vyhledávací služby obchodováním schopnost škálovat indexy pomocí oddílů pro schopnost hostit více indexů v jedné službě.

Služba S3 je určena k hostování pevného počtu indexů (maximálně 200) a umožňuje každému indexu škálovat velikost vodorovně, protože do služby jsou přidány nové oddíly. Přidání oddílů do služeb S3 HD zvyšuje maximální počet indexů, které může služba hostovat. Ideální maximální velikost pro individuální index S3HD je kolem 50 - 80 GB, ačkoli neexistuje žádný pevný limit velikosti pro každý index uložený systémem.

## <a name="considerations-for-multitenant-applications"></a>Důležité informace pro víceklientské aplikace
Víceklientské aplikace musí efektivně distribuovat prostředky mezi klienty při zachování určité úrovně ochrany osobních údajů mezi různými tenanty. Existuje několik aspektů při navrhování architektury pro takovou aplikaci:

* *Izolace klienta:* Vývojáři aplikací musí přijmout vhodná opatření, aby zajistili, že žádní klienti nemají neoprávněný nebo nežádoucí přístup k datům jiných klientů. Kromě hlediska ochrany osobních údajů vyžadují strategie izolace tenantů efektivní správu sdílených prostředků a ochranu před hlučných sousedů.
* *Náklady na prostředky cloudu:* Stejně jako u jiných aplikací musí softwarová řešení zůstat nákladově konkurenceschopná jako součást víceklientské aplikace.
* *Snadné operace:* Při vývoji víceklientské architektury je důležitým aspektem dopad na operace a složitost aplikace. Azure Cognitive Search má [99,9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globální stopa:* Víceklientské aplikace může být nutné efektivně sloužit klientům, které jsou distribuovány po celém světě.
* *Škálovatelnost:* Vývojáři aplikací musí zvážit, jak se odsouhlasit mezi udržování dostatečně nízkou úroveň složitosti aplikace a navrhování aplikace škálovat s počtem klientů a velikost dat a zatížení klientů.

Azure Cognitive Search nabízí několik hranic, které lze použít k izolaci dat a úloh klientů.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modelování multitenancy pomocí Azure Cognitive Search
V případě víceklientského scénáře vývojář aplikace spotřebovává jednu nebo více vyhledávacích služeb a rozdělí své klienty mezi služby, indexy nebo obojí. Azure Cognitive Search má několik běžných vzorů při modelování víceklientského scénáře:

1. *Index na klienta:* Každý klient má svůj vlastní index v rámci vyhledávací služby, která je sdílena s ostatními klienty.
2. *Služba na klienta:* Každý klient má vlastní vyhrazenou službu Azure Cognitive Search, která nabízí nejvyšší úroveň oddělení dat a úloh.
3. *Mix obou:* Větší, aktivnější klienti jsou přiřazeny vyhrazené služby, zatímco menší klienti jsou přiřazeny jednotlivé indexy v rámci sdílených služeb.

## <a name="1-index-per-tenant"></a>1. Index na nájemce
![Zobrazení modelu indexu na klienta](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

V modelu indexu na tenanta více klientů zabírají jednu službu Azure Cognitive Search, kde každý klient má svůj vlastní index.

Klienti dosáhnout izolace dat, protože všechny požadavky na vyhledávání a operace dokumentu jsou vydávány na úrovni indexu v Azure Cognitive Search. Ve vrstvě aplikace je potřeba povědomí nasměrovat provoz různých klientů na správné indexy a zároveň spravovat prostředky na úrovni služeb napříč všemi tenanty.

Klíčovým atributem modelu indexu na tenanta je možnost pro vývojáře aplikace přeplnit kapacitu vyhledávací služby mezi klienty aplikace. Pokud mají klienti nerovnoměrné rozložení úlohy, optimální kombinace klientů může být distribuována mezi indexy vyhledávací služby tak, aby vyhovovala řadě vysoce aktivních klientů náročných na prostředky a současně obsluhovala dlouhý ocas méně aktivních klientů. Kompromis je neschopnost modelu zpracovat situace, kdy je každý klient současně vysoce aktivní.

Model indexu na tenanta poskytuje základ pro model s proměnnými náklady, kde se předem nakupuje celá služba Azure Cognitive Search a následně je naplněna tenanty. To umožňuje, aby nevyužitá kapacita byla určena pro zkušební verze a bezplatné účty.

Pro aplikace s globální nároky index na tenanta nemusí být nejúčinnější. Pokud jsou klienti aplikace distribuováni po celém světě, může být pro každou oblast nezbytná samostatná služba, která může duplikovat náklady v každé z nich.

Azure Cognitive Search umožňuje škálování jednotlivých indexů a celkový počet indexů růst. Pokud je vybrána vhodná cenová úroveň, oddíly a repliky mohou být přidány do celé vyhledávací služby, když se z hlediska úložiště nebo provozu zvětší individuální index v rámci služby.

Pokud celkový počet indexů roste příliš velký pro jednu službu, musí být zřízena jiná služba pro nové klienty. Pokud indexy mají být přesunuty mezi vyhledávací služby jako nové služby jsou přidány, data z indexu musí být ručně zkopírovány z jednoho indexu do druhého jako Azure Cognitive Search neumožňuje index, který má být přesunut.

## <a name="2-service-per-tenant"></a>2. Služba na nájemce
![Zobrazení modelu služby na tenanta](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

V architektuře služby na tenanta má každý klient vlastní vyhledávací službu.

V tomto modelu aplikace dosahuje maximální úroveň izolace pro své klienty. Každá služba má vyhrazené úložiště a propustnost pro zpracování požadavku na vyhledávání, stejně jako samostatné klíče rozhraní API.

Pro aplikace, kde každý klient má velké nároky nebo zatížení má malou variabilitu od tenanta k tenantovi, model služby na tenanta je efektivní volbou, protože prostředky nejsou sdíleny mezi různými klienty.

Služba na modelu klienta také nabízí výhodu předvídatelné, pevné náklady modelu. Neexistuje žádná počáteční investice do celé vyhledávací služby, dokud není tenant vyplnit, ale náklady na klienta je vyšší než index na tenanta modelu.

Model služby na tenanta je efektivní volbou pro aplikace s globální stopou. U geograficky distribuovaných nájemců je snadné mít službu každého klienta v příslušné oblasti.

Problémy při škálování tohoto vzoru vznikají, když jednotliví klienti přerůst jejich služby. Azure Cognitive Search aktuálně nepodporuje upgrade cenové úrovně vyhledávací služby, takže všechna data by bylo nutné ručně zkopírovat do nové služby.

## <a name="3-mixing-both-models"></a>3. Míchání obou modelů
Dalším vzorem pro modelování víceklientské je míchání index-per-tenant a service-per-tenant strategie.

Smícháním dva vzory, největší klienti aplikace můžete obsadit vyhrazené služby, zatímco dlouhý ocas méně aktivní, menší tenantové mohou obsadit indexy ve sdílené službě. Tento model zajišťuje, že největší klienti mají trvale vysoký výkon ze služby a zároveň pomáhá chránit menší klienty před hlučných sousedů.

Implementace této strategie však závisí na prognóze, kteří klienti budou vyžadovat vyhrazenou službu versus index ve sdílené službě. Složitost aplikace se zvyšuje s potřebou spravovat oba tyto modely vícetenancy.

## <a name="achieving-even-finer-granularity"></a>Dosažení ještě jemnější rozlišovací schopnosti
Výše uvedené návrhové vzory pro modelování víceklientských scénářů v Azure Cognitive Search předpokládají jednotný obor, kde každý klient je celá instance aplikace. Aplikace však někdy může zpracovat mnoho menších oborů.

Pokud modely služby na tenanta a index na tenanta nejsou dostatečně malé obory, je možné modelovat index k dosažení ještě jemnější stupeň rozlišovací schopnost.

Chcete-li mít jeden index chovat odlišně pro různé koncové body klienta, pole lze přidat do indexu, který určuje určitou hodnotu pro každého možného klienta. Pokaždé, když klient volá Azure Cognitive Search k dotazu nebo upravit index, kód z klientské aplikace určuje příslušnou hodnotu pro toto pole pomocí azure cognitive search [filtr](https://msdn.microsoft.com/library/azure/dn798921.aspx) schopnosti v době dotazu.

Tuto metodu lze použít k dosažení funkce samostatných uživatelských účtů, samostatné úrovně oprávnění a dokonce i zcela samostatné aplikace.

> [!NOTE]
> Použití výše popsaného přístupu ke konfiguraci jednoho indexu pro obsluhu více klientů má vliv na relevanci výsledků hledání. Skóre relevance vyhledávání se vypočítává v oboru na úrovni indexu, nikoli v oboru na úrovni tenanta, takže všechna data klientů jsou začleněna do podkladových statistik skóre relevance, jako je frekvence termínů.
> 
> 

## <a name="next-steps"></a>Další kroky
Azure Cognitive Search je přesvědčivou volbou pro mnoho aplikací. Při vyhodnocování různých návrhových vzorů pro víceklientské aplikace zvažte [různé cenové úrovně](https://azure.microsoft.com/pricing/details/search/) a příslušná omezení [služeb,](search-limits-quotas-capacity.md) abyste co nejlépe přizpůsobili Azure Cognitive Search tak, aby vyhovovalo úlohám aplikací a architekturám všech velikostí.

Jakékoli dotazy týkající se Azure Cognitive Search azuresearch_contact@microsoft.coma víceklientské scénáře lze přesměrovat na .


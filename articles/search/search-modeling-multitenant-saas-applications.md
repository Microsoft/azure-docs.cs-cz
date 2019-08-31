---
title: Modelování víceklientské architektury pro izolaci obsahu v jedné službě – Azure Search
description: Seznamte se s běžnými návrhovými vzory pro víceklientské aplikace SaaS při použití Azure Search.
manager: nitinme
author: LiamCavanagh
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.openlocfilehash: 8b7538da41241f005298537c2969e5fce72b3c38
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182229"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Vzory návrhu pro víceklientské aplikace SaaS a Azure Search
Víceklientské aplikace nabízí stejné služby a možnosti pro libovolný počet klientů, kteří nemůžou zobrazit nebo sdílet data žádného jiného tenanta. Tento dokument popisuje strategie izolace klientů pro víceklientské aplikace vytvořené pomocí Azure Search.

## <a name="azure-search-concepts"></a>Azure Search koncepty
Jako řešení vyhledávání jako služby umožňuje vývojářům přidávat do aplikací bohatě vyhledávaná prostředí Azure Search, aniž by museli spravovat žádnou infrastrukturu nebo se stát odborníkem na načítání informací. Data se odešlou do služby a pak se uloží do cloudu. Pomocí jednoduchých požadavků na rozhraní Azure Search API se dají data upravit a prohledat. Přehled služby najdete v [tomto článku](https://aka.ms/whatisazsearch). Než začnete diskutovat na vzory návrhu, je důležité pochopit některé koncepty v Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Vyhledávání služeb, indexů, polí a dokumentů
Při použití Azure Search se jeden přihlašuje k odběru *vyhledávací služby*. Když se data nahrají do Azure Search, uloží se do *indexu* v rámci vyhledávací služby. V rámci jedné služby může být několik indexů. Aby bylo možné používat známé koncepty databází, může být vyhledávací služba likened k databázi, zatímco indexy v rámci služby mohou být likened do tabulek v rámci databáze.

Každý index v rámci vyhledávací služby má vlastní schéma, které je definováno řadou přizpůsobitelných *polí*. Data jsou přidána do indexu Azure Search ve formě jednotlivých *dokumentů*. Každý dokument musí být nahrán do konkrétního indexu a musí odpovídat schématu indexu. Při vyhledávání dat pomocí Azure Search jsou dotazy fulltextového vyhledávání vydány pro konkrétní index.  Aby bylo možné porovnat tyto koncepty s databázemi, mohou být pole likened do sloupců v tabulce a dokumenty mohou být likened do řádků.

### <a name="scalability"></a>Škálovatelnost
Všechny Azure Search služby v [cenové úrovni](https://azure.microsoft.com/pricing/details/search/) Standard se můžou škálovat ve dvou dimenzích: úložiště a dostupnost.

* *Oddíly* lze přidat ke zvýšení úložiště vyhledávací služby.
* Do služby lze přidat repliky a zvýšit tak propustnost požadavků, které může služba vyhledávání zpracovat.

Přidávání a odebírání oddílů a replik v nástroji umožní, aby služba vyhledávání vzrostla s množstvím dat a provozem, který aplikace požaduje. Aby služba vyhledávání dosáhla [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)pro čtení, vyžaduje dvě repliky. Aby služba dosáhla [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)pro čtení i zápis, vyžaduje tři repliky.

### <a name="service-and-index-limits-in-azure-search"></a>Omezení služby a indexu v Azure Search
V Azure Search existuje několik různých [cenových úrovní](https://azure.microsoft.com/pricing/details/search/) , každá z vrstev má různá [omezení a kvóty](search-limits-quotas-capacity.md). Některá z těchto omezení jsou na úrovni služby, některé jsou na úrovni indexu a některé jsou na úrovni oddílu.

|  | Basic | Standard1 | Standardní2 | Standardní3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximální počet replik na službu |3 |12 |12 |12 |12 |
| Maximální počet oddílů na službu |1 |12 |12 |12 |3 |
| Maximální počet jednotek hledání (repliky * oddíly) na službu |3 |36 |36 |36 |36 (max. 3 oddíly) |
| Maximální velikost úložiště na službu |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximální velikost úložiště na oddíl |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximální počet indexů na službu |5 |50 |200 |200 |3000 (max. 1000 indexů/oddílů) |

#### <a name="s3-high-density"></a>Vysoká hustota S3
V cenové úrovni S3 Azure Search je k dispozici možnost pro režim vysoké hustoty (HD) navržený speciálně pro víceklientské scénáře. V mnoha případech je potřeba zajistit podporu velkého počtu menších klientů v rámci jedné služby, abyste dosáhli výhod jednoduchosti a nákladové efektivity.

S3 HD umožňuje zabalit celou řadu malých indexů pod správu jedné vyhledávací služby díky možnosti horizontálního navýšení kapacity indexů pomocí oddílů, aby bylo možné hostovat víc indexů v jediné službě.

Služba S3 by mohla mít mezi 1 a 200 indexy, které dohromady můžou hostovat až 1 400 000 000 dokumentů. S3 HD na druhé straně umožní jednotlivým indexům přejít pouze do 1 000 000 dokumentů, ale může zpracovávat až 1000 indexů na oddíl (až 3000 na službu) s celkovým počtem výskytů 200 000 000 na oddíl (až do 600 000 000 podle služby).

## <a name="considerations-for-multitenant-applications"></a>Předpoklady pro víceklientské aplikace
Víceklientské aplikace musí efektivně distribuovat prostředky mezi klienty a přitom zachovat určitou úroveň ochrany osobních údajů mezi různými klienty. Při navrhování architektury této aplikace je potřeba mít několik důležitých informací:

* *Izolace tenanta:* Vývojáři aplikací musí přijmout vhodná opatření, aby se zajistilo, že žádní klienti nemají neoprávněný nebo nežádoucí přístup k datům jiných tenantů. Kromě perspektivy ochrany osobních údajů vyžadují strategie izolace klientů efektivní správu sdílených prostředků a ochranu od sousedních sousedních směrovačů.
* *Náklady na prostředek v cloudu:* Stejně jako u jakékoli jiné aplikace musí být softwarová řešení nadále nákladná konkurenceschopná jako součást víceklientské aplikace.
* *Snadné operace:* Při vývoji víceklientské architektury je důležité zvážit dopad na operace a složitost aplikace. Azure Search má [smlouvu SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globální nároky:* Víceklientské aplikace můžou potřebovat efektivně obsluhovat klienty, kteří jsou distribuováni po celém světě.
* *Škálovatelnost* Vývojáři aplikací musí vzít v úvahu, jak jsou sladěné mezi udržováním dostatečně nízké úrovně složitosti aplikace a návrhem aplikace pro škálování pomocí počtu klientů a velikosti dat a zatížení tenanta.

Azure Search nabízí několik hranic, které se dají použít k izolaci dat a zatížení klientů.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelování více tenantů s Azure Search
V případě víceklientské scénáře používá vývojář aplikace jednu nebo více vyhledávacích služeb a rozděluje klienty mezi služby, indexy nebo obojí. Azure Search má několik běžných vzorů při modelování scénáře s více klienty:

1. *Index na tenanta:* Každý tenant má svůj vlastní index v rámci vyhledávací služby, která je sdílená s ostatními klienty.
2. *Služba na tenanta:* Každý tenant má svou vlastní vyhrazenou Azure Search službu, která nabízí nejvyšší úroveň dat a oddělení úloh.
3. *Kombinace obou:* Větším, více aktivním klientům se přiřazují vyhrazené služby, zatímco v rámci sdílených služeb jsou v menších klientech přiřazeny jednotlivé indexy.

## <a name="1-index-per-tenant"></a>1. Index na tenanta
![Portrayal modelu indexu na tenanta](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

V modelu indexu pro jednotlivé klienty používá více tenantů jednu Azure Search službu, kde má každý tenant svůj vlastní index.

Klienti dosáhnou izolace dat, protože všechny žádosti o vyhledávání a operace dokumentů jsou vydávány na úrovni indexu v Azure Search. V aplikační vrstvě je potřeba povědomí o směrování různých přenosů klientů na správné indexy a zároveň spravovat prostředky na úrovni služby napříč všemi klienty.

Klíčový atribut modelu indexu na tenanta je schopnost, aby vývojář aplikace mohl přerušit kapacitu vyhledávací služby mezi klienty aplikace. Pokud mají klienti nestejnoměrnou distribuci úloh, optimální kombinaci klientů je možné distribuovat v rámci indexů vyhledávací služby, aby se vešlo na řadu vysoce aktivních klientů náročných na prostředky a zároveň dlouhodobě zachovává méně. aktivní klienti. Obchod je neschopnost modelu zvládnout situace, kdy je každý tenant souběžně vysoce aktivní.

Model s indexem na tenanta poskytuje základ pro variabilní nákladový model, kde je celá služba Azure Search zakoupená předem a následně vyplněna klienty. To umožňuje určit nevyužitou kapacitu pro zkušební a bezplatné účty.

Pro aplikace s globálním nárokem nemusí být model indexu na tenanta nejúčinnější. Pokud jsou klienti aplikace distribuováni po celém světě, může být pro každou oblast nezbytná samostatná služba, která může v každé z nich provádět duplicitní náklady.

Azure Search umožňuje škálování jednotlivých indexů a celkový počet indexů, které se mají zvětšit. Pokud se vybere příslušná cenová úroveň, oddíly a repliky se dají přidat do celé vyhledávací služby, když se jednotlivý index v rámci služby rozroste moc velký vzhledem k úložišti nebo provozu.

Pokud celkový počet indexů roste pro jednu službu příliš velký, je nutné zřídit jinou službu, která bude vyhovovat novým klientům. Pokud je třeba přesunout indexy mezi vyhledávacími službami, když jsou přidány nové služby, data z indexu musí být ručně zkopírována z jednoho indexu do druhé, protože Azure Search nepovoluje přesunutí indexu.

## <a name="2-service-per-tenant"></a>2. Služba na tenanta
![Portrayal modelu služby – na tenanta](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

V architektuře pro jednotlivé klienty má každý tenant vlastní vyhledávací službu.

V tomto modelu aplikace dosahuje maximální úrovně izolace pro své klienty. Každá služba má vyhrazené úložiště a propustnost pro zpracování žádosti o vyhledávání a také samostatné klíče rozhraní API.

Pro aplikace, ve kterých má každý tenant velká místa, nebo má příliš proměnlivost od klienta až po tenanta, je model služby-Klient-tenant efektivní volbou, protože prostředky nejsou sdíleny napříč různými úlohami tenantů.

Model služby na jeden tenant také nabízí předvídatelný fixní nákladový model. V celé službě vyhledávání neexistuje žádná investice, dokud není tenant, který ji vyplní, ale náklady na tenanta jsou vyšší než model indexu na tenanta.

Model služby pro klienty je efektivní volbou pro aplikace s globálním nároky. S geograficky distribuovanými klienty je snadné mít v příslušné oblasti služby každého tenanta.

Problémy při škálování tohoto modelu vyplývají z toho, kdy jednotliví klienti rozšiřují svoji službu. Azure Search aktuálně nepodporuje upgrade cenové úrovně služby vyhledávání, takže všechna data by musela být ručně zkopírována do nové služby.

## <a name="3-mixing-both-models"></a>3. Kombinování obou modelů
Dalším modelem víceklientské architektury pro více tenantů je kombinování strategií index-na tenanta i služby na straně klienta.

Díky smíchání dvou vzorů můžou největší klienti aplikace zabírat vyhrazené služby, i když má méně aktivního a menšího počtu klientů, můžou ve sdílené službě zabírat indexy. Tento model zajišťuje, že největší klienti mají konzistentně vysoký výkon služby a současně pomáhají chránit menší klienty ze všech sousedních směrovačů s vysokou dostupností.

Implementace této strategie ale spoléhá na předvídání, které klienty budou vyžadovat vyhrazenou službu oproti indexu ve sdílené službě. Složitost aplikace se zvyšuje s potřebou spravovat oba tyto modely víceklientské architektury.

## <a name="achieving-even-finer-granularity"></a>Dosažení ještě jemnějších členitosti
Výše uvedené vzory návrhu pro modelování scénářů s více klienty v Azure Search předpokládají jednotný obor, ve kterém je každý tenant celá instance aplikace. Aplikace ale můžou někdy zvládnout mnoho menších oborů.

Pokud modely služeb pro klienty a klienti nejsou dostatečně malé, je možné modelovat index, abyste dosáhli ještě jemnější úrovně členitosti.

Chcete-li, aby se jeden index choval odlišně pro různé koncové body klienta, lze do indexu přidat pole, které určuje určitou hodnotu pro každého možného klienta. Pokaždé, když klient volá Azure Search pro dotazování nebo změnu indexu, kód z klientské aplikace určuje odpovídající hodnotu pro toto pole pomocí funkce [filtrování](https://msdn.microsoft.com/library/azure/dn798921.aspx) Azure Search v době dotazu.

Tato metoda se dá použít k dosažení funkcí samostatných uživatelských účtů, samostatných úrovní oprávnění a dokonce i zcela samostatných aplikací.

> [!NOTE]
> Pomocí výše popsaného přístupu můžete nakonfigurovat jeden index, který bude obsluhovat více tenantů, ovlivnit závažnost výsledků hledání. Výsledky hledání podle relevance jsou vypočítány v oboru na úrovni indexu, nikoli v oboru na úrovni tenanta, takže data všech tenantů jsou zahrnuta v základních statistikách podle relevance skóre, jako je například frekvence.
> 
> 

## <a name="next-steps"></a>Další postup
Azure Search je zajímavou volbou pro mnoho aplikací, [Přečtěte si další informace o robustních schopnostech služby](https://aka.ms/whatisazsearch). Při vyhodnocování různých vzorů návrhu pro víceklientské aplikace Vezměte v úvahu [různé cenové úrovně](https://azure.microsoft.com/pricing/details/search/) a příslušná [omezení služby](search-limits-quotas-capacity.md) , aby se co nejlépe přizpůsobil Azure Search pro úlohy a architektury všech velikostí aplikací.

Jakékoli otázky týkající se Azure Search a víceklientské scénáře lze směrovat na azuresearch_contact@microsoft.com.


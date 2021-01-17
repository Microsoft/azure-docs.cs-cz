---
title: Odhadněte náklady
titleSuffix: Azure Cognitive Search
description: Přečtěte si o fakturovaných událostech, cenovém modelu a tipech pro správu nákladů na provozování služby Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539554"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Jak odhadnout a spravovat náklady na službu Azure Kognitivní hledání

V tomto článku se dozvíte o cenovém modelu, fakturovaných událostech a tipy pro správu nákladů na provozování služby Azure Kognitivní hledání.

## <a name="pricing-model"></a>Cenový model

Architektura škálovatelnosti v Azure Kognitivní hledání je založená na flexibilních kombinacích replik a oddílů, takže můžete měnit kapacitu v závislosti na tom, jestli potřebujete další dotazy nebo indexování, a platíte jenom za to, co potřebujete.

Množství prostředků používaných vaší vyhledávací službou vynásobené fakturační sazbou stanovenou vrstvou služby určuje náklady na provoz služby. Náklady a kapacita jsou pevně vázané. Při odhadování nákladů je potřeba pochopit kapacitu potřebnou ke spouštění vašich úloh indexování a dotazování, což vám dává nejlepší představu o plánovaných nákladech.

Pro účely fakturace existují dva jednoduché vzorce, které je třeba znát:

| Vzorec | Popis |
|---------|-------------|
| **R × P = SU** | Počet použitých replik vynásobený počtem použitých oddílů se rovná množství *jednotek hledání* (SU) používaných službou. SU je jednotka prostředku, která může být buď oddílem, nebo replikou. |
| **SU * fakturační sazba = měsíční výdaje** | Počet služeb SUs vynásobený fakturační sazbou úrovně, ve které jste službu zřídili, je primárním rozhodujícím rozhodujícím z vašich celkových měsíčních faktur. Některé funkce nebo úlohy mají závislosti na dalších službách Azure, což může zvýšit náklady na vaše řešení na úrovni předplatného. V části Fakturovatelné události najdete funkce, které se dají přidat do faktury. |

Každá služba začíná jednou SU (jedna replika vynásobená jedním oddílem) jako minimální. Maximum pro jakoukoli službu je 36 SUs. Toto maximum je možné dosáhnout několika způsoby: 6 oddílů × 6 replik nebo 3 oddíly x 12 replik, například. Je běžné použít méně než celkovou kapacitu (například 3-replika, 3-partition Service se účtuje jako 9 SUs). Platné kombinace najdete v grafu [kombinací oddílů a repliky](search-capacity-planning.md#chart) .

Fakturační sazba je každou hodinu. Každá úroveň má postupně vyšší míru. Vyšší úrovně se dodávají s většími a rychlými oddíly a to přispívá k celkové hodinové sazbě za tuto úroveň. Sazby za jednotlivé úrovně můžete zobrazit na stránce s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) .

Většina zákazníků přinese jenom část celkové kapacity online, která uchovává zbývající rezervu. Pro účely fakturace určuje počet oddílů a replik, které přivedete do režimu online, vypočítané vzorcem SU za každou hodinu. 

## <a name="billable-events"></a>Fakturovatelné události

K řešení postavenému na Azure Kognitivní hledání může doplatit tyto náklady následujícími způsoby:

+ [Náklady](#service-costs) na samotnou službu, která běží nepřetržitě, při minimální konfiguraci (jeden oddíl a replika), podle základní sazby. Můžete si to představit jako pevné náklady na spuštění služby.

+ Přidejte kapacitu (repliky nebo oddíly), kde se náklady zvyšují po přírůstcích s fakturovatelnou sazbou. Pokud je vysoká dostupnost obchodním požadavkem, budete potřebovat 3 repliky.

+ Poplatky za šířku pásma (odchozí přenos dat)

+ Služby doplňku vyžadované pro konkrétní funkce nebo funkce:

  + Rozšíření AI (vyžaduje [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + znalostní báze Knowledge Store (vyžaduje [Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + přírůstkové obohacení (vyžaduje [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), platí pro rozšíření AI)
  + klíče spravované zákazníkem a dvojité šifrování (vyžaduje [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + privátní koncové body pro model bez přístupu k Internetu (vyžaduje [privátní odkaz Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Náklady na služby

Na rozdíl od virtuálních počítačů nebo jiných prostředků, které můžou být pozastavené, aby se předešlo poplatkům, služba Azure Kognitivní hledání je vždycky dostupná na hardwaru vyhrazeném pro výhradní použití. Vytvoření služby je fakturovatelná událost, která se spustí při vytvoření služby a končí při odstranění služby. 

Minimální poplatek je první jednotka vyhledávání (jedna replika x jeden oddíl) s fakturovatelnou sazbou. Toto minimum je pevně nastavené pro dobu života služby, protože služba nemůže běžet bez jakýchkoli těchto konfigurací. 

Kromě minima můžete přidat repliky a oddíly nezávisle na sobě. Přírůstkové zvýšení kapacity prostřednictvím replik a oddílů zvýší vaše vyúčtování na základě následujícího vzorce: **(repliky × oddíly × fakturace)**, kde sazba, která se vám bude účtovat, bude záviset na cenové úrovni, kterou vyberete.

Při odhadování nákladů na řešení vyhledávání Pamatujte na to, že ceny a kapacita nejsou lineární (dvojnásobná kapacita je větší než dvojnásobek nákladů). Příklad toho, jak vzorec funguje, najdete v tématu [jak přidělit repliky a oddíly](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Poplatky za šířku pásma

Pokud je zdroj dat Azure v jiné oblasti než Azure Kognitivní hledání, může použití [indexerů](search-indexer-overview.md) ovlivnit fakturaci. V tomto scénáři může být pro přesun odchozích dat ze zdroje dat Azure do Azure Kognitivní hledání náklady. Podrobnosti najdete na stránkách s cenami v dané datové platformě Azure.

Pokud vytvoříte službu Azure Kognitivní hledání ve stejné oblasti, ve které jsou vaše data, můžete poplatky za odchozí přenosy dat zcela eliminovat. Tady jsou některé informace na [stránce s cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Příchozí data: Microsoft neúčtuje žádná příchozí data do žádné služby v Azure. 

+ Odchozí data: odchozí data odkazují na výsledky dotazu. Kognitivní hledání za odchozí data neúčtuje, ale odchozí poplatky z Azure jsou možné, pokud jsou služby v různých oblastech.

  Tyto poplatky nejsou ve skutečnosti součástí faktury za Azure Kognitivní hledání. Tady jsou zmíněné informace, protože pokud odesíláte výsledky do jiných oblastí nebo mimo Azure, můžou se tyto náklady projevit v celkové faktuře.

### <a name="ai-enrichment-with-cognitive-services"></a>Obohacení AI pomocí Cognitive Services

V případě [obohacení AI](cognitive-search-concept-intro.md)byste měli naplánovat [připojení Fakturovatelné Cognitive Services prostředku Azure](cognitive-search-attach-cognitive-services.md)ve stejné oblasti jako Azure kognitivní hledání na cenové úrovni S0 pro zpracování průběžných plateb. K připojení Cognitive Services nejsou žádné pevné náklady. Platíte jenom za zpracování, které potřebujete.

| Operace | Dopad fakturace |
|-----------|----------------|
| Trhliny dokumentů, extrakce textu | Free |
| Trhliny dokumentů, extrakce obrázků | Účtuje se podle počtu imagí extrahovaných z vašich dokumentů. V [konfiguraci indexeru](/rest/api/searchservice/create-indexer#indexer-parameters)je **imageAction** parametr, který aktivuje extrakci imagí. Pokud je **imageAction** nastavené na None (výchozí nastavení), nebudete se vám za extrakci imagí účtovat. Sazba za extrakci imagí je popsána na stránce s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) pro Azure kognitivní hledání.|
| [Předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md) | Účtuje se se stejnou sazbou, jako kdyby jste provedli úlohu přímo pomocí Cognitive Services. |
| Vlastní dovednosti | Vlastní dovednost je funkce, kterou zadáte. Náklady na používání vlastní dovednosti závisí výhradně na tom, jestli vlastní kód volá jiné měřené služby. |

Funkce [přírůstkového navýšení (verze Preview)](cognitive-search-incremental-indexing-conceptual.md) vám umožní poskytnout mezipaměť, která umožňuje efektivnější použití indexeru při spouštění pouze těch dovedností, které jsou nezbytné v případě, že v budoucnu upravíte dovednosti a ušetříte čas a peníze.

## <a name="tips-for-managing-costs"></a>Tipy pro správu nákladů

Následující pokyny vám pomůžou snížit náklady nebo efektivněji spravovat náklady:

+ Vytvořte všechny prostředky ve stejné oblasti nebo v několika oblastech, abyste minimalizovali nebo vyloučili poplatky za šířku pásma.

+ Konsolidujte všechny služby do jedné skupiny prostředků, jako je například Azure Kognitivní hledání, Cognitive Services a jakékoli další služby Azure používané ve vašem řešení. V Azure Portal Najděte skupinu prostředků a pomocí **cost Managementch** příkazů si projděte informace o skutečné a předpokládané útratě.

+ Zvažte webovou aplikaci Azure pro front-end aplikaci, aby žádosti a odpovědi zůstaly v rámci hranice datového centra.

+ Nahorizontální navýšení operací náročných na prostředky, jako je indexování, a následná změna nastavení pro běžné úlohy dotazů. Začněte s minimální konfigurací pro Azure Kognitivní hledání (jeden SU tvořený jedním oddílem a jednou replikou) a pak sledujte aktivitu uživatelů, abyste mohli identifikovat vzorce používání, které by znamenaly nutnost větší kapacity. Pokud existuje předvídatelný vzor, může být možné synchronizovat škálování s aktivitou (pro automatizaci musíte napsat kód).

+ Správa nákladů je integrovaná do infrastruktury Azure. Další informace o sledování nákladů, nástrojů a rozhraní API najdete v přehledu [fakturace a správy nákladů](../cost-management-billing/cost-management-billing-overview.md) .

Vypínání vyhledávací služby na dočasné bázi není možné. Vyhrazené prostředky jsou vždycky v provozu a jsou vyhrazené pro vaše výhradní použití po dobu života vaší služby. Odstranění služby je trvalé a zároveň odstraní přidružená data.

V rámci samotné služby je jediným způsobem, jak snížit vaše vyúčtování, je snížit počet replik a oddílů na úroveň, která stále poskytuje přijatelný výkon a [dodržování předpisů SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), nebo vytvořit službu na nižší úrovni (hodinové sazby S1 jsou nižší než ceny S2 a S3). Za předpokladu, že jste službu zařídili na konci vašich projekce zatížení, můžete při zvětšování služby vytvořit druhou větší službu, znovu sestavit indexy na druhou službu a pak odstranit první z nich.

## <a name="next-steps"></a>Další kroky

Naučte se monitorovat a spravovat náklady v rámci předplatného Azure.

> [!div class="nextstepaction"]
> [Dokumentace ke správě nákladů a fakturaci Azure](../cost-management-billing/cost-management-billing-overview.md)
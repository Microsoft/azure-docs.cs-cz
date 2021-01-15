---
title: Výběr cenové úrovně
titleSuffix: Azure Cognitive Search
description: 'Azure Kognitivní hledání se dá zřídit v těchto úrovních: Free, Basic a Standard a Standard jsou dostupné v různých konfiguracích prostředků a úrovních kapacity.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4c58968cb6a38a10433915ec8fa00336ccad301e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216406"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Výběr cenové úrovně pro Azure Kognitivní hledání

Při [vytváření vyhledávací služby](search-create-service-portal.md)se volí cenová úroveň, která je pevná pro dobu života služby. Vybraná úroveň určuje:

+ Množství indexů a dalších objektů, které můžete vytvořit (maximální limit)
+ Velikost a rychlost oddílů (fyzické úložiště)
+ Fakturovatelná sazba, pevná měsíční cena, ale také přírůstkové náklady, pokud přidáváte oddíly nebo repliky

Kromě toho přináší několik [prémiových funkcí](#premium-features) požadavky na úroveň.

## <a name="tier-descriptions"></a>Popisy vrstev

Mezi vrstvy patří optimalizace **Free**, **Basic**, **Standard** a **Storage**. Optimalizované úložiště Standard a Storage jsou k dispozici v několika konfiguracích a kapacitě.

Následující snímek obrazovky z Azure Portal zobrazuje dostupné úrovně minus ceny (které najdete na portálu a na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/). 

![Cenové úrovně Azure Kognitivní hledání](media/search-sku-tier/tiers.png "Cenové úrovně Azure Kognitivní hledání")

**Free** vytvoří omezené vyhledávací služby pro menší projekty, jako jsou spuštěné kurzy a ukázky kódu. Interně jsou repliky a oddíly sdíleny mezi více odběratelů. Nemůžete škálovat bezplatnou službu nebo spouštět významné úlohy.

**Základní** a **standardní** jsou nejčastěji používané Fakturovatelné úrovně s výchozím nastavením **Standard** . Díky vyhrazeným prostředkům v rámci vašeho řízení můžete nasazovat větší projekty, optimalizovat výkon a zvýšit kapacitu.

Některé úrovně jsou optimalizované pro určité typy práce. Například **Standard 3 vysoká hustota (S3 HD)** je *hostující režim* pro S3, kde je základní hardware optimalizovaný pro velký počet menších indexů a je určený pro víceklientské scénáře. S3 HD má stejné poplatky za jednotku jako S3, ale hardware je optimalizovaný pro rychlé čtení souborů na velkém počtu menších indexů.

Vrstvy **optimalizované pro úložiště** nabízejí větší kapacitu úložiště s nižší cenou za TB než úrovně Standard. Primární kompromis je vyšší latence dotazů, které byste měli ověřit pro konkrétní požadavky na aplikaci. Další informace o požadavcích na výkon této úrovně najdete v tématu věnovaném [důležitým informacím o výkonu a optimalizaci](search-performance-optimization.md).

Další informace o různých úrovních najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/), v článku [omezení služby v Azure kognitivní hledání](search-limits-quotas-capacity.md) a na stránce portálu při zřizování služby.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Dostupnost funkcí podle úrovně

Většina funkcí je dostupná na všech úrovních, včetně úrovně Free. Úroveň, kterou zvolíte, bude mít v několika případech vliv na vaši schopnost implementovat funkci. Následující tabulka popisuje omezení funkcí, která souvisí s úrovní služeb.

| Příznak | Omezení |
|---------|-------------|
| [Indexery](search-indexer-overview.md) | Indexery nejsou k dispozici na S3 HD.  |
| [Obohacení AI](search-security-manage-encryption-keys.md) | Běží na bezplatné úrovni, ale nedoporučuje se. |
| [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) | Není k dispozici na úrovni Free. |
| [Přístup k bráně firewall protokolu IP](service-configure-firewall.md) | Není k dispozici na úrovni Free. |
| [Privátní koncový bod (integrace s privátním odkazem Azure)](service-create-private-endpoint.md) | Pro příchozí připojení k vyhledávací službě není na úrovni Free k dispozici. Pro odchozí připojení prostřednictvím indexerů k jiným prostředkům Azure, které nejsou dostupné na bezplatném nebo S3 HD. Pro indexery, které používají dovednosti, nejsou dostupné na úrovni Free, Basic, S1 nebo S3 HD.|

Funkce náročné na prostředky nemusí fungovat správně, pokud jim neposkytnete dostatečnou kapacitu. Například [rozšíření AI](cognitive-search-concept-intro.md) má dlouhodobě běžící dovednosti, které vyprší časový limit bezplatné služby, pokud není datová sada malá.

## <a name="billable-events"></a>Fakturovatelné události

K řešení postavenému na Azure Kognitivní hledání může doplatit tyto náklady následujícími způsoby:

+ [Náklady](#service-costs) na samotnou službu, která běží nepřetržitě, při minimální konfiguraci (jeden oddíl a replika), podle základní sazby. Můžete si to představit jako pevné náklady na spuštění služby.

+ Přidejte kapacitu (repliky nebo oddíly), kde se náklady zvyšují po přírůstcích Fakturovatelné sazby.

+ Poplatky za šířku pásma (odchozí přenos dat)

+ Služby doplňku vyžadované pro konkrétní funkce nebo funkce:

  + Rozšíření AI (vyžaduje [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + znalostní báze Knowledge Store (vyžaduje [Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + přírůstkové obohacení (vyžaduje [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), platí pro rozšíření AI)
  + klíče spravované zákazníkem a dvojité šifrování (vyžaduje [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + privátní koncové body pro model bez přístupu k Internetu (vyžaduje [privátní odkaz Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Náklady na služby

Na rozdíl od virtuálních počítačů nebo jiných prostředků, které můžou být pozastavené, aby se předešlo poplatkům, služba Azure Kognitivní hledání je vždycky dostupná na hardwaru vyhrazeném pro výhradní použití. Vytvoření služby je fakturovatelná událost, která se spustí při vytvoření služby a končí při odstranění služby. 

Minimální poplatek je první jednotka vyhledávání (jedna replika x jeden oddíl) s fakturovatelnou sazbou. Toto minimum je pevně nastavené pro dobu života služby, protože služba nemůže běžet bez jakýchkoli těchto konfigurací. Kromě minima můžete přidat repliky a oddíly nezávisle na sobě. Přírůstkové zvýšení kapacity prostřednictvím replik a oddílů zvýší vaše vyúčtování na základě následujícího vzorce: [(repliky × oddíly x sazba)](#search-units), kde se sazba vám bude účtovat, záleží na cenové úrovni, kterou vyberete.

Při odhadování nákladů na řešení hledání mějte na paměti, že ceny a kapacita nejsou lineární. (Dvojnásobná kapacita větší než dvojnásobek nákladů.) Příklad toho, jak vzorec funguje, najdete v tématu [jak přidělit repliky a oddíly](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Poplatky za šířku pásma

Pokud je zdroj dat Azure v jiné oblasti než Azure Kognitivní hledání, může použití [indexerů](search-indexer-overview.md) ovlivnit fakturaci. V tomto scénáři může být pro přesun odchozích dat ze zdroje dat Azure do Azure Kognitivní hledání náklady. Podrobnosti najdete na stránkách s cenami v dané datové platformě Azure.

Pokud vytvoříte službu Azure Kognitivní hledání ve stejné oblasti, ve které jsou vaše data, můžete poplatky za odchozí přenosy dat zcela eliminovat. Tady jsou některé informace na [stránce s cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Příchozí data: Microsoft neúčtuje žádná příchozí data do žádné služby v Azure. 

+ Odchozí data: odchozí data odkazují na výsledky dotazu. Kognitivní hledání za odchozí data neúčtuje, ale odchozí poplatky z Azure jsou možné, pokud jsou služby v různých oblastech. Tyto poplatky nejsou ve skutečnosti součástí faktury za Azure Kognitivní hledání. Tady jsou zmíněné informace, protože pokud odesíláte výsledky do jiných oblastí nebo mimo Azure, můžou se tyto náklady projevit v celkové faktuře.

### <a name="ai-enrichment-with-cognitive-services"></a>Obohacení AI pomocí Cognitive Services

V případě [obohacení AI](cognitive-search-concept-intro.md)byste měli naplánovat [připojení Fakturovatelné Cognitive Services prostředku Azure](cognitive-search-attach-cognitive-services.md)ve stejné oblasti jako Azure kognitivní hledání na cenové úrovni S0 pro zpracování průběžných plateb. K připojení Cognitive Services nejsou žádné pevné náklady. Platíte jenom za zpracování, které potřebujete.

| Operace | Dopad fakturace |
|-----------|----------------|
| Trhliny dokumentů, extrakce textu | Free |
| Trhliny dokumentů, extrakce obrázků | Účtuje se podle počtu imagí extrahovaných z vašich dokumentů. V [konfiguraci indexeru](/rest/api/searchservice/create-indexer#indexer-parameters)je **imageAction** parametr, který aktivuje extrakci imagí. Pokud je **imageAction** nastavené na None (výchozí nastavení), nebudete se vám za extrakci imagí účtovat. Sazba za extrakci imagí je popsána na stránce s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) pro Azure kognitivní hledání.|
| [Předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md) | Účtuje se se stejnou sazbou, jako kdyby jste provedli úlohu přímo pomocí Cognitive Services. |
| Vlastní dovednosti | Vlastní dovednost je funkce, kterou zadáte. Náklady na používání vlastní dovednosti závisí výhradně na tom, jestli vlastní kód volá jiné měřené služby. |

Funkce [přírůstkového navýšení (verze Preview)](cognitive-search-incremental-indexing-conceptual.md) vám umožní poskytnout mezipaměť, která umožňuje efektivnější použití indexeru při spouštění pouze těch dovedností, které jsou nezbytné v případě, že v budoucnu upravíte dovednosti a ušetříte čas a peníze.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Vzorec fakturace (R × P = SU)

Nejdůležitější koncept fakturace pro pochopení pro Azure Kognitivní hledání operací je *jednotka pro hledání* (SU). Vzhledem k tomu, že Azure Kognitivní hledání závisí na obou replikách a oddílech pro indexování a dotazování, nemá smysl vyúčtováním jenom z jednoho nebo druhého. Místo toho je fakturace založena na složeném z obou.

SU je produktem *replik* a *oddílů* používaných službou: **(R × P = SU)**.

Každá služba začíná jednou SU (jedna replika vynásobená jedním oddílem) jako minimální. Maximum pro jakoukoli službu je 36 SUs. Toto maximum je možné dosáhnout několika způsoby: 6 oddílů × 6 replik nebo 3 oddíly x 12 replik, například. Je běžné použít méně než celkovou kapacitu (například 3-replika, 3-partition Service se účtuje jako 9 SUs). Platné kombinace najdete v grafu [kombinací oddílů a repliky](search-capacity-planning.md#chart) .

Fakturační sazba je každou hodinu. Každá úroveň má postupně vyšší míru. Vyšší úrovně se dodávají s většími a rychlými oddíly a to přispívá k celkové hodinové sazbě za tuto úroveň. Sazby za jednotlivé úrovně můžete zobrazit na stránce s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) .

Většina zákazníků přinese jenom část celkové kapacity online, která uchovává zbývající rezervu. Pro účely fakturace určuje počet oddílů a replik, které přivedete do režimu online, vypočítané vzorcem SU za každou hodinu.

## <a name="next-steps"></a>Další kroky

Nákladová Správa je nedílnou součástí plánování kapacity. V dalším kroku pokračujte v následujícím článku, kde najdete pokyny, jak odhadnout kapacitu a spravovat náklady.

> [!div class="nextstepaction"]
> [Jak spravovat náklady a odhadnout kapacitu v Azure Kognitivní hledání](search-sku-manage-costs.md)
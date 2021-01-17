---
title: Výběr cenové úrovně
titleSuffix: Azure Cognitive Search
description: 'Přečtěte si o cenových úrovních (nebo SKU) pro Azure Kognitivní hledání. Vyhledávací službu je možné zřídit na těchto úrovních: Free, Basic a Standard. Úroveň Standard je k dispozici v různých konfiguracích prostředků a úrovních kapacity.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 1a1fc0ce634282ffd4fcf374138fe97a04f32062
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539605"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Výběr cenové úrovně pro Azure Kognitivní hledání

Při [vytváření vyhledávací služby](search-create-service-portal.md)se volí cenová úroveň (nebo SKU), která je pevná pro dobu života služby. Odhadované měsíční náklady se zobrazí na stránce **Vybrat cenovou úroveň** na portálu. Pokud vytváříte službu prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure CLI, tato vrstva se zadává přes **`-Sku`** parametr.

Vybraná úroveň určuje:

+ Maximální počet indexů a dalších objektů, které můžete vytvořit ve službě
+ Velikost a rychlost oddílů (fyzické úložiště)
+ Fakturovatelná sazba jako pevná měsíční cena, ale také přírůstková cena, pokud přidáváte kapacitu

V několika případech úroveň, kterou zvolíte, určuje dostupnost [prémiových funkcí](#premium-features).

## <a name="tier-descriptions"></a>Popisy vrstev

Mezi vrstvy patří optimalizace **Free**, **Basic**, **Standard** a **Storage**. Optimalizované úložiště Standard a Storage jsou k dispozici v několika konfiguracích a kapacitě. Následující snímek obrazovky z Azure Portal zobrazuje dostupné úrovně minus ceny (které najdete na portálu a na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Graf cenové úrovně" border="true":::

**Free** vytvoří omezené vyhledávací služby pro menší projekty, jako jsou spuštěné kurzy a ukázky kódu. Systémové prostředky jsou interně sdíleny mezi více odběratelů. Nemůžete škálovat bezplatnou službu nebo spouštět významné úlohy.

**Základní** a **standardní** jsou nejčastěji používané Fakturovatelné úrovně, jejichž **standardně** je výchozí nastavení, protože poskytuje větší flexibilitu při škálování pro úlohy. Díky vyhrazeným prostředkům v rámci vašeho řízení můžete nasazovat větší projekty, optimalizovat výkon a zvýšit kapacitu.

Některé úrovně jsou navržené pro určité typy práce. Například **Standard 3 vysoká hustota (S3 HD)** je *hostující režim* pro S3, kde je základní hardware optimalizovaný pro velký počet menších indexů a je určený pro víceklientské scénáře. S3 HD má stejné poplatky za jednotku jako S3, ale hardware je optimalizovaný pro rychlé čtení souborů na velkém počtu menších indexů.

Vrstvy **optimalizované pro úložiště** nabízejí větší kapacitu úložiště s nižší cenou za TB než úrovně Standard. Primární kompromis je vyšší latence dotazů, které byste měli ověřit pro konkrétní požadavky na aplikaci. Další informace o požadavcích na výkon této úrovně najdete v tématu věnovaném [důležitým informacím o výkonu a optimalizaci](search-performance-optimization.md).

Další informace o různých úrovních najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/), v článku [omezení služby v Azure kognitivní hledání](search-limits-quotas-capacity.md) a na stránce portálu při zřizování služby.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Dostupnost funkcí podle úrovně

Většina funkcí je dostupná na všech úrovních, včetně úrovně Free. Úroveň, kterou zvolíte, bude mít v několika případech vliv na vaši schopnost implementovat funkci. Následující tabulka popisuje omezení funkcí, která souvisí s úrovní služeb.

| Příznak | Omezení |
|---------|-------------|
| [Indexery](search-indexer-overview.md) | Indexery nejsou k dispozici na S3 HD.  |
| [Obohacení AI](search-security-manage-encryption-keys.md) | Běží na bezplatné úrovni, ale nedoporučuje se. |
| [Spravované nebo důvěryhodné identity pro odchozí (indexer) přístup](search-howto-managed-identities-data-sources.md) | Není k dispozici na úrovni Free.|
| [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) | Není k dispozici na úrovni Free. |
| [Přístup k bráně firewall protokolu IP](service-configure-firewall.md) | Není k dispozici na úrovni Free. |
| [Privátní koncový bod (integrace s privátním odkazem Azure)](service-create-private-endpoint.md) | Pro příchozí připojení k vyhledávací službě není na úrovni Free k dispozici. Pro odchozí připojení prostřednictvím indexerů k jiným prostředkům Azure, které nejsou dostupné na bezplatném nebo S3 HD. Pro indexery, které používají dovednosti, nejsou dostupné na úrovni Free, Basic, S1 nebo S3 HD.|

Funkce náročné na prostředky nemusí fungovat správně, pokud jim neposkytnete dostatečnou kapacitu. Například [rozšíření AI](cognitive-search-concept-intro.md) má dlouhodobě běžící dovednosti, které vyprší časový limit bezplatné služby, pokud není datová sada malá.

## <a name="upper-limits"></a>Horní meze

Úrovně určují maximální úložiště samotné služby a také maximální počet indexů, indexerů, zdrojů dat, dovednosti a mapování synonym, které můžete vytvořit. Úplný konec všech omezení najdete v tématu [omezení služeb v Azure kognitivní hledání](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Velikost oddílu a rychlost

Ceny vrstev obsahují podrobnosti o úložišti pro jednotlivé oddíly, které jsou v rozsahu od 2 GB po dobu Basic, až do 2 TB pro úrovně optimalizované pro úložiště (L2). Další hardwarové charakteristiky, jako je například rychlost provozu, latence a přenosové rychlosti, nejsou publikovány, ale úrovně navržené pro konkrétní architektury řešení jsou postaveny na hardwaru, který obsahuje funkce pro podporu těchto scénářů.

## <a name="billing-rates"></a>Fakturační sazby

Úrovně mají různé fakturační sazby s vyšší sazbou za úrovně, které běží na dražším hardwaru nebo poskytují dražší funkce. Fakturační sazbu za vrstvu najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/) Azure pro Azure kognitivní hledání.

Jakmile vytvoříte službu, bude se vám účtovat *cena za pevné náklady* na provoz služby po hodinách a na *přírůstkové náklady* , pokud se rozhodnete přidat větší kapacitu.

Služba Search Services přiděluje výpočetní prostředky ve formě *oddílů* (pro úložiště) a *replik* (instance dotazovacího stroje). Zpočátku se služba vytvoří s jednou z nich a fakturační sazba je včetně obou prostředků. Pokud ale kapacitu škálovat, účtují se náklady nahoru nebo dolů v přírůstcích po fakturovatelných sazbách.

V následujícím příkladu je uvedena ukázka. Předpokládejme hypotetickou fakturační sazbu $100 za měsíc. Pokud službu Search udržujete na počáteční kapacitě jednoho oddílu a jednu repliku, pak $100 je to, co můžete očekávat na konci měsíce. Pokud však přidáte dvě další repliky, abyste dosáhli vysoké dostupnosti, měsíční vyúčtování se zvýší na $300 ($100 pro první dvojici oddílů repliky, za kterou následuje $200 pro tyto dvě repliky).

Tento cenový model vychází z konceptu použití fakturačního tarifu na počet *jednotek hledání* (SU) používaných vyhledávací službou. Všechny služby jsou zpočátku zřízené na jednom SU, ale můžete zvýšit výkon tím, že přidáte buď oddíly nebo repliky, které budou zpracovávat větší zatížení. Další informace najdete v tématu [odhad nákladů na vyhledávací službu](search-sku-manage-costs.md).

## <a name="next-steps"></a>Další kroky

Nejlepším způsobem, jak zvolit cenovou úroveň, je začít s nejméně nákladovou úrovní a pak s nimi a testováním informovat vaše rozhodnutí o zachování služby nebo vytvoření nového na vyšší úrovni. V případě dalších kroků doporučujeme vytvořit vyhledávací službu v rámci vrstvy, která bude vyhovovat úrovni testování, kterou navrhujete, a pak si přečtěte následující pokyny pro doporučení k odhadu nákladů a kapacity.

+ [Vytvoření vyhledávací služby](search-create-service-portal.md)
+ [Odhadněte náklady](search-sku-manage-costs.md)
+ [Odhad kapacity](search-sku-manage-costs.md)
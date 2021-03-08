---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f6bd0c13d5cbad802613e2bdea8fd6002f4deea2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444631"
---
Centrum událostí uspořádá sekvence událostí do jednoho nebo více oddílů. Jakmile přijdete o novější události, přidají se na konec této sekvence. Oddíl si lze představit jako „protokol transakcí“.

Oddíly uchovávají data události obsahující tělo události, uživatelem definovaný kontejner vlastností, který popisuje událost, a metadata, jako je jejich posun v oddílu, číslo v posloupnosti datových proudů a časové razítko na straně služby, na které bylo přijato.

![Diagram, který zobrazuje starší posloupnost událostí.](./media/event-hubs-partitions/partition.png)

Event Hubs je navržená tak, aby pomáhala při zpracování velkých objemů událostí, a pomáhá s nimi dvěma způsoby:

Nejdřív, i když Event Hubs je služba PaaS, existuje fyzická realita a udržuje protokol, který zachovává pořadí událostí, vyžaduje, aby se tyto události zachovaly společně v podkladovém úložišti a jeho replikách a aby výsledkem byl limit propustnosti pro takový protokol. Vytváření oddílů umožňuje používat pro stejné centrum událostí více paralelních protokolů a vynásobit dostupnou nezpracované propustnosti v/v.

Za druhé, vaše vlastní aplikace musí být schopné udržet se zpracováním objemu událostí, které se odesílají do centra událostí. Může být komplexní a vyžaduje značnou kapacitu paralelního zpracování škálované na více instancí. Důvod pro oddíly je stejný, jak je uvedeno výše: kapacita jednoho procesu pro zpracování událostí je omezená, takže budete potřebovat několik procesů a oddíly, jak vaše řešení tyto procesy doručí, a zároveň zajišťuje, aby každá událost měla jasného vlastníka zpracování. 

Event Hubs zachovává události pro nakonfigurovanou dobu uchování, která se vztahuje na všechny oddíly. Události jsou automaticky odebrány po dosažení doby uchování. Pokud zadáte dobu uchování jednoho dne, událost nebude k dispozici přesně 24 hodin poté, co byla přijata. Události nemůžete explicitně odstranit. 

Povolená doba uchování je až 7 dní pro Event Hubs Standard a až 90 dní pro Event Hubs úrovně Dedicated. Pokud potřebujete Archivovat události nad povolenou dobu uchovávání, můžete je [automaticky ukládat do Azure Storage nebo Azure Data Lake zapnutím funkce Event Hubs Capture](../articles/event-hubs/event-hubs-capture-overview.md), a pokud potřebujete tyto podrobné archivy prohledávat nebo analyzovat, můžete [je snadno importovat do Azure synapse](../articles/event-hubs/store-captured-data-data-warehouse.md) nebo jiných podobných úložišť a analytických platforem. 

Důvodem Event Hubs "omezení uchovávání dat na základě času je zabránit velkým objemům historických zákaznických dat v hlubokém úložišti, které je indexováno pouze pomocí časového razítka, a umožňuje pouze sekvenční přístup. Filozofie architektury tady je, že historické údaje potřebují mnohem lepší indexování a větší přímý přístup než rozhraní pro zpracování událostí v reálném čase, které Event Hubs nebo Kafka poskytují. Moduly streamování událostí nejsou vhodné pro hraní role datových laků nebo dlouhodobých archivů pro účely plnění událostí. 

Vzhledem k tomu, že oddíly jsou nezávislé a obsahují vlastní posloupnost dat, často se jejich velikost mění různým tempem. V Event Hubs nezáleží na tom, že vyžaduje zásah správce, jak by byl, například v Apache Kafka, ale bez rovnoměrné distribuce povede k nerovnoměrnému zatížení pro vaše procesory událostí.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Počet oddílů se zadává při vytváření a musí být mezi 1 a 32 ve Event Hubs Standard. Počet oddílů může být až 2000 oddílů na kapacitní jednotku v Event Hubs úrovně Dedicated. 

Pro konkrétní centrum událostí doporučujeme, abyste si zvolili aspoň tolik oddílů, kolik jich očekáváte v trvalých [jednotkách propustnosti (tu)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) . Měli byste počítat s jedním oddílem s kapacitou propustnosti 1. (1 megabajt v, 2 MB odchozí). Počet propustnosti můžete škálovat na svůj obor názvů nebo jednotky kapacity clusteru nezávisle na počtu oddílů. Centrum událostí s 32 oddíly nebo centrum událostí s 1 oddílem má za následek stejné náklady, pokud je obor názvů nastavený na 1 část kapacity. 

Aplikace řídí mapování událostí na oddíly jedním ze tří způsobů:

- Zadáním klíče oddílu, který je konzistentně namapovaný (pomocí funkce hash) na jeden z dostupných oddílů. 
- Pokud nezadáte klíč oddílu, který umožňuje zprostředkovateli náhodně zvolit oddíl pro danou událost.
- Explicitně odesílají události do konkrétního oddílu.

Zadání klíče oddílu umožňuje udržovat související události společně ve stejném oddílu a v přesném pořadí, v jakém byly odeslány. Klíč oddílu je řetězec, který je odvozen z kontextu vaší aplikace a identifikuje vzájemné vztahy událostí.

Posloupnost událostí identifikovaných klíčem oddílu je *datový proud*. Oddíl je multiplexované úložiště protokolu pro mnoho takových datových proudů. 

Počet oddílů centra událostí ve [vyhrazeném Event Hubs clusteru](../articles/event-hubs/event-hubs-dedicated-overview.md) se dá po vytvoření centra událostí [zvýšit](../articles/event-hubs/dynamically-add-partitions.md) , ale distribuce datových proudů napříč oddíly se v době, kdy se provedla, změní na změny oddílů oddílů na oddíly. proto byste se měli pokusit, abyste se těmto změnám vyhnuli, pokud se jedná o relativní pořadí událostí ve vaší aplikaci.

Nastavení počtu oddílů na maximální povolenou hodnotu se měří, ale vždy mějte na paměti, že datové proudy událostí musí být strukturované, aby bylo možné využít více oddílů. Pokud potřebujete zachování absolutního pořadí napříč všemi událostmi nebo jenom několik podproudy, možná nebudete moct využít mnoho oddílů. Mnoho oddílů navíc usnadňuje zpracování na straně sebe složitější. 

Oddíly lze odesílat přímo, a proto se nedoporučuje. Místo toho můžete použít konstrukce vyšší úrovně, které jsou představené v části [vydavatelé událostí](../articles/event-hubs/event-hubs-features.md#event-publishers) . 


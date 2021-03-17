---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2cb203a00bb00767126f95e1fdc2f5aff8990f01
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601269"
---
Event Hubs uspořádá sekvence událostí odeslané do centra událostí do jednoho nebo více oddílů. Jakmile přijdete o novější události, přidají se na konec této sekvence. 

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Oddíl lze představit jako "potvrzovací protokol". Oddíly uchovávají data událostí, která obsahují tělo události, uživatelem definovaný kontejner vlastností popisující událost, metadata, jako je jeho posun v oddílu, jeho číslo v posloupnosti datových proudů a časové razítko na straně služby, na kterém byla přijata.

![Diagram, který zobrazuje starší posloupnost událostí.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Výhody používání oddílů
Event Hubs je navržená tak, aby pomáhala při zpracování velkých objemů událostí, a pomáhá s nimi dvěma způsoby:

- I když Event Hubs je služba PaaS, existuje fyzická realita a udržuje protokol, který zachovává pořadí událostí, vyžaduje, aby se tyto události zachovaly společně v podkladovém úložišti a jeho replikách a aby výsledkem byl limit propustnosti pro takový protokol. Vytváření oddílů umožňuje používat pro stejné centrum událostí více paralelních protokolů a vynásobit dostupnou nezpracované propustnosti v/v.
- Vaše vlastní aplikace musí být schopné udržet se zpracováním objemu událostí, které se odesílají do centra událostí. Může být komplexní a vyžaduje značnou kapacitu paralelního zpracování škálované na více instancí. Kapacita jednoho procesu pro zpracování událostí je omezená, takže potřebujete několik procesů. Oddíly představují způsob, jakým vaše řešení tyto procesy doručí, a ještě zajišťuje, aby každá událost měla jasného vlastníka zpracování. 

### <a name="number-of-partitions"></a>Počet oddílů
Počet oddílů se zadává při vytváření a musí být mezi 1 a 32 ve Event Hubs Standard. Počet oddílů může být až 2000 oddílů na kapacitní jednotku v Event Hubs úrovně Dedicated. 

Pro konkrétní centrum událostí doporučujeme, abyste si zvolili aspoň tolik oddílů, kolik jich očekáváte v trvalých [jednotkách propustnosti (tu)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) . Měli byste počítat s jedním oddílem s kapacitou propustnosti 1. (1 megabajt v, 2 MB odchozí). Počet propustnosti můžete škálovat na svůj obor názvů nebo jednotky kapacity clusteru nezávisle na počtu oddílů. Centrum událostí s 32 oddíly nebo centrum událostí s 1 oddílem má za následek stejné náklady, pokud je obor názvů nastavený na 1 část kapacity. 

Počet oddílů centra událostí ve [vyhrazeném Event Hubs clusteru](../articles/event-hubs/event-hubs-dedicated-overview.md) se dá po vytvoření centra událostí [zvýšit](../articles/event-hubs/dynamically-add-partitions.md) , ale distribuce datových proudů napříč oddíly se v době, kdy se provedla, změní na změny oddílů oddílů na oddíly. proto byste se měli pokusit, abyste se těmto změnám vyhnuli, pokud se jedná o relativní pořadí událostí ve vaší aplikaci.

Nastavení počtu oddílů na maximální povolenou hodnotu se měří, ale vždy mějte na paměti, že datové proudy událostí musí být strukturované, aby bylo možné využít více oddílů. Pokud potřebujete zachování absolutního pořadí napříč všemi událostmi nebo jenom několik podproudy, možná nebudete moct využít mnoho oddílů. Mnoho oddílů navíc usnadňuje zpracování na straně sebe složitější. 


### <a name="mapping-of-events-to-partitions"></a>Mapování událostí na oddíly
Klíč oddílu můžete použít k mapování příchozích dat událostí do konkrétních oddílů pro účely organizace dat. Klíč oddílu je hodnota zadaná odesílatelem, která byla předaná do centra událostí. Zpracovává se pomocí statické hashovací funkce, která vytvoří přiřazení k oddílu. Pokud při publikování události nezadáte klíč oddílu, použije se přiřazení metodou kruhového dotazování.

Zdroj události zná jenom svůj klíč oddílu, a ne oddíl, do kterého se události publikují. Díky tomuto oddělení klíče a oddílu odesílatel toho nepotřebuje vědět o zpracování příjmu dat příliš mnoho. Vhodným klíčem oddílu je jedinečná identita uživatele nebo zařízení, ale k seskupení souvisejících událostí do jednoho oddílu je možné použít i další atributy, například geografickou polohu.

Zadání klíče oddílu umožňuje udržovat související události společně ve stejném oddílu a v přesném pořadí, v jakém byly odeslány. Klíč oddílu je řetězec, který je odvozen z kontextu vaší aplikace a identifikuje vzájemné vztahy událostí. Posloupnost událostí identifikovaných klíčem oddílu je *datový proud*. Oddíl je multiplexované úložiště protokolu pro mnoho takových datových proudů. 

> [!NOTE]
> I když můžete odesílat události přímo do oddílů, nedoporučujeme ji, zejména v případě, že je pro vás důležitá vysoká dostupnost. Odchází z dostupnosti centra událostí na úrovni oddílů. Další informace najdete v tématu [dostupnost a konzistence](../articles/event-hubs/event-hubs-availability-and-consistency.md).


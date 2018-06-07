---
title: Pochopení uchovávání dat ve vašem prostředí Azure časové řady Insights | Microsoft Docs
description: Tento článek popisuje dvě nastavení, které řídí uchovávání dat ve vašem prostředí Statistika Azure časové řady.
ms.service: time-series-insights
services: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: ad0cbf38a1a32632869c490c5a46796eaa3884f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652446"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Pochopení uchovávání dat v přehledy časové řady
Tento článek popisuje dvě nastavení, které mají vliv uchovávání dat ve vašem prostředí časové řady přehledy (TSI).

Každé prostředí TSI má nastavení, které řídí **doby uchování dat**. Hodnota rozdělena na 1 až 400 dnů. Data jsou odstraněna podle prostředí úložiště kapacity nebo uchovávání doba trvání (1-400), nastane dříve.

Každé prostředí TSI má další nastavení **chování k překročení limitu úložiště**. Toto nastavení řídí chování příjem příchozích dat a mazání po dosažení maximální kapacity prostředí. Existují dvě chování můžete vybrat ze:
- **Vymazat stará data** (výchozí)  
- **Pozastavení příjem příchozích dat**

> [!NOTE]
> Ve výchozím nastavení se při vytvoření nového prostředí uchovávání nastaven tak, aby **vymazat stará data**. Toto nastavení můžou být podle potřeby po času vytvoření pomocí portálu Azure na **konfigurace** stránky TSI prostředí.

Další informace o přepínání uchování chování, zkontrolujte [konfigurace uchování v časové řady přehledy](time-series-insights-how-to-configure-retention.md).

Porovnání chování uchovávání dat:

## <a name="purge-old-data"></a>Vymazat stará data
- Toto chování je výchozí chování pro TSI prostředí a předměty stejných prostředí TSI chování vystavoval vzhledem k tomu, že spustit ve verzi public preview.  
- Toto chování je preferovaná, když uživatelé chtějí vždy najdete v části jejich *nejnovější data* ve svém prostředí TSI. 
- Toto chování *odstraní* dat jednou prostředí (dobu uchování, velikost nebo počet, nastane dříve) limitu. Ve výchozím nastavení do 30 dní uchovávání informací. 
- Nejstarší ingestovaný data se vyprazdňují první (FIFO přístup).

### <a name="example-1"></a>Příklad 1:
Vezměte v úvahu příklad prostředí s uchování chování **pokračovat příjem příchozích dat a vymazat stará data**: V tomto příkladu **doby uchování dat** je nastaven na 400 dní. **Kapacita** nastavena na S1 jednotky, která obsahuje 30 GB celkové kapacity.   Předpokládejme, že příchozí data hromadí na 500 MB každý den v průměru. Toto prostředí můžete zachovat pouze 60 dní, po který data zadána Míra příchozích dat, protože je dosaženo maximální kapacity na 60 dnů. Příchozí data hromadí jako: 500 MB každý den x 60 dnů = 30 GB. 

V tomto příkladu 61st dne prostředí zobrazuje nejčerstvější data, ale odstraní nejstarší data, která je starší než 60 dnů. K vyprázdnění díky prostor pro nová data streamování, aby nová data může být zkoumána. 

Pokud uživatele, které chcete zachovat data déle, můžete zvýšit velikost prostředí tak, že přidáte další jednotky nebo méně dat můžete posouvat nastavení.  

### <a name="example-2"></a>Příklad 2:
Vezměte v úvahu prostředí je také nakonfigurovat chování uchování **pokračovat příjem příchozích dat a vymazat stará data**. V tomto příkladu **doby uchování dat** je nastavená na hodnotu nižší než 180 dnů. **Kapacita** nastavena na S1 jednotky, která obsahuje 30 GB celkové kapacity. Chcete-li uložit data pro úplné 180 dní, nesmí překročit denní příjem příchozích dat za den, 0.166 GB (166 MB).  

Vždy, když toto prostředí denní míra příchozího překročí 0.166 GB denně, data nelze uložit na 180 dní, vzhledem k tomu, že získá některá data odstraněna. Vezměte v úvahu tato stejné prostředí během zaneprázdněn časového rámce. Předpokládejme, že průměrná 0.189 GB za den může zvýšit rychlost příjem příchozích dat v prostředí. V tomto zaneprázdněn časovém rámci, zůstanou zachovány 158 dnů dat (30GB nebo 0.189 = 158.73 dní uchovávání). Tato doba je menší než časový rámec uchování požadovaným datům.

## <a name="pause-ingress"></a>Pozastavení příjem příchozích dat
- Toto chování je určena k Ujistěte se, že data se nevyprazdňují, pokud velikost a počet limitu před dobu uchování.  
- Toto chování poskytuje další čas pro uživatele k navýšení kapacity jejich prostředí před dat se vyprázdní se kvůli před nedodržením doby uchování
- Toto chování pomáhá chránit před ztrátou dat, ale pokud příjem příchozích dat je pozastaven po uplynutí doby uchování zdroje událostí vytvoří příležitost ztrátu nejnovější data.
- Ale po dosažení maximální kapacity prostředí pozastaví prostředí příchozí přenos dat, dokud provedou další akce: 
   - Můžete zvýšit maximální kapacita v prostředí. Další informace najdete v tématu [postup škálování prostředí časové řady Statistika](time-series-insights-how-to-scale-your-environment.md) přidat další jednotky škálování.
   - Je dosaženo období uchovávání dat a vymazat data proto přináší prostředí níže své maximální kapacity.

### <a name="example-3"></a>Příklad 3:
Vezměte v úvahu prostředí s chováním uchování nakonfigurovaný tak, aby **pozastavit příjem příchozích dat**. V tomto příkladu **období uchovávání dat** nastavena na 60 dnů. **Kapacita** nastavená na 3 jednotky S1. Předpokládejme, že toto prostředí má příjem příchozích dat 2GB dat denně. V tomto prostředí je pozastavena příjem příchozích dat po dosažení maximální kapacity. V ten moment se zobrazuje prostředí se stejnou datovou dokud obnoví příjem příchozích dat nebo dokud nebude 'continue příchozího' povolena (což by vyprázdnit starší data, aby uvolnil prostor pro nová data). 

Když se obnoví příjem příchozích dat:
- Data proudí v pořadí, ve kterém byla přijata zdroj události
- Události jsou indexované podle jejich časové razítko, pokud jste nepřekročili zásady uchovávání informací na zdroji událostí. Další informace o konfiguraci uchování zdroje událostí [Event Hubs – nejčastější dotazy](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Měli byste nastavit výstrahy na oznámení, abyste se vyhnuli příjem příchozích dat byla pozastavena. Ztráty dat je možné, protože doba uchování výchozí je 1 den pro zdroje událostí Azure. Proto po příjem příchozích dat je pozastavena, budete pravděpodobně ke ztrátě nejnovější data další akce. Musíte zvýšit kapacitu nebo přepínač chování **vymazat stará data** aby nedošlo ke ztrátě dat, která je možné.

V ovlivněné služby Event Hubs, zvažte úpravu **uchování zpráv** vlastnost účelem minimalizace ztráty dat, když dojde k pozastavení příjem příchozích dat v přehledy časové řady.

![Uchování zprávu centra událostí.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Pokud žádné vlastnosti, které jsou nakonfigurované na zdroj události (timeStampPropertyName), TSI výchozí časové razítko příchodem centra událostí jako osy x. Pokud timeStampPropertyName nakonfigurovaný tak, aby byl něco jiného, prostředí vyhledá nakonfigurované timeStampPropertyName v paketu data události jsou analýze. 

Pokud potřebujete škálování prostředí zohlednit dodatečnou kapacitu nebo zvyšte délku uchování, najdete v části [postup škálování prostředí časové řady Insights](time-series-insights-how-to-scale-your-environment.md) Další informace.  

## <a name="next-steps"></a>Další postup
Další informace o přepínání uchování chování, zkontrolujte [konfigurace uchování v časové řady přehledy](time-series-insights-how-to-configure-retention.md).

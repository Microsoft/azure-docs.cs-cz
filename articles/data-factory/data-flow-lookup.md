---
title: Mapování transformace vyhledávání toků dat
description: Azure Data Factory transformace vyhledávání toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/03/2019
ms.openlocfilehash: 5cc54c95759ba1490f498305f05cc49a4411686d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930316"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory transformace vyhledávání toku dat

Pomocí vyhledávání můžete přidat referenční data z jiného zdroje do toku dat. Transformace vyhledávání vyžaduje definovaný zdroj, který odkazuje na vaši referenční tabulku a odpovídá na klíčová pole.

![Transformace vyhledávání](media/data-flow/lookup1.png "Vyhledávání")

Vyberte klíčová pole, která chcete porovnat mezi poli příchozích datových proudů a poli ze zdroje odkazů. Nejdřív je potřeba vytvořit nový zdroj na plátně pro návrh toku dat, který se použije jako pravá strana pro vyhledávání.

Když se najde shody, výsledné řádky a sloupce z reference source se přidají do toku dat. Můžete zvolit, která pole zájmu chcete zahrnout do jímky na konci toku dat. Případně můžete použít transformaci SELECT po vyhledávání a vyřadit seznam polí, aby zůstala pouze pole z obou datových proudů, které chcete zachovat.

Transformace vyhledávání provádí ekvivalent levého vnějšího spojení. Zobrazí se všechny řádky z levého zdroje společně s shodami z pravé strany. Pokud máte ve svém vyhledávání více hodnot, nebo pokud chcete výraz pro vyhledávání přizpůsobit, je vhodnější přepnout na transformaci spojení a použít vzájemné spojení. Tím se vyhnete případným chybám kartézském produktu při spuštění.

## <a name="match--no-match"></a>Shoda/bez shody

Po transformaci vyhledávání můžete použít následnou transformaci k zkontrolování výsledků každého řádku shody pomocí funkce Expression `isMatch()` k dalšímu výběru v logice na základě toho, zda hledání vedlo ke shodě mezi řádky nebo ne.

![Vzor vyhledávání](media/data-flow/lookup111.png "Vzor vyhledávání")

Po použití transformace vyhledávání můžete přidat transformaci s podmíněným rozdělením na funkci ```isMatch()```. V předchozím příkladu procházejí porovnávací řádky přes horní datový proud a nevyhovující tok řádků prostřednictvím ```NoMatch```ho datového proudu.

## <a name="first-or-last-value"></a>První nebo poslední hodnota

Pokud máte více odpovídajících hodnot vyhledávání, můžete chtít snížit více odpovídajících řádků, a to výběrem první nebo poslední shody. Můžete to provést pomocí agregované transformace po vyhledání.

V tomto případě se pro výběr první hodnoty ze shod vyhledávání používá agregovaná transformace s názvem ```PickFirst```.

![Vyhledávací agregace](media/data-flow/lookup333.png "Vyhledávací agregace")

![Nejdříve vyhledávání](media/data-flow/lookup444.png "Nejdříve vyhledávání")

## <a name="optimizations"></a>Optimalizace

V Data Factory jsou toky dat spouštěny v prostředích Spark s horizontálním škálováním. Pokud se vaše datová sada vejde do paměťového prostoru pracovního uzlu, můžeme optimalizovat výkon vyhledávání.

![Připojení všesměrového vysílání](media/data-flow/broadcast.png "Vysílací spojení")

### <a name="broadcast-join"></a>Připojení všesměrového vysílání

Vyberte levé nebo pravé straněné připojení všesměrového vysílání, které vyžádáte ADF a nahrajte celou datovou sadu z obou stran relace vyhledávání do paměti. U menších datových sad to může výrazně zlepšit výkon vyhledávání.

### <a name="data-partitioning"></a>Dělení dat

Rozdělení dat můžete zadat také tak, že na kartě optimalizace v transformaci vyhledávání vyberete možnost nastavit rozdělení, aby se vytvořily sady dat, které mohou být pro jednotlivé pracovní procesy lépe přizpůsobeny paměti.

## <a name="next-steps"></a>Další kroky

* [](data-flow-join.md) Transformace a [Exists existují](data-flow-exists.md) podobné úkoly v toku dat mapování ADF. Podívejte se na následující transformace.
* Rozdělení řádků na párové a neshodné hodnoty pomocí [podmíněného rozdělení](data-flow-conditional-split.md) s ```isMatch()```

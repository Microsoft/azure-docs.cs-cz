---
title: Azure Data Factory transformace vyhledávání toku dat
description: Azure Data Factory transformace vyhledávání toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 25d8588f8e2c968dc2516938263aaa7d6ddcff13
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387859"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory transformace vyhledávání toku dat

Pomocí vyhledávání můžete přidat referenční data z jiného zdroje do toku dat. Transformace vyhledávání vyžaduje definovaný zdroj, který odkazuje na vaši referenční tabulku a odpovídá na klíčová pole.

![Transformace vyhledávání](media/data-flow/lookup1.png "Vyhledávání")

Vyberte klíčová pole, která chcete porovnat mezi poli příchozích datových proudů a poli ze zdroje odkazů. Nejdřív je potřeba vytvořit nový zdroj na plátně pro návrh toku dat, který se použije jako pravá strana pro vyhledávání.

Když se najde shody, výsledné řádky a sloupce z reference source se přidají do toku dat. Můžete zvolit, která pole zájmu chcete zahrnout do jímky na konci toku dat. Případně můžete použít transformaci SELECT po vyhledávání a vyřadit seznam polí, aby zůstala pouze pole z obou datových proudů, které chcete zachovat.

Transformace vyhledávání provádí ekvivalent levého vnějšího spojení. Zobrazí se všechny řádky z levého zdroje společně s shodami z pravé strany. Pokud máte ve svém vyhledávání více hodnot, nebo pokud chcete výraz pro vyhledávání přizpůsobit, je vhodnější přepnout na transformaci spojení a použít vzájemné spojení. Tím se vyhnete případným chybám kartézském produktu při spuštění.

## <a name="match--no-match"></a>Shoda/bez shody

Po transformaci vyhledávání můžete použít následnou transformaci k zkontrolování výsledků každého řádku shody pomocí funkce Expression `isMatch()` pro další volby v logice na základě toho, zda hledání vedlo ke shodě mezi řádky nebo nikoli.

## <a name="optimizations"></a>Optimalizace

V Data Factory jsou toky dat spouštěny v prostředích Spark s horizontálním škálováním. Pokud se vaše datová sada vejde do paměťového prostoru pracovního uzlu, můžeme optimalizovat výkon vyhledávání.

![Připojení všesměrového vysílání](media/data-flow/broadcast.png "Připojení všesměrového vysílání")

### <a name="broadcast-join"></a>Připojení všesměrového vysílání

Vyberte levé nebo pravé straněné připojení všesměrového vysílání, které vyžádáte ADF a nahrajte celou datovou sadu z obou stran relace vyhledávání do paměti. U menších datových sad to může výrazně zlepšit výkon vyhledávání.

### <a name="data-partitioning"></a>Dělení dat

Rozdělení dat můžete zadat také tak, že na kartě optimalizace v transformaci vyhledávání vyberete možnost nastavit rozdělení, aby se vytvořily sady dat, které mohou být pro jednotlivé pracovní procesy lépe přizpůsobeny paměti.

## <a name="next-steps"></a>Další kroky

[](data-flow-join.md) Transformace a [Exists existují](data-flow-exists.md) podobné úkoly v toku dat mapování ADF. Podívejte se na následující transformace.

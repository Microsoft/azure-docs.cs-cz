---
title: Azure Data Factory transformace vyhledávání toku dat
description: Azure Data Factory transformace vyhledávání toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d762bddbe098e30cbf9e9c02da3c06073a358b12
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249257"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory transformace vyhledávání toku dat

Pomocí vyhledávání můžete přidat referenční data z jiného zdroje do toku dat. Transformace vyhledávání vyžaduje definovaný zdroj, který odkazuje na vaši referenční tabulku a odpovídá na klíčová pole.

(media/data-flow/lookup1.png "Vyhledávání") ![transformace vyhledávání]

Vyberte klíčová pole, která chcete porovnat mezi poli příchozích datových proudů a poli ze zdroje odkazů. Nejdřív je potřeba vytvořit nový zdroj na plátně pro návrh toku dat, který se použije jako pravá strana pro vyhledávání.

Když se najde shody, výsledné řádky a sloupce z reference source se přidají do toku dat. Můžete zvolit, která pole zájmu chcete zahrnout do jímky na konci toku dat. Případně můžete použít transformaci SELECT po vyhledávání a vyřadit seznam polí, aby zůstala pouze pole z obou datových proudů, které chcete zachovat.

Transformace vyhledávání provádí ekvivalent levého vnějšího spojení. Zobrazí se všechny řádky z levého zdroje společně s shodami z pravé strany. Pokud máte ve svém vyhledávání více hodnot, nebo pokud chcete výraz pro vyhledávání přizpůsobit, je vhodnější přepnout na transformaci spojení a použít vzájemné spojení. Tím se vyhnete případným chybám kartézském produktu při spuštění.

## <a name="match--no-match"></a>Shoda/bez shody

Po transformaci vyhledávání můžete použít následnou transformaci k zkontrolování výsledků každého řádku shody pomocí funkce Expression `isMatch()` pro další volby v logice na základě toho, zda hledání vedlo ke shodě mezi řádky nebo nikoli.

## <a name="optimizations"></a>Optimalizace

V Data Factory jsou toky dat spouštěny v prostředích Spark s horizontálním škálováním. Pokud se vaše datová sada vejde do paměťového prostoru pracovního uzlu, můžeme optimalizovat výkon vyhledávání.

Připojení všesměrového vysílání ![připojení](media/data-flow/broadcast.png "všesměrového") vysílání

### <a name="broadcast-join"></a>Připojení všesměrového vysílání

Vyberte levé nebo pravé straněné připojení všesměrového vysílání, které vyžádáte ADF a nahrajte celou datovou sadu z obou stran relace vyhledávání do paměti. U menších datových sad to může výrazně zlepšit výkon vyhledávání.

### <a name="data-partitioning"></a>Dělení dat

Rozdělení dat můžete zadat také tak, že na kartě optimalizace v transformaci vyhledávání vyberete možnost nastavit rozdělení, aby se vytvořily sady dat, které mohou být pro jednotlivé pracovní procesy lépe přizpůsobeny paměti.

## <a name="next-steps"></a>Další kroky

[](data-flow-join.md) Transformace a [Exists existují](data-flow-exists.md) podobné úkoly v toku dat mapování ADF. Podívejte se na následující transformace.

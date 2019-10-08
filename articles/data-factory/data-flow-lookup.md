---
title: Azure Data Factory transformace vyhledávání toku dat
description: Azure Data Factory transformace vyhledávání toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029340"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory transformace vyhledávání toku dat



Pomocí vyhledávání můžete přidat referenční data z jiného zdroje do toku dat. Transformace vyhledávání vyžaduje definovaný zdroj, který odkazuje na vaši referenční tabulku a odpovídá na klíčová pole.

(media/data-flow/lookup1.png "Vyhledávání") ![transformace vyhledávání]

Vyberte klíčová pole, která chcete porovnat mezi poli příchozích datových proudů a poli ze zdroje odkazů. Nejdřív je potřeba vytvořit nový zdroj na plátně pro návrh toku dat, který se použije jako pravá strana pro vyhledávání.

Když se najde shody, výsledné řádky a sloupce z reference source se přidají do toku dat. Můžete zvolit, která pole zájmu chcete zahrnout do jímky na konci toku dat.

## <a name="match--no-match"></a>Shoda/bez shody

Po transformaci vyhledávání můžete použít následnou transformaci k zkontrolování výsledků každého řádku shody pomocí funkce Expression `isMatch()` pro další volby v logice na základě toho, zda hledání vedlo ke shodě mezi řádky nebo nikoli.

## <a name="optimizations"></a>Optimalizace

V Data Factory jsou toky dat spouštěny v prostředích Spark s horizontálním škálováním. Pokud se vaše datová sada vejde do paměťového prostoru pracovního uzlu, můžeme optimalizovat výkon vyhledávání.

Připojení všesměrového vysílání ![připojení](media/data-flow/broadcast.png "všesměrového") vysílání

### <a name="broadcast-join"></a>Připojení všesměrového vysílání

Vyberte levé nebo pravé straněné připojení všesměrového vysílání, které vyžádáte ADF a nahrajte celou datovou sadu z obou stran relace vyhledávání do paměti.

### <a name="data-partitioning"></a>Dělení dat

Rozdělení dat můžete zadat také tak, že na kartě optimalizace v transformaci vyhledávání vyberete možnost nastavit rozdělení, aby se vytvořily sady dat, které mohou být pro jednotlivé pracovní procesy lépe přizpůsobeny paměti.

## <a name="next-steps"></a>Další kroky

[](data-flow-join.md) Transformace a [Exists existují](data-flow-exists.md) podobné úkoly v toku dat mapování ADF. Podívejte se na následující transformace.

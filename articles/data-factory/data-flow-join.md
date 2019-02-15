---
title: Azure Data Factory Data toku spojení transformace
description: Azure Data Factory Data toku spojení transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 517afe21fbf9241e2b2423525e9caee12a5603f6
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271669"
---
# <a name="azure-data-factory-data-flow-join-transformation"></a>Azure Data Factory Data toku spojení transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Kombinovat data ze dvou tabulek v toku dat pomocí spojení. Klikněte na transformaci, která levé vztah, který se přidat připojení k transformaci z panelu nástrojů. Uvnitř spojení transformací vyberete jiný datový proud z toku dat a být správné relace.

![Připojte se k transformaci](media/data-flow/join.png "připojení")

## <a name="join-types"></a>Připojte se k typy

Vyberte typ spojení se vyžaduje pro připojení k transformaci

### <a name="inner-join"></a>Vnitřní spojení

Vnitřní spojení předá pouze řádky, které splňují podmínky sloupců z obou tabulek

### <a name="left-outer"></a>Levé vnější

Všechny řádky z levé datového proudu nesplnění podmínek smluv podmínka spojení se předaly a výstupní sloupce z jiné tabulky jsou nastaveny na hodnotu NULL, kromě všechny řádky vrácené vnitřní spojení.

### <a name="right-outer"></a>Pravé vnější

Všechny řádky z pravé datového proudu nesplnění podmínek smluv podmínka spojení se předaly a výstupní sloupce, které odpovídají druhé tabulce jsou nastaveny na hodnotu NULL, kromě všechny řádky vrácené vnitřního spojení.

### <a name="full-outer"></a>Úplné vnější

Úplné vnější vytvoří všechny sloupce a řádky z obou stran s hodnotami NULL pro sloupce, které není k dispozici v dalších table

### <a name="cross-join"></a>Křížové spojení

Specifické pro smíšený produkt dvou datových proudů s výrazem

## <a name="specify-join-conditions"></a>Zadejte podmínky spojení

Podmínka Left Join je z datových proudů připojená vlevo od vašeho připojení. Podmínka Right Join je druhý datový proud připojené k vaší spojení v dolní části, který bude konektor s přímým přístupem do jiného datového proudu nebo odkaz na jiný datový proud.

Je nutné zadat alespoň 1 podmínky připojení (1.. n). Je možné pole, které jsou buď přímo odkazovat, vybrat z rozevírací nabídky nebo výrazy.

## <a name="join-performance-optimizations"></a>Připojte se k optimalizaci výkonu

Na rozdíl od sloučení spojení v nástroje, jako jsou služby SSIS spojení v toku dat ADF není povinné sloučení operace spojení. Klíče spojení proto není potřeba seřazeny nejprve. Operace spojení dojde ve Sparku pomocí Databricks založené na operaci optimální připojení ve Sparku: Připojení k vysílání / straně mapy:

![Připojte se k transformaci optimalizovat](media/data-flow/joinoptimize.png "připojte se k optimalizaci")

Pokud vaše datová sada se vejde do paměti Databricks pracovní uzel, doporučujeme optimalizovat výkon spojení. Můžete také určit, dělení dat na operace spojení k vytvoření sad dat, která lépe vejde do paměti na jeden pracovního procesu.

## <a name="self-join"></a>Spojení sama na sebe

Spojení sama na sebe podmínky v toku dat ADF můžete dosáhnout použitím vyberte transformace na alias existující datový proud. Nejprve vytvořte "Novou větev" z datového proudu a pak přidat, vyberte na alias celý původního datového proudu.

![Spojení sama na sebe](media/data-flow/selfjoin.png "spojení sama na sebe")

V diagramu vyberte transformací, která je v horní části. Veškeré její probíhající činnosti je aliasing původního datového proudu na "OrigSourceBatting". V zvýrazněné spojení transformací, která se pod ní uvidíte, že používáme tento datový proud vyberte alias jako pravé spojení, abychom mohli odkazovat na stejný klíč v levé a pravé straně vnitřního spojení.

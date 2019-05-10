---
title: 'Importovat z webové adresy URL prostřednictvím protokolu HTTP: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití importovat z webové adresy URL prostřednictvím modulu HTTP služby Azure Machine Learning na čtení dat z veřejné webové stránky pro použití v experimentu služby machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bff913efb38c9e5589c795386dfbbc480d799a37
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411445"
---
# <a name="import-from-web-url-via-http-module"></a>Importovat z webové adresy URL prostřednictvím modulu HTTP

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží ke čtení dat z veřejné webové stránky pro použití v experimentu služby machine learning.

Následující omezení platí pro data publikovaná na webové stránce:

- Data musí být v jednom z podporovaných formátů: CSV, TSV, ARFF nebo SvmLight. Další data způsobí chyby.
- Bez ověřování je vyžaduje nebo podporované. Data musí být veřejně dostupný. 

Existují dva způsoby, jak získat data: nastavení zdroje dat pomocí průvodce nebo nakonfigurovat ručně.

## <a name="use-the-data-import-wizard"></a>Použijte Průvodce importem dat

1. Přidat **Import dat** modulu do experimentu. V modulu v rozhraní, můžete najít v **datový vstup a výstup** kategorie.

2. Klikněte na tlačítko **spusťte Průvodce importem dat** a vyberte adresy URL webového přes protokol HTTP.

3. Vložte adresu URL a vyberte formát data.

4. Po dokončení konfigurace.

Chcete-li upravit existující datové připojení, spusťte průvodce znovu. Načte všechny podrobnosti o předchozí konfiguraci průvodce tak, že není nutné znovu spustit od začátku

## <a name="manually-set-properties-in-the-import-data-module"></a>Ručně nastavit vlastnosti v modulu Import dat

Následující kroky popisují, jak ručně nakonfigurovat zdroj pro import.

1. Přidat [Import dat](import-data.md) modulu do experimentu. V modulu v rozhraní, můžete najít v **datový vstup a výstup** kategorie.

2. Pro **zdroj dat**vyberte **adresa URL webu přes protokol HTTP**.

3. Pro **URL**, zadejte nebo vložte úplnou adresu URL stránky, která obsahuje data, které chcete načíst.

    Adresa URL by měla obsahovat úplnou cestu s názvem souboru a rozšíření, na stránce, která obsahuje data pro načtení a adresu URL webu.

    Datové sady Iris z strojového učení úložiště na University of California, Irvine obsahuje například na následující stránce:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Pro **formát dat**, vyberte jednu z podporovaných data formátů ze seznamu.

    Doporučujeme vždy zkontrolujte data předem určit formát. Na stránce UC Irvine používá formát sdíleného svazku clusteru. Další formáty dat podporovaných jsou TSV ARFF a SvmLight.

5. Pokud jsou data ve formátu CSV, TSV, použijte **soubor má řádek záhlaví** možnost označuje, zda zdroj dat obsahuje záhlaví řádku. Řádek záhlaví slouží k přiřazení názvy sloupců.

6. Vyberte **použití mezipaměti výsledky** možnosti Pokud neočekáváte dat změnit, nebo pokud chcete, aby se zabránilo opětovné načtení dat jednotliví při spuštění experimentu.

    Pokud je vybraná tato možnost, experiment načte data první čas modulu je spuštěn a poté používá verze uložené v mezipaměti datové sady.

    Pokud chcete znovu načíst datovou sadu na každé iterace experimentu datové sady, zrušte výběr **použití mezipaměti výsledky** možnost. Výsledky jsou také znovu načteny nejsou žádné změny parametrů [Import dat](import-data.md).

7. Spusťte experiment.

## <a name="results"></a>Výsledky

Jakmile budete hotovi, klikněte na výstupní datovou sadu a vyberte **vizualizovat** zobrazíte, pokud data byla úspěšně importována.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 
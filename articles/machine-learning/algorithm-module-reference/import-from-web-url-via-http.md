---
title: 'Importovat z webové adresy URL přes HTTP: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat službu import z webové adresy URL přes modul HTTP ve službě Azure Machine Learning ke čtení dat z veřejné webové stránky pro použití v experimentu machine learningu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128760"
---
# <a name="import-from-web-url-via-http-module"></a>Import z webové adresy URL přes modul HTTP

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Tento modul slouží ke čtení dat z veřejné webové stránky pro použití v experimentu strojového učení.

Následující omezení platí pro data publikovaná na webové stránce:

- Data musí být v jednom z podporovaných formátů: CSV, TSV, ARFF nebo SvmLight. Jiná data způsobí chyby.
- Není vyžadováno nebo není podporováno ověřování. Data musí být veřejně dostupná. 

Existují dva způsoby, jak získat data: pomocí průvodce nastavte zdroj dat nebo ho nakonfigurujte ručně.

## <a name="use-the-data-import-wizard"></a>Použití Průvodce importem dat

1. Přidejte modul **Import dat** do experimentu. Modul můžete najít v rozhraní v kategorii **vstup a výstup dat** .

2. Klikněte na tlačítko **Spustit Průvodce importem dat** a vyberte možnost Webová adresa URL prostřednictvím protokolu HTTP.

3. Vložte adresu URL a vyberte formát dat.

4. Po dokončení konfigurace.

Chcete-li upravit existující datové připojení, spusťte průvodce znovu. Průvodce načte všechny předchozí podrobnosti o konfiguraci, takže se nemusíte znovu spouštět od začátku.

## <a name="manually-set-properties-in-the-import-data-module"></a>Ručně nastavit vlastnosti v modulu import dat

Následující postup popisuje, jak ručně nakonfigurovat zdroj importu.

1. Přidejte modul [Import dat](import-data.md) do experimentu. Modul můžete najít v rozhraní v kategorii **vstup a výstup dat** .

2. Jako **zdroj dat**vyberte **Webová adresa URL přes http**.

3. Do pole **Adresa URL**zadejte nebo vložte úplnou adresu URL stránky, která obsahuje data, která chcete načíst.

    Adresa URL by měla obsahovat adresu URL webu a úplnou cestu s názvem a příponou souboru na stránku, která obsahuje data, která se mají načíst.

    Například následující stránka obsahuje datovou sadu Iris z úložiště Machine Learning pro University of Kalifornie, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. V poli **Formát dat**vyberte jeden z podporovaných formátů dat ze seznamu.

    Pro určení formátu doporučujeme vždy, když data předáte předem. Stránka Irvine UC používá formát CSV. Další podporované formáty dat jsou TSV, ARFF a SvmLight.

5. Pokud jsou data ve formátu CSV nebo TSV, použijte k určení, jestli zdrojová data obsahují řádek záhlaví, možnost **soubor má řádek** záhlaví. Řádek záhlaví slouží k přiřazení názvů sloupců.

6. Vyberte možnosti **použít výsledky uložené v mezipaměti** , pokud neočekáváte, že se data mění, nebo pokud se chcete vyhnout opětovnému načítání dat při každém spuštění experimentu.

    Pokud je vybrána tato možnost, experiment načte data při prvním spuštění modulu a poté použije verzi datové sady uloženou v mezipaměti.

    Pokud chcete datovou sadu znovu načíst pro každou iteraci datové sady experiment, zrušte výběr možnosti **použít výsledky v mezipaměti** . Pokud jsou v parametrech [importu dat](import-data.md)nějaké změny, jsou výsledky také znovu načteny.

7. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení klikněte na výstupní datovou sadu, vyberte **vizualizovat** a ověřte, jestli se data úspěšně importovala.


## <a name="next-steps"></a>Další postup

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
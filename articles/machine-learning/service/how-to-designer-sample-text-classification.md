---
title: 'Návrhář: příklad klasifikace knihy klasifikace'
titleSuffix: Azure Machine Learning
description: Sestavte si klasifikátor pro více tříd logistické regrese pro předpověď kategorie společnosti s datovou sadou Wikipedii SP 500 pomocí návrháře Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 43545c2d3bb3afe4e1c458f14c1ba30e41eea721
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196005"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Sestavte klasifikátor pro předpověď kategorie společnost pomocí návrháře Azure Machine Learning.

**Návrhář (Preview) – ukázka 7**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Tato ukázka předvádí, jak použít moduly Text Analytics k sestavení kanálu klasifikace textu v Návrháři Azure Machine Learning (Preview).

Cílem klasifikace textu je přiřazení části textu k jedné nebo více předdefinovaným třídám nebo kategoriím. Část textu může být dokument, novinka, vyhledávací dotaz, e-mail, seznam vstupenek, lístky podpory, názory zákazníků, Uživatelská recenze produktu atd. Mezi aplikace pro klasifikaci textu patří kategorizace článků o novinkách a obsahu novinek do témat, organizování webových stránek do hierarchických kategorií, filtrování nevyžádané pošty, analýzy mínění, předpověď záměru uživatele z vyhledávacích dotazů, směrování Podpora lístků a analýza zpětné vazby od zákazníků. 

Tento kanál navlacích **klasifikátoru logistické regrese** pro předpověď kategorie společnosti s **datovou sadou Wikipedii SP 500 odvozenou od Wikipedii**.  

Základní kroky pro školicí model strojového učení s textovými daty jsou:

1. Získání dat

1. Předběžné zpracování textových dat

1. Strojírenství funkcí

   Převede funkci text na číselnou funkci s modulem pro extrakci funkcí, jako je například hashování funkcí, extrahuje n-gram funkce z textových dat.

1. Trénování modelu

1. Datová sada skóre

1. Vyhodnocení modelu

Tady je konečný a dokončený Graf kanálu, na kterém budeme pracovat. Pro všechny moduly poskytneme odůvodnění, abyste mohli dělat podobná rozhodnutí sami.

[![Graf kanálu](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png)](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Data

V tomto kanálu použijeme datovou sadu **WIKIPEDII SP 500** . Datová sada je odvozena od Wikipedii (https://www.wikipedia.org/) na základě článků z každé & P 500 společnosti. Před nahráním do návrháře Azure Machine Learning byla datová sada zpracována takto:

- Extrakce textového obsahu pro každou konkrétní firmu
- Odebrat formátování wikiwebu
- Odebrat jiné než alfanumerické znaky
- Převést veškerý text na malá písmena
- Byly přidány známé kategorie společnosti.

Pro některé společnosti se nepovedlo najít články, takže počet záznamů je menší než 500.

## <a name="pre-process-the-text-data"></a>Předběžné zpracování textových dat

K Předzpracování textových dat používáme modul **textu předzpracování** , včetně rozpoznávání vět, vět tokenizovat a tak dále. Všechny podporované možnosti najdete v článku [**předzpracování textu**](../algorithm-module-reference/preprocess-text.md) . Po předběžném zpracování dat TEX používáme modul **rozdělit data** k náhodnému rozdělení vstupních dat, aby datová sada školení obsahovala 50% původních dat a testovací datová sada obsahuje 50% původních dat.

## <a name="feature-engineering"></a>Strojírenství funkcí
V této ukázce použijeme dvě metody, které provádí strojírenství funkcí.

### <a name="feature-hashing"></a>Hashování funkcí
Použili jsme modul [**hashování funkcí**](../algorithm-module-reference/feature-hashing.md) k převodu prostého textu článků na celá čísla a použití celočíselných hodnot jako vstupních funkcí modelu. 

Modul **hashování funkcí** lze použít k převodu textových dokumentů s proměnlivou délkou na numerické vektory funkce se stejnou délkou, a to pomocí metody hash 32-bit murmurhash v3, kterou poskytuje knihovna dostupné pro. Cílem použití funkce hashing funkcí je zmenšení rozměru; také funkce hashing funkcí umožňuje rychleji vyhledat váhy funkcí v době klasifikace, protože místo porovnání řetězců používá porovnání hodnot hash.

V ukázkovém kanálu nastavíme počet bitů hash na hodnotu 14 a nastavíte počet n-gramů na 2. V případě těchto nastavení může tabulka hash uchovávat 2 ^ 14 záznamů, ve kterých každá funkce hash představuje jednu nebo více n-gramů funkcí a její hodnota představuje četnost výskytů n-gramů v instanci textu. V případě mnoha problémů je zatřiďovací tabulka této velikosti větší než adekvátní, ale v některých případech může být potřeba více místa pro zamezení kolizí. Vyhodnoťte výkon řešení Machine Learning pomocí různých počtu bitů. 

### <a name="extract-n-gram-feature-from-text"></a>Extrahovat z textu funkci N-gramů

N-gram je souvislá sekvence n podmínek z dané sekvence textu. N-gram velikosti 1 se označuje jako unigram; n-gram velikosti 2 je bigram; n-gram velikosti 3 je Hiragana. N-gramům větších velikostí se někdy říká hodnota n, například "4 gramy", "pět-gram" atd.

V modulu [**textu jsme použili funkci pro extrakci N-gramů**](../algorithm-module-reference/extract-n-gram-features-from-text.md)jako jiné řešení pro strojírenství funkcí. Tento modul nejprve extrahuje sadu n-gramů, kromě n-gramů, počet dokumentů, ve kterých se každý n-gram zobrazí v textu, počítá (DF). V této ukázce se k výpočtu hodnot funkcí používá metrika TF-IDF. Pak převede nestrukturovaná textová data na numerické funkce, které mají stejnou délku, kde každá funkce představuje TF-IDF n-gram v instanci text.

Po převodu textových dat na číselné vektory funkce se k odebrání textových dat z datové sady použije modul **výběrového sloupce** . 

## <a name="train-the-model"></a>Trénování modelu

Vaše volba algoritmu často závisí na požadavcích případu použití. Vzhledem k tomu, že cílem tohoto kanálu je předpověď kategorie společnosti, je model klasifikátoru více tříd dobrou volbou. Vzhledem k tomu, že počet funkcí je velký a že se jedná o zhuštěné funkce, používáme pro tento kanál model **logistické regrese** .

## <a name="test-evaluate-and-compare"></a>Testování, vyhodnocení a porovnání

 Datovou sadu rozdělíme a použijeme různé datové sady ke školení a testování modelu, aby bylo vyhodnocení modelu více objektivně.

Po vyzkoušení modelu budeme k vygenerování předpokládaných výsledků a vyhodnocení modelů používat **model skóre** a **vyhodnocovat moduly modelů** . Před použitím modulu **skóre model** se ale při provádění školení vyžaduje, aby se prováděla technologie pro práci s funkcemi. 

Pro modul **hashování funkcí** je snadné provádět inženýry funkcí v toku bodování jako školicí tok. Použijte modul pro **Vyhashení funkcí** přímo ke zpracování vstupních textových dat.

V případě **extrakce N-gramů z modulu textu** připojíme **výstup slovníku výsledků** z toku dat školení ke **vstupnímu slovníku** v toku dat bodování a nastavíte parametr **režimu slovníku** na **jen pro čtení.** .
[![graf skóre n-gramů](./media/how-to-ui-sample-text-classification/n-gram.png)](./media/how-to-ui-sample-text-classification/n-gram.png)

Po dokončení technického kroku se dá **model skóre** použít k vygenerování předpovědi pro testovací datovou sadu pomocí proučeného modelu. Pro kontrolu výsledku vyberte výstupní port **modelu skóre** a pak vyberte **vizualizovat**.

Pak předáte skóre do modulu **vyhodnocení modelu** za účelem generování metrik vyhodnocení. **Model vyhodnocení** má dva vstupní porty, aby bylo možné vyhodnotit a porovnat datové sady s skóre, které jsou generovány různými metodami. V této ukázce porovnáváme výkon výsledku generovaného metodou hashování funkcí a n-gramem metody.
Chcete-li zjistit výsledek, vyberte výstupní port **modelu vyhodnocení** a pak vyberte **vizualizovat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro návrháře:
- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-designer-sample-regression-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů](how-to-designer-sample-classification-predict-income.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: předpověď změn](how-to-designer-sample-classification-churn.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-designer-sample-classification-flight-delay.md)

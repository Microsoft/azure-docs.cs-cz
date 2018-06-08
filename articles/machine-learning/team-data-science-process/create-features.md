---
title: Konstruování v vědecké zpracování dat | Microsoft Docs
description: Popisuje účely funkce inženýrství a obsahuje příklady jeho role v proces vylepšení dat machine learning.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 3fde69e8-5e7b-49ad-b3fb-ab8ef6503a4d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: b4194ef5ab1c2c09206ea0acf78cb539bc2fc0b7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836513"
---
# <a name="feature-engineering-in-data-science"></a>Funkce analýzy v vědecké zpracování dat
Tento článek popisuje účely funkce inženýrství a obsahuje příklady jeho role v proces vylepšení dat machine learning. V příkladech používají pro ilustraci tohoto procesu jsou vykreslovány z Azure Machine Learning Studio. 

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **nabídky** odkazy na články, které popisují, jak vytvořit funkce pro data v různých prostředích. Tato úloha je krok v [tým datové vědy procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Funkce engineering pokusy o zvýšení předpovídat učení algoritmy vytvořením funkce z nezpracovaná data, která pomáhají zjednodušit proces učení. Technici a výběr funkcí je jednou ze součástí sady TDSP uvedených v [co je životní cyklus proces vědecké účely Team dat?](overview.md) Funkce inženýrství a výběr jsou součástí **vyvíjet funkce** krok TDSP. 

* **konstruování**: Tento proces se pokusí vytvořit další relevantní funkce z existující nezpracovaná funkce v datech a ke zvýšení předpovídat algoritmus učení.
* **funkce Výběr**: Tento proces vybere klíče podmnožinu funkcí, původní data ve snaze snížit dimenzionalitu problému školení.

Za normálních okolností **konstruování** se použije první ke generování další funkce a potom **funkci Výběr** krok se provádí eliminovat důležité, redundantní nebo vysoce korelační funkce.

Jsou Cvičná data používané pro strojové učení se dá vylepšit často podle extrakce funkcí z nezpracovaná data shromážděná. Příklad inženýrství funkce v kontextu naučit, jak klasifikovat bitové kopie ručně psané znaky je vytvoření trochu hustotu mapy vytvářejí na základě data distribuce nezpracovaná bit. Tato mapa může pomoci najít okrajů znaky efektivnější než jednoduše přímo pomocí nezpracovaná rozdělení.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Vytvoření funkce z vašich dat – konstruování
Jsou Cvičná data se skládá z matice tvořený příklady (záznamy nebo připomínky, které jsou uložené v řádcích), z nichž každý má sadu funkcí (proměnné nebo pole, které jsou uloženy ve sloupcích). Očekává se, že funkce zadané v experimentální návrhu charakterizovat vzorů v datech. I když mnoho polí nezpracovaná data mohou být přímo součástí sadu vybranou funkci použity při cvičení modelu, je případ, že je potřeba další (inženýrství) funkce zkonstruovat z funkcí v nezpracovaná data pro generování datové sadě služby rozšířené školení.

Jaký druh funkce by měl být vytvořen při tréninku modelu vylepšit datovou sadu? Inženýrství funkce, které zlepšují školení poskytují informace, které lépe odlišuje vzorů v datech. Očekává se, že nové funkce poskytují další informace, které není jasně zaznamenané nebo snadno zřejmá v sadě původního nebo stávající funkce. Ale tento proces je něco obrázky. Rozhodnutí o zvukové a produktivitu často vyžadují znalosti některé domény.

Při spouštění pomocí Azure Machine Learning, je to nejjednodušší provádět uchopit tento proces namítají pomocí ukázky součástí nástroje Studio. Zde jsou uvedeny dva příklady:

* Příklad regrese [předpovědi počet kolo pronájem](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) v pod dohledem experimentu, kde jsou známé cílové hodnoty
* Pomocí příkladu text dolování klasifikace [Hashování](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Příklad 1: Přidání dočasné funkcí pro regresní model
Umožňuje pomocí experimentu "vyžádání prognózy z kol" v nástroji Azure Machine Learning Studio ukazují, jak analyzovat funkce pro úlohu regrese. Cílem tohoto experimentu je k předvídání požadavků na kol, který je počet kolo pronájem v rámci konkrétní měsíc/den/hodinu. Datovou sadu "kolo pronájem UCI datovou sadu" slouží jako nezpracovaná vstupní data. Tato datová sada je založená na skutečná data od společnosti Bikeshare velké, která udržuje síť kolo pronájem v Washington DC ve Spojených státech amerických. Datové sady v rámci konkrétní hodinu dne v roce 2011 a roce 2012 představuje počet kolo pronájem a obsahuje 17379 řádků a sloupců 17. Nezpracovaná funkce sada obsahuje počasí (teplotní/vlhkosti/větru rychlost) a typ den (svátek nebo den v týdnu). Pole k předpovědi je "cnt" počet, který reprezentuje pronájem kolo v rámci konkrétní hodinu a které rozsahy od 1 do 977.

S cílem vytváření efektivní funkce jsou Cvičná data, čtyři regrese, který modely jsou vytvořeny pomocí stejného algoritmu, ale s datovými sadami čtyř různých školení. Čtyři datové sady představují stejnou nezpracovaná vstupní data, ale se zvyšující číslo funkce nastavit. Tyto funkce jsou rozděleny do čtyř kategorií:

1. A = počasí + svátek + den v týdnu + předpokládaných den víkendu funkce
2. B = počet kol, které byly pronajaté v každé z předchozích 12 hodin
3. C = počet kol, které byly pronajaté v každé z předchozích 12 dnů v tutéž hodinu
4. D = počet kol, které byly pronajaté v každé z předchozí 12 týdnů v tutéž hodinu a den

Kromě toho sada A funkce, která již existuje v původní nezpracovaná data, další tři sady funkcí se vytvoří pomocí funkce technici procesu. Funkci nastavit B zachycení poslední vyžádání kol. Funkci nastavit C zachycení vyžádání kol v konkrétní hodinu. Funkci nastavit D zachycení vyžádání kol na konkrétní hodiny a určitý den v týdnu. Čtyři školení datové sady každý zahrnuje funkce sada A, A + B, A + B + C a A + B + C + D.

V rámci experimentu Azure Machine Learning jsou tyto čtyři datové sady školení formátu prostřednictvím čtyři větve z předem zpracovaných vstupní datové sady. S výjimkou krajní levé větve, každý z těchto větve obsahuje [spustit skript jazyka R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modul, ve kterém jsou odvozené funkcí (funkci nastavit B, C a D) v uvedeném pořadí sestavený a připojeno k importované datovou sadu. Následující obrázek ukazuje skriptu jazyka R použít k vytvoření sada funkcí B v druhé levém větve.

![Vytvoření funkce](./media/create-features/addFeature-Rscripts.png)

Porovnání výkonu výsledky čtyři modely je shrnuto v následující tabulce: 

![porovnání výsledků](./media/create-features/result1.png)

K dosažení nejlepších výsledků se zobrazí funkce A + B + C. Všimněte si, že snižuje míra chyb při sada dalších funkcí jsou součástí jsou Cvičná data. Ověří, předpokládá, že sada funkcí B, C poskytovat další relevantní informace pro regresní úlohu. Ale přidávání funkci D zřejmě není zadejte jakékoli další snížení míra chyb.

## <a name="example2"></a> Příklad 2: Vytvoření funkce v textu dolování
Funkce inženýrství je široce použity v úlohy související s dolování text, například klasifikace a postojích analýzy dokumentu. Například pokud chcete klasifikovat dokumenty do několika kategorií, typické předpokladem je, že jsou zahrnuté v jedné kategorii doc slova nebo fráze méně pravděpodobné, ke kterým došlo v jiné kategorii dokumentů. Jinými slovy frekvence rozdělení slova nebo fráze se moci charakterizovat dokumentu různých kategorií. V aplikacích dolování text protože jednotlivé obsah textu obvykle slouží jako vstupní data funkci technici procesu je nutné vytvořit funkcí zahrnující frekvencí slovo nebo frázi.

K dosažení této úlohy, o techniku názvem **hashování** se použije pro efektivní zapnout funkce libovolný text do indexů. Místo přidružení jednotlivých funkcí text (slova nebo fráze) do konkrétního indexu, tato metoda funkce použití funkce hash pro funkce a použitím jejich hodnoty hash jako indexy, které přímo.

V Azure Machine Learning je [Hashování](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul, který vytvoří tyto slovo nebo frázi funkce pohodlně. Následující obrázek znázorňuje příklad použití tohoto modulu. Vstupní datové sady obsahuje dva sloupce: hodnocení kniha od 1 do 5 a zkontrolujte skutečný obsah. Cílem tohoto [Hashování](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modulu je načíst spoustu nové funkce, které zobrazují četnost výskytu slova odpovídající / zkontrolujte phrase(s) v rámci konkrétního seznamu. Pokud chcete používat tento modul, proveďte následující kroky:

* Nejprve vybrat sloupec, který obsahuje vstupní text ("Col2" v tomto příkladu).
* Druhý, nastavte "Hashing bitsize" na 8, což znamená, že 2 ^ 8 = 256 funkcí se vytvoří. Word či fázi v veškerý text se rozdělí na 256 indexy. Parametr "Hashing bitsize" rozsahy od 1 do 31. Hledaný text / phrase(s) jsou méně pravděpodobné, že se rozdělily do stejné indexu, pokud nastavení, že bude vyšší číslo.
* Třetí nastavte parametr "N-gram" 2. Tato hodnota získá četnost výskytu unigrams (funkce pro každý jednoslovné) a bigrams (funkce pro každý pár slov sousední) ze vstupního textu. Parametr "N-gram" rozsah od 0 do 10, která určuje maximální počet po sobě jdoucích slova mají být zahrnuty do funkce.  

![Modul "Funkci Hashing"](./media/create-features/feature-Hashing1.png)

Následující obrázek znázorňuje, co tyto nové funkce vypadají.

![Příklad "Funkci Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Závěr
Funkce inženýrství a vybrané zvýšit efektivitu proces školení, která se pokusí o extrahování klíčových informací obsažených v datech. Také zvyšují výkon těchto modelů přesně klasifikovat vstupních dat a k předvídání více robustní výsledky, které vás zajímají. Funkce inženýrství a výběr můžete také kombinovat aby výukové výpočetně tractable. Dělá to tak rozšíření a pak snižuje počet funkcí, které jsou potřeba k nakalibrovat nebo cvičení modelu. Funkce vybrané pro trénování modelu matematicky platí, že jsou minimální sadu nezávislých proměnných, které popisují vzorů v datech a pak úspěšně předpovědi výstupy.

Není vždy nutně provést výběr funkce inženýrství nebo funkce. Jestli je potřeba, nebo ne závisí na data na straně nebo shromážděných, algoritmus vybrané a cílem experimentu.


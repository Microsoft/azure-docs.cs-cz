---
title: Strojírenství funkcí v oblasti datových věd – vědecký proces týmového zpracování dat
description: Přečtěte si informace o technikách funkcí a její roli v procesu rozšíření dat Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 7cf97edcb2f2f7a23d5c899194d497deade1f507
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031857"
---
# <a name="feature-engineering-in-data-science"></a>Strojírenství funkcí v oblasti datových věd

V tomto článku se seznámíte s technologickými postupy a jejími rolemi v tématu zvyšování dat ve službě Machine Learning. Naučte se z ilustrované příklady vypracované z [Azure Machine Learning Studio (klasické)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) experimenty. 

* **Inženýrské funkce**: proces vytváření nových funkcí z nezpracovaných dat za účelem zvýšení prediktivního výkonu výukového algoritmu. Inženýrské funkce by měly zachytit Další informace, které nejsou v původní sadě funkcí snadno zjevné.
* **Výběr funkcí**: proces výběru klíčové sady funkcí k omezení dimenzionálního problému při školení.

Obvykle se pro generování dalších funkcí používá technologický **inženýr** , který vygeneruje další funkce a potom se provede **Výběr funkcí** , aby se vyloučily nepodstatné, redundantní nebo vysoce korelační funkce.

Technologie a výběr funkcí jsou součástí [fáze modelování](lifecycle-modeling.md) vědeckého zpracování týmových dat (TDSP). Další informace o TDSP a životním cyklu pro datové vědy najdete v tématu [co je to TDSP?](overview.md)

## <a name="what-is-feature-engineering"></a>Co je to strojírenství funkcí?

Školicí data se skládají z matice tvořené řádky a sloupci. Každý řádek v matici je sledování nebo záznam. Sloupce každého řádku jsou funkce, které popisují každý záznam. Funkce určené v experimentálním návrhu by měly charakterizovat vzory v datech.

I když mnoho nezpracovaných datových polí lze použít přímo k výuce modelu, je často nutné vytvořit další (inženýrské) funkce pro vylepšenou datovou sadu.

Inženýrské funkce, které zvyšují školení, poskytují informace, které lépe odlišují vzory v datech. Ale tento proces je něco z obrázku. Zvuková a produktivní rozhodnutí často vyžadují odbornost v doméně.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Příklad 1: Přidání dočasná funkce pro regresní model

Pojďme využít [předpověď počtu bicyklů na základě](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) experimentů v Azure Machine Learning Studio (Classic) k předvedení funkcí pro regresní úlohu. Cílem tohoto experimentu je předpovědět poptávku za kolo v určitý měsíc/den/hodinu.

### <a name="bike-rental-dataset"></a>Datová sada půjčovny kol

[Datová sada UCI pro půjčovnu kol](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) je založená na skutečných datech od společnosti sdílené na kolo, která je založená na USA. Představuje počet nájemné za kolo v konkrétní hodinu dne v letech 2011 a 2012. Obsahuje 17 379 řádků a 17 sloupců.

Neupravená sada funkcí obsahuje povětrnostní podmínky (rychlost/vlhkost/větru) a typ dne (svátek/den). Pole, které se má odhadnout, je počet, který představuje nájemné za kolo v určitou hodinu. Počítá rozsah od 1 do 977.

### <a name="create-a-feature-engineering-experiment"></a>Vytvoření technického experimentu funkcí

Díky cíli vytváření efektivních funkcí v školicích datech jsou čtyři regresní modely sestavené pomocí stejného algoritmu, ale se čtyřmi různými školicími datovými sadami. Čtyři datové sady reprezentují stejná neupravená vstupní data, ale zvyšují počet funkcí. Tyto funkce jsou seskupené do čtyř kategorií:

1. A = počasí + svátk + den a den v týdnu + víkend pro předpokládaný den
2. B = počet kol, které byly pronajaty v každé z předchozích 12 hodin
3. C = počet kol, které byly pronajaty v každém z předchozích 12 dnů ve stejnou hodinu
4. D = počet kol, které byly pronajaty v každém z předchozích 12 týdnů ve stejnou hodinu a stejný den

Kromě sady funkcí A, která již existuje v původních nezpracovaných datech, jsou vytvořeny další tři sady funkcí prostřednictvím procesu technické funkce. Sada funkcí B zachycuje poslední požadavek na kolaci. Sada funkcí C zachytí poptávku pro kolaci v určitou hodinu. Sada funkcí D zachycuje poptávku za konkrétní hodinu a konkrétní den v týdnu. Mezi čtyři datové sady školení patří sada funkcí A, A + B, A + B + C a + B + C + D v uvedeném pořadí.

### <a name="feature-engineering-using-studio-classic"></a>Strojírenství funkcí pomocí studia (Classic)

V experimentu studia (Classic) se tyto čtyři datové sady školení vytvoří prostřednictvím čtyř větví z předem zpracované vstupní datové sady. S výjimkou první větve obsahuje každá z těchto větví modul [spuštění skriptu jazyka R](/azure/machine-learning/studio-module-reference/execute-r-script) , ve kterém jsou vytvořeny a připojeny odvozené funkce (funkce, B, C a D) k importované datové sadě.

Následující obrázek ukazuje skript R, který se používá k vytvoření sady funkcí B ve druhé levé větvi.

![vytváření funkcí](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Výsledky

Porovnání výsledků těchto čtyř modelů je shrnuto v následující tabulce: 

![porovnání výsledku](./media/create-features/result1.png)

Nejlepší výsledky jsou zobrazeny funkcemi A + B + C. Rychlost chyb se snižuje, když jsou do školicích dat zahrnuty další sady funkcí. Ověřuje domněnku, že funkce nastavená B, C poskytuje další důležité informace pro regresní úlohu. Přidání funkce D ale pravděpodobně neposkytuje žádné další snížení míry chyb.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Příklad 2: vytvoření funkcí pro dolování textu

V úlohách souvisejících s dolováním textu, jako je klasifikace dokumentů a analýza mínění, se běžně používá strojírenství funkcí. Vzhledem k tomu, že jednotlivé části nezpracovaného textu obvykle slouží jako vstupní data, proces technické funkce je nutný k vytvoření funkcí, které zahrnují frekvence slov a frází.

### <a name="feature-hashing"></a>Hodnoty hash funkcí

K dosažení této úlohy se použije technika označovaná jako [hodnota hash funkcí](/azure/machine-learning/studio-module-reference/feature-hashing) pro efektivní zapnutí libovolných textových funkcí v indexech. Namísto přidružování každé textové funkce (slova/fráze) k určitému indexu Tato metoda aplikuje funkci hash na funkce a pomocí jejich hodnot hash jako indexy přímo.

V nástroji Studio (Classic) je modul pro [vytřídění hodnoty hash](/azure/machine-learning/studio-module-reference/feature-hashing) , který snadno vytváří funkce slov/fráze. Následující obrázek ukazuje příklad použití tohoto modulu. Vstupní datová sada obsahuje dva sloupce: kniha ohodnocená od 1 do 5 a skutečný obsah kontroly. Cílem tohoto modulu je načíst řadu nových funkcí, které znázorňují četnost výskytů odpovídajících slov (/Phrase) v rámci určité recenze knihy. Chcete-li použít tento modul, proveďte následující kroky:

* Nejprve vyberte sloupec, který obsahuje vstupní text (v tomto příkladu "col2").
* Potom nastavte "hashing bitsize" na 8, což znamená, že se vytvoří 2 ^ 8 = 256 funkcí. Ve slově nebo ve všech textech bude hodnota hash na 256 indexy. Parametr "hashing bitsize" je v rozsahu od 1 do 31. Pokud je nastavení nastavené na větší číslo, může být v těchto slovnících (/Phrase) hodnota hash na stejný index.
* Na třetí nastavte parametr "N-gramy" na 2. Tato hodnota získá četnost výskytů unigrams (funkce pro každé jedno slovo) a bigrams (funkce pro všechny dvojice sousedících slov) od vstupního textu. Parametr "N-gramy" rozsahů od 0 do 10, který označuje maximální počet sekvenčních slov, která mají být součástí funkce.  

![Modul hashování funkcí](./media/create-features/feature-Hashing1.png)

Následující obrázek ukazuje, jak tato nová funkce vypadá.

![Příklad "hashování funkcí"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Závěr
Inženýrské a vybrané funkce zvyšují efektivitu školicího procesu, který se pokusí extrahovat klíčové informace obsažené v datech. Také zlepšují sílu těchto modelů, aby přesně klasifikaci vstupních dat a předpovědi výsledků jejich zájmu byly robustnější.

Vytváření a výběr funkcí se také můžou kombinovat, aby se učení lépe vyjímat. Tím provedete vylepšením a následným snížením počtu funkcí potřebných k kalibraci nebo výukovém modelu. Ve matematických případech jsou vybrané funkce minimální sadou nezávislých proměnných, které vysvětlují vzorce v datech a odhadují výsledky.

Nemusíte vždycky nutně provádět strojírenství funkcí nebo výběr funkcí. Závisí na datech, zvoleném algoritmu a cíli experimentu.

## <a name="next-steps"></a>Další kroky

Chcete-li vytvořit funkce pro data v konkrétních prostředích, přečtěte si následující články:

* [Vytváření funkcí pro data v SQL Server](create-features-sql-server.md)
* [Vytváření funkcí pro data v clusteru Hadoop pomocí dotazů na podregistry](create-features-hive.md)
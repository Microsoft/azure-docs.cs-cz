---
title: Strojírenství funkcí v oblasti datových věd – vědecký proces týmového zpracování dat
description: Vysvětluje účely strojírenství funkcí a poskytuje příklady jeho role v procesu rozšíření dat Machine Learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e633c5742b8a7882149a347ced46e55440cb6913
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492431"
---
# <a name="feature-engineering-in-data-science"></a>Strojírenství funkcí v oblasti datových věd
Tento článek vysvětluje účely konstrukce funkcí a poskytuje příklady jeho role v procesu rozšíření dat Machine Learning. Příklady, které slouží k ilustraci tohoto procesu, jsou vykresleny z Azure Machine Learning Studio. 

Tento úkol je krok v rámci [vědeckého zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Konstrukce funkcí se snaží zvýšit prediktivní výkon výukových algoritmů tím, že vytvoří funkce z nezpracovaných dat, které usnadňují proces učení. Strojírenství a výběr funkcí je jednou z TDSP, které jsou uvedené v části [co je životní cyklus vědeckých procesů týmu dat?](overview.md) Technologie a výběr funkcí jsou části kroku **vývoj funkcí** TDSP. 

* **inženýrské funkce**: Tento proces se pokusí vytvořit další relevantní funkce z existujících nezpracovaných funkcí v datech a zvýšit prediktivní sílu tohoto sledovacího algoritmu.
* **Výběr funkcí**: Tento proces vybere podmnožinu klíčových prvků pro původní data v pokusu snížit dimenzionální problém při výuce.

Obvykle se pro generování dalších funkcí používá **inženýr funkcí** a pak se provede krok **výběru funkcí** , který eliminuje nepodstatné, redundantní nebo vysoce korelační funkce.

Školicí údaje, které se používají ve strojovém učení, je často možné rozšířit extrakcí funkcí z shromažďovaných nezpracovaných dat. Příkladem inženýrské funkce v kontextu učení, jak klasifikovat obrázky psaných znaků, je vytvoření mapy bitové hustoty vytvořené z nezpracovaných bitových distribučních dat. Tato mapa vám může pomoci najít okraje znaků efektivněji než jednoduše pomocí nezpracované distribuce přímo.

Chcete-li vytvořit funkce pro data v konkrétních prostředích, přečtěte si následující články:

* [Vytváření funkcí pro data v SQL Server](create-features-sql-server.md)
* [Vytváření funkcí pro data v clusteru Hadoop pomocí dotazů na podregistry](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Vytváření funkcí z vašich techniků pro datové funkce
Školicí data sestávají z matice tvořené příklady (záznamy nebo pozorování uložené v řádcích), z nichž každá má sadu funkcí (proměnné nebo pole uložené ve sloupcích). U funkcí určených pro experimentální návrh se očekává, že charakterizují vzory v datech. I když mnoho nezpracovaných datových polí může být přímo zahrnuté do vybrané sady funkcí používané pro výuku modelu, často je potřeba vytvořit další (inženýrské) funkce z funkcí v nezpracovaných datech a vygenerovat tak vylepšenou datovou sadu školení.

Jaký druh funkcí je potřeba vytvořit pro vylepšení datové sady při výuce modelu? Inženýrské funkce, které zvyšují školení, poskytují informace, které lépe odlišují vzory v datech. Očekává se, že nové funkce budou poskytovat další informace, které nejsou jasně zaznamenané nebo snadno zjevné v původní nebo existující sadě funkcí. Ale tento proces je něco z obrázku. Zvuková a produktivní rozhodnutí často vyžadují určité znalosti v doméně.

Když začnete s Azure Machine Learning, je nejjednodušší tento proces snadno uchopit pomocí ukázek poskytovaných v studiu. Zde jsou uvedeny dva příklady:

* Regresní ukázkový [odhad počtu nájemních kol](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) v experimentu pod dohledem, kde jsou známé cílové hodnoty
* Příklad klasifikace dolování textu pomocí [funkce hashing funkcí](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Příklad 1: Přidání dočasná funkce pro regresní model
Pomocí experimentu "vypovídání poptávky z kol" v Azure Machine Learning Studio (Classic) předvedeme předvedení funkcí pro regresní úlohu. Cílem tohoto experimentu je předpovědět poptávku za jednotlivé kolace, tj. počet nájemné za kolo v rámci určitého měsíce/dne a hodiny. Datová sada se používá jako nezpracované vstupní údaje. Tato datová sada je založená na skutečných datech ze společnosti Bikeshare společnosti, která udržuje síť pronájmu kol v oblasti Washington DC ve USA. Datová sada představuje počet nájemné za kolo v konkrétní hodinu dne v letech 2011 a rok 2012 a obsahuje řádky 17379 a 17 sloupců. Neupravená sada funkcí obsahuje povětrnostní podmínky (rychlost/vlhkost/větru) a typ dne (svátek/den). Pole, které se má předpovědět, je počet "CNT", který představuje nájemné za kolo v konkrétní hodinu a který rozsah od 1 do 977.

Díky cíli vytváření efektivních funkcí v školicích datech jsou čtyři regresní modely sestavené pomocí stejného algoritmu, ale se čtyřmi různými školicími datovými sadami. Čtyři datové sady reprezentují stejná neupravená vstupní data, ale zvyšují počet funkcí. Tyto funkce jsou seskupené do čtyř kategorií:

1. A = počasí + svátk + den a den v týdnu + víkend pro předpokládaný den
2. B = počet kol, které byly pronajaty v každé z předchozích 12 hodin
3. C = počet kol, které byly pronajaty v každém z předchozích 12 dnů ve stejnou hodinu
4. D = počet kol, které byly pronajaty v každém z předchozích 12 týdnů ve stejnou hodinu a stejný den

Kromě sady funkcí A, která již existuje v původních nezpracovaných datech, jsou vytvořeny další tři sady funkcí prostřednictvím procesu technické funkce. Sada funkcí B zachycuje u těchto kol velmi poslední poptávku. Sada funkcí C zachytí poptávku pro kolaci v určitou hodinu. Sada funkcí D zachycuje poptávku za konkrétní hodinu a konkrétní den v týdnu. Mezi čtyři datové sady školení patří sada funkcí A, A + B, A + B + C a + B + C + D v uvedeném pořadí.

Ve Azure Machine Learning experimentu jsou tyto čtyři datové sady školení vytvořeny prostřednictvím čtyř větví z předem zpracované vstupní datové sady. S výjimkou větve úplně vlevo obsahuje každá z těchto větví modul [spuštění skriptu jazyka R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , ve kterém jsou odvozené funkce (funkce, B, C a D) sestaveny a připojeny k importované datové sadě. Následující obrázek ukazuje skript R, který se používá k vytvoření sady funkcí B ve druhé levé větvi.

![vytváření funkcí](./media/create-features/addFeature-Rscripts.png)

Porovnání výsledků těchto čtyř modelů je shrnuto v následující tabulce: 

![porovnání výsledku](./media/create-features/result1.png)

Nejlepší výsledky jsou zobrazeny funkcemi A + B + C. Počítejte s tím, že při zahrnutí dalších sad funkcí do školicích dat se sníží rychlost chyb. Ověřuje domněnku, že funkce nastavená B, C poskytuje další důležité informace pro regresní úlohu. Přidání funkce D ale pravděpodobně neposkytuje žádné další snížení míry chyb.

## <a name="example2"></a>Příklad 2: vytváření funkcí v dolování textu
Inženýrské funkce se běžně používá v úlohách souvisejících s dolováním textu, jako je například klasifikace dokumentů a mínění analýza. Pokud například chcete klasifikovat dokumenty do několika kategorií, typický předpoklad je, že slova nebo fráze zahrnuté do jedné kategorie dokumentu jsou v jiné kategorii dokumentů méně pravděpodobně k dispozici. Jinými slovy, frekvence distribuce slov/frází může charakterizovat různé kategorie dokumentů. Vzhledem k tomu, že jednotlivé části Obsahového obsahu obvykle slouží jako vstupní data, jsou v aplikacích pro dolování textu k dispozici procesy pro vytváření funkcí, které zahrnují frekvence slov a frází.

K dosažení této úlohy se použije technika označovaná jako **hodnota hash funkcí** pro efektivní zapnutí libovolných textových funkcí v indexech. Namísto přidružování každé textové funkce (slova/fráze) k určitému indexu Tato metoda funguje tak, že použije funkce hash na funkce a použije jejich hodnoty hash jako indexy přímo.

V Azure Machine Learning je modul [hashování funkcí](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) , který vytváří tyto funkce slov/fráze pohodlně. Následující obrázek ukazuje příklad použití tohoto modulu. Vstupní datová sada obsahuje dva sloupce: kniha ohodnocená od 1 do 5 a skutečný obsah kontroly. Cílem tohoto modulu [hashování funkcí](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) je načíst řadu nových funkcí, které znázorňují četnost výskytů odpovídajících slov (/Phrase) v rámci určité recenze knihy. Chcete-li použít tento modul, proveďte následující kroky:

* Nejprve vyberte sloupec, který obsahuje vstupní text (v tomto příkladu "col2").
* Potom nastavte "hashing bitsize" na 8, což znamená, že se vytvoří 2 ^ 8 = 256 funkcí. Ve slově nebo ve všech textech bude hodnota hash na 256 indexy. Parametr "hashing bitsize" je v rozsahu od 1 do 31. Pokud je nastavení nastavené na větší číslo, může být v těchto slovnících (/Phrase) hodnota hash na stejný index.
* Na třetí nastavte parametr "N-gramy" na 2. Tato hodnota získá četnost výskytů unigrams (funkce pro každé jedno slovo) a bigrams (funkce pro všechny dvojice sousedících slov) od vstupního textu. Parametr "N-gramy" rozsahů od 0 do 10, který označuje maximální počet sekvenčních slov, která mají být součástí funkce.  

![Modul hashování funkcí](./media/create-features/feature-Hashing1.png)

Následující obrázek ukazuje, jak tato nová funkce vypadá.

![Příklad "hashování funkcí"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Závěr
Inženýrské a vybrané funkce zvyšují efektivitu školicího procesu, který se pokusí extrahovat klíčové informace obsažené v datech. Také zlepšují sílu těchto modelů, aby přesně klasifikaci vstupních dat a předpovědi výsledků jejich zájmu byly robustnější. Vytváření a výběr funkcí se také můžou kombinovat, aby se učení lépe vyjímat. Tím provedete vylepšením a následným snížením počtu funkcí potřebných k kalibraci nebo výukovém modelu. Matematicky řečeno, funkce vybrané pro výuku modelu jsou minimální sada nezávislých proměnných, které vysvětlují vzory v datech a pak mají úspěšné předpověď výsledků.

Nemusíte vždycky nutně provádět strojírenství funkcí nebo výběr funkcí. Bez ohledu na to, jestli je to potřeba, nebo nezáleží na tom, jak se data mají předat nebo shromažďovat, vybraný algoritmus a cíl experimentu.


---
title: Funkce inženýrství v datové vědě - Team Data Science Process
description: Vysvětluje účely funkce inženýrství a poskytuje příklady jeho role v procesu zlepšování dat strojového učení.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721128"
---
# <a name="feature-engineering-in-data-science"></a>Inženýrské funkce v datové vědě
Tento článek vysvětluje účely funkce inženýrství a poskytuje příklady jeho role v procesu zlepšování dat strojového učení. Příklady použité k ilustraci tohoto procesu jsou vykresleny z Azure Machine Learning Studio. 

Tento úkol je krokem v [procesu vědecké vědy o týmových datech (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

Funkce inženýrství se snaží zvýšit prediktivní sílu učení algoritmy vytvořením funkce z nezpracovaných dat, které pomáhají usnadnit proces učení. Inženýring a výběr funkcí je jednou z částí tdsp popsané v [co je týmový datový proces životního cyklu?](overview.md) Technické znalosti a výběr funkcí jsou součástí kroku **Vyvinout prvky** tdsp. 

* **inženýring funkcí**: Tento proces se pokouší vytvořit další relevantní funkce ze stávajících nezpracovaných funkcí v datech a zvýšit prediktivní sílu algoritmu učení.
* **Výběr funkce**: Tento proces vybere klíčovou podmnožinu původních datových funkcí ve snaze snížit dimenzionalitu trénovacího problému.

Za normálních okolností **funkce inženýrství** se použije nejprve generovat další funkce a pak se provede krok **výběru funkce** k odstranění irelevantní, redundantní nebo vysoce korelované funkce.

Trénovací data používaná ve strojovém učení lze často vylepšit extrakcí funkcí ze shromážděných nezpracovaných dat. Příkladem navržené funkce v kontextu učení, jak klasifikovat obrazy ručně psaných znaků, je vytvoření mapy bitové hustoty vytvořené z dat distribuce nezpracovaných bitů. Tato mapa může pomoci najít okraje znaků efektivněji než jednoduše pomocí raw distribuce přímo.

Chcete-li vytvořit funkce pro data v konkrétních prostředích, přečtěte si následující články:

* [Vytváření funkcí pro data na serveru SQL Server](create-features-sql-server.md)
* [Vytváření funkcí pro data v clusteru Hadoop pomocí dotazů Hive](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Vytváření funkcí z dat – technické znalosti
Trénovací data se skládají z matice složené z příkladů (záznamy nebo pozorování uložená v řádcích), z nichž každá má sadu funkcí (proměnné nebo pole uložená ve sloupcích). Očekává se, že prvky uvedené v experimentálním návrhu budou charakterizovat vzory v datech. Přestože mnoho nezpracovaných datových polí může být přímo zahrnuto do vybrané sady funkcí použité k trénování modelu, často se stává, že další (navržené) funkce musí být vytvořeny z funkcí v nezpracovaných datech, aby se vygenerovala vylepšená trénovací datová sada.

Jaký druh funkcí by měl y být vytvořeny pro vylepšení datové sady při trénování modelu? Navržené funkce, které vylepšují školení, poskytují informace, které lépe odlišují vzory v datech. Očekává se, že nové funkce poskytnou další informace, které nejsou jasně zachyceny nebo snadno patrné v původní nebo existující sadě funkcí. Ale tento proces je něco jako umění. Správná a produktivní rozhodnutí často vyžadují určité odborné znalosti v oblasti.

Když začínáte s Azure Machine Learning, je nejjednodušší pochopit tento proces konkrétně pomocí ukázek poskytovaných ve studiu. Zde jsou uvedeny dva příklady:

* Příklad regrese [Predikce počtu zapůjčení jízdních kol](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) v kontrolovaném experimentu, kde jsou známy cílové hodnoty
* Příklad klasifikace dolování textu pomocí [funkce hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Příklad 1: Přidání temporálních prvků pro regresní model
Využijme experiment "Prognóza poptávky jízdních kol" v Azure Machine Learning Studio (klasické) k předvedení, jak navrhnout funkce pro regresní úlohu. Cílem tohoto experimentu je předpovědět poptávku po kolech, tedy počet půjčoven kol během určitého měsíce/ dne/hodiny. Jako nezpracovaná vstupní data se používá datová sada "Bike Rental UCI dataset". Tato datová sada je založena na skutečných datech od společnosti Capital Bikeshare, která udržuje síť pro pronájem kol ve Washingtonu DC ve Spojených státech. Datová sada představuje počet půjčení jízdních kol během určité hodiny dne v letech 2011 a 2012 a obsahuje 17379 řádků a 17 sloupců. Sada prvků obsahuje povětrnostní podmínky (rychlost teploty/vlhkosti/větru) a typ dne (dovolená/den v týdnu). Pole předpovědět je 'cnt' počet, který představuje půjčovny kol v rámci určité hodiny a která se pohybuje od 1 do 977.

S cílem vytvořit efektivní funkce v trénovacích datech jsou čtyři regresní modely sestaveny pomocí stejného algoritmu, ale se čtyřmi různými trénovacími datovými sadami. Čtyři datové sady představují stejné nezpracovaná vstupní data, ale s rostoucím počtem sad funkcí. Tyto funkce jsou rozděleny do čtyř kategorií:

1. A = počasí + dovolená + den v týdnu + víkendové funkce pro předpokládaný den
2. B = počet motocyklů, která byla pronajata v každé z předchozích 12 hodin
3. C = počet kol, která byla pronajata v každém z předchozích 12 dnů ve stejnou hodinu
4. D = počet kol, která byla pronajata v každém z předchozích 12 týdnů ve stejnou hodinu a stejný den

Kromě sady funkcí A, která již existuje v původních nezpracovaných datech, jsou další tři sady funkcí vytvořeny prostřednictvím procesu vytváření prvků. Sada funkcí B zachycuje poslední poptávku po kolech. Sada funkcí C zachycuje poptávku po kolech v určitou hodinu. Sada funkcí D zachycuje poptávku po kolech v určitou hodinu a konkrétní den v týdnu. Čtyři trénovací datové sady obsahují sadu funkcí A, A+B, A+B+C a A+B+C+D.

V experimentu Azure Machine Learning tyto čtyři trénovací datové sady jsou tvořeny prostřednictvím čtyř větví z předem zpracované vstupní datové sady. S výjimkou větve nejvíce vlevo obsahuje každá z těchto větví modul [Skript spouštění R,](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) ve kterém jsou odvozené funkce (sada prvků B, C a D) konstruovány a připojeny k importované datové sadě. Následující obrázek znázorní skript R použitý k vytvoření sady funkcí B v druhé levé větvi.

![vytváření funkcí](./media/create-features/addFeature-Rscripts.png)

Porovnání výsledků výkonu čtyř modelů je shrnuto v následující tabulce: 

![porovnání výsledků](./media/create-features/result1.png)

Nejlepší výsledky jsou zobrazeny funkcemi A +B+C. Míra chyb se snižuje, pokud jsou do trénovacích dat zahrnuta další sada funkcí. Ověřuje předpoklad, že sada funkcí B, C poskytuje další relevantní informace pro úlohu regrese. Zdá se však, že přidání funkce D neposkytuje žádné další snížení míry chyb.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>Příklad 2: Vytváření funkcí v dolování textu
Funkce inženýrství je široce používán v úkolech souvisejících s dolování textu, jako je například klasifikace dokumentů a analýzy mínění. Pokud například chcete dokumenty zařadit do několika kategorií, je typickým předpokladem, že slovo/fráze obsažené v jedné kategorii dokumentu jsou méně pravděpodobné, že se objeví v jiné kategorii dokumentu. Jinými slovy, frekvence distribuce slov/frází je schopna charakterizovat různé kategorie dokumentů. V aplikacích dolování textu, protože jednotlivé části obsahu textu obvykle slouží jako vstupní data, je proces inženýrského prvku funkce potřebný k vytvoření funkcí zahrnujících frekvence slov a frází.

K dosažení tohoto úkolu je použita technika nazývaná **zapisování funkcí** pro efektivní přeměnu libovolných textových prvků na indexy. Namísto přidružování jednotlivých textových funkcí (slov/frází) k určitému indexu tato metoda funguje tak, že na prvky použije funkci hash a použije jejich hodnoty hash jako indexy přímo.

V Azure Machine Learning je modul [Hashing funkce,](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) která vytváří funkce slovo a frázi pohodlně. Následující obrázek ukazuje příklad použití tohoto modulu. Vstupní datová sada obsahuje dva sloupce: hodnocení knihy v rozsahu od 1 do 5 a skutečný obsah recenze. Cílem tohoto modulu [Hashing funkce](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) je získat spoustu nových funkcí, které ukazují četnost výskytu odpovídajících slov / frází v rámci konkrétní recenze knihy. Chcete-li použít tento modul, proveďte následující kroky:

* Nejprve vyberte sloupec, který obsahuje vstupní text ("Col2" v tomto příkladu).
* Za druhé nastavte "Hashing bitsize" na 8, což znamená, že budou vytvořeny 2 ^ 8 = 256 funkcí. Slovo/fáze ve všem textu bude zahasováno na 256 indexů. Parametr "Hashing bitsize" se pohybuje od 1 do 31. Slovo (y)/phrase(s) je méně pravděpodobné, že bude zahlceno do stejného indexu, pokud je jeho nastavení větší číslo.
* Za třetí nastavte parametr "N-gramů" na 2. Tato hodnota získá četnost výskytu unigramů (funkce pro každé slovo) a bigrams (funkce pro každou dvojici sousedních slov) ze vstupního textu. Parametr "N-gramů" se pohybuje od 0 do 10, což označuje maximální počet sekvenčních slov, která mají být zahrnuta do funkce.  

![Modul "Funkce hash"](./media/create-features/feature-Hashing1.png)

Následující obrázek ukazuje, jak tyto nové funkce vypadají.

![Příklad "Zahašování funkcí"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Závěr
Inženýrské a vybrané funkce zvyšují efektivitu procesu školení, který se pokouší extrahovat klíčové informace obsažené v datech. Zlepšují také výkon těchto modelů pro přesné klasifikaci vstupních dat a robustnější předvídání výsledků zájmu. Funkce inženýrství a výběr může také kombinovat, aby se učení více výpočetní zvladatelné. Činí tak tím, že zvyšuje a pak snižuje počet funkcí potřebných ke kalibraci nebo trénování modelu. Matematicky řečeno, funkce vybrané pro trénování modelu jsou minimální sadu nezávislých proměnných, které vysvětlují vzory v datech a pak úspěšně předpovědět výsledky.

Není vždy nutné provádět technické nebo funkce. Zda je to potřeba nebo ne, závisí na datech, která mají být k dispozici nebo shromážděna, na vybraném algoritmu a na cíli experimentu.


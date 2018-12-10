---
title: Konstrukce funkcí v datové vědy – vědecké zpracování týmových dat
description: Popisuje účely vytváření funkcí a poskytuje příklady jejich rolí v procesu rozšíření dat služby machine learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5767399189e4ed5168fbcc083ef9b4830cac1421
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138951"
---
# <a name="feature-engineering-in-data-science"></a>Konstrukce funkcí v datové vědy
Tento článek vysvětluje účely vytváření funkcí a poskytuje příklady jejich rolí v procesu rozšíření dat služby machine learning. Příklady slouží ke znázornění tohoto procesu jsou vykreslovány vedle z Azure Machine Learning Studio. 

Tato úloha je nějaký krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Funkce engineering pokusy o sofistikované prediktivní učení se supervizí vytvořením funkce z nezpracovaných dat, která usnadňují proces učení. Konstrukce a výběr funkcí TDSP uvedených v jedné části je [co je životní cyklus vědeckého zpracování týmových dat?](overview.md) Vytváření funkcí a výběru jsou části **vývoj funkcí** krok TDSP. 

* **konstrukce funkcí**: Tento proces pokusí vytvořit další odpovídající funkce ze stávajících funkcí nezpracovaných dat a prediktivní sofistikované algoritmus učení.
* **Výběr funkcí**: Tento proces vybere klíče podmnožinu funkcí původní data za účelem snížení dimenzionalitu problém školení.

Obvykle **konstruování** se použije první generovat další funkce a pak **výběr funkcí** krok se provádí za účelem odstranění irelevantní, redundantní nebo vysoce korelační funkce.

Extrakcí funkcí z nezpracovaných dat shromážděných často dá vylepšit trénovacích dat používají ve službě machine learning. Příklad inženýrství funkce v rámci učit, jak klasifikovat imagí zapsaných znaků je vytvoření trochu hustota mapy vyrobena z dat distribuční nezpracovaná bit. Toto mapování může pomoct efektivněji než jednoduše přímo pomocí nezpracované rozdělení vyhledejte hrany znaky.

Vytvoření funkcí pro data v konkrétních prostředí, najdete v následujících článcích:

* [Vytvoření funkcí pro data v systému SQL Server](create-features-sql-server.md)
* [Vytvoření funkcí pro data v clusteru Hadoop pomocí dotazů Hive](create-features-hive.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Vytvoření funkce z dat – návrh funkcí
Trénovacích dat se skládá z matice skládající se z příkladů (záznamy nebo pozorování, které jsou uložené v řádcích), z nichž každý má sadu funkcí (proměnných nebo polí, které jsou uložené ve sloupcích). Chcete-li charakterizovat vzory v datech se očekává funkce zadané v experimentální návrh. Počet polí nezpracovaná data mohou být přímo součástí vybranou funkci set využívají k tréninku modelu, je často případ, že je potřeba další (inženýrství) funkce zkonstruovat z funkce v nezpracovaných dat pro generování rozšířené trénovací datové sady.

Jaký druh funkcí měl by být vytvořen při cvičení modelu rozšířit datovou sadu? Inženýrství funkce, které zlepšují školení poskytují informace, které lépe odlišují vzory v datech. Očekává se, že nové funkce poskytují další informace, které není jasně zachycené nebo snadno zřejmé v sadě původní ani do existující funkce. Ale tento proces je něco obrázky. Rozhodnutí o zvuku a produktivní často vyžadují některé odborné znalosti domény.

Při spuštění pomocí služby Azure Machine Learning, je nejjednodušší pochopit její podstatu tohoto procesu namítají použití ukázek, které jsou součástí sady Studio. Dva příklady jsou uvedeny zde:

* Příklad regrese [předpovědi počet pronajatých kol za](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) v pod dohledem experiment, ve kterém se ví, cílové hodnoty
* Text dolování klasifikace příklad použití [Hashování](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Příklad 1: Přidejte dočasné funkce pro regresní model
S použitím experiment "Prognózování poptávky po pro kola" v nástroji Azure Machine Learning Studio demonstrují, jakým způsobem projektovat funkce pro úlohu regrese. Cílem tohoto experimentu je k predikci poptávky kol, to znamená, počet pronajatých kol za v rámci konkrétní měsíc/den/hodina. Datovou sadu "kol pronájmu Cyklistických datovou sadu" slouží jako nezpracovaný vstupní data. Tato datová sada je založen na reálná data z Bikeshare velké společnosti, která udržuje síť pronájem kola ve Washingtonu D.C. ve Spojených státech. Datová sada představuje počet pronajatých kol za v rámci konkrétní hodina dne v roce 2011 a roku 2012 a obsahuje 17379 řádky a sloupce 17. Nezpracovaná funkce sada obsahuje počasí (rychlost teploty a vlhkosti/větru) a zadejte den (sváteční/den v týdnu). Pole k předpovědi je počet "cnt", která představuje pronajatých kol za do konkrétní hodiny a který od 1 do 977.

S cílem vytváření účinné funkce v trénovacích dat čtyři regrese, který modely jsou sestaveny na základě stejný algoritmus, ale s čtyři různé cvičných datových sad. Čtyři datové sady představují stejné nezpracovaná vstupní data, ale s rostoucím počtem funkcí nastavit. Tyto funkce jsou rozděleny do čtyř kategorií:

1. Objekt = počasí + den v týdnu a svátků + předpokládané den víkendu funkce
2. B = počet pronajatých byly po v každém z předchozích 12 hodin
3. C = počet pronajatých byly po v každém z předchozích 12 dnů v jedné hodiny
4. D = počet pronajatých byly po v každém z předchozích 12 týdnů na jedné hodiny ve stejný den

Kromě nastavení A funkce, která již existuje v původních nezpracovaných dat, se vytvoří další tři sady funkcí prostřednictvím funkce technické procesu. Funkci nastavit B zachycení úplně čerstvé poptávka po kol. Funkci nastavit C zachycení poptávka po kola na konkrétní hodiny. Funkce nastavit poptávka kol po zachycení D konkrétní hodiny nebo na konkrétní den v týdnu. Čtyři trénovací datové sady jednotlivých zahrnuje funkce set A, A + B, A + B + C a A + B + C + D.

V Azure Machine Learning experimentu jsou tyto čtyři cvičných datových sad vytvořený prostřednictvím čtyři větve z předem zpracovaných vstupní datové sady. S výjimkou první větev, každá z těchto větví obsahuje [spustit skript jazyka R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modul, ve kterém odvozené funkce (funkce sady B, C a D) jsou v uvedeném pořadí vytvořený a připojí k importované datové sady. Následující obrázek ukazuje skript jazyka R, který umožňuje vytvořit sadu funkcí B v druhé větvi vlevo.

![Vytvoření funkce](./media/create-features/addFeature-Rscripts.png)

Porovnání výsledky výkonu ze čtyř modelů je shrnuto v následující tabulce: 

![výsledek porovnání](./media/create-features/result1.png)

Nejlepších výsledků jsou zobrazeny pro funkce A + B + C. Všimněte si, že chybovost snižuje při další funkce jsou součástí trénovací data. Ověří, předpokládá, že sada funkcí B, C poskytnout další relevantní informace pro úlohu regrese. Ale přidání funkce D nefunguje zadejte jakékoli další snížení frekvence chyb.

## <a name="example2"></a> Příklad 2: Vytvoření funkce v textu dolování
Vytváření funkcí je široce použít v úlohy související s dolování textu, jako je například Analýza dokumentů klasifikace a mínění. Například když chcete klasifikovat dokumenty do několika kategorií, typické předpokladem je, že jsou zahrnuté v jedné kategorii dokumentu aplikace word/fráze méně pravděpodobné, že v jiné kategorii dokumentu dojde k. Jinými slovy je možné charakterizovat jiného dokumentu kategorií frekvence rozdělení slov nebo frází. V aplikacích dolování text protože jednotlivé obsah textu obvykle slouží jako vstupní data, je funkce technické procesu potřeba k vytvoření funkce zahrnující frekvencí slovo nebo fráze.

K provedení této úlohy, volá se metoda **hashování** platí pro efektivní zapnutí funkcí libovolného textu do indexy. Místo přidružení jednotlivých funkcí text (slova nebo fráze) do konkrétního indexu, funkce v této metody tak, že použití funkce hash pro funkce a přímo pomocí jejich hodnoty hash jako indexy.

Ve službě Azure Machine Learning, je [Hashování](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul, který vytvoří toto slovo nebo fráze funkce jednoduše. Následující obrázek ukazuje příklad použití tohoto modulu. Vstupní datová sada obsahuje dva sloupce: hodnocení kniha od 1 do 5 a skutečnou kontrolu obsahu. Cílem tohoto [Hashování](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modulu je k načtení spoustu nových funkcí, které zobrazují četnost výskytu slova odpovídající / zkontrolujte phrase(s) v rámci konkrétního seznamu. Pokud chcete použít tento modul, proveďte následující kroky:

* Nejdřív vyberte sloupec, který obsahuje vstupní text ("Col2" v tomto příkladu).
* Za druhé, nastavte "Hashing bitsize" 8, což znamená, že 2 ^ 8 = 256, vytvoří se funkce. Word/fáze veškerý text se rozdělí na 256 indexy. Parametr "Hashing bitsize" od 1 do 31. Slova / phrase(s) jsou méně pravděpodobné, že se rozdělily na stejný index, pokud bude většímu počtu nastavení.
* Třetí nastavte parametr "N-gramy" na 2. Tato hodnota získá četnost výskytu unigrams (funkce pro každé slovo) a bigrams (funkce pro každý pár slov v příspěvcích sousední) ze vstupního textu. Parametr "N-gramy" od 0 do 10, která určuje maximální počet slov sekvenční mají být zahrnuty ve funkci.  

!["Funkce Hashing" modulu](./media/create-features/feature-Hashing1.png)

Následující obrázek znázorňuje, co tyto nové funkce vypadají.

!["Funkce Hashing" Příklad](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Závěr
Inženýrství a vybrané funkce zvýšit účinnost procesu trénování, která se pokusí extrahovat klíčové informace obsažené v datech. Také zvyšují power tyto modely přesně klasifikovat vstupních dat a více robustní předvídat výstupy, které vás zajímají. Aby studijním výpočetně tractable můžete také kombinovat funkce konstrukce a výběr. Dělá to tak, že rozšíření a pak snížení počtu funkce potřebné pro nakalibrovat pro ni nebo trénování modelu. Funkce vybrané pro trénování modelu matematicky vzato jsou minimální sadu nezávislých proměnných, které popisují vzory v datech a pak úspěšně předpovědět výsledky.

Není vždy nutně provést výběr součástí inženýrství nebo funkce. Určuje, zda je potřeba, nebo ne závisí na datech ručně nebo shromáždí, použije algoritmus vybrané a cílem tohoto experimentu.


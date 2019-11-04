---
title: 'Výběr funkcí založených na filtrech: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul výběru funkcí založený na filtrech ve službě Azure Machine Learning k identifikaci funkcí v datové sadě s největším prediktivním výkonem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517966"
---
# <a name="filter-based-feature-selection"></a>Výběr funkcí založených na filtrech

Tento článek popisuje, jak použít modul [výběru funkce založený na filtrech](filter-based-feature-selection.md) v Návrháři Azure Machine Learning (Preview) k identifikaci sloupců ve vstupní datové sadě, které mají největší prediktivní napájení. 

Obecně platí, že *Výběr funkcí* odkazuje na proces použití statistických testů na vstupy, které mají zadaný výstup, k určení, které sloupce mají více prediktivní výstup. Modul [výběru funkce založený na filtrech](filter-based-feature-selection.md) poskytuje několik algoritmů výběru funkcí, ze kterých můžete vybírat, včetně korelačních metod, jako je Pearsonova korelace a hodnot chí-kvadrát. 

Když použijete modul [výběru funkce založený na filtrech](filter-based-feature-selection.md) , zadáte datovou sadu, určíte sloupec, který obsahuje popisek nebo závislou proměnnou, a pak zadáte jedinou metodu, která se má použít při měření důležitosti funkce.

Modul výstupuje datovou sadu, která obsahuje nejlepší sloupce funkce seřazené podle prediktivního výkonu. Také Vypíše názvy funkcí a jejich skóre z vybrané metriky.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>Co je výběr funkcí založených na filtrech a proč ho používat?  

Tento modul pro výběr funkcí se nazývá "založený na filtrech", protože vybranou metriku můžete použít k identifikaci nepodstatných atributů a odfiltrovat redundantní sloupce z modelu.  Zvolíte jednu statistickou míru, která bude vyhovovat vašim datům, a modul vypočítá skóre pro každý sloupec funkce. Sloupce se vrátí podle skóre jejich funkcí. 

Výběrem správných funkcí můžete potenciálně zlepšit přesnost a efektivitu klasifikace. 

Obvykle používáte pouze sloupce s nejlepším skóre k sestavení prediktivního modelu. Sloupce s nedostatečným skóre výběru funkcí mohou být ponechány v datové sadě a při sestavování modelu ignorovány.  

### <a name="how-to-choose-a-feature-selection-metric"></a>Volba metriky výběru funkcí

**Výběr funkcí založených na filtrech** poskytuje celou řadu metrik pro vyhodnocení hodnoty informací v jednotlivých sloupcích.  V této části najdete obecný popis jednotlivých metrik a jejich použití. Další požadavky na použití jednotlivých metrik jsou uvedeny v části [technické poznámky](#technical-notes) a v [pokynech](#how-to-configure-filter-based-feature-selection) ke konfiguraci jednotlivých modulů.

-   **Korelace Pearsonova**  

     Pearsonova korelační korelace nebo korelační koeficient Pearsonova je také známo ve statistických modelech jako hodnota `r`. U všech dvou proměnných vrátí hodnotu, která označuje sílu korelace.

     Korelační koeficient Pearsonova je vypočítán tím, že přijímá koodchylku dvou proměnných a vydělí součin jejich směrodatných odchylek. Koeficient není ovlivněn změnami měřítka ve dvou proměnných.  

-   **Chí na druhou**  

     Obousměrný test chí-kvadrát je statistická metoda, která měří způsob, jakým se očekává, že se očekávané hodnoty blíží skutečným výsledkům. Metoda předpokládá, že proměnné jsou náhodné a vykresleny z adekvátního vzorku nezávislých proměnných. Výsledné statistiky chí-kvadrát ukazují, jak daleko jsou výsledky z očekávaného (náhodného) výsledku.  


> [!TIP]
> Pokud pro metodu výběru vlastní funkce potřebujete jinou možnost, použijte modul [spuštění skriptu jazyka R](execute-r-script.md) . 
##  <a name="how-to-configure-filter-based-feature-selection"></a>Postup konfigurace výběru funkcí založených na filtrech

Zvolíte standardní statistickou metriku a modul vypočítá korelaci mezi dvojicí sloupců: sloupec popisku a sloupec funkce

1.  Přidejte do svého kanálu modul **výběru funkcí založený na filtrech** . Můžete ji najít v kategorii **výběru funkcí** v návrháři.

2. Připojte vstupní datovou sadu, která obsahuje alespoň dva sloupce, které jsou potenciálními funkcemi.  

    Aby se zajistilo, že by se měl analyzovat sloupec a vygenerovalo se skóre funkce, použijte modul [Upravit metadata](edit-metadata.md) a nastavte atribut **infeature** . 

    > [!IMPORTANT]
    > Ujistěte se, že sloupce, které poskytujete jako vstup, jsou potenciální funkce. Například sloupec, který obsahuje jednu hodnotu, nemá žádnou hodnotu informace.
    >
    > Pokud víte, že existují sloupce, které by měly špatné funkce, můžete je odebrat z výběru sloupce. Můžete také použít modul [Upravit metadata](edit-metadata.md) a označit ho jako **kategorií**. 
3.  V případě **metody bodování funkcí**vyberte jednu z následujících vytvořených statistických metod, které se použijí při výpočtu skóre.  

    | Metoda              | Požadavky                             |
    | ------------------- | ---------------------------------------- |
    | Korelace Pearsonova | Popisek může být text nebo číslo. Funkce musí být číselné. |
    Chí na druhou| Popisky a funkce mohou být textové nebo číselné. Tuto metodu použijte pro výpočet důležitosti funkcí pro dva kategorií sloupce.|

    > [!TIP]
    > Pokud změníte vybranou metriku, resetují se všechny ostatní výběry, takže nezapomeňte tuto možnost nastavit jako první.)
4.  Pokud chcete vygenerovat skóre jenom pro ty sloupce, které už byly označené jako funkce, vyberte možnost **pracovat na sloupcích funkce pouze** . 

    Pokud zrušíte výběr této možnosti, modul vytvoří skóre pro libovolný sloupec, který jinak splňuje kritéria, až do počtu sloupců zadaných v **počtu požadovaných funkcí**.  

5.  V poli **cílový sloupec**klikněte na **Spustit selektor sloupců** a vyberte sloupec popisku buď podle názvu, nebo podle jeho indexu (indexy jsou založené na jednom).  

     Pro všechny metody, které zahrnují statistickou korelaci, je vyžadován sloupec popisku. Modul vrátí chybu v době návrhu, pokud jste zvolili možnost žádný sloupec popisku nebo více sloupců popisku. 

6.  V poli **počet požadovaných funkcí**zadejte počet sloupců funkce, které se mají vrátit jako výsledek.  

     - Minimální počet funkcí, které můžete zadat, je 1, ale doporučujeme tuto hodnotu zvýšit.  

     - Pokud je zadaný počet požadovaných funkcí větší než počet sloupců v datové sadě, budou vráceny všechny funkce, a to i s nulovými skóremi.  

    - Pokud zadáte méně výsledných sloupců, než jsou sloupce funkce, jsou tyto funkce seřazené podle sestupného skóre a vrátí se jenom ty hlavní funkce. 

7.  Spusťte kanál, nebo vyberte modul pro [Výběr funkcí založený na filtrech](filter-based-feature-selection.md) a pak klikněte na **Spustit vybrané**.


## <a name="results"></a>Výsledky

Po dokončení zpracování:

+ Chcete-li zobrazit úplný seznam analyzovaných sloupců funkcí a jejich skóre, klikněte pravým tlačítkem myši na modul, vyberte možnost **funkce**a klikněte na příkaz **vizualizace**.  

+ Chcete-li zobrazit datovou sadu, která je generována na základě kritérií výběru vaší funkce, klikněte pravým tlačítkem myši na modul, vyberte možnost **sada dat**a klikněte na možnost **vizualizovat**. 

Pokud datová sada obsahuje méně sloupců, než jste očekávali, podívejte se na nastavení modulu a zadejte datové typy sloupců, které jste zadali jako vstup. Například pokud nastavíte **počet požadovaných funkcí** na 1, výstupní datová sada obsahuje pouze dva sloupce: sloupec popisek a nejmnohem vysoce seřazený sloupec funkce.


##  <a name="technical-notes"></a>Technické poznámky  

### <a name="implementation-details"></a>Podrobnosti implementace

Pokud používáte korelaci Pearsonova s numerickou funkcí a popiskem kategorií, skóre funkce se vypočítá takto:  

1.  Pro každou úroveň ve sloupci kategorií vypočítá podmíněný průměr číselného sloupce.  

2.  Korelace sloupce podmíněného znamená s číselným sloupcem.  

### <a name="requirements"></a>Požadavky  

-   Skóre výběru funkce nelze vygenerovat pro žádný sloupec, který je určen jako **popisek** nebo jako sloupec **skóre** .  

-   Pokud se pokusíte použít metodu bodování se sloupcem datového typu, který není podporován metodou, modul buď vyvolá chybu, nebo se do sloupce přiřadí nula skóre.  

-   Pokud sloupec obsahuje logickou hodnotu (true/false), bude zpracována jako true = 1 a false = 0.  

-   Sloupec nemůže být funkcí, pokud byl označený jako **popisek** nebo **skóre**.  

### <a name="how-missing-values-are-handled"></a>Jak se zpracovávají chybějící hodnoty  

-   Nemůžete zadat sloupec Target (Label), který obsahuje všechny chybějící hodnoty.  

-   Pokud sloupec obsahuje chybějící hodnoty, při výpočtu skóre pro sloupec budou ignorovány.  

-   Pokud sloupec označený jako sloupec funkce obsahuje všechny chybějící hodnoty, přiřadí se nula skóre.   


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 


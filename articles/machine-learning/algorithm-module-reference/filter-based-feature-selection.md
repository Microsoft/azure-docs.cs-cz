---
title: 'Výběr funkce založený na filtru: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu výběru funkcí založených na filtru v Azure Machine Learning identifikovat funkce v datové sadě s největším prediktivním výkonem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477591"
---
# <a name="filter-based-feature-selection"></a>Výběr funkcí na základě filtrů

Tento článek popisuje, jak používat modul výběru funkcí založených na filtru v návrháři Azure Machine Learning (preview). Tento modul vám pomůže identifikovat sloupce ve vstupní datové sadě, které mají největší prediktivní výkon. 

Obecně se *výběr funkcí* vztahuje k procesu aplikace statistických testů na vstupy zadaný výstup. Cílem je určit, které sloupce jsou prediktivnější výstupu. Modul Výběr funkcí založený na filtru poskytuje více algoritmů výběru funkcí, ze kterých si můžete vybrat. Modul obsahuje korelační metody, jako je například Pearsonova korelace a hodnoty chí-kvadrát. 

Při použití modulu výběr funkce založené na filtru zadáte datovou sadu a identifikujete sloupec, který obsahuje popisek nebo závislou proměnnou. Potom zadáte jedinou metodu, která se použije při měření důležitosti funkce.

Modul vyveze datovou sadu, která obsahuje sloupce nejlepších funkcí, seřazené podle prediktivního výkonu. Také výstupy názvy funkcí a jejich skóre z vybrané metriky.  

## <a name="what-filter-based-feature-selection-is"></a>Jaký výběr funkcí založených na filtru je  

Tento modul pro výběr funkcí se nazývá "na základě filtru", protože pomocí vybrané metriky najdete irelevantní atributy. Potom odfiltrovat redundantní sloupce z modelu. Zvolíte jednu statistickou míru, která vyhovuje vašim datům, a modul vypočítá skóre pro každý sloupec funkce. Sloupce jsou vráceny seřazené podle jejich skóre funkcí. 

Výběrem správných funkcí můžete potenciálně zlepšit přesnost a efektivitu klasifikace. 

Obvykle používáte pouze sloupce s nejlepší skóre k vytvoření prediktivní model. Sloupce se špatným skóre výběru funkcí mohou být ponechány v datové sadě a ignorovány při vytváření modelu.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Jak vybrat metriku výběru funkce

Modul Výběr funkcí založených na filtru poskytuje řadu metrik pro posouzení informační hodnoty v každém sloupci. Tato část obsahuje obecný popis jednotlivých metrik a způsob jeho použití. Další požadavky na použití jednotlivých metrik naleznete v [technických poznámkách](#technical-notes) a v [pokynech](#how-to-configure-filter-based-feature-selection) pro konfiguraci jednotlivých modulů.

-   **Pearsonova korelace**  

    Pearsonova korelační statistika neboli Pearsonův korelační koeficient je `r` také známá ve statistických modelech jako hodnota. Pro všechny dvě proměnné vrátí hodnotu, která označuje sílu korelace.

    Pearsonův korelační koeficient se vypočítá tak, že se vezme kovariance dvou proměnných a vydělí se součinem jejich směrodatných odchylek. Změny měřítka ve dvou proměnných nemají vliv na koeficient.  

-   **Chi na druhou**  

    Obousměrný test chí-squared je statistická metoda, která měří, jak blízko jsou očekávané hodnoty skutečným výsledkům. Metoda předpokládá, že proměnné jsou náhodné a jsou čerpány z odpovídajícího vzorku nezávislých proměnných. Výsledná statistika chí-kvadrát označuje, jak daleko jsou výsledky od očekávaného (náhodného) výsledku.  


> [!TIP]
> Pokud potřebujete jinou možnost pro vlastní metodu výběru funkce, použijte modul [Spustit skript R.](execute-r-script.md) 

## <a name="how-to-configure-filter-based-feature-selection"></a>Konfigurace výběru funkcí založených na filtru

Zvolíte standardní statistickou metriku. Modul vypočítá korelaci mezi dvojicí sloupců: sloupec popisku a sloupec prvku.

1.  Přidejte do kanálu modul výběru funkcí založených na filtru. Najdete ji v kategorii **Výběr funkcí** v návrháři.

2. Připojte vstupní datovou sadu, která obsahuje alespoň dva sloupce, které jsou potenciálními funkcemi.  

    Chcete-li zajistit, aby byl sloupec analyzován a generováno skóre prvku, nastavte atribut **IsFeature** pomocí modulu [Upravit metadata.](edit-metadata.md) 

    > [!IMPORTANT]
    > Ujistěte se, že sloupce, které poskytujete jako vstup, jsou potenciální funkce. Například sloupec, který obsahuje jednu hodnotu, nemá žádnou informační hodnotu.
    >
    > Pokud víte, že některé sloupce by vytvořily špatné funkce, můžete je odebrat z výběru sloupců. Pomocí modulu [Upravit metadata](edit-metadata.md) můžete také označit jako **kategorické**. 
3.  V **části Metoda hodnocení funkcí**zvolte jednu z následujících zavedených statistických metod, které se mají použít při výpočtu skóre.  

    | Metoda              | Požadavky                             |
    | ------------------- | ---------------------------------------- |
    | Pearsonova korelace | Popisek může být textový nebo číselný. Funkce musí být číselné. |
    Chi na druhou| Popisky a funkce mohou být textové nebo číselné. Tuto metodu použijte pro výpočet důležitosti funkce pro dva kategorické sloupce.|

    > [!TIP]
    > Pokud vybranou metriku změníte, všechny ostatní výběry se vynulují. Takže se ujistěte, že tuto možnost nejprve.
4.  Vyberte možnost **Pracovat pouze se sloupci prvku,** chcete-li generovat skóre pouze pro sloupce, které byly dříve označeny jako prvky. 

    Pokud tuto možnost vymažete, modul vytvoří skóre pro libovolný sloupec, který jinak splňuje kritéria, až do počtu sloupců zadaných v **poli Počet požadovaných funkcí**.  

5.  Ve **sloupci Cíl**vyberte **volič sloupce Spuštění,** chcete-li vybrat sloupec popisku podle názvu nebo podle indexu. (Indexy jsou založeny na jednom.)  
    Sloupec popisku je vyžadován pro všechny metody, které zahrnují statistickou korelaci. Modul vrátí chybu návrhu, pokud zvolíte žádný sloupec popisku nebo více sloupců popisku. 

6.  Do **pole Počet požadovaných funkcí**zadejte počet sloupců prvků, které chcete vrátit jako výsledek:  

    - Minimální počet funkcí, které můžete zadat, je jeden, ale doporučujeme zvýšit tuto hodnotu.  

    - Pokud je zadaný počet požadovaných prvků větší než počet sloupců v datové sadě, jsou vráceny všechny funkce. Dokonce i funkce s nulovým skóre jsou vráceny.  

    - Pokud zadáte méně sloupců výsledků, než jsou sloupce prvků, budou prvky seřazeny podle sestupné hodu. Jsou vráceny pouze horní funkce. 

7.  Odešlete kanál nebo vyberte modul výběr u stavů založený na filtru a pak vyberte **Spustit vybranou položku**.


## <a name="results"></a>Výsledky

Po dokončení zpracování:

+ Chcete-li zobrazit úplný seznam analyzovaných sloupců prvků a jejich skóre, klepněte pravým tlačítkem myši na modul a vyberte možnost **Vizualizovat**.  

+ Chcete-li zobrazit datovou sadu na základě kritérií výběru funkcí, klepněte pravým tlačítkem myši na modul a vyberte možnost **Vizualizovat**. 

Pokud datová sada obsahuje méně sloupců, než jste očekávali, zkontrolujte nastavení modulu. Zkontrolujte také datové typy sloupců, které jsou k dispozici jako vstup. Pokud například nastavíte **počet požadovaných prvků** na 1, výstupní datová sada obsahuje pouze dva sloupce: sloupec popisku a sloupec s nejvíce vysoce hodnocenými funkcemi.


##  <a name="technical-notes"></a>Technické poznámky  

### <a name="implementation-details"></a>Podrobnosti implementace

Pokud použijete Pearsonovu korelaci na číselný prvek a kategorický popisek, skóre funkce se vypočítá takto:  

1.  Pro každou úroveň v kategorickém sloupci vypočítejte podmíněný průměr číselného sloupce.  

2.  Korelujte sloupec podmíněných prostředků s číselným sloupcem.  

### <a name="requirements"></a>Požadavky  

-   Skóre výběru funkcí nelze vygenerovat pro žádný sloupec, který je označen jako sloupec **Popisek** nebo **Skóre.**  

-   Pokud se pokusíte použít metodu vyhodnocování se sloupcem datového typu, který metoda nepodporuje, modul vyvolá chybu. Nebo bude sloupci přiřazeno nulové skóre.  

-   Pokud sloupec obsahuje logické (true/false) hodnoty, jsou `True = 1` `False = 0`zpracovány jako a .  

-   Sloupec nemůže být funkcí, pokud byl označen jako **Popisek** nebo **Skóre**.  

### <a name="how-missing-values-are-handled"></a>Jak jsou zpracovány chybějící hodnoty  

-   Jako cílový sloupec (popisek) nelze zadat žádný sloupec, ve kterých chybí všechny hodnoty.  

-   Pokud sloupec obsahuje chybějící hodnoty, modul je ignoruje při výpočtu skóre pro sloupec.  

-   Pokud sloupec označený jako sloupec prvku obsahuje všechny chybějící hodnoty, modul přiřadí nulové skóre.   


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 


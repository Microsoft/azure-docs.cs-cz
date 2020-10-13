---
title: 'Výběr funkcí založených na filtrech: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul výběru funkcí založený na filtrech v Azure Machine Learning k identifikaci funkcí v datové sadě s největším prediktivním výkonem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f4a7f5581703ae6932f3b40e62085fed76f5e6f2
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945698"
---
# <a name="filter-based-feature-selection"></a>Výběr funkcí na základě filtrů

Tento článek popisuje, jak použít modul výběru funkce založený na filtrech v Návrháři Azure Machine Learning. Tento modul vám pomůže identifikovat sloupce ve vstupní datové sadě, které mají největší prediktivní výkon. 

Obecně platí, že *Výběr funkcí* odkazuje na proces použití statistických testů na vstupy v zadaném výstupu. Cílem je určit, které sloupce mají více prediktivní výstup. Modul výběru funkcí založený na filtrech poskytuje několik algoritmů výběru funkcí, ze kterých si můžete vybrat. Modul zahrnuje metody korelace, jako je korelace Pearsonova a hodnoty chí-kvadrát. 

Když použijete modul výběru funkce založený na filtrech, poskytnete datovou sadu a určíte sloupec, který obsahuje popisek nebo závislou proměnnou. Pak můžete zadat jedinou metodu, která se má použít při měření důležitosti funkce.

Modul výstupuje datovou sadu, která obsahuje nejlepší sloupce funkce seřazené podle prediktivního výkonu. Také Vypíše názvy funkcí a jejich skóre z vybrané metriky.  

## <a name="what-filter-based-feature-selection-is"></a>Výběr funkcí založených na filtrech  

Tento modul pro výběr funkcí se nazývá "založený na filtrech", protože vybraná metrika se používá k vyhledání nerelevantních atributů. Pak vyfiltrujete redundantní sloupce z modelu. Zvolíte jednu statistickou míru, která bude vyhovovat vašim datům, a modul vypočítá skóre pro každý sloupec funkce. Sloupce se vrátí podle skóre jejich funkcí. 

Výběrem správných funkcí můžete potenciálně zlepšit přesnost a efektivitu klasifikace. 

Obvykle používáte pouze sloupce s nejlepším skóre k sestavení prediktivního modelu. Sloupce s nedostatečným skóre výběru funkcí mohou být ponechány v datové sadě a při sestavování modelu ignorovány.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Volba metriky výběru funkcí

Modul výběru funkcí Filter-Based poskytuje celou řadu metrik pro vyhodnocení hodnoty informací v jednotlivých sloupcích. V této části najdete obecný popis jednotlivých metrik a jejich použití. Další požadavky na používání každé metriky najdete v [technických poznámkách](#technical-notes) a v [pokynech](#how-to-configure-filter-based-feature-selection) ke konfiguraci jednotlivých modulů.

-   **Korelace Pearsonova**  

    Korelační korelace Pearsonova nebo korelačního koeficientu Pearsonova je také známo ve statistických modelech jako `r` hodnota. U všech dvou proměnných vrátí hodnotu, která označuje sílu korelace.

    Korelační koeficient Pearsonova je vypočítán tím, že přijímá koodchylku dvou proměnných a vydělí součin jejich směrodatných odchylek. Změny měřítka ve dvou proměnných neovlivňují koeficient.  

-   **Chí na druhou**  

    Obousměrný test chí-kvadrát je statistická metoda, která měří způsob, jakým se očekává, že se očekávané hodnoty blíží skutečným výsledkům. Metoda předpokládá, že proměnné jsou náhodné a vykresleny z adekvátního vzorku nezávislých proměnných. Výsledné statistiky chí-kvadrát ukazují, jak daleko jsou výsledky z očekávaného (náhodného) výsledku.  


> [!TIP]
> Pokud pro metodu výběru vlastní funkce potřebujete jinou možnost, použijte modul [spuštění skriptu jazyka R](execute-r-script.md) . 

## <a name="how-to-configure-filter-based-feature-selection"></a>Jak nakonfigurovat výběr funkcí Filter-Based

Zvolíte standardní statistickou metriku. Modul vypočítá korelaci mezi dvojicí sloupců: sloupec popisku a sloupec funkce.

1.  Přidejte modul výběru funkcí Filter-Based k vašemu kanálu. Můžete ji najít v kategorii **výběru funkcí** v návrháři.

2. Připojte vstupní datovou sadu, která obsahuje alespoň dva sloupce, které jsou potenciálními funkcemi.  

    Chcete-li zajistit, aby byl sloupec analyzován, a je vygenerováno skóre funkce, použijte modul [Upravit metadata](edit-metadata.md) a **nastavte atribut vlastnost** is. 

    > [!IMPORTANT]
    > Ujistěte se, že sloupce, které poskytujete jako vstup, jsou potenciální funkce. Například sloupec, který obsahuje jednu hodnotu, nemá žádnou hodnotu informace.
    >
    > Pokud víte, že některé sloupce by měly špatné funkce, můžete je z výběru sloupce odebrat. Můžete také použít modul [Upravit metadata](edit-metadata.md) a označit ho jako **kategorií**. 
3.  V případě **metody bodování funkcí**vyberte jednu z následujících vytvořených statistických metod, které se použijí při výpočtu skóre.  

    | Metoda              | Požadavky                             |
    | ------------------- | ---------------------------------------- |
    | Korelace Pearsonova | Popisek může být text nebo číslo. Funkce musí být číselné. |
    Chí na druhou| Popisky a funkce mohou být textové nebo číselné. Tuto metodu použijte pro výpočet důležitosti funkcí pro dva kategorií sloupce.|

    > [!TIP]
    > Pokud změníte vybranou metriku, všechny ostatní výběry se resetují. Nezapomeňte nejdřív tuto možnost nastavit.
4.  Vyberte možnost **pracovat se sloupci funkce pouze** k vygenerování skóre pouze pro sloupce, které byly dříve označeny jako funkce. 

    Pokud zrušíte zaškrtnutí tohoto políčka, modul vytvoří skóre pro libovolný sloupec, který jinak splňuje kritéria, až do počtu sloupců zadaných v **počtu požadovaných funkcí**.  

5.  V poli **cílový sloupec**vyberte možnost **Spustit selektor sloupců** a vyberte sloupec popisku buď podle názvu, nebo podle jeho indexu. (Indexy jsou založené na jednom.)  
    Pro všechny metody, které zahrnují statistickou korelaci, je vyžadován sloupec popisku. Modul vrátí chybu v době návrhu, pokud jste zvolili možnost žádný sloupec popisku nebo více sloupců popisku. 

6.  V poli **počet požadovaných funkcí**zadejte počet sloupců funkce, které chcete vrátit, jako výsledek:  

    - Minimální počet funkcí, které můžete zadat, je jeden, ale doporučujeme tuto hodnotu zvýšit.  

    - Pokud je zadaný počet požadovaných funkcí větší než počet sloupců v datové sadě, vrátí se všechny funkce. Vrátí se i funkce s nulovým skóre.  

    - Pokud zadáte méně sloupců výsledků, než jsou sloupce funkce, jsou tyto funkce seřazené podle sestupného skóre. Vrátí se jenom ty hlavní funkce. 

7.  Odešlete kanál.

> [!IMPORTANT]
> Pokud budete používat **Výběr funkcí založených na filtrech** v odvození, je nutné použít [transformaci Select Columns](./select-columns-transform.md) k uložení vybrané funkce a [použít transformaci](./apply-transformation.md) , která použije funkci vybranou transformace na datovou sadu bodování.
>
> Chcete-li zajistit, aby výběry sloupců byly pro proces bodování stejné, přečtěte si následující snímek obrazovky.
> [!div class="mx-imgBorder"]
> ![Vzorový kanál](media/module/filter-based-feature-selection-score.png)

## <a name="results"></a>Výsledky

Po dokončení zpracování:

+ Pokud chcete zobrazit úplný seznam sloupců analyzovaných funkcí a jejich skóre, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**.  

+ Pokud chcete zobrazit datovou sadu na základě kritérií výběru vaší funkce, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**. 

Pokud datová sada obsahuje méně sloupců, než jste očekávali, podívejte se do nastavení modulu. Také ověřte datové typy sloupců, které jsou zadány jako vstup. Například pokud nastavíte **počet požadovaných funkcí** na 1, výstupní datová sada obsahuje pouze dva sloupce: sloupec popisek a nejmnohem vysoce seřazený sloupec funkce.


##  <a name="technical-notes"></a>Technické poznámky  

### <a name="implementation-details"></a>Podrobnosti implementace

Pokud používáte korelaci Pearsonova s numerickou funkcí a popiskem kategorií, skóre funkce se vypočítá takto:  

1.  Pro každou úroveň ve sloupci kategorií vypočítá podmíněný průměr číselného sloupce.  

2.  Korelace sloupce podmíněného znamená s číselným sloupcem.  

### <a name="requirements"></a>Požadavky  

-   Skóre výběru funkce nelze vygenerovat pro žádný sloupec, který je určen jako sloupec **popisku** nebo **skóre** .  

-   Pokusíte-li se použít metodu bodování se sloupcem datového typu, který metoda nepodporuje, modul vyvolá chybu. Do sloupce se ale přiřadí nula skóre.  

-   Pokud sloupec obsahuje logickou hodnotu (true/false), zpracuje se jako `True = 1` a `False = 0` .  

-   Sloupec nemůže být funkcí, pokud byl označený jako **popisek** nebo **skóre**.  

### <a name="how-missing-values-are-handled"></a>Jak se zpracovávají chybějící hodnoty  

-   Nemůžete zadat sloupec Target (Label), který obsahuje všechny chybějící hodnoty.  

-   Pokud sloupec obsahuje chybějící hodnoty, modul je při výpočtu skóre sloupce ignoruje.  

-   Pokud sloupec označený jako sloupec funkce obsahuje všechny chybějící hodnoty, modul přiřadí nula skóre.   


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 


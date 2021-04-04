---
title: 'Seskupit data do přihrádek: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu seskupit data do přihrádek seskupovat čísla nebo měnit distribuci souvislých dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91998494"
---
# <a name="group-data-into-bins-module"></a>Seskupit data do modulu přihrádky

Tento článek popisuje, jak použít modul seskupit data do přihrádek v Návrháři Azure Machine Learning, abyste mohli seskupovat čísla nebo měnit distribuci souvislých dat.

Modul seskupit data do přihrádek podporuje více možností pro binningu data. Můžete přizpůsobit, jak jsou okraje přihrádky nastaveny a jak jsou hodnoty rozděleny do přihrádek. Můžete například:  

+ Ručně zadejte řadu hodnot, které budou sloužit jako hranice přihrádky.  
+ Přiřaďte hodnoty do přihrádek pomocí *quantiles* nebo percentilu.  
+ Vynutit rovnoměrné rozložení hodnot do přihrádek.  

## <a name="more-about-binning-and-grouping"></a>Další informace o binningu a seskupení

*Binningu* data seskupení (někdy označovaná jako *kvantizační*) jsou důležitým nástrojem pro přípravu číselných dat pro Machine Learning. To je užitečné ve scénářích, jako jsou tyto:

+ Sloupec souvislých čísel má pro efektivní model příliš mnoho jedinečných hodnot. Takže můžete automaticky nebo ručně přiřadit hodnoty do skupin a vytvořit tak menší sadu diskrétních rozsahů.

+ Chcete nahradit sloupec čísel hodnotami kategorií, které reprezentují konkrétní rozsahy.

    Například můžete chtít seskupit hodnoty ve sloupci věk zadáním vlastních rozsahů, například 1-15, 16-22, 23-30 a tak dále pro demografické údaje uživatele.

+ Datová sada má několik extrémních hodnot, a to vše mimo očekávaný rozsah a tyto hodnoty mají vliv na vyškolený model. Chcete-li zmírnit posun v modelu, můžete data transformovat na jednotnou distribuci pomocí metody quantiles.

    V této metodě modul data skupiny do přihrádek určuje ideální umístění přihrádek a šířky přihrádek, aby se zajistilo, že přibližně stejný počet vzorků patří do každé přihrádky. V závislosti na zvolené normalizované metodě se hodnoty v přihrádkách buď transformují na percentily, nebo namapují na číslo přihrádky.

### <a name="examples-of-binning"></a>Příklady binningu

Následující diagram znázorňuje rozdělení číselných hodnot před a po binningu pomocí metody *quantiles* . Všimněte si, že v porovnání s nezpracovanými daty zůstala data rozdělenýá a transformovaná na normální škálu jednotek.  

> [!div class="mx-imgBorder"]
> ![Vizualizace výsledku](media/module/group-data-into-bins-result-example.png)

Vzhledem k tomu, že existuje mnoho způsobů, jak seskupovat data, je vše přizpůsobitelné, doporučujeme experimentovat s různými metodami a hodnotami. 

## <a name="how-to-configure-group-data-into-bins"></a>Konfigurace dat skupin do přihrádek

1. Přidejte do kanálu modul **data skupiny** do kanálu v návrháři. Tento modul můžete najít v **transformaci dat** kategorií.

2. Připojte datovou sadu, která má číselná data pro přihrádku. Kvantizační lze použít pouze pro sloupce, které obsahují číselná data. 

    Pokud datová sada obsahuje sloupce, které nejsou číselné, použijte modul [Vybrat sloupce v datové sadě](select-columns-in-dataset.md) k výběru podmnožiny sloupců, se kterými chcete pracovat.

3. Zadejte režim binningu. Režim binningu určuje jiné parametry, proto nezapomeňte nejprve vybrat možnost **režimu binningu** . Podporovány jsou následující typy binningu:

    - **Quantiles**: metoda Quantile přiřazuje hodnoty k přihrádkám na základě pořadí percentilu. Tato metoda se označuje také jako binningu s rovnou výškou.

    - **Stejná šířka**: s touto možností musíte zadat celkový počet přihrádek. Hodnoty z datového sloupce jsou umístěny v přihrádkách, takže každá přihrádka má stejný interval mezi počátečními a koncovými hodnotami. V důsledku toho mohou mít některé přihrádky více hodnot, pokud se data clumped kolem určitého bodu.

    - **Vlastní hrany**: můžete zadat hodnoty, které začínají jednotlivými přihrádkami. Hodnota Edge je vždy dolní hranice přihrádky. 
    
      Předpokládejme například, že chcete seskupit hodnoty do dvou přihrádek. Jedna bude mít hodnoty větší než 0 a jedna bude mít hodnoty menší nebo rovny 0. V tomto případě pro okraje bin zadáte **0** do **čárkami odděleného seznamu okrajů bin**. Výstupem modulu bude hodnota 1 a 2, která označuje index přihrádky pro každou hodnotu řádku. Všimněte si, že seznam hodnot oddělených čárkou musí být ve vzestupném pořadí, například 1, 3, 5, 7.
    
    > [!Note]
    > V nástroji Studio (Classic) je definovaný režim *entropie MDL* a neexistuje žádný odpovídající otevřený zdrojový balíček, který by se mohl ještě využít pro podporu v návrháři.        

4. Pokud používáte režimy **quantiles** a **stejné šířky** binningu, použijte možnost **počet přihrádek** k určení, kolik přihrádek nebo *quantiles* chcete vytvořit.

5. Pro **sloupce do přihrádky** použijte selektor sloupců a vyberte sloupce, které mají hodnoty, které chcete vyhodnotit. Sloupce musí být číselného datového typu.

    Stejné pravidlo binningu se použije na všechny použitelné sloupce, které vyberete. Pokud potřebujete vytvořit přihrádku některých sloupců pomocí jiné metody, použijte pro každou sadu sloupců samostatnou instanci dat skupiny do modulu bin.

    > [!WARNING]
    > Pokud vyberete sloupec, který není povoleným typem, je vygenerována Běhová chyba. Modul vrátí chybu, jakmile najde libovolný sloupec nepovoleného typu. Pokud se zobrazí chyba, zkontrolujte všechny vybrané sloupce. Chyba nezobrazuje seznam všech neplatných sloupců.

6. V **režimu výstupu** určete, jak chcete výstup hodnot quantized:

    + **Append**: vytvoří nový sloupec s hodnotami rozdělený a připojí ho ke vstupní tabulce.

    + **Umístění**: nahradí původní hodnoty novými hodnotami v datové sadě.

    + **ResultOnly**: vrátí pouze sloupce výsledků.

7. Pokud vyberete režim **quantiles** binningu, pomocí možnosti **normalizace Quantile** určete, jak jsou hodnoty normalizovány před řazením do quantiles. Všimněte si, že normalizace hodnot transformuje hodnoty, ale nemá vliv na konečný počet přihrádek.

    Podporovány jsou následující typy normalizace:

    + **Procenta**: hodnoty jsou normalizovány v rozsahu [0100].

    + **PQuantile**: hodnoty jsou normalizovány v rozsahu [0, 1].

    + **QuantileIndex**: hodnoty jsou normalizovány v rozsahu [1, počet přihrádek].

8. Pokud zvolíte možnost **Vlastní okraje** , zadejte čárkami oddělený seznam čísel, který chcete použít jako *okraje přihrádek* v textovém poli čárkami **oddělený seznam okrajů přihrádek** . 

    Hodnoty označují bod, který vydělí přihrádky. Pokud například zadáte jednu hodnotu okraje přihrádky, budou vygenerovány dvě přihrádky. Pokud zadáte dvě hodnoty okraje přihrádky, vygenerují se tři přihrádky.

    Hodnoty musí být seřazeny v pořadí, v jakém jsou vytvořeny přihrádky, od nejnižší po nejvyšší.

10. Vyberte **sloupce značek jako možnost kategorií** a určete tak, že sloupce quantized by měly být zpracovány jako proměnné kategorií.

11. Odešlete kanál.

## <a name="results"></a>Výsledky

Modul seskupit data do přihrádek vrátí datovou sadu, ve které byl každý prvek rozdělený podle zadaného režimu. 

Vrátí také *transformaci binningu*. Tato funkce může být předána modulu [použít transformaci](apply-transformation.md) k vytvoření nové ukázky dat pomocí stejného režimu binningu a parametrů.  

> [!TIP]
> Pokud používáte binningu na školicích datech, je nutné použít stejnou metodu binningu pro data, která používáte pro testování a předpovědi. Je také nutné použít stejné umístění přihrádek a šířky přihrádek. 
> 
> Aby se zajistilo, že data jsou vždycky transformovaná pomocí stejné metody binningu, doporučujeme uložit užitečné transformace dat. Pak je použijte pro jiné datové sady pomocí modulu [použít transformaci](apply-transformation.md) .

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

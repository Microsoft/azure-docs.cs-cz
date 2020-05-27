---
title: Seskupit data do přihrádek
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu seskupit data do přihrádek seskupovat čísla nebo měnit distribuci souvislých dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: b33aa3d2ab22fc986100d6141dd03d5547ef1862
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853824"
---
# <a name="group-data-into-bins"></a>Seskupit data do přihrádek

Tento článek popisuje, jak pomocí modulu [Seskupit data do přihrádek](group-data-into-bins.md) v Návrháři Azure Machine Learning (Preview) seskupovat čísla nebo měnit distribuci souvislých dat.

Modul [Seskupit data do přihrádek](group-data-into-bins.md) podporuje více možností pro binningu data. Můžete přizpůsobit, jak jsou okraje přihrádky nastaveny a jak jsou hodnoty rozděleny do přihrádek. Můžete například:  

+ Ručně zadejte řadu hodnot, které budou sloužit jako hranice přihrádky.  
+ Přiřaďte hodnoty do přihrádek pomocí *quantiles*nebo percentilu.  
+ Vynutit rovnoměrné rozložení hodnot do přihrádek.  

### <a name="more-about-binning-and-grouping"></a>Další informace o binningu a seskupení

*Binningu* nebo seskupení dat (někdy označované jako *kvantizační*) je důležitý nástroj pro přípravu číselných dat pro strojové učení a je užitečný v podobných scénářích:

+ Sloupec souvislých čísel má příliš mnoho jedinečných hodnot, aby bylo možné efektivně modelovat, takže automaticky nebo ručně přiřadíte hodnoty do skupin, aby bylo možné vytvořit menší množinu diskrétních rozsahů.

+ Nahradí sloupec čísel hodnotami kategorií, které reprezentují konkrétní rozsahy.

    Například můžete chtít seskupit hodnoty ve sloupci věk zadáním vlastních rozsahů, například 1-15, 16-22, 23-30 a tak dále pro demografické údaje uživatele.

+ Datová sada má několik extrémních hodnot, a to vše mimo očekávaný rozsah a tyto hodnoty mají vliv na vyškolený model. Chcete-li zmírnit posun v modelu, můžete data transformovat na jednotnou distribuci pomocí metody quantiles.

    V této metodě modul [data skupiny do přihrádek](group-data-into-bins.md) určuje ideální umístění přihrádek a šířky přihrádek, aby se zajistilo, že přibližně stejný počet vzorků patří do každé přihrádky. V závislosti na zvolené normalizované metodě se hodnoty v přihrádkách buď transformují buď na percentily, nebo namapované na číslo přihrádky.

### <a name="examples-of-binning"></a>Příklady binningu

Následující diagram znázorňuje rozdělení číselných hodnot před a po binningu pomocí metody **quantiles** . Všimněte si, že v porovnání s nezpracovanými daty zůstala data rozdělenýá a transformovaná na normální škálu jednotek.  

' Příklad můžete najít z výsledku tohoto spuštění kanálu: https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net '

Vzhledem k tomu, že existuje mnoho způsobů, jak seskupovat data, je vše přizpůsobitelné, doporučujeme experimentovat s různými metodami a hodnotami. 

## <a name="how-to-configure-group-data-into-bins"></a>Konfigurace dat skupin do přihrádek

1. Přidejte do kanálu modul **data skupiny** do kanálu v Návrháři (Preview). Tento modul můžete najít v **transformaci dat**kategorií.

2. Připojte datovou sadu, která má číselná data pro přihrádku.  Kvantizační se dá použít jenom pro sloupce obsahující číselná data. 

    Pokud datová sada obsahuje sloupce, které nejsou číselné, použijte modul [Vybrat sloupce v datové sadě](select-columns-in-dataset.md) k výběru podmnožiny sloupců, se kterými chcete pracovat.

3. Zadejte režim binningu. Režim binningu Určuje další parametry, takže si nezapomeňte nejdřív vybrat možnost **režimu binningu** . Podporovány jsou následující typy binningu:

    **Quantiles**: metoda Quantile přiřazuje hodnoty k přihrádkám na základě pořadí percentilu. Quantiles se také označuje jako binningu stejné výšky.

    **Stejná šířka**: s touto možností musíte zadat celkový počet přihrádek. Hodnoty z datového sloupce jsou umístěny v přihrádkách, takže každá přihrádka má stejný interval mezi počátečními a koncovými hodnotami. V důsledku toho mohou mít některé přihrádky více hodnot, pokud se data clumped kolem určitého bodu.

    **Vlastní hrany**: můžete zadat hodnoty, které začínají jednotlivými přihrádkami. Hodnota Edge je vždy dolní hranice přihrádky.  Předpokládejme například, že chcete seskupit hodnoty do dvou přihrádek, jednu s hodnotou větší než 0 a jednu s hodnotou menší nebo rovnou 0. V tomto případě pro okraje bin napište 0 do **čárkami odděleného seznamu okrajů bin**. Výstupem modulu by byl 1 a 2, který označuje index bin pro každou hodnotu řádku. Upozorňujeme, že seznam hodnot oddělených čárkou musí být ve vzestupném pořadí, například 1, 3, 5, 7.

4. **Počet přihrádek**: Pokud používáte režimy **quantiles**a **stejné šířky** binningu, použijte tuto možnost k určení, kolik přihrádek nebo *quantiles*, které chcete vytvořit.

5. Pro **sloupce do přihrádky**použijte selektor sloupců a vyberte sloupce, které mají hodnoty, které chcete vyhodnotit. Sloupce musí být číselného datového typu.

    Stejné pravidlo binningu se použije na všechny použitelné sloupce, které vyberete. Proto pokud potřebujete vytvořit z přihrádky některé sloupce pomocí jiné metody, použijte samostatnou instanci [dat skupiny do přihrádek](group-data-into-bins.md) pro každou sadu sloupců.

    > [!WARNING]
    > Pokud vyberete sloupec, který není povoleným typem, dojde k vygenerování běhové chyby. Modul vrátí chybu, jakmile najde libovolný sloupec nepovoleného typu. Pokud se zobrazí chyba, zkontrolujte všechny vybrané sloupce. Chyba nezobrazuje seznam všech neplatných sloupců.

6. V **režimu výstupu**určete, jak chcete výstup hodnot quantized.

      + **Append**: vytvoří nový sloupec s hodnotami rozdělený a připojí ho ke vstupní tabulce.

      + **Umístění**: nahradí původní hodnoty novými hodnotami v datové sadě.

      + **ResultOnly**: vrátí pouze sloupce výsledků.

7. Pokud vyberete režim **quantiles** binningu, pomocí možnosti **normalizace Quantile** určete, jak jsou hodnoty normalizovány před řazením do quantiles. Všimněte si, že normalizace hodnot transformuje hodnoty, ale nemá vliv na konečný počet přihrádek.

    Podporovány jsou následující typy normalizace:

    + **Procenta**: hodnoty jsou normalizovány v rozsahu [0100].

    + **PQuantile**: hodnoty jsou normalizovány v rozsahu [0, 1].

    + **QuantileIndex**: hodnoty jsou normalizovány v rozsahu [1, počet přihrádek].

8. Pokud zvolíte možnost **Vlastní okraje** , zadejte čárkami oddělený seznam čísel, který chcete použít jako *okraje přihrádky* v textovém poli čárkami **oddělený seznam okrajů přihrádek** .  Hodnoty označují bod, který vydělí přihrádky, takže pokud zadáte jednu hodnotu okraje přihrádky, vygenerují se dvě přihrádky. Zadáte-li dvě hodnoty okraje přihrádky, budou vygenerovány tři přihrádky a tak dále.

    Hodnoty musí být seřazeny v pořadí, v jakém jsou vytvořeny přihrádky, od nejnižší po nejvyšší.

10. **Označit sloupce jako kategorií**: tuto možnost vyberte, pokud chcete určit, že by se měly sloupce quantized zpracovat jako proměnné kategorií.

11. Odešlete kanál.

### <a name="results"></a>Výsledky

Modul [Seskupit data do přihrádek](group-data-into-bins.md) vrátí datovou sadu, ve které byl každý prvek rozdělený podle zadaného režimu. 

Vrátí také **transformaci binningu**, což je funkce, kterou lze předat modulu [použít transformaci](apply-transformation.md) pro nové vzorky dat pomocí stejného režimu binningu a parametrů.  

> [!TIP]
> Pamatujte, že pokud používáte binningu na školicích datech, musíte použít stejnou metodu binningu pro data, která používáte pro testování a předpovědi. To zahrnuje metodu binningu, umístění přihrádek a šířky přihrádek. 
> 
> Aby se zajistilo, že data jsou vždycky transformovaná pomocí stejné metody binningu, doporučujeme uložit užitečné transformace dat a pak je použít na jiné datové sady pomocí modulu [použít transformaci](apply-transformation.md) .

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

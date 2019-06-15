---
title: 'Normalizujte Data: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu normalizovat Data ve službě Azure Machine Learning k transformaci datové sady přes *normalizace*...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029442"
---
# <a name="normalize-data-module"></a>Normalizovat Data modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k transformaci datové sady přes *normalizace*.

Normalizace je technika, často používat jako součást přípravy dat pro machine learning. Cílem normalizace je ke změně hodnot číselných sloupců v datové sadě používat společné měřítko, bez narušující rozdíly v oblasti hodnot nebo ztráta informace. Normalizace je také nutný pro některé algoritmy modelovat data správně.

Předpokládejme například, že vstupní datová sada obsahuje jeden sloupec s hodnotami od 0 do 1 a další sloupec s hodnotami od 10 000 až 100 000. Skvělé rozdíl v *škálování* čísel může způsobit problémy při pokusu o kombinování hodnoty jako funkce při modelování.

*Normalizace* tyto problémy se vyhnete tak, že vytvoříte nové hodnoty, které obecné distribuce a poměry ve zdrojových datech udržovat zachováním hodnot v rámci nastavení použilo pro všechny číselné sloupce použít v modelu škálovací.

Tento modul nabízí několik možností pro transformaci číselná data:

- Všechny hodnoty můžete změnit na hodnotu 0-1 škálovat nebo transformovat hodnoty představující je podle percentilu řadí místo absolutní hodnoty.
- Normalizaci lze použít, do jednoho sloupce nebo do více sloupců ve stejné datové sady.
- Pokud budete muset opakovat pokus, nebo použít stejný postup normalizace k dalším datům, můžete uložit jako transformace normalizace kroky a použít k dalším datovým sadám, které mají stejné schéma.

> [!WARNING]
> Některé algoritmy vyžadují, že data budou normalizovány před trénování modelu. Jiné algoritmy provádět vlastní škálování dat nebo normalizace. Proto když zvolíte algoritmu používané k sestavení prediktivního modelu strojového učení, nezapomeňte zkontrolovat požadavky na data algoritmu před použitím normalizace na trénovací data.

##  <a name="configure-normalize-data"></a>Konfigurace normalizovat Data

Můžete použít jenom jedna metoda normalizace současně pomocí tohoto modulu. Proto se stejnou metodu normalizace platí pro všechny sloupce, které jste vybrali. Pokud chcete použít normalizace různé metody, použijte druhou instanci **normalizovat Data**.

1. Přidat **normalizovat Data** modulu do experimentu. V modulu v Azure Machine Learning, můžete najít v části **transformace dat**v **škálování a snižte** kategorie.

2. Připojte datové sady, který obsahuje alespoň jeden sloupec s čísly.

3. Pomocí selektoru sloupců vyberte číselné sloupce, které chcete normalizovat. Pokud se rozhodnete není jednotlivé sloupce ve výchozím nastavení **všechny** číselný typ sloupce ve vstupním jsou zahrnuty, a stejně normalizace platí pro všechny vybrané sloupce. 

    To může vést k neobvyklé výsledky při zahrnutí číselné sloupce, které by neměly být normalizovány! Vždy zkontrolujte pečlivě sloupce.

    Pokud nejsou zjištěny žádné číselné sloupce, zkontrolujte sloupec metadat, která chcete ověřit, že datový typ sloupce je podporované číselného typu.

    > [!TIP]
    > Aby bylo zajištěno, že sloupce z určitých typů jsou k dispozici jako vstup, zkuste použít [výběr sloupců v datové sadě](./select-columns-in-dataset.md) modulu před **normalizovat Data**.

4. **Použijte hodnotu 0 pro konstantní sloupce, pokud je zaškrtnuto**:  Tuto možnost vyberte, pokud libovolný číselný sloupec obsahuje pouze jednu hodnotu neměnné. Tím se zajistí, že nejsou použity tyto sloupce v operacích normalizace.

5. Z **metoda transformace** rozevírací seznam, zvolte jeden matematické funkce, která se vztahují na všechny vybrané sloupce. 
  
    - **Zscore**: Převede všechny hodnoty z skóre.
    
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:  
  
      ![normalizace pomocí z&#45;skóre](media/module/aml-normalization-z-score.png)
  
      Střední a standardní odchylka se počítají samostatně pro každý sloupec. Směrodatná odchylka základního souboru se používá.
  
    - **MinMax**: Normalizátor maximálních lineárně změní měřítko všechny funkce, které do intervalu [0,1].
    
      Posunutí hodnoty každé funkce tak, aby minimální hodnota je 0, a pak vydělí nové maximální hodnotu (která je rozdíl mezi původní maximální a minimální hodnoty) se provádí změny měřítka intervalu [0,1].
      
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:  
  
      ![normalizace pomocí nejnižší&#45;max. funkce](media/module/aml-normalization-minmax.png "AML_normalization minmax")  
  
    - **Logistické**: Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:

      ![Vzorec pro normalizace funkcí logistické](media/module/aml-normalization-logistic.png "AML_normalization logistické")  
  
    - **LogNormal**: Tato možnost převede všechny hodnoty logaritmicko škálování.
  
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:
  
      ![Vzorec protokolu&#45;normální rozdělení](media/module/aml-normalization-lognormal.png "AML_normalization logaritmicko")
    
      Tady μ a σ jsou parametry distribuce vypočítává empirických z dat jako odhady maximální pravděpodobnost, že pro každý sloupec samostatně.  
  
    - **TanH**: Všechny hodnoty se převedou na hyperbolický tangens.
    
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:
    
      ![normalizace funkcí tanh](media/module/aml-normalization-tanh.png "AML_normalization tanh")

6. Spusťte experiment nebo dvakrát klikněte **normalizovat Data** modul a vyberte **spustit vybrané**. 

## <a name="results"></a>Výsledky

**Normalizovat Data** modul generuje dva výstupy:

- Chcete-li zobrazit transformovaný hodnoty, klikněte pravým tlačítkem na modul, vyberte **transformují datová sada**a klikněte na tlačítko **vizualizovat**.

    Ve výchozím nastavení jsou tyto hodnoty převedeny na místě. Pokud chcete porovnat transformovaný hodnoty na původní hodnoty, použijte [přidat sloupce](./add-columns.md) modulu k opětovnému zkombinování datové sady a zobrazit sloupce vedle sebe.

- Pokud chcete transformace uložit, aby do jiné podobné datové sady můžete použít stejnou metodu normalizace, klikněte pravým tlačítkem na modul, vyberte **transformační funkce**a klikněte na tlačítko **uloží jako transformace**.

    Pak můžete načíst uložené transformace z **transformuje** skupinu v levém navigačním podokně a použijte ji pro datovou sadu se stejným schématem pomocí [. / použít transformace](apply-transformation.md).  


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 
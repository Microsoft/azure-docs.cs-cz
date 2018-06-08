---
title: 'Krok 2: Nahrání dat do experimentu Machine Learning | Microsoft Docs'
description: 'Vývoj prediktivního řešení návod krok 2: nahrání veřejná data uložena do Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.component: studio
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: dfa6ae8011da0299c270035b8b781d70a8e80119
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835786"
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Krok 2 průvodce: Nahrání stávajících dat do experimentu služby Azure Machine Learning
Toto je druhý krok tohoto průvodce, [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. **Nahrání existujících dat**
3. [Vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)
4. [Natrénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)
5. [Nasazení webové služby](walkthrough-5-publish-web-service.md)
6. [Přístup k webové službě](walkthrough-6-access-web-service.md)

- - -
K vývoji prediktivního modelu pro úvěrové riziko, potřebujeme data, která jsme můžete použít k trénování a pak model otestujeme. V tomto návodu použijeme "UCI Statlog (němčině platební Data) Data Set" z úložiště UC Irvine Machine Learning. Najdete ho tady:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Použijeme soubor s názvem **german.data**. Stáhněte si tento soubor na místní pevný disk.  

**German.data** datová sada obsahuje řádky 20 proměnných pro 1000 posledních žadatel o platební. Tyto proměnné 20 představují datová sada je sada funkcí ( *funkce vector*), který nabízí charakteristiky identifikace každý platební žadatel. Sloupec v jednotlivých řádcích představuje žadatele počítané úvěrové riziko, s 700 uchazeči označený jako nízkou úvěrové riziko a 300 vysokým rizikem.

Web UCI obsahuje popis atributů vektoru funkce pro tato data. To zahrnuje finanční informace, platební historii, stav zaměstnání a osobní údaje. Pro každý žadatel binární hodnocení byl daný, která určuje, jestli jsou na nízkou nebo vysokou úvěrového rizika. 

Tato data použijeme pro trénování modelu prediktivní analýzy. Když jsme hotovi, našeho modelu byste měli mít přijmout funkce vector pro nové jednotlivé a předvídání, zda je nízkou nebo vysokou úvěrové riziko.  

Zde je zajímavé Točitost. Popis sady dat na webu UCI uvádí, co ji stojí Pokud jsme misclassify osoby úvěrové riziko.
Pokud model předpovídá vysoké úvěrové riziko pro uživatele, který je ve skutečnosti nízkou úvěrové riziko, model chybnou udělal.
Ale zpětné chybnou klasifikaci je pětkrát nákladnější finanční instituce: Pokud model předpovídá nízkou úvěrové riziko pro uživatele, který je ve skutečnosti vysoké úvěrové riziko.

Ano chceme cvičení našeho modelu tak, aby náklady na tento druhý typ chybnou pětkrát vyšší než misclassifying jiným způsobem.
Jeden způsob, jak to provést při tréninku modelu v našem experimentu je duplikování (pětkrát) ty položky, které představují někdo s vysokou úvěrové riziko. Poté Pokud model misclassifies někdo jako nízkou úvěrové riziko, pokud jsou ve skutečnosti vysoce rizikové, model nemá tento stejný chybnou pětkrát, jednou pro každou duplicitní. Tím se zvýší náklady na této chyby ve výsledcích školení.


## <a name="convert-the-dataset-format"></a>Převést formát datové sady
Původní datové sady používá formát oddělené prázdné. Machine Learning Studio funguje lépe se soubor s oddělovači (CSV), proto jsme budete převést datovou sadu nahrazením mezer čárkami.  

Existuje mnoho způsobů, jak převést tato data. Jedním ze způsobů je pomocí následujícího příkazu prostředí Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Dalším způsobem je pomocí příkazu menšit Unix:  

    sed 's/ /,/g' german.data > german.csv  

V obou případech jsme vytvořili textový soubor s oddělovači verzi dat do souboru s názvem **german.csv** , jsme můžete použít v našem experimentu.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Nahrání datové sady do nástroje Machine Learning Studio
Jakmile data byl převeden do formátu CSV, musíme nahrajte ho do nástroje Machine Learning Studio. 

1. Otevřete domovskou stránku Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Klikněte na nabídku ![nabídky][1] v levém horním rohu okna, klikněte na **Azure Machine Learning**, vyberte **Studio**a přihlaste se.

3. Klikněte na tlačítko **+ nový** v dolní části okna.

4. Vyberte **datovou sadu**.

5. Vyberte **z místního souboru**.

    ![Přidejte datovou sadu, z místního souboru][2]

6. V **nahrát nová datová sada** dialogové okno, klikněte na tlačítko **Procházet** a najděte **german.csv** souborů, které jste vytvořili.

7. Zadejte název pro datovou sadu. V tomto návodu volání ji "UCI němčina platební karty Data".

8. Datový typ, vyberte **obecné soubor CSV neobsahuje záhlaví (. nh.csv)**.

9. Pokud chcete přidáte popis.

10. Klikněte **OK** zaškrtnutí.  

    ![Nahrání datové sady][3]

To nahrává data do datové sady modul, který používáme v experimentu.

Můžete spravovat datové sady, které jste odeslali do nástroje Studio kliknutím **datové sady** karty na levé straně okna Studio.

![Spravovat datové sady][4]

Další informace o importu dalších typů dat do experimentu najdete v tématu [importu trénovacích dat do Azure Machine Learning Studio](import-data.md).

**Další krok: [vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png

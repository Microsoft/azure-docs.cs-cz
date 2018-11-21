---
title: 'Krok 2: Nahrání dat do experimentu Machine Learning | Dokumentace Microsoftu'
description: 'Vývoj prediktivního řešení návod krok 2: nahrání uloženou veřejná data do Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 81f0cd108075d39c4694163736ad391cf938f958
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263702"
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Krok 2 průvodce: Nahrání stávajících dat do experimentu služby Azure Machine Learning
Toto je druhém kroku tohoto návodu, [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. **Nahrání existujících dat**
3. [Vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)
4. [Natrénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)
5. [Nasazení webové služby](walkthrough-5-publish-web-service.md)
6. [Přístup k webové službě](walkthrough-6-access-web-service.md)

- - -
K vývoji prediktivního modelu pro úvěrové riziko, potřebujeme data, která můžeme použít k trénování a pak model otestujeme. V tomto návodu používáme "UCI Statlog (německé daty o úvěrovém) Data Set" v úložišti UC Irvine Machine Learning. Najdete ho tady:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Soubor s názvem použijeme **german.data**. Stáhněte si tento soubor na místní pevný disk.  

**German.data** datová sada obsahuje řádky 20 proměnných pro 1000 posledních žadatel o kredit. Tyto proměnné 20 představují sadu funkcí datové sady ( *funkce vector*), poskytující charakteristiky identifikace pro každý kredit žadatel. Další sloupce v jednotlivých řádcích představuje žadatele počítané úvěrové riziko s vysokým rizikem označeno jako nízké úvěrové riziko a 300 700 uchazeči.

Web UCI poskytuje popis atributů vektoru funkce pro tato data. Jedná se o finanční informace, historie kreditu, stav pracovní a osobní údaje. Pro každý žadatel binární hodnocení byl daný označující, zda jsou nízké nebo vysoké úvěrové riziko. 

Tato data použijeme pro trénování modelu prediktivní analýzy. Když máme Hotovo, měl náš model přijmout vektor funkce pro nové uživatele a předpovědět, jestli uživatel je nízké nebo vysoké úvěrové riziko.  

Tady je zajímavé prvkem. Popis datové sady na webu UCI uvádí, co to stojí, pokud jsme misclassify osoby úvěrové riziko.
Pokud tento model předpovídá vysoké úvěrové riziko u někoho, kdo je ve skutečnosti nízké úvěrové riziko, model chybnou provedl.
Reverzní chybnou je pětkrát dražší do finanční instituce, ale: Pokud tento model předpovídá nízké úvěrové riziko u někoho, kdo je ve skutečnosti vysoké úvěrové riziko.

Ano chceme náš model trénování, tak, aby náklady na tento druhý typ. chybnou pětkrát větší než misclassifying jiným způsobem.
Jeden jednoduchý způsob, jak to udělat při cvičení modelu v našich experimentu je tak, že duplikujete (pětkrát) ty položky, které představují někdo s vysoké úvěrové riziko. Potom Pokud model misclassifies někdo jako nízké úvěrové riziko, když jsou ve skutečnosti vysoce rizikové, model nemá tento stejný chybnou pětkrát, jednou pro každý duplicitní. Tím se zvýší náklady na této chyby ve výsledcích školení.


## <a name="convert-the-dataset-format"></a>Převést formát datové sady
Původní datové sady používá formát oddělených prázdné. Machine Learning Studio lépe funguje pomocí souboru hodnot oddělených čárkami (CSV), proto jsme budete nahrazením mezer čárkami převést datové sady.  

Existuje mnoho způsobů, jak převést tato data. Jedním ze způsobů je pomocí následujícího příkazu Windows Powershellu:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Dalším způsobem je použití příkazu sed systému Unix:  

    sed 's/ /,/g' german.data > german.csv  

V obou případech jsme vytvořili oddělených čárkou verzi dat do souboru s názvem **german.csv** , který používáme v našich experimentu.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Nahrajte datovou sadu do nástroje Machine Learning Studio
Po převedení dat do formátu CSV, musíme ho nahrát do nástroje Machine Learning Studio. 

1. Otevřete domovskou stránku Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Klikněte na nabídku ![nabídky][1] v levém horním rohu okna, klikněte na tlačítko **Azure Machine Learning**vyberte **Studio**a přihlaste se.

3. Klikněte na tlačítko **+ nová** v dolní části okna.

4. Vyberte **datovou sadu**.

5. Vyberte **z místního souboru**.

    ![Přidat datovou sadu z místního souboru][2]

6. V **nahrát novou datovou sadu** dialogového okna, klikněte na tlačítko **Procházet** a najít **german.csv** souborů, které jste vytvořili.

7. Zadejte název pro tuto datovou sadu. V tomto návodu říkat "Údajů o kreditních kartách UCI němčina".

8. Datový typ, vyberte **obecný soubor CSV neobsahuje záhlaví (. nh.csv)**.

9. Pokud chcete přidáte popis.

10. Klikněte na tlačítko **OK** zaškrtávací políčko.  

    ![Nahrajte datovou sadu][3]

To nahrává data do datové sady modul, který používáme v jednom experimentu.

Můžete spravovat datové sady, které jste odeslali do Studio kliknutím **datových sad** karty na levé straně okna nástroje Studio.

![Správa datových sad][4]

Další informace o importu dalších typů dat do experimentu najdete v tématu [Import cvičných dat do Azure Machine Learning Studio](import-data.md).

**Další krok: [vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png

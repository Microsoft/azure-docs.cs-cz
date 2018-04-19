---
title: "Struktury projektů pomocí šablony procesu vědecké účely dat Team | Microsoft Docs"
description: "Jak vytvořit instanci šablony tým datové vědy procesu (TDSP) v Azure Machine Learning, která struktura projekty pro spolupráci"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: 0f7a6d0a58e2dcfd94e20a81ae12b42f238016c8
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Struktura projektů pomocí šablony procesu Team dat vědecké účely

Tento dokument obsahuje pokyny k vytváření projektů vědecké účely dat v Azure Machine Learning s šablonami tým datové vědy procesu (TDSP). Tyto šablony přispívají k struktura projekty pro spolupráci a reprodukovatelnosti. 


## <a name="what-is-the-team-data-science-process"></a>Co je vědecké zpracování týmových dat?
TDSP je proces vědecké účely agilní, iterativní, data pro spouštění a doručování pokročilou analýzu řešení. Je určený ke zlepšení spolupráce a efektivitu datové vědy týmy v podnikových organizací. Podporuje těchto cílů s čtyři klíčové komponenty:

   * Standardní [datové vědy cyklu](../team-data-science-process/lifecycle.md) definice.
   * Strukturu standardizované projektu [dokumentaci a vytváření sestav šablon projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Infrastruktury a prostředky pro spuštění projektu, například v uvedeném pořadí, výpočetního prostředí a úložiště infrastrukturu a kód úložiště.
   * [Nástrojů a pomůcek](https://github.com/Azure/Azure-TDSP-Utilities) pro vědecké zpracování dat projektu úlohy, jako například:
      - Spolupráce správy verzí
      - Revize kódu
      - Zkoumání dat a modelování
      - Plánování práce

Podrobnější diskuzi o TDSP, najdete v článku [přehled tým datové vědy procesu](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Proč používat TDSP strukturu a šablony?
Klíč k usnadnění efektivní spolupráce na datové vědě týmy je standardizace struktura, životního cyklu a vědecké účely projekty dat naleznete v dokumentaci. Vytváření projektů learning počítače pomocí šablony TDSP zajistit taková rozhraní pro koordinované týmovou spolupráci.

Dřív vydané [úložiště GitHub pro strukturu TDSP projektů a šablon](https://github.com/Azure/Azure-TDSP-ProjectTemplate) pro dosažení těchto cílů. Ale nebylo možné, až do této chvíle se vytvořit instanci TDSP strukturu a šablon v rámci nástroje vědecké účely data. Nyní je možné vytvořit projekt strojové učení, který vytvoří instanci TDSP struktura a dokumentace šablony. 

## <a name="things-to-note-before-creating-a-new-project"></a>Všimněte před vytvořením nového projektu
Zkontrolujte následující položky *před* vytvoření nového projektu:
* Zkontrolujte TDSP Machine Learning [šablony](https://aka.ms/tdspamlgithubrepo).
* Obsah (jiné než co se již nachází ve složce "dokumenty") musí být menší než 25 MB. Viz poznámka, která následuje tohoto seznamu.
* Ukázka\_složky dat je pouze pro malé datové soubory (méně než 5 MB), pomocí kterých můžete Otestujte svůj kód nebo spustit časná vývoj.
* Ukládání souborů, jako je aplikace Word a PowerPoint souborů, zvýšit velikost složky "dokumenty" podstatně. Doporučujeme, abyste najít spolupráce Wiki, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration), nebo jiný prostředek spolupráce k ukládání těchto souborů.
* Zjistěte, jak pro zpracování velkých souborů a výstupů v Machine Learning, přečtěte si téma [uložením změn a plánování práce s velkými soubory](http://aka.ms/aml-largefiles).

> [!NOTE]
> Všechny související dokumentace obsah (text, markdowns, obrázky a další soubory dokumentu), je *není* používá během spuštění projektu, jiné než soubor readme.md, se musí nacházet ve složce s názvem "dokumenty" (všechny malá písmena). Složka "dokumenty" je speciální složky ignorovat Machine Learning provádění tak, aby obsah v této složce nemáte získat zkopírován do výpočetní cíle zbytečně. Objekty v této složce také nemáte započítávat krytky 25 MB pro velikost projektu. Složce "dokumenty", je třeba místo pro ukládání souborů velký obrázek je potřeba v dokumentaci. Tyto soubory jsou stále sledovány objektem Git prostřednictvím historie spouštění. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Vytvoření instance struktury TDSP a šablony z Galerie šablon Machine Learning
Pokud chcete vytvořit nový projekt šablonami TDSP struktura a dokumentace, proveďte následující postupy.

### <a name="create-a-new-project"></a>Vytvoření nového projektu
Chcete-li vytvořit nový projekt, otevřete Azure Machine Learning. V části **projekty** v levém podokně vyberte znaménko plus (**+**) a potom vyberte **nový projekt**.

![Nový projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Vytvoření nového projektu strukturovaná TDSP
   1. Zadejte parametry a informace v příslušné políčko nebo seznamu:

      - název projektu
      - Adresáři projektu
      - Popis projektu
      - Prázdnou cestu úložiště Git
      - Název pracovního prostoru

   2. Potom v **vyhledávání** zadejte **TDSP**. 
   3. Když **struktury projekt se TDSP** možnost se zobrazí, vyberte tuto šablonu. 
   4. Vyberte **vytvořit** tlačítko vytvořte nový projekt se strukturou TDSP. Pokud jste zadali jako prázdný úložiště Git, při vytváření projektu (v příslušné textového pole), pak tohoto úložiště naplní projektu strukturu a obsah po vytvoření projektu.

![Vytvoření projektu TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Zkontrolujte strukturu TDSP projektu
Po vytvoření nového projektu, můžete zkontrolovat jeho strukturu (viz levém panelu na následujícím obrázku). Obsahuje všechny aspekty standardizované dokumentace pro pochopení firmy. Tyto položky zahrnují fázích životního cyklu TDSP, umístění dat, definice a architektuře této dokumentace šablony. 

Struktura zobrazená je odvozený od TDSP struktura, která je publikovaná v [strukturu, dokumenty a artefaktů šablony projektů TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate), se některé změny. Například několik šablon dokumentu jsou sloučeny do jednoho markdownu, konkrétně, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Struktura složek projektu
Šablona projektu TDSP obsahuje následující složky nejvyšší úrovně:
   - **kód**: obsahuje kód.
   - **dokumentace**: obsahuje nezbytné dokumentaci o projektu (například soubory markdown a související média).
   - **sample_data**: obsahuje **UKÁZKOVÉ (malé)** data, která můžete použít pro včasné vývoj nebo testování. Obvykle se tyto sady nejsou větší než několik (5) MB. Tato složka není pro úplnou nebo velké datové sady.

![Ukázková data](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Struktura TDSP a šablony
Budete muset přidat informace o projektu struktura a šablony. Se očekává k naplnění těchto s kódem a informace potřebné ke spouštění a poskytovat projektu. [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) soubor je šablonu, která budete muset upravit s informacemi, které jsou relevantní pro váš projekt. Se dodává se sadou otázek, které vám pomůžou vyplňte informace pro každý ze čtyř fází [životního cyklu TDSP](../team-data-science-process/lifecycle.md).

Příklad projektu struktury vypadá během provádění nebo po dokončení se zobrazí v levém panelu na následujícím obrázku. Tento projekt je z [proces vědecké účely dat Team ukázkový projekt: klasifikovat příjmů z USA úplné zjišťování dat v Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) ukázkový projekt.

![Příklad projektu struktury](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Zdokumentujte projektu
Odkazovat [TDSP dokumentace šablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate) informace o tom, jak dokumentu projektu. V aktuální šabloně dokumentaci Machine Learning TDSP, doporučujeme, jestli jste zahrnuli všechny informace v [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) souboru. Tato šablona by měla být vyplní informace, které jsou specifické pro váš projekt. 

Poskytujeme také [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) šablony. Tuto šablonu můžete použít k přidání informací, který není zahrnutý v projektu primární dokumentu, ale které jsou stále vhodné pro dokumentu. 

### <a name="example-project-report"></a>Ukázková sestava projektu
Můžete získat [příklad projektu sestavy](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Pomocí této sestavy projektu [USA příjem klasifikace ukázkový projekt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) ukazuje, jak vytvořit a používat šablonu TDSP pro projekt vědecké účely data.

## <a name="next-steps"></a>Další kroky
Pro usnadnění vaší znalosti o tom, jak používat TDSP strukturu a šablon v projektech Machine Learning, poskytujeme několik příkladů dokončený projekt v dokumentaci pro Machine Learning:

- Příklad, který ukazuje, jak vytvořit projekt TDSP v Machine Learning, najdete v části [proces vědecké účely dat Team ukázkový projekt: klasifikovat příjmů z úplné zjišťování nám dat v Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Ukázku, která používá přímý učení v přirozeném jazyce zpracování (NLP) v instanci TDSP projektu v Machine Learning, najdete v části [rozpoznávání entity Bio lékařské pomocí přirozeného jazyka zpracování pomocí hloubkové learning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).


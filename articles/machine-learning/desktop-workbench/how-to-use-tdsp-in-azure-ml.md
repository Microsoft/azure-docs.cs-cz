---
title: Strukturování projektů s šablonou vědecké zpracování týmových dat | Dokumentace Microsoftu
description: Jak vytvořit instanci šablony vědecké zpracování týmových dat (TDSP) ve službě Azure Machine Learning, která struktury projektů pro spolupráci
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 8e13ba0bcbd24dcc3cd08a0d2886f1da9d85de79
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249910"
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Strukturování projektů s šablonou vědecké zpracování týmových dat

Tento dokument obsahuje pokyny o tom, jak vytvořit projekty datových věd v dřívější verzi služby Azure Machine Learning pomocí šablony vědecké zpracování týmových dat (TDSP). Tyto šablony přispívají k strukturování projektů pro dosahování úrovně spolupráce a reprodukovatelnost. 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

## <a name="what-is-the-team-data-science-process"></a>Co je vědecké zpracování týmových dat?
TDSP je proces agile, iterativní, datové vědy pro provádění a doručování Pokročilá analytická řešení. Je určený ke zlepšení spolupráce a efektivitu datové vědy týmy v podnikových organizacích. Podporuje tyto cíle s čtyř klíčových komponent:

   * Standardní [životního cyklu datové vědy](../team-data-science-process/lifecycle.md) definice.
   * Strukturu projektu standardizované [dokumentaci a vytváření sestav šablony projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Infrastrukturu a prostředky pro spuštění projektu, jako jsou, výpočetní výkon a úložiště infrastrukturu a kód úložišť.
   * [Nástroje a pomůcky](https://github.com/Azure/Azure-TDSP-Utilities) pro datové vědy projektu úkoly, jako například:
      - Společná správa verzí
      - Revize kódu
      - Zkoumání a modelování dat
      - Plánování práce

Podrobnější diskuzi o TDSP, najdete v článku [přehled vědeckého zpracování týmových dat](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Proč byste měli použít TDSP strukturu a šablony?
Normalizaci struktury, životní cyklus a dokumentaci projekty datových věd je klíčem k umožňuje efektivní spolupráci na datové vědě týmy. Vytvoření projektů machine learning pomocí šablony TDSP poskytuje tyto architekturu pro koordinovat týmovou spolupráci.

Dříve jsme vydali [úložiště GitHub pro šablony a struktura projektu TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) a dosáhnout těchto cílů. Ale nebylo možné, až doteď, k vytvoření instance struktury TDSP a šablon v rámci nástroje pro datové vědy. Nyní je možné vytvořit projekt služby machine learning, která vytváří instanci TDSP strukturu a dokumentace ke službě šablon. 

## <a name="things-to-note-before-creating-a-new-project"></a>Všimněte před vytvořením nového projektu
Zkontrolujte následující položky *před* vytvoření nového projektu:
* Projděte si TDSP Machine Learning [šablony](https://aka.ms/tdspamlgithubrepo).
* Obsah (jiné než co je již k dispozici ve složce "dokumenty") se musí být kratší než 25 MB. Přečtěte si poznámku, která následuje tohoto seznamu.
* Ukázka\_složka dat je pouze pro malé datové soubory (méně než 5 MB), se kterými můžete testovat kód nebo spustit raného vývoje.
* Ukládání souborů, jako je Word a PowerPoint soubory, zvýšit velikost složky "docs" podstatně. Doporučujeme, který chcete najít spolupráci Wiki [SharePoint](https://products.office.com/sharepoint/collaboration), nebo jiný spolupracující prostředek k ukládání těchto souborů.
* Další způsob zpracování velkých souborů a výstupy ve službě Machine Learning, [uložením změn a práci s velkými soubory](https://aka.ms/aml-largefiles).

> [!NOTE]
> Všechny související s dokumentací obsah (text, markdowns, obrázky a další soubory dokumentu), který je *není* použít během spuštění projektu, jiné než souboru readme.md se musí nacházet ve složce s názvem "dokumenty" (malými písmeny). Složka "dokumenty" je speciální složky ignorují spouštěním Machine Learning, takže obsah v této složce nemáte se zkopíruje do cílových výpočetních prostředí zbytečně. Objekty v této složce také není počítají 25 MB limit pro velikost projektu. Složce "docs" například je místem, kde můžete ukládat soubory velký obrázek, je potřeba v dokumentaci k sadě. Tyto soubory jsou pořád sledovat Git prostřednictvím historie spuštění. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Vytvoření instance struktury TDSP a šablon z Galerie šablon Machine Learning
K vytvoření nového projektu s TDSP struktury a dokumentace šablony, proveďte následující postupy.

### <a name="create-a-new-project"></a>Vytvoření nového projektu
Chcete-li vytvořit nový projekt, otevřete Azure Machine Learning. V části **projekty** v levém podokně vyberte symbol plus (**+**) a pak vyberte **nový projekt**.

![Nový projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Vytvoření nového projektu s TDSP strukturované
   1. Zadejte parametry a informace v příslušné políčko nebo v seznamu:

      - Název projektu
      - Adresář projektu
      - Popis projektu
      - Prázdnou cestu úložiště Git
      - Název pracovního prostoru

   2. Pak v **hledání** zadejte **TDSP**. 
   3. Když **strukturování projektu s TDSP** možnost se zobrazí, vyberte tuto šablonu. 
   4. Vyberte **vytvořit** tlačítko pro vytvoření nového projektu s TDSP strukturou. Pokud zadáte prázdné úložiště Git při vytváření projektu (v příslušné textové pole), pak daného úložiště naplníte strukturu projektu a obsah po vytvoření projektu.

![Vytvořte projekt TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Zkontrolujte strukturu projekt TDSP
Po vytvoření nového projektu můžete zkontrolovat jeho strukturu (viz na levém panelu na následujícím obrázku). Obsahuje všechny aspekty standardizované dokumentace pro obchodní vysvětlení. Tyto položky zahrnují v jednotlivých fázích životního cyklu TDSP, umístění dat, definice a architektuře této dokumentaci k šabloně. 

Struktura zobrazená je odvozen z TDSP strukturu, která je publikovaná v [strukturu, dokumenty a artefaktů šablony projektů TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate), se některé změny. Například několik šablon dokumentů jsou sloučeny do jednoho markdownu, jmenovitě [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Struktura složek projektu
Šablona projektu TDSP obsahuje následující složky nejvyšší úrovně:
   - **kód**: obsahuje kód.
   - **dokumentace**: obsahuje dokumentaci o projektu (například soubory markdown a související média).
   - **sample_data**: obsahuje **vzorku (malé)** data, která můžete použít pro počáteční vývoj a testování. Obvykle tyto sady nejsou větší než několik (5) MB. Tato složka není pro úplné nebo rozsáhlých datových sad.

![Ukázková data](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Použití TDSP strukturu a šablony
Budete muset přidat informace specifické pro projekt pro strukturu a šablony. Už jste očekávali k naplnění těchto prvků s kódem a informace potřebné ke spouštění a dodávat váš projekt. [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) souboru je šablona, budete muset upravit informace relevantní pro váš projekt. Jde o sadu otázek, které vám pomůžou potřebné informace pro každý ze čtyř fází [životního cyklu TDSP](../team-data-science-process/lifecycle.md).

Příklad, jak struktura projektu vypadat při spuštění nebo po dokončení se zobrazí v levém panelu na následujícím obrázku. Tento projekt je z [ukázkový projekt vědeckého zpracování týmových dat: klasifikace příjmů v USA sčítání dat ve službě Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) ukázkového projektu.

![Ukázková struktura projektu](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Zdokumentujte svůj projekt
Odkazovat [TDSP dokumentace šablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate) informace o tom, jak dokumentovat váš projekt. V aktuální šablony dokumentace ke službě Machine Learning TDSP, doporučujeme zahrnout všechny informace [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) souboru. Tato šablona by měla být vyplní informace, které jsou specifické pro váš projekt. 

Poskytujeme také [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) šablony. Tuto šablonu můžete použít libovolné informace, který není součástí dokumentu primárního projektu, ale to je stále užitečná k dokumentaci. 

### <a name="example-project-report"></a>Příklad projektu sestavy
Můžete získat [příklad projektu sestavy](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Tuto sestavu projektu [USA příjem klasifikace ukázkový projekt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) ukazuje, jak použít šablonu TDSP pro projekt vědeckého zpracování dat a vytvoření instance.

## <a name="next-steps"></a>Další postup
Pro usnadnění pochopíte, jak používat TDSP strukturu a šablon v projektech pro Machine Learning, zajišťuje několik příkladů dokončený projekt v dokumentaci pro Machine Learning:

- Příklad, který ukazuje, jak vytvořit projekt TDSP ve službě Machine Learning, naleznete v tématu [ukázkový projekt vědeckého zpracování týmových dat: klasifikace příjmů v USA sčítání dat ve službě Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Ukázku, která využívá obsáhlý learning v přirozeného jazyka (NLP) zpracování v projektu vytvořit instanci TDSP v Machine Learning najdete v tématu [životopis medical rozpoznávání entit s obsáhlým learningem zpracování přirozeného jazyka pomocí](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).


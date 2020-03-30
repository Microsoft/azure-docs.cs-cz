---
title: Nastartovat experimenty z příkladů
titleSuffix: ML Studio (classic) - Azure
description: Naučte se, jak pomocí ukázkových experimentů strojového učení vytvářet nové experimenty pomocí Azure AI Gallery a Azure Machine Learning Studio (klasické).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: 1a913e714565ffb99237357e331ef5cf0189c40f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204217"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Vytváření experimentů Azure Machine Learning Studio (klasické) z pracovních příkladů v Galerii AI Azure

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Zjistěte, jak začít s příklady experimentů z [galerie Azure AI](https://gallery.azure.ai/) místo vytváření experimentů se strojovým učením od nuly. Příklady můžete použít k sestavení vlastních řešení strojového učení.

Galerie má příkladexperimenty microsoft azure machine learning studio (klasické) tým, stejně jako příklady sdílené komunitou Machine Learning. Také můžete klást otázky nebo experimenty komentovat.

Abyste se dozvěděli, jak používat galerii, podívejte se na tříminutové video [Copy other people's work to do data science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Kopírování práce jiných lidí pro vědecké zkoumání dat) z řady [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) (Vědecké zkoumání dat pro začátečníky).



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Vyhledání experimentu ke zkopírování v galerii Azure AI
Pokud se chcete podívat, jaké experimenty jsou k dispozici, přejděte do [Galerie](https://gallery.azure.ai/) a klikněte na **Experimenty** v horní části stránky.

### <a name="find-the-newest-or-most-popular-experiments"></a>Nalezení nejnovějších nebo nejoblíbenějších experimentů
Na této stránce si můžete zobrazit **naposled přidané** experimenty, přechodem dolů se podívat, **co je oblíbené**, nebo si prohlédnout nejnovější **populární experimenty Microsoftu**.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Vyhledání experimentu, který splňuje určité požadavky
Procházení všech experimentů:

1. V horní části stránky klikněte na **Browse all** (Procházet vše).
2. Na levé straně v oddílu **Zpřesnit podle** v části **Kategorie** vyberte **Experiment**. Zobrazí se všechny experimenty v galerii.
3. Experimenty, které splňují vaše požadavky, můžete najít několika různými způsoby:
   * **Vyberte filtry na levé straně.** Pokud chcete například procházet experimenty, které používají algoritmus detekce anomálií založený na PCA, klikněte na **Experiment** v části **Kategorie**. Potom v části **použitých algoritmů** klikněte na **Zobrazit vše** a v dialogovém okně zvolte **Detekce anomálií založená na PCA**. Možná si budete muset posunout zobrazení, abyste tuto možnost viděli.<br></br>
     ![Výběr filtrů](./media/sample-experiments/choose-an-algorithm.png)
   * **Použijte vyhledávací pole.** Pokud chcete například najít experimenty, kterými přispěl Microsoft, které se týkají rozpoznávaní číslic a které používají algoritmus podpůrného vektorového stroje se dvěma třídami, zadejte do vyhledávacího pole „digit recognition“. Potom vyberte filtry **Experiment**, **Pouze obsah společnosti Microsoft**a **Dvoutřídní podpůrný vektorový počítač**:<br></br>
     ![Použití vyhledávacího pole](./media/sample-experiments/search-for-experiments.png)
4. Kliknutím na experiment o něm zobrazíte více informací.
5. Pokud chcete experiment spustit nebo upravit, klikněte na stránce experimentu na **Open in Studio** (Otevřít v nástroji Studio). <br></br>

    ![Příklad experimentu](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Vytvoření nového experimentu s využitím příkladu jako šablony
Můžete také vytvořit nový experiment v Machine Learning Studio (klasické) pomocí galerie příklad jako šablonu.

1. Přihlaste se do [Studia](https://studio.azureml.net) pomocí přihlašovacích údajů účtu Microsoft a kliknutím na **Nový** vytvořte experiment.
2. Projděte si obsah příkladu a na některý klikněte.

Nový experiment je vytvořen ve vašem Machine Learning Studio (klasické) pracovní prostor pomocí příkladu experimentu jako šablony.

## <a name="next-steps"></a>Další kroky
* [Import dat z různých zdrojů](import-data.md)
* [Stručný úvodní kurz k jazyku R ve službě Machine Learning](r-quickstart.md)
* [Nasazení webové služby Machine Learning](deploy-a-machine-learning-web-service.md)

---
title: Vytváření experimentů na základě příkladů strojového učení – Azure | Dokumentace Microsoftu
description: Naučte se používat příklady experimentů se strojovým učením k vytváření nových experimentů s galerií Azure AI a službou Azure Machine Learning.
keywords: machine learning examples, sample experiment, machine learning sample, AI examples
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2018
ms.openlocfilehash: 7cb5c869e4523333ce0651559632070e5a19163c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-machine-learning-experiments-from-working-examples-in-azure-ai-gallery"></a>Vytváření experimentů se strojovým učením na základě funkčních příkladů v galerii Azure AI

Zjistěte, jak začít s příklady experimentů z [galerie Azure AI](https://gallery.cortanaintelligence.com/) místo vytváření experimentů se strojovým učením od nuly. Příklady můžete použít k sestavení vlastních řešení strojového učení.

V galerii jsou příklady experimentů od týmu Microsoft Azure Machine Learning i příklady sdílené komunitou služby Machine Learning. Také můžete klást otázky nebo experimenty komentovat.

Abyste se dozvěděli, jak používat galerii, podívejte se na tříminutové video [Copy other people's work to do data science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Kopírování práce jiných lidí pro vědecké zkoumání dat) z řady [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) (Vědecké zkoumání dat pro začátečníky).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Vyhledání experimentu ke zkopírování v galerii Azure AI
Pokud se chcete podívat, jaké experimenty jsou k dispozici, přejděte do [Galerie](https://gallery.cortanaintelligence.com/) a klikněte na **Experimenty** v horní části stránky.

### <a name="find-the-newest-or-most-popular-experiments"></a>Nalezení nejnovějších nebo nejoblíbenějších experimentů
Na této stránce si můžete zobrazit **naposled přidané** experimenty, přechodem dolů se podívat, **co je oblíbené**, nebo si prohlédnout nejnovější **populární experimenty Microsoftu**.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Vyhledání experimentu, který splňuje určité požadavky
Procházení všech experimentů:

1. V horní části stránky klikněte na **Browse all** (Procházet vše).
2. Na levé straně v oddílu **Zpřesnit podle** v části **Kategorie** vyberte **Experiment**. Zobrazí se všechny experimenty v galerii.
3. Experimenty, které splňují vaše požadavky, můžete najít několika různými způsoby:
   * **Vyberte filtry vlevo.** Pokud chcete například procházet experimenty, které používají algoritmus detekce anomálií založený na PCA, klikněte na **Experiment** v části **Kategorie**. Potom v části **použitých algoritmů** klikněte na **Zobrazit vše** a v dialogovém okně zvolte **Detekce anomálií založená na PCA**. Možná si budete muset posunout zobrazení, abyste tuto možnost viděli.<br></br>
     ![Výběr filtrů](./media/sample-experiments/choose-an-algorithm.png)
   * **Použijte vyhledávací pole.** Pokud chcete například najít experimenty, kterými přispěl Microsoft, které se týkají rozpoznávaní číslic a které používají algoritmus podpůrného vektorového stroje se dvěma třídami, zadejte do vyhledávacího pole „digit recognition“. Vyberte filtry **Experiment**, **Microsoft content only** (Jen obsah Microsoftu) a **Two-Class Support Vector Machine**:<br></br>
     ![Použití vyhledávacího pole](./media/sample-experiments/search-for-experiments.png)
4. Kliknutím na experiment o něm zobrazíte více informací.
5. Pokud chcete experiment spustit nebo upravit, klikněte na stránce experimentu na **Open in Studio** (Otevřít v nástroji Studio). <br></br>

    ![Příklad experimentu](./media/sample-experiments/example-experiment.png)

    > [!NOTE]
    > Když poprvé otevřete experiment v Machine Learning Studiu, můžete ho vyzkoušet zdarma, nebo si koupit předplatné Azure. [Další informace o bezplatné zkušební verzi Machine Learning Studia v porovnání s placenou službou](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Vytvoření nového experimentu s využitím příkladu jako šablony
Nový experiment v nástroji Machine Learning Studio můžete vytvořit také pomocí příkladu z galerie jako šablony.

1. Přihlaste se do [Studia](https://studio.azureml.net) pomocí přihlašovacích údajů účtu Microsoft a kliknutím na **Nový** vytvořte experiment.
2. Projděte si obsah příkladu a na některý klikněte.

V pracovním prostoru Machine Learning Studio se vytvoří nový experiment s využitím příkladu experimentu jako šablony.

## <a name="next-steps"></a>Další kroky
* [Import dat z různých zdrojů](import-data.md)
* [Stručný úvodní kurz k jazyku R ve službě Machine Learning](r-quickstart.md)
* [Nasazení webové služby Machine Learning](publish-a-machine-learning-web-service.md)

---
title: 'ML Studio (Classic): zahájení experimentů z příkladů – Azure'
description: Naučte se používat ukázková experimenty strojového učení k vytváření nových experimentů s Azure AI Gallery a Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: d62b958189ac01f1beabdbf17ee5cc6a08481e23
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362295"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Vytváření Azure Machine Learning Studio (klasických) experimentů z pracovních příkladů v Azure AI Gallery

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  




Zjistěte, jak začít s příklady experimentů z [galerie Azure AI](https://gallery.azure.ai/) místo vytváření experimentů se strojovým učením od nuly. Příklady můžete použít k sestavení vlastních řešení strojového učení.

Galerie má příklady experimentů podle týmu Microsoft Azure Machine Learning Studio (Classic) a také příklady, které sdílí komunita Machine Learning. Také můžete klást otázky nebo experimenty komentovat.

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
   * **Použijte vyhledávací pole.** Pokud chcete například najít experimenty, kterými přispěl Microsoft, které se týkají rozpoznávaní číslic a které používají algoritmus podpůrného vektorového stroje se dvěma třídami, zadejte do vyhledávacího pole „digit recognition“. Pak vyberte filtry **experiment**, **Microsoft Content Only**a **Two-Class support Vector Machine**:<br></br>
     ![Použití vyhledávacího pole](./media/sample-experiments/search-for-experiments.png)
4. Kliknutím na experiment o něm zobrazíte více informací.
5. Pokud chcete experiment spustit nebo upravit, klikněte na stránce experimentu na **Open in Studio** (Otevřít v nástroji Studio). <br></br>

    ![Příklad experimentu](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Vytvoření nového experimentu s využitím příkladu jako šablony
Nový experiment můžete také vytvořit v Machine Learning Studio (Classic) pomocí příkladu v galerii jako šablonu.

1. Přihlaste se do [Studia](https://studio.azureml.net) pomocí přihlašovacích údajů účtu Microsoft a kliknutím na **Nový** vytvořte experiment.
2. Projděte si obsah příkladu a na některý klikněte.

V pracovním prostoru Machine Learning Studio (klasického) se vytvoří nový experiment s použitím ukázkového experimentu jako šablony.

## <a name="next-steps"></a>Další kroky
* [Import dat z různých zdrojů](import-data.md)
* [Nasazení webové služby Machine Learning](deploy-a-machine-learning-web-service.md)

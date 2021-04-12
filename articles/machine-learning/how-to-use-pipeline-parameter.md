---
title: Použití parametrů kanálu v Návrháři k sestavování univerzálních kanálů
titleSuffix: Azure Machine Learning
description: Použití parametrů kanálu v Návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 30ae737a170c337fe6be51521aeb358cdcebd44b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107328"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Použití parametrů kanálu v Návrháři k sestavování univerzálních kanálů

Použijte parametry kanálu k vytváření flexibilních kanálů v návrháři. Parametry kanálu umožňují dynamicky nastavovat hodnoty za běhu a zapouzdřit logiku kanálu a znovu použít prostředky.

Parametry kanálu jsou zvláště užitečné při odesílání spuštění kanálu, [rekurzi modelů](how-to-retrain-designer.md)nebo [provádění dávkových předpovědi](how-to-run-batch-predictions-designer.md).

V tomto článku se dozvíte, jak provést následující akce:

> [!div class="checklist"]
> * Vytvořit parametry kanálu
> * Odstranění a Správa parametrů kanálu
> * Spuštění kanálu triggeru během úprav parametrů kanálu

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* Seznámení s průvodcem pro návrháře dokončíte pomocí [kurzu návrháře](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Vytvořit parametr kanálu

Existují tři způsoby, jak v Návrháři vytvořit parametr kanálu:
- Vytvořte parametr kanálu na panelu nastavení a navažte ho na modul.
- Povýší parametr modulu na parametr kanálu.
- Zvýšení úrovně datové sady na parametr kanálu

> [!NOTE]
> Parametry kanálu podporují jenom základní datové typy `int` , jako jsou, `float` a `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Možnost 1: vytvoření parametru kanálu na panelu nastavení

V této části vytvoříte parametr kanálu na panelu nastavení.

V tomto příkladu vytvoříte parametr kanálu, který definuje, jak kanál vyplní chybějící data pomocí modulu **Vyčištění chybějících dat** .

1. Vedle názvu konceptu kanálu vyberte **ikonu ozubeného kolečka** a otevřete panel **Nastavení** .

1. V části **parametry kanálu** vyberte **+** ikonu.

1.  Zadejte název parametru a výchozí hodnotu. 

    Zadejte například `replace-missing-value` jako název parametru a `0` jako výchozí hodnotu.

   ![Snímek obrazovky, který ukazuje, jak vytvořit parametr kanálu](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Po vytvoření parametru kanálu [ho musíte připojit k parametru modulu](#attach-module-parameter-to-pipeline-parameter) , který chcete dynamicky nastavit.

### <a name="option-2-promote-a-module-parameter"></a>Možnost 2: zvýšení úrovně parametru modulu

Nejjednodušší způsob, jak vytvořit parametr kanálu pro hodnotu modulu, je propagovat parametr modulu. Pomocí následujících kroků povýšíte parametr modulu na parametr kanálu:

1. Vyberte modul, ke kterému chcete přiřadit parametr kanálu.
1. V podokně podrobností modulu MouseOver parametr, který chcete zadat.
1. Vyberte tři tečky (**...**), které se zobrazí.
1. Vyberte možnost **Přidat do parametru kanálu**.

    ![Snímek obrazovky, který ukazuje, jak propagovat parametr modulu na kanál parametr1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Zadejte název parametru a výchozí hodnotu.
1. Vyberte **Uložit**.

Pro tento parametr teď můžete zadat nové hodnoty, kdykoli odešlete tento kanál.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Možnost 3: zvýšení úrovně datové sady na parametr kanálu

Pokud chcete odeslat svůj kanál s proměnnými datovými sadami, je nutné zvýšit vaši datovou sadu na parametr kanálu:

1. Vyberte datovou sadu, kterou chcete převést na parametr kanálu.

1. V panelu podrobností datové sady, ověřte **nastavení parametru kanálu**.

   ![Snímek obrazovky, který ukazuje, jak nastavit datovou sadu jako parametr kanálu](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

Při příštím spuštění kanálu teď můžete zadat jinou datovou sadu pomocí parametru kanálu.

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>Připojit a odpojit parametr modulu k parametru kanálu 

V této části se dozvíte, jak připojit a odpojit parametr modulu k parametru kanálu.

### <a name="attach-module-parameter-to-pipeline-parameter"></a>Připojit parametr modulu k parametru kanálu

Stejné parametry modulu duplicitních modulů můžete připojit ke stejnému parametru kanálu, pokud chcete hodnotu změnit najednou při aktivaci spuštění kanálu.

Následující příklad obsahuje duplicitní modul **Vyčištění chybějících dat** . U každého modulu **Vyčištění chybějících dat** připojte **náhradní hodnotu** k parametru kanálu **nahradit-chybějící-hodnota**:

1. Vyberte modul **Vyčištění chybějících dat** .

1. V podokně podrobností modulu napravo od plátna nastavte **režim čištění** na "vlastní substituční hodnota".

1. MouseOver pole **nahrazující hodnotu** .

1. Vyberte tři tečky (**...**), které se zobrazí.

1. Vyberte parametr kanálu `replace-missing-value` .

   ![Snímek obrazovky, který ukazuje, jak připojit parametr kanálu](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Úspěšně jste připojili pole **nahrazující hodnota** k parametru kanálu. 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>Odpojit parametr modulu k parametru kanálu

Po připojení **náhradní hodnoty** k parametru kanálu není možné provést akci.

Parametr modulu můžete odpojit od parametru kanálu kliknutím na tlačítko se třemi tečkami (**...**) vedle parametru modulu a výběrem příkazu **Odpojit od parametru kanálu**.

 ![Snímek obrazovky, který po připojení k parametru kanálu zobrazuje nenapadnutelný](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Aktualizace a odstranění parametrů kanálu

V této části se dozvíte, jak aktualizovat a odstranit parametry kanálu.

### <a name="update-pipeline-parameters"></a>Aktualizovat parametry kanálu

Pomocí následujících kroků aktualizujte parametr kanálu modulu:

1. V horní části plátna vyberte ikonu ozubeného kolečka.
1. V části **parametry kanálu** můžete zobrazit a aktualizovat název a výchozí hodnotu pro všechny parametry kanálu.

### <a name="delete-a-dataset-pipeline-parameter"></a>Odstraní parametr kanálu datové sady.

Pomocí následujících kroků odstraňte parametr kanálu datové sady:

1. Vyberte modul DataSet.
1. Zrušte **označení sady možností jako parametr kanálu**.


### <a name="delete-module-pipeline-parameters"></a>Odstranit parametry kanálu modulu

Pomocí následujícího postupu odstraňte parametr kanálu modulu:

1. V horní části plátna vyberte ikonu ozubeného kolečka.

1. Vyberte tři tečky (**...**) vedle parametru kanálu.

    V tomto zobrazení se dozvíte, ke kterým modulům je připojen parametr kanálu.

    ![Snímek obrazovky zobrazující aktuální parametr kanálu aplikovaný na modul](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. Vyberte **Odstranit parametr** a odstraňte parametr kanálu.

    > [!NOTE]
    > Odstraněním parametru kanálu dojde k odpojení všech parametrů připojeného modulu a hodnota odpojených parametrů modulu bude uchovávat aktuální hodnotu parametru kanálu.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Aktivace spuštění kanálu s parametry kanálu 

V této části se dozvíte, jak odeslat běh kanálu při nastavování parametrů kanálu.

### <a name="resubmit-a-pipeline-run"></a>Opětovné odeslání běhu kanálu

Po odeslání kanálu s parametry kanálu můžete znovu odeslat běh kanálu s různými parametry:

1. Přejít na stránku s podrobnostmi o kanálu V okně s **přehledem spuštění kanálu** můžete kontrolovat aktuální parametry kanálu a jejich hodnoty.

1. Vyberte **znovu odeslat**.
1. V části **nastavení spuštění kanálu** zadejte nové parametry kanálu. 

![Snímek obrazovky, který zobrazuje kanál opětovného odeslání s parametry kanálu](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Použití publikovaných kanálů

Můžete také publikovat kanál a použít jeho parametry kanálu. **Publikovaný kanál** je kanál, který byl nasazen do výpočetního prostředku, který mohou klientské aplikace vyvolat prostřednictvím koncového bodu REST.

Publikované koncové body jsou zvláště užitečné pro scénáře přeškolení a předpovědi dávek. Další informace najdete v tématu [postup revýuky modelů v Návrháři](how-to-retrain-designer.md) nebo [spuštění dávkového předpovědi v Návrháři](how-to-run-batch-predictions-designer.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit parametry kanálu v návrháři. Dále si přečtěte téma jak můžete použít parametry kanálu k [reučení modelů](how-to-retrain-designer.md) nebo provádění [dávkových předpovědi](how-to-run-batch-predictions-designer.md).

Můžete se také seznámit [s tím, jak pomocí sady SDK programově používat kanály](how-to-deploy-pipelines.md).

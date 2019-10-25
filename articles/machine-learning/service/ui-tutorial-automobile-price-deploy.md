---
title: 'Kurz: nasazení modelu strojového učení pomocí vizuálního rozhraní'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet řešení prediktivní analýzy v Azure Machine Learning vizuálním rozhraní. Naučte se, vyhodnocovat a nasazují model strojového učení pomocí přetažení modulů.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792678"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Kurz: nasazení modelu strojového učení pomocí vizuálního rozhraní

Pokud chcete jiným osobám umožnit používání prediktivního modelu vyvinutého v [rámci jednoho kurzu](ui-tutorial-automobile-price-train-score.md), můžete ho nasadit jako koncový bod v reálném čase. V části 1 jste si vyškole svůj model. Nyní je čas vytvořit nové předpovědi na základě vstupu uživatele. V této části kurzu:

> [!div class="checklist"]
> * Nasazení koncového bodu v reálném čase
> * Vytvoření clusteru Inferencing
> * Testování koncového bodu v reálném čase

## <a name="prerequisites"></a>Předpoklady

Kompletní [část tohoto kurzu](ui-tutorial-automobile-price-train-score.md) se naučíte, jak ve vizuálním rozhraní naučit a vyhodnocovat model strojového učení.

## <a name="deploy-a-real-time-endpoint"></a>Nasazení koncového bodu v reálném čase

Aby bylo možné svůj kanál nasadit, musíte:

1. Převeďte školicí kanál na kanál pro odvození v reálném čase, který odebere školicí moduly a přidá vstupy a výstupy pro požadavky Inferencing.
1. Nasaďte kanál odvození.

### <a name="create-a-real-time-inference-pipeline"></a>Vytvoření kanálu odvození v reálném čase

1. V horní části plátna kanálu vyberte **vytvořit kanál odvození**  > **kanál pro odvození v reálném čase** .

    Když vyberete **vytvořit kanál odvození**, nastane několik věcí:
    
    * Trained model je uložen jako modul **DataSet** v paletě modulu. Můžete ji najít v části **Moje datové sady**.
    * Moduly, jako je **model výuky** a **rozdělená data**, které se použily pro školení, se odeberou.
    * Uložený vycvičený model se přidá zpátky do kanálu.
    * Jsou přidány výstupní moduly **webové služby** a **webové služby** . Tyto moduly identifikují, kam budou data uživatelů zadávat model a kde se vrátí data.

    > [!Note]
    > **Školicí kanál** se uloží pod novou kartu v horní části plátna kanálu. Může být také nalezen jako publikovaný kanál ve vizuálním rozhraní.
    >

    Váš kanál by teď měl vypadat takto:  

   ![Snímek obrazovky znázorňující očekávanou konfiguraci kanálu po jeho přípravě na nasazení](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. Vyberte **Spustit** a použijte stejný cíl výpočtů a experiment jste použili v části 1.

1. Vyberte modul určení **skóre modelu** .

1. V podokně Vlastnosti vyberte **výstupy**  > **vizualizaci** , abyste ověřili, že model stále pracuje. Můžete vidět, že se zobrazí původní data spolu s předpovězenou cenou ("popisky s skóre").

1. Vyberte **Nasadit**.

### <a name="create-an-inferencing-cluster"></a>Vytvoření clusteru Inferencing

V dialogovém okně, které se zobrazí, můžete v pracovním prostoru vybrat z existujících clusterů Azure Kubernetes Service (AKS) a model nasadit. Pokud nemáte cluster AKS, vytvořte ho pomocí následujících kroků.

1. V dialogovém okně vyberte **COMPUTE** a přejděte na stránku **COMPUTE** .

1. Na pásu karet navigace vyberte **odvozené clustery**  >  **+ nové**.

    ![Snímek obrazovky ukazující, jak přejít k novému podoknu clusteru odvození](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. V podokně odvození clusteru nakonfigurujte novou službu Kubernetes.

1. Jako **výpočetní název**zadejte "AKS-COMPUTE".
    
1. Vyberte **oblast**dostupnou v okolí.

1. Vyberte **Create** (Vytvořit).

    > [!Note]
    > Vytvoření nové služby AKS trvá přibližně 15 minut. Stav zřizování můžete kontrolovat na stránce **odvození clusterů** .
    >

### <a name="deploy-the-real-time-endpoint"></a>Nasazení koncového bodu v reálném čase

Až se dokončí zřizování služby AKS, vraťte se do kanálu Inferencing v reálném čase, abyste mohli dokončit nasazení.

1. Na plátně vyberte **nasadit** .

1. Vyberte **nasadit nový koncový bod v reálném čase**. 

1. Vyberte cluster AKS, který jste vytvořili.

1. Vyberte **Nasadit**.

    ![Snímek obrazovky ukazující, jak nastavit nový koncový bod v reálném čase](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Po dokončení nasazení se zobrazí oznámení o úspěchu nad plátnem, může to trvat několik minut.

## <a name="test-the-real-time-endpoint"></a>Testování koncového bodu v reálném čase

Koncový bod v reálném čase můžete otestovat tak, že přejdete na stránku **koncové body** v navigačním podokně pracovního prostoru na levé straně.

1. Na stránce **koncové body** vyberte koncový bod, který jste nasadili.

    ![Snímek obrazovky zobrazující kartu koncových bodů v reálném čase s zvýrazněným nedávno vytvořeným koncovým bodem](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Vyberte **test**.

1. Zadejte testovací data nebo použijte možnost automatického vyplňování ukázkových dat a vyberte **test**.

    Požadavek na test se odešle do koncového bodu a výsledky se zobrazí na stránce. I když je pro vstupní data vygenerována hodnota ceny, není použita k vygenerování hodnoty předpovědi.

    ![Snímek obrazovky ukazující, jak otestovat koncový bod v reálném čase pomocí popisku s skóre pro zvýrazněnou cenu](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s klíčovými kroky při vytváření, nasazování a využívání modelu strojového učení ve vizuálním rozhraní. Další informace o tom, jak můžete pomocí vizuálního rozhraní vyřešit jiné typy problémů, najdete v našich dalších ukázkových kanálech.

> [!div class="nextstepaction"]
> [Ukázka klasifikace úvěrového rizika](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)

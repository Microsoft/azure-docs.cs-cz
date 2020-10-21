---
title: 'Kurz: nasazení modelů ML pomocí návrháře'
titleSuffix: Azure Machine Learning
description: V tomto kurzu se dozvíte, jak vytvořit řešení prediktivní analýzy v Návrháři Azure Machine Learning. Využijte moduly pro vytažení, skóre a nasazení modelu strojového učení.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: 95e5b3ac568cfa370fd1e49fad990b681aef46d9
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311520"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Kurz: nasazení modelu strojového učení pomocí návrháře


Můžete nasadit prediktivní model vyvinutý v [rámci jednoho kurzu](tutorial-designer-automobile-price-train-score.md) , který jiným uživatelům umožní tuto možnost použít. V první části jste si vyškole svůj model. Nyní je čas vytvořit nové předpovědi na základě vstupu uživatele. V této části kurzu budete:

> [!div class="checklist"]
> * Vytvoří kanál pro odvození v reálném čase.
> * Vytvořte cluster Inferencing.
> * Nasaďte koncový bod v reálném čase.
> * Otestujte koncový bod v reálném čase.

## <a name="prerequisites"></a>Předpoklady

Kompletní [část tohoto kurzu](tutorial-designer-automobile-price-train-score.md) se naučíte, jak v Návrháři naučit a vyhodnocovat model strojového učení.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Vytvoření kanálu odvození v reálném čase

Pokud chcete svůj kanál nasadit, musíte nejdřív převést kanál školení na kanál pro odvození v reálném čase. Tento proces odebere školicí moduly a přidá vstupy a výstupy webové služby pro zpracování požadavků.

### <a name="create-a-real-time-inference-pipeline"></a>Vytvoření kanálu odvození v reálném čase

1. Nad plátnem kanálu vyberte **vytvořit odvození kanálu**  >  **odvození kanálu v reálném čase**.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Snímek obrazovky ukazující, kde najít tlačítko vytvořit kanál":::

    Váš kanál by teď měl vypadat takto: 

   ![Snímek obrazovky znázorňující očekávanou konfiguraci kanálu po jeho přípravě na nasazení](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Když vyberete **vytvořit kanál odvození**, nastane několik věcí:
    
    * Trained model je uložen jako modul **DataSet** v paletě modulu. Můžete ji najít v části **Moje datové sady**.
    * Odeberou se moduly, jako je **model výuky** a **rozdělená data** .
    * Uložený vycvičený model se přidá zpátky do kanálu.
    * Jsou přidány výstupní moduly **webové služby** a **webové služby** . Tyto moduly ukazují, kde data uživatele vstoupí do kanálu a kde se vrátí data.

    > [!NOTE]
    > Ve výchozím nastavení bude **vstup webové služby** očekávat stejné schéma dat jako školicí data použitá k vytvoření prediktivního kanálu. V tomto scénáři je cena zahrnutá ve schématu. Cena se ale během předpovědi nepoužívá jako faktor.
    >

1. Vyberte **Odeslat**a použijte stejný cíl výpočtů a experiment, který jste použili v první části.

    Pokud se jedná o první spuštění, může trvat až 20 minut, než se váš kanál dokončí. Výchozí nastavení COMPUTE mají minimální velikost uzlu 0, což znamená, že Návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky už jsou přidělené. Kromě toho Návrhář používá výsledky v mezipaměti pro každý modul k dalšímu zvýšení efektivity.

1. Vyberte **Nasadit**.

## <a name="create-an-inferencing-cluster"></a>Vytvoření clusteru Inferencing

V dialogovém okně, které se zobrazí, můžete vybrat z existujících clusterů Azure Kubernetes Service (AKS), do kterých chcete model nasadit. Pokud nemáte cluster AKS, vytvořte ho pomocí následujících kroků.

1. V dialogovém okně, které se zobrazí, vyberte **COMPUTE** . tím přejdete na stránku **COMPUTE** .

1. Na pásu karet navigace vyberte **odvozené clustery**  >  **+ nové**.

    ![Snímek obrazovky znázorňující, jak se dostat k novému podoknu clusteru odvození](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. V podokně odvození clusteru nakonfigurujte novou službu Kubernetes.

1. Jako **výpočetní název**zadejte *AKS-COMPUTE* .
    
1. Vyberte okolní oblast, která je k dispozici pro **oblast**.

1. Vyberte **Vytvořit**.

    > [!NOTE]
    > Vytvoření nové služby AKS trvá přibližně 15 minut. Stav zřizování můžete kontrolovat na stránce **odvození clusterů** .
    >

## <a name="deploy-the-real-time-endpoint"></a>Nasazení koncového bodu v reálném čase

Až se dokončí zřizování služby AKS, vraťte se do kanálu Inferencing v reálném čase, abyste mohli dokončit nasazení.

1. Na plátně vyberte **nasadit** .

1. Vyberte **nasadit nový koncový bod v reálném čase**. 

1. Vyberte cluster AKS, který jste vytvořili.

1. Vyberte **Nasadit**.
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Snímek obrazovky ukazující, kde najít tlačítko vytvořit kanál":::

    Po dokončení nasazení se zobrazí oznámení o úspěchu nad plátnem. Může to trvat několik minut.

## <a name="view-the-real-time-endpoint"></a>Zobrazení koncového bodu v reálném čase

Po dokončení nasazení můžete zobrazit koncový bod v reálném čase tak, že na stránce **koncové body** kliknete.

1. Na stránce **koncové body** vyberte koncový bod, který jste nasadili.

1. Na kartě **Podrobnosti** můžete zobrazit další informace, jako je například identifikátor URI, stav a značky REST.

1. Na kartě **spotřebovávat** můžete najít klíče zabezpečení a nastavit metody ověřování.

Další informace o využívání webové služby najdete v tématu [Spotřeba modelu nasazeného jako WebService](how-to-consume-web-service.md) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s klíčovými kroky při vytváření, nasazování a využívání modelu Machine Learning v návrháři. Další informace o tom, jak můžete pomocí návrháře vyřešit jiné typy problémů, najdete v našich ukázkových kanálech.

> [!div class="nextstepaction"]
> [Ukázky návrháře](samples-designer.md)

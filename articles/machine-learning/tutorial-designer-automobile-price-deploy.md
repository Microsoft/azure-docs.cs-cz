---
title: 'Kurz: nasazení modelů ML pomocí návrháře'
titleSuffix: Azure Machine Learning
description: Sestavte řešení prediktivní analýzy v Návrháři Azure Machine Learning. Naučte se, vyhodnocovat a nasazovat model strojového učení pomocí modulů přetažení.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: 6bba5ad17cbb6f1ed72d06b37c6d6af9ebd26495
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246464"
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

1. Vyberte **Odeslat** a použijte stejný cíl výpočtů a experiment, který jste použili v první části.

    Pokud se jedná o první spuštění, může trvat až 20 minut, než se váš kanál dokončí. Výchozí nastavení COMPUTE mají minimální velikost uzlu 0, což znamená, že Návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky už jsou přidělené. Kromě toho Návrhář používá výsledky v mezipaměti pro každý modul k dalšímu zvýšení efektivity.

1. Vyberte **Nasadit**.

## <a name="create-an-inferencing-cluster"></a>Vytvoření clusteru Inferencing

V dialogovém okně, které se zobrazí, můžete vybrat z existujících clusterů Azure Kubernetes Service (AKS), do kterých chcete model nasadit. Pokud nemáte cluster AKS, vytvořte ho pomocí následujících kroků.

1. V dialogovém okně, které se zobrazí, vyberte **COMPUTE** . tím přejdete na stránku **COMPUTE** .

1. Na pásu karet navigace vyberte **odvozené clustery**  >  **+ nové**.

    ![Snímek obrazovky znázorňující, jak se dostat k novému podoknu clusteru odvození](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. V podokně odvození clusteru nakonfigurujte novou službu Kubernetes.

1. Jako **výpočetní název** zadejte *AKS-COMPUTE* .
    
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

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Snímek obrazovky ukazující, jak nastavit nový koncový bod v reálném čase":::

    Můžete také změnit **upřesňující** nastavení koncového bodu v reálném čase.
    
    |Rozšířené nastavení|Popis|
    |---|---|
    |Povolit diagnostiku Application Insights a shromažďování dat| Určuje, jestli se má povolit Azure Application Ingishts shromažďovat data z nasazených koncových bodů. </br> Ve výchozím nastavení: false |
    |Časový limit vyhodnocování| Časový limit (v milisekundách), který se má vymáhat pro volání bodování webové služby.</br>Ve výchozím nastavení: 60000|
    |Automatické škálování povoleno|   Určuje, zda má být povoleno automatické škálování webové služby.</br>Ve výchozím nastavení: true|
    |Minimální počet replik| Minimální počet kontejnerů, které se mají použít při automatickém škálování této webové služby.</br>Ve výchozím nastavení: 1|
    |Maximální počet replik| Maximální počet kontejnerů, které se mají použít při automatickém škálování této webové služby.</br> Ve výchozím nastavení: 10|
    |Cílové využití|Cílové využití (v procentech z 100), které by měl modul automatického škálování zkusit zachovat pro tuto webovou službu.</br> Ve výchozím nastavení: 70|
    |Interval aktualizace|Jak často (v sekundách) se automatické škálování pokusí škálovat tuto webovou službu.</br> Ve výchozím nastavení: 1|
    |Rezervní kapacita procesoru|Počet jader procesoru, které se mají přidělit této webové službě.</br> Ve výchozím nastavení: 0,1|
    |Kapacita rezervy paměti|Velikost paměti (v GB), která má být přidělena této webové službě.</br> Ve výchozím nastavení: 0,5|
        

1. Vyberte **Nasadit**. 

    Po dokončení nasazení se zobrazí oznámení o úspěchu nad plátnem. Může to trvat několik minut.

> [!TIP]
> Pokud v poli nastavení koncového bodu v reálném čase vyberete **Azure Container instance** pro **COMPUTE** , můžete ho nasadit taky na **Azure Container instance** (ACI).
> Instance kontejneru Azure se používá pro testování nebo vývoj. Použijte ACI pro úlohy s nižší škálou zatížení procesoru, které vyžadují méně než 48 GB paměti RAM.

## <a name="view-the-real-time-endpoint"></a>Zobrazení koncového bodu v reálném čase

Po dokončení nasazení můžete zobrazit koncový bod v reálném čase tak, že na stránce **koncové body** kliknete.

1. Na stránce **koncové body** vyberte koncový bod, který jste nasadili.

1. Na kartě **Podrobnosti** můžete zobrazit další informace, jako je například identifikátor URI, stav a značky REST.

1. Na kartě **spotřebovávat** můžete najít klíče zabezpečení a nastavit metody ověřování.

1. Na kartě **protokoly nasazení** můžete najít podrobné protokoly nasazení koncového bodu v reálném čase. 

Další informace o využívání webové služby najdete v tématu [Spotřeba modelu nasazeného jako WebService](how-to-consume-web-service.md) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s klíčovými kroky při vytváření, nasazování a využívání modelu Machine Learning v návrháři. Další informace o tom, jak můžete použít návrháře, najdete v následujících odkazech:

+ [Ukázky Návrháře](samples-designer.md): Naučte se, jak pomocí návrháře řešit jiné typy problémů.
+ [Použijte Azure Machine Learning Studio ve službě Azure Virtual Network](how-to-enable-studio-virtual-network.md).
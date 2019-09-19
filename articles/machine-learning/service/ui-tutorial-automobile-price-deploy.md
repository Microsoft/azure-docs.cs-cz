---
title: 'Kurz: Nasazení modelu Machine Learning pomocí vizuálního rozhraní'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet řešení prediktivní analýzy v vizuálním rozhraní Azure Machine Learning. Naučte se, vyhodnocovat a nasazují model strojového učení pomocí přetažení modulů. Tento kurz je druhou částí série dvou částí pro předpověď cen automobilů pomocí lineární regrese.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 9378c6a14c3b755a6456ef68ecd73730cb77fc79
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128984"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Kurz: Nasazení modelu Machine Learning pomocí vizuálního rozhraní

Pokud chcete jiným uživatelům umožnit používání prediktivního modelu vyvinutého v [rámci jednoho kurzu](ui-tutorial-automobile-price-train-score.md), můžete ho nasadit jako webovou službu Azure. Zatím jste experimentování s školením modelu. Nyní je čas vytvořit nové předpovědi na základě vstupu uživatele. V této části kurzu:

> [!div class="checklist"]
> * Příprava modelu pro nasazení
> * Nasazení webové služby
> * Testování webové služby
> * Správa webové služby
> * Využívání webové služby

## <a name="prerequisites"></a>Požadavky

Kompletní [část tohoto kurzu](ui-tutorial-automobile-price-train-score.md) se naučíte, jak ve vizuálním rozhraní naučit a vyhodnocovat model strojového učení.

## <a name="prepare-for-deployment"></a>Příprava nasazení

Než nasadíte experiment jako webovou službu, musíte nejprve převést *školicí experiment* na *prediktivní experiment*.

1. V dolní části plátna experimentu vyberte **vytvořit prediktivní experiment***.

    ![Animovaný obrázek GIF znázorňující automatický převod školicích experimentů do prediktivního experimentu](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Když vyberete možnost **vytvořit prediktivní experiment**, nastane několik věcí:
    
    * Trained model je uložený jako modul **trained modelu** v paletě modulu. Najdete ho pod **školenými modely**.
    * Moduly, které byly použity pro vzdělávání se odeberou; konkrétně:
      * Trénování modelu
      * Rozdělení dat
      * Vyhodnocení modelu
    * Uložený vycvičený model se přidá zpátky do experimentu.
    * Jsou přidány výstupní moduly **webové služby** a **webové služby** . Tyto moduly identifikují, kam budou data uživatelů zadávat model a kde se vrátí data.

    **Školicí experiment** se pořád ukládá na nové karty v horní části plátna experimentu.

1. **Spusťte** experiment.

1. Vyberte výstup modulu určení **skóre modelu** a vyberte **Zobrazit výsledky** , abyste ověřili, že model stále pracuje. Můžete vidět, že se zobrazí původní data společně s předpovězenou cenou ("popisky s skóre").

Experiment by teď měl vypadat takto:  

![Snímek obrazovky znázorňující očekávanou konfiguraci experimentu po jeho přípravě na nasazení](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Nasazení webové služby

1. Pod plátnem vyberte **nasadit webovou službu** .

1. Vyberte **výpočetní cíl** , pro který chcete spustit webovou službu.

    V současné době vizuální rozhraní podporuje nasazení pouze do výpočetních cílů služby Azure Kubernetes Service (AKS). V pracovním prostoru služby Machine Learning můžete vybrat z dostupných výpočetních cílů AKS nebo nakonfigurovat nové prostředí AKS pomocí kroků v zobrazeném dialogovém okně.

    ![Snímek obrazovky znázorňující možnou konfiguraci pro nový cíl výpočtů](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Vyberte **nasadit webovou službu**. Po dokončení nasazení se zobrazí následující oznámení. Nasazení může trvat několik minut.

    ![Snímek obrazovky s potvrzovací zprávou pro úspěšné nasazení](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test webové služby

Webové služby vizuálního rozhraní můžete testovat a spravovat tak, že přejdete na kartu **webové služby** .

1. Přejít na část webové služby. Uvidíte webovou službu, kterou jste nasadili, pomocí pojmenování **kurzu – předpověď ceny automobilu [prediktivní exp]** .

     ![Snímek obrazovky zobrazující kartu webové služby s zvýrazněnou webovou službou naposledy vytvořenou](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Pokud chcete zobrazit další podrobnosti, vyberte název webové služby.

1. Vyberte **test**.

    [![Snímek obrazovky zobrazující stránku testování webové služby](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Zadejte testovací data nebo použijte možnost automatického vyplňování ukázkových dat a vyberte **test**.

    Požadavek na test se odešle do webové služby a výsledky se zobrazí na stránce. I když je pro vstupní data vygenerována hodnota ceny, není použita k vygenerování hodnoty předpovědi.

## <a name="consume-the-web-service"></a>Využívání webové služby

Uživatelé teď můžou odesílat požadavky rozhraní API na webovou službu Azure a získávat výsledky pro předpověď ceny svých nových Automobiles.

**Požadavek nebo odpověď** – uživatel pošle jednu nebo více řádků dat automobilu do služby pomocí protokolu HTTP. Služba reaguje s jednou nebo více sadami výsledků.

Ukázková volání REST můžete najít na stránce s podrobnostmi webové služby na kartě **spotřebovat** .

   ![Snímek obrazovky znázorňující ukázkové volání REST, které uživatelé můžou najít na kartě spotřebovat](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Přejděte na kartu **API doc** , kde najdete další podrobnosti o rozhraní API.

## <a name="manage-models-and-deployments"></a>Správa modelů a nasazení

Nasazení modelů a webových služeb, která vytvoříte v rámci vizuálního rozhraní, lze spravovat také z pracovního prostoru Azure Machine Learning.

1. Otevřete pracovní prostor v [Azure Portal](https://portal.azure.com/).  

1. V pracovním prostoru vyberte **modely**. Pak vyberte experiment, který jste vytvořili.

    ![Snímek obrazovky, který ukazuje, jak přejít k experimentům v Azure Portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Na této stránce se zobrazí další podrobnosti o modelu.

1. Vyberte **nasazení**. zobrazí se seznam všech webových služeb, které používají model. Vyberte název webové služby, který se bude nacházet na stránce s podrobnostmi webové služby. Na této stránce můžete získat podrobnější informace o webové službě.

    [![Podrobná sestava spuštění snímku obrazovky](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

Tyto modely a nasazení můžete najít také v částech **modely** a **koncové body** [cílové stránky pracovního prostoru (Preview)](https://ml.azure.com).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s klíčovými kroky při vytváření, nasazování a využívání modelu strojového učení ve vizuálním rozhraní. Další informace o tom, jak můžete pomocí vizuálního rozhraní vyřešit jiné typy problémů, najdete v našich dalších ukázkových experimentech.

> [!div class="nextstepaction"]
> [Ukázka klasifikace úvěrového rizika](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)

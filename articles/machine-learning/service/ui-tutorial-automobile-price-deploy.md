---
title: 'Kurz: Nasadit model vizuální rozhraní strojového učení'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvořit řešení prediktivní analýzy v vizuální rozhraní služby Azure Machine Learning. Trénování, stanovení skóre a nasadit model strojového učení pomocí přetažení a vyřadit moduly. Tento kurz je druhou částí série dvojdílného na předpověď cen automobilů prostřednictvím lineární regrese.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: dd28fb51a4fc3fbf3dfc893f2f5f159ccafdb4b3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839306"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Kurz: Nasadit model vizuální rozhraní strojového učení

Ostatním uživatelům příležitost dobře se používají prediktivní model vyvinutý v [první části kurzu](ui-tutorial-automobile-price-train-score.md), můžete ji nasadit jako webová služba Azure. Zatím jste experimentovali s trénování modelu. Teď je čas ke generování nových předpovědí na základě uživatelského zadání. V této části kurzu jste:

> [!div class="checklist"]
> * Příprava nasazení modelu
> * Nasazení webové služby
> * Testování webové služby
> * Správa webové služby
> * Používání této webové služby

## <a name="prerequisites"></a>Požadavky

Kompletní [první části kurzu](ui-tutorial-automobile-price-train-score.md) se naučíte trénování a stanovíte jeho skóre modelu strojového učení ve vizuální rozhraní.

## <a name="prepare-for-deployment"></a>Příprava nasazení

Před nasazením experiment jako webové služby, je nejprve nutné převést vaše *výukového experimentu* do *prediktivní experiment*.

1. Vyberte **vytvářet prediktivní Experiment*** v dolní části na plátno experimentu.

    ![Animovaný gif zobrazující automatický převod výukového experimentu na prediktivní experiment](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Když vyberete **vytvářet prediktivní Experiment**, stane několik věcí:
    
    * Trénovaný model se ukládá jako **Trénovaného modelu** modulu paletě modulů. Najdete ho pod **Trénované modely**.
    * Moduly, které byly použity pro vzdělávání se odeberou; konkrétně:
      * Trénování modelu
      * Rozdělení dat
      * Vyhodnocení modelu
    * Uložené trénovaného modelu je přidána zpět do experimentu.
    * **Webová služba vstup** a **webové služby výstup** moduly jsou přidány. Tyto moduly Identifikujte, kde se data uživatele zadejte modelu a kde se data vrací.

    **Výukového experimentu** je uložen v části nové karty v horní části na plátno experimentu.

1. **Spusťte** experiment.

1. Vybrat výstup **Score Model** modul a vyberte **zobrazit výsledky** ověření modelu je funkční. Uvidíte, že původní data se zobrazí spolu s předpokládanou cena ("popisky vyhodnocení").

Experiment by teď měl vypadat takto:  

![Snímek obrazovky zobrazující očekávané konfiguraci testu po Příprava pro nasazení](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Nasazení webové služby

1. Vyberte **nasadit webovou službu** dole na plátně.

1. Vyberte **cílové výpočetní** , že chcete spustit webovou službu.

    Vizuální rozhraní v současné době podporuje pouze nasazení na cílových výpočetních prostředí Azure Kubernetes Service (AKS). Můžete vybrat z dostupných cílových výpočetních prostředí AKS ve vaší služby pracovního prostoru machine learning nebo konfigurace pomocí kroků v dialogu, který se zobrazí nové prostředí AKS.

    ![Snímek obrazovky ukazující, možné konfigurace pro nové cílové výpočetní prostředí](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Vyberte **nasazení webové služby**. Zobrazí se vám následující oznámení po dokončení nasazení. Nasazení může trvat několik minut.

    ![Snímek obrazovky znázorňující zprávu potvrzení pro úspěšné nasazení.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test webové služby

Můžete otestovat a spravovat webové služby vizuální rozhraní tak, že přejdete na **webových služeb** kartu.

1. Přejděte do části webové služby. Zobrazí se webová služba, které jste nasadili s názvem **kurz – předpovídat cenu automobilu [prediktivní Exp]** .

     ![Snímek obrazovky zobrazující kartu web service se nedávno vytvořenou webovou službou zvýrazněnou](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Vyberte název webové služby, chcete-li zobrazit další podrobnosti.

     ![Zobrazit snímek obrazovky ukazující, další podrobnosti, které jsou k dispozici ve webové službě](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Vyberte **Test**.

    ![Snímek obrazovky zobrazující stránku webové služby](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Vstup testování dat nebo použijte autofilled ukázková data a vyberte **Test**.

    K webové službě se odešle požadavek testu a výsledky se zobrazí na stránce. I když hodnota ceny se vygeneruje pro vstupní data, není použit ke generování hodnoty předpovědi.

## <a name="consume-the-web-service"></a>Používání této webové služby

Uživatelé teď můžete odesílat žádosti rozhraní API do služby Azure web a zobrazí výsledky odhadnout cenu jejich nových automobilů.

**Žádost/odpověď** -uživatel odesílá jeden nebo více řádků data automobilů ke službě s použitím protokolu HTTP. Služba jako odpověď vrátí jednu nebo více sad výsledků.

Můžete najít ukázkové volání REST **využívání** kartu Podrobnosti stránku webové služby.

   ![Snímek obrazovky zobrazující ukázkový REST volání, že uživatelé mohou najít na kartě využívání](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Přejděte **dokumentace rozhraní API** kartu Podrobnosti pro rozhraní API.

  ![Snímek obrazovky zobrazující další podrobnosti o rozhraní API, které uživatelé můžete najít na kartě dokumentace rozhraní API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments"></a>Správa modelů a nasazení

Modely a nasazením webových služeb, které vytvoříte ve vizuální rozhraní můžete také spravovat z pracovního prostoru služby Azure Machine Learning.

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  

1. V pracovním prostoru vyberte **modely**. Vyberte experiment, který jste vytvořili.

    ![Snímek obrazovky ukazující, jak se orientovat na experimentů na webu Azure Portal](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Na této stránce uvidíte další podrobnosti o tomto modelu.

    ![Snímek obrazovky zobrazující přehled statistiky experimentu na webu Azure Portal](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Vyberte **nasazení**, zobrazí se seznam všech webových služeb, které používají model. Vyberte název webové služby, bude přejděte na stránku podrobností webové služby. Na této stránce můžete získat podrobnější informace o webové služby.

    ![Spusťte sestavu podrobný snímek obrazovky](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, že klíč kroky vytváření, nasazování a používání strojového učení modelu ve vizuální rozhraní. Další informace o použití vizuální rozhraní pro jiné druhy problémů vyřešit, najdete v článku navýšení kapacity našich ukázkových experimentů.

> [!div class="nextstepaction"]
> [Ukázka klasifikaci rizik kredit](ui-sample-classification-predict-credit-risk-cost-sensitive.md)

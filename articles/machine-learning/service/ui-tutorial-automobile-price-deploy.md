---
title: 'Kurz: Nasadit model vizuální rozhraní strojového učení'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvořit řešení prediktivní analýzy v vizuální rozhraní služby Azure Machine Learning. Trénování, stanovení skóre a nasadit model strojového učení pomocí přetažení a vyřadit moduly. Tento kurz je druhou částí série dvojdílného na předpověď cen automobilů prostřednictvím lineární regrese.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: fe26417c6e3a9f159e884c19d7bd7c9dc7569229
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787836"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Kurz: Nasadit model vizuální rozhraní strojového učení

V tomto kurzu provedete rozšířené podívat vývoj prediktivního řešení ve vizuální rozhraní služby Azure Machine Learning. Tento kurz je **druhou částí z dvoudílné série kurzů**. V [první části kurzu](ui-tutorial-automobile-price-train-score.md), školení, skóre a vyhodnotit model pro předpověď ceny automobilu. V této části kurzu jste:

> [!div class="checklist"]
> * Příprava nasazení modelu
> * Nasazení webové služby
> * Testování webové služby
> * Správa webové služby
> * Používání této webové služby

## <a name="prerequisites"></a>Požadavky

Kompletní [první části kurzu](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Ostatním uživatelům příležitost dobře se prediktivní model vyvinutý v tomto kurzu použít, můžete ji nasadit jako webová služba Azure.

Zatím jste experimentovali s trénování modelu. Teď je čas ke generování nových předpovědí na základě uživatelského zadání.

Příprava pro nasazení je dvoustupňový proces:  

1. Převést *výukového experimentu* , kterou jste vytvořili do *prediktivní experiment*
1. Prediktivní experiment nasadit jako webovou službu

Možná budete chtít nejdřív vytvořit kopii experimentu tak, že vyberete **uložit jako** v dolní části na plátno experimentu.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod výukového experimentu na prediktivní experiment

Chcete-li získat tento model připravené na nasazení, převeďte tento výukový experiment prediktivní experiment. To obvykle zahrnuje tři kroky:

1. Uložit model natrénovali a nahraďte vaše školicí moduly
1. Trim experiment odebrat modulů, které byly potřeba jenom pro školení
1. Definujte, ve kterém webová služba bude přijímat vstupní data a kde se vygeneruje výstup

Tyto kroky může provést ručně nebo můžete vybrat **nastavení webové služby** v dolní části plátnem experimentu je možné je mít znovu provedeno automaticky.

![Animovaný gif zobrazující automatický převod výukového experimentu na prediktivní experiment](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Když vyberete **nastavení webové služby**, stane několik věcí:

* Trénovaného modelu je převést na jediné **Trénovaného modelu** modulu. Uloží se paletě modulů nalevo od plátna experimentu. Najdete ho pod **Trénované modely**.
* Moduly, které byly použity pro vzdělávání se odeberou; konkrétně:
  * Trénování modelu
  * Rozdělení dat
  * Vyhodnocení modelu
* Uložené trénovaného modelu je přidána zpět do experimentu
* **Webová služba vstup** a **webové služby výstup** moduly jsou přidány. Tyto moduly Identifikujte, kde se data uživatele zadejte modelu a kde se data vrací.

Uvidíte, že experiment se uloží do dvou částí v části nové karty v horní části na plátno experimentu. Původní výukového experimentu je na kartě **výukového experimentu**, a nově vytvořený prediktivní experiment probíhá **prediktivní experiment**. Prediktivní experiment je ta, kterou budete nasazovat jako webové služby.

Experiment by teď měl vypadat takto:  

![Snímek obrazovky zobrazující očekávané konfiguraci testu po Příprava pro nasazení](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Spusťte experiment jednou (vyberte **spustit**). Vyberte cílové výpočetní prostředí, který chcete experiment ke spuštění v dialogovém okně automaticky otevíraného okna. Pokud chcete ověřit model je funkční, vyberte výstup modulu určení skóre modelu a vyberte **zobrazení výsledků**. Uvidíte, že původní data se zobrazí spolu s předpokládanou cena ("popisky vyhodnocení").

## <a name="deploy-the-web-service"></a>Nasazení webové služby

K nasazení nové webové služby získané ze svého experimentu:

1. Vyberte **nasadit webovou službu** dole na plátně.
1. Vyberte **cílové výpočetní** , že chcete spustit webovou službu.

    Vizuální rozhraní v současné době podporuje pouze nasazení na cílových výpočetních prostředí Azure Kubernetes Service (AKS). Můžete vybrat z dostupných cílových výpočetních prostředí AKS ve vaší služby pracovního prostoru machine learning nebo konfigurace pomocí kroků v dialogu, který se zobrazí nové prostředí AKS.

    ![Snímek obrazovky ukazující, možné konfigurace pro nové cílové výpočetní prostředí](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Vyberte **nasazení webové služby**. Zobrazí se vám následující oznámení po dokončení nasazení. Nasazení může trvat několik minut.

    ![Snímek obrazovky znázorňující zprávu potvrzení pro úspěšné nasazení.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test webové služby

Vstupní data uživatel zadá modelu nasazené prostřednictvím **webové služby vstup** modulu. Vstup je pak skóre v **Score Model** modulu. Způsob, jakým jste nastavili prediktivní experiment, modelu očekává, že data ve stejném formátu jako původní datové sady automobilů cena. A konečně, výsledky se vrátí uživateli prostřednictvím **webové služby výstup** modulu.

Testovat webovou službu na kartě webových služeb v vizuální rozhraní.

1. Přejděte do části webové služby. Zobrazí se webová služba, které jste nasadili s názvem **kurz – předpovídat cenu automobilu [prediktivní Exp]**.

     ![Snímek obrazovky zobrazující kartu web service se nedávno vytvořenou webovou službou zvýrazněnou](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Vyberte název webové služby, chcete-li zobrazit další podrobnosti.

     ![Zobrazit snímek obrazovky ukazující, další podrobnosti, které jsou k dispozici ve webové službě](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Vyberte **Test**.

    ![Snímek obrazovky zobrazující stránku webové služby](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Vstup testování dat nebo použijte autofilled ukázková data a vyberte **Test** v dolní části. K webové službě se odešle požadavek testu a výsledky se zobrazí na stránce.

## <a name="manage-the-web-service"></a>Správa webové služby

Po nasazení webové služby, můžete spravovat z **webových služeb** kartu v vizuální rozhraní.

Webové služby můžete odstranit tak, že vyberete **odstranit** na stránce podrobností webové služby.

   ![Snímek obrazovky ukazující umístění tlačítka odstranit webové služby v dolní části okna](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Používání této webové služby

V předchozích krocích v tomto kurzu jste nasadili automobilů prediktivního modelu jako webová služba Azure. Uživatelé teď můžete odesílat data do ní a zobrazí výsledky přes rozhraní REST API.

**Žádost/odpověď** -uživatel odesílá jeden nebo více řádků data automobilů ke službě s použitím protokolu HTTP. Služba jako odpověď vrátí jednu nebo více sad výsledků.

Můžete najít ukázkové volání REST **využívání** kartu Podrobnosti stránku webové služby.

   ![Snímek obrazovky zobrazující ukázkový REST volání, že uživatelé mohou najít na kartě využívání](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Přejděte **dokumentace rozhraní API** kartu Podrobnosti pro rozhraní API.

  ![Snímek obrazovky zobrazující další podrobnosti o rozhraní API, které uživatelé můžete najít na kartě dokumentace rozhraní API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Správa modelů a nasazení v pracovním prostoru služby Azure Machine Learning

Modely a nasazením webových služeb, které vytvoříte ve vizuální rozhraní je možné spravovat z pracovního prostoru služby Azure Machine Learning.

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

V tomto kurzu jste zjistili, že klíč kroky vytváření, nasazování a používání strojového učení modelu ve vizuální rozhraní. Další informace o použití vizuální rozhraní pro jiné druhy problémů vyřešit, projděte si ukázkové experimenty.

> [!div class="nextstepaction"]
> [Ukázka klasifikaci rizik kredit](ui-sample-classification-predict-credit-risk-cost-sensitive.md)

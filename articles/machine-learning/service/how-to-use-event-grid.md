---
title: Vytváření pracovních postupů strojového učení řízených událostmi
titleSuffix: Azure Machine Learning
description: Naučte se používat Event Grid s Azure Machine Learning k povolení řešení založených na událostech.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: b10b848918fda7a1f271a4e617b1706971f103d7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622239"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Vytváření pracovních postupů strojového učení řízených událostmi (Preview)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) podporuje události služby Azure Machine Learning. Můžete například použít události z dokončování spuštění, registrace modelu, nasazení modelu a detekce odchodu dat v oboru pracovního prostoru.

Další informace najdete v tématu [Azure Machine Learning integrace s Event Grid](concept-event-grid-integration.md) a [schématu Azure Machine Learning Event Grid](/azure/event-grid/event-schema-machine-learning).

Pomocí Event Grid můžete povolit běžné scénáře, jako například:

* Aktivují se kanály pro přeškolení.
* Streamování událostí z Azure Machine Learning do různých koncových bodů

## <a name="prerequisites"></a>Požadavky

* Přístup přispěvatele nebo vlastníka k pracovnímu prostoru služby Azure Machine Learning, pro který budete vytvářet události.
* Vyberte koncový bod obslužné rutiny události, například Webhook nebo centrum událostí. Další informace naleznete v tématu [obslužné rutiny událostí](https://docs.microsoft.com/azure/event-grid/event-handlers). 

## <a name="register-resource-providers"></a>Registrovat poskytovatele prostředků

Pokud jste použili Azure Event Grid nebo Machine Learning před vydáním listopadu 1 2019, bude pravděpodobně nutné znovu zaregistrovat poskytovatele prostředků, než budete moci postupovat podle kroků v tomto dokumentu. Pokud chcete poskytovatele znovu zaregistrovat, použijte následující postup:

1. Přejít na Azure Portal a vyberte __předplatná__. Vyberte předplatné, se kterým chcete pracovat.
1. Vyberte __poskytovatelé prostředků__a pak vyhledejte __EventGrid__.
1. Vyberte položku __Microsoft. EventGrid__ a pak vyberte __znovu registrovat__.

    ![znovu zaregistrovat – poskytovatel prostředků](media/how-to-use-event-grid/re-register-resource-provider.png)

1. Vyhledejte __MachineLearningServices__, vyberte položku a pak vyberte __znovu registrovat__.

> [!TIP]
> Pokud nemáte oprávnění k dokončení těchto kroků, požádejte správce předplatného, aby je provedl.

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>Konfigurace událostí strojového učení pomocí Azure Portal

1. Otevřete [Azure Portal](https://portal.azure.com) a přejít do pracovního prostoru Azure Machine Learning.

1. Na levém panelu vyberte možnost __události__ a potom vyberte možnost **odběry událostí**. 

    ![SELECT-events-in-Workspace. png](media/how-to-use-event-grid/select-event.png)

1. Vyberte typ události, který se má spotřebovat. Například následující snímek obrazovky má __zaregistrovaný model__, byl __nasazen model__, __dokončen běh__a __byl zjištěn posun datových sad__:

    ![Přidat typ události](media/how-to-use-event-grid/add-event-type.png)

1. Vyberte koncový bod, do kterého se má událost publikovat. Na následujícím snímku obrazovky je __centrum událostí__ vybraným koncovým bodem:

    ![rutina Select-Event-](media/how-to-use-event-grid/select-event-handler.png)

Po potvrzení výběru klikněte na __vytvořit__. Po dokončení konfigurace budou tyto události vloženy do koncového bodu.

## <a name="set-up-azure-event-grid-using-cli"></a>Nastavení Azure Event Grid pomocí rozhraní příkazového řádku

Můžete buď nainstalovat nejnovější rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), nebo použít Azure Cloud Shell poskytované jako součást předplatného Azure.

Pokud chcete nainstalovat rozšíření Event Grid, použijte z rozhraní příkazového řádku následující příkaz:

```azurecli-interactive
az add extension --name eventgrid
```

Následující příklad ukazuje, jak vybrat předplatné Azure a pak vytvořit nové odběry událostí pro Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="sample-scenarios"></a>Ukázkové scénáře

### <a name="use-a-logic-app-to-send-email-alerts"></a>Použití aplikace logiky k posílání e-mailových upozornění

Využijte [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) ke konfiguraci e-mailů pro všechny vaše události. Přizpůsobte si podmínky a určete příjemce, aby bylo možné spolupracovat a povědomí mezi týmy spolupracují.

1. V Azure Portal přejdete do pracovního prostoru Azure Machine Learning a na levém panelu vyberete kartu události. Tady vyberte __Logic Apps__. 

    ![SELECT-Logic-AP](media/how-to-use-event-grid/select-logic-ap.png)

1. Přihlaste se do uživatelského rozhraní aplikace logiky a jako typ tématu vyberte Machine Learning Service. 

    ![vybrat-téma-typ](media/how-to-use-event-grid/select-topic-type.png)

1. Vyberte události, které mají být oznamovány. Například následující snímek obrazovky __RunCompleted__.

    ![SELECT-Event-runcomplete](media/how-to-use-event-grid/select-event-runcomplete.png)

1. Můžete také přidat filtry, které aktivují pouze aplikaci logiky pro podmnožinu typů událostí. Na následujícím snímku obrazovky je použit __Filtr předpony__ __/datadriftID/runs/__ .

    ![filtr – události](media/how-to-use-event-grid/filtering-events.png)

1. Dále přidejte krok pro použití této události a hledání e-mailu. K příjmu událostí můžete použít několik různých poštovních účtů. Můžete taky nakonfigurovat podmínky, kdy se má poslat e-mailové upozornění.

    ![vybrat e-mail – akce](media/how-to-use-event-grid/select-email-action.png)

1. Vyberte __Odeslat e-mail__ a vyplňte parametry. V předmětu můžete zahrnout __Typ události__ a __téma__ , které vám pomůžou události filtrovat. Do textu zprávy můžete také přidat odkaz na stránku pracovního prostoru pro spuštění. 

    ![Konfigurace – e-mail – tělo](media/how-to-use-event-grid/configure-email-body.png)

1. Pokud chcete tuto akci Uložit, vyberte **Uložit jako** v levém horním rohu stránky. Z pravého panelu, který se zobrazí, potvrďte vytvoření této akce.

    ![potvrdit-Logic-App-Create](media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Použití aplikace logiky k aktivaci reškolicích pracovních postupů, když dojde k posunu dat

Modely se v průběhu času přejdou zastaralě a nezůstávají užitečné v kontextu, ve kterém běží. Jedním ze způsobů, jak zjistit, jestli je čas na reučení modelu, je detekce posunu dat. 

Tento příklad ukazuje, jak pomocí služby Event Grid s aplikací logiky Azure aktivovat rekurzi. V příkladu se aktivuje kanál Azure Data Factory, pokud dojde k posunu dat mezi školením modelu a obsluhou datových sad.

Než začnete, proveďte následující akce:

* Nastavení monitoru datové sady pro [detekci posunu dat]( https://aka.ms/datadrift) v pracovním prostoru
* Vytvoří publikovaný [kanál Azure Data Factory](https://docs.microsoft.com/azure/data-factory/).

V tomto příkladu se k kopírování souborů do úložiště objektů BLOB a spuštění publikovaného kanálu Machine Learning používá jednoduchý kanál Data Factory. Další informace o tomto scénáři najdete v tématu Postup nastavení [Machine Learning kroku v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF – mlpipeline – fáze](media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Začněte vytvářením aplikace logiky. Otevřete [Azure Portal](https://portal.azure.com), vyhledejte Logic Apps a vyberte vytvořit.

    ![hledání-logika – aplikace](media/how-to-use-event-grid/search-for-logic-app.png)

1. Vyplňte požadované informace. Pro zjednodušení prostředí použijte stejné předplatné a skupinu prostředků jako Azure Data Factory kanál a Azure Machine Learning pracovní prostor.

    ![nastavení-Logic-App-for-ADF](media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po vytvoření aplikace logiky vyberte, __kdy dojde k události Event Grid prostředku__. 

    ![Výběr-Event-Grid-Trigger](media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Přihlaste se a vyplňte podrobnosti události. Nastavte __název prostředku__ na název pracovního prostoru. Nastavte __Typ události__ na __DatasetDriftDetected__.

    ![přihlášení a přidání – událost](media/how-to-use-event-grid/login-and-add-event.png)

1. Přidejte nový krok a vyhledejte __Azure Data Factory__. Vyberte __vytvořit spuštění kanálu__. 

    ![Vytvoření-adfpipeline-run](media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Přihlaste se a zadejte publikovaný Azure Data Factory kanál, který se má spustit.

    ![určení – ADF – kanál](media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Uložte a vytvořte aplikaci logiky pomocí tlačítka **Uložit** v levém horním rohu stránky. Pokud chcete zobrazit svou aplikaci, přejděte do pracovního prostoru v [Azure Portal](https://portal.azure.com) a klikněte na **události**.

    ![show-Logic-App – Webhook](media/how-to-use-event-grid/show-logic-app-webhook.png)

Kanál Data Factory se teď aktivuje, když dojde k posunu. Zobrazte si podrobnosti o běhu posunu dat a kanálu Machine Learning na [novém portálu pracovního prostoru](https://ml.azure.com). 

![zobrazení v pracovním prostoru](media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Použití Azure Functions k nasazení modelu na základě značek

Objekt Azure Machine Learning modelu obsahuje parametry, na jejichž základě můžete nasazovat nasazení, jako je název modelu, verze, značka a vlastnost. Událost registrace modelu může aktivovat koncový bod a pomocí funkce Azure můžete nasadit model založený na hodnotě těchto parametrů.

Příklad naleznete v části úložiště [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) a postupujte podle pokynů v souboru **Readme** .

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných událostech najdete v tématu [schéma události Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) .
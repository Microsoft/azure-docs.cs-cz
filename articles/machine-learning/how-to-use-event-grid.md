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
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129720"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Vytváření pracovních postupů strojového učení řízených událostmi (Preview)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) podporuje události Azure Machine Learning. Můžete se přihlásit k odběru a spotřebovávat události, jako je například změna stavu spuštění, dokončení běhu, registrace modelu, nasazení modelu a detekce posunu dat v pracovním prostoru.

Další informace o typech událostí najdete v tématu [Azure Machine Learning Integration with Event Grid](concept-event-grid-integration.md) a [schématu Azure Machine Learning Event Grid](/azure/event-grid/event-schema-machine-learning).

Pomocí Event Grid můžete povolit běžné scénáře, jako například:

* Odeslat e-maily při spuštění a dokončení spuštění
* Použití funkce Azure Function po registraci modelu
* Streamování událostí z Azure Machine Learning do různých koncových bodů
* Aktivace kanálu ML při zjištění posunu

> [!NOTE] 
> V současné době se události runStatusChanged aktivují jenom v případě, že se stav spuštění **nezdařil** .
>

## <a name="prerequisites"></a>Předpoklady
* Přístup přispěvatele nebo vlastníka k pracovnímu prostoru Azure Machine Learning, pro který budete vytvářet události.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Konfigurace EventGrid pomocí Azure Portal

1. Otevřete [Azure Portal](https://portal.azure.com) a přejít do pracovního prostoru Azure Machine Learning.

1. Na levém panelu vyberte možnost __události__ a potom vyberte možnost **odběry událostí**. 

    ![SELECT-events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Vyberte typ události, který se má spotřebovat. Například následující snímek obrazovky má __zaregistrovaný model__, byl __nasazen model__, __dokončen běh__a __byl zjištěn posun datových sad__:

    ![Přidat typ události](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Vyberte koncový bod, do kterého se má událost publikovat. Na následujícím snímku obrazovky je __centrum událostí__ vybraným koncovým bodem:

    ![rutina Select-Event-](./media/how-to-use-event-grid/select-event-handler.png)

Po potvrzení výběru klikněte na __vytvořit__. Po dokončení konfigurace budou tyto události vloženy do koncového bodu.


### <a name="configure-eventgrid-using-the-cli"></a>Konfigurace EventGrid pomocí rozhraní příkazového řádku

Můžete buď nainstalovat nejnovější rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), nebo použít Azure Cloud Shell poskytované jako součást předplatného Azure.

Pokud chcete nainstalovat rozšíření Event Grid, použijte z rozhraní příkazového řádku následující příkaz:

```azurecli-interactive
az add extension --name eventgrid
```

Následující příklad ukazuje, jak vybrat předplatné Azure a vytvořit nový odběr události pro Azure Machine Learning:

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

## <a name="filter-events"></a>Filtrovat události

Při nastavování událostí můžete použít filtry, aby se aktivovaly jenom pro konkrétní data událostí. V následujícím příkladu můžete pro události změny stavu spuštění filtrovat podle typů spuštění. Událost se aktivuje pouze v případě, že jsou splněna kritéria. Další informace o datech událostí, která můžete filtrovat podle, najdete v tématu [schéma Event gridu Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) . 

1. Přejít na Azure Portal, vyberte nové předplatné nebo existující. 

1. Vyberte kartu filtry a přejděte dolů k části Rozšířené filtry. V **klíči** a **hodnotě** zadejte typy vlastností, podle kterých chcete filtrovat. Tady vidíte, že událost se aktivuje jenom v případě, že typ spuštění je spuštění kanálu nebo krok kanálu.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtrovat události":::

## <a name="sample-scenarios"></a>Ukázkové scénáře

### <a name="use-a-logic-app-to-send-email-alerts"></a>Použití aplikace logiky k posílání e-mailových upozornění

Využijte [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) ke konfiguraci e-mailů pro všechny vaše události. Přizpůsobte si podmínky a určete příjemce, aby bylo možné spolupracovat a povědomí mezi týmy spolupracují.

1. V Azure Portal přejdete do pracovního prostoru Azure Machine Learning a na levém panelu vyberete kartu události. Tady vyberte __Logic Apps__. 

    ![SELECT-Logic-AP](./media/how-to-use-event-grid/select-logic-ap.png)

1. Přihlaste se do uživatelského rozhraní aplikace logiky a jako typ tématu vyberte Machine Learning Service. 

    ![vybrat-téma-typ](./media/how-to-use-event-grid/select-topic-type.png)

1. Vyberte události, které mají být oznamovány. Například následující snímek obrazovky __RunCompleted__.

    ![SELECT-Event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Můžete použít metodu filtrování v části výše nebo přidat filtry, aby se aktivovala pouze aplikace logiky pro podmnožinu typů událostí. Na následujícím snímku obrazovky je použit __Filtr předpony__ __/datadriftID/runs/__ .

    ![filtr – události](./media/how-to-use-event-grid/filtering-events.png)

1. Dále přidejte krok pro použití této události a hledání e-mailu. K příjmu událostí můžete použít několik různých poštovních účtů. Můžete taky nakonfigurovat podmínky, kdy se má poslat e-mailové upozornění.

    ![vybrat e-mail – akce](./media/how-to-use-event-grid/select-email-action.png)

1. Vyberte __Odeslat e-mail__ a vyplňte parametry. V předmětu můžete zahrnout __Typ události__ a __téma__ , které vám pomůžou události filtrovat. Do textu zprávy můžete také přidat odkaz na stránku pracovního prostoru pro spuštění. 

    ![Konfigurace – e-mail – tělo](./media/how-to-use-event-grid/configure-email-body.png)

1. Pokud chcete tuto akci Uložit, vyberte **Uložit jako** v levém horním rohu stránky. Z pravého panelu, který se zobrazí, potvrďte vytvoření této akce.

    ![potvrdit-Logic-App-Create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Použití aplikace logiky k aktivaci reškolicích pracovních postupů, když dojde k posunu dat

Modely se v průběhu času přejdou zastaralě a nezůstávají užitečné v kontextu, ve kterém běží. Jedním ze způsobů, jak zjistit, jestli je čas na reučení modelu, je detekce posunu dat. 

Tento příklad ukazuje, jak pomocí služby Event Grid s aplikací logiky Azure aktivovat rekurzi. V příkladu se aktivuje kanál Azure Data Factory, pokud dojde k posunu dat mezi školením modelu a obsluhou datových sad.

Než začnete, proveďte následující akce:

* Nastavení monitoru datové sady pro [detekci posunu dat]( https://aka.ms/datadrift) v pracovním prostoru
* Vytvoří publikovaný [kanál Azure Data Factory](https://docs.microsoft.com/azure/data-factory/).

V tomto příkladu se k kopírování souborů do úložiště objektů BLOB a spuštění publikovaného kanálu Machine Learning používá jednoduchý kanál Data Factory. Další informace o tomto scénáři najdete v tématu Postup nastavení [Machine Learning kroku v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF – mlpipeline – fáze](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Začněte vytvářením aplikace logiky. Otevřete [Azure Portal](https://portal.azure.com), vyhledejte Logic Apps a vyberte vytvořit.

    ![hledání-logika – aplikace](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Vyplňte požadované informace. Pro zjednodušení prostředí použijte stejné předplatné a skupinu prostředků jako Azure Data Factory kanál a Azure Machine Learning pracovní prostor.

    ![nastavení-Logic-App-for-ADF](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po vytvoření aplikace logiky vyberte, __kdy dojde k události Event Grid prostředku__. 

    ![Výběr-Event-Grid-Trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Přihlaste se a vyplňte podrobnosti události. Nastavte __název prostředku__ na název pracovního prostoru. Nastavte __Typ události__ na __DatasetDriftDetected__.

    ![přihlášení a přidání – událost](./media/how-to-use-event-grid/login-and-add-event.png)

1. Přidejte nový krok a vyhledejte __Azure Data Factory__. Vyberte __vytvořit spuštění kanálu__. 

    ![Vytvoření-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Přihlaste se a zadejte publikovaný Azure Data Factory kanál, který se má spustit.

    ![určení – ADF – kanál](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Uložte a vytvořte aplikaci logiky pomocí tlačítka **Uložit** v levém horním rohu stránky. Pokud chcete zobrazit svou aplikaci, přejděte do pracovního prostoru v [Azure Portal](https://portal.azure.com) a klikněte na **události**.

    ![show-Logic-App – Webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Kanál Data Factory se teď aktivuje, když dojde k posunu. Zobrazte si podrobnosti o běhu posunu dat a kanálu Machine Learning na [novém portálu pracovního prostoru](https://ml.azure.com). 

![zobrazení v pracovním prostoru](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Použití Azure Functions k nasazení modelu na základě značek

Objekt Azure Machine Learning modelu obsahuje parametry, na jejichž základě můžete nasazovat nasazení, jako je název modelu, verze, značka a vlastnost. Událost registrace modelu může aktivovat koncový bod a pomocí funkce Azure můžete nasadit model založený na hodnotě těchto parametrů.

Příklad naleznete v části úložiště [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) a postupujte podle pokynů v souboru **Readme** .

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných událostech najdete v tématu [schéma události Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) .

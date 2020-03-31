---
title: Vytváření pracovních postupů strojového učení založeného na událostech
titleSuffix: Azure Machine Learning
description: Zjistěte, jak používat mřížku událostí pomocí Azure Machine Learning k povolení řešení řízených událostmi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129720"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Vytváření pracovních postupů strojového učení řízeného událostmi (preview)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) podporuje události Azure Machine Learning. Můžete se přihlásit k odběru a využívat události, jako je změna stavu spuštění, dokončení spuštění, registrace modelu, nasazení modelu a detekce posunu dat v pracovním prostoru.

Další informace o typech událostí najdete v [tématu Integrace Azure Machine Learning s Event Grid](concept-event-grid-integration.md) a schéma [mřížky událostí Azure Machine Learning](/azure/event-grid/event-schema-machine-learning).

Pomocí funkce Event Grid povolte běžné scénáře, jako jsou:

* Odesílání e-mailů při selhání spuštění a dokončení spuštění
* Použití funkce azure po registraci modelu
* Streamování událostí z Azure Machine Learning do různých koncových bodů
* Aktivace kanálu ML při zjištění posunu

> [!NOTE] 
> Aktuálně se události runStatusChanged aktivují pouze v případě, že se nezdařil stav **spuštění.**
>

## <a name="prerequisites"></a>Požadavky
* Přístup přispěvatele nebo vlastníka k pracovnímu prostoru Azure Machine Learning, pro který vytvoříte události.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Konfigurace eventgridu pomocí portálu Azure

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte do pracovního prostoru Azure Machine Learning.

1. Na levém panelu vyberte __Události__ a pak vyberte **Předplatná událostí**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Vyberte typ události, který chcete spotřebovat. Například následující snímek obrazovky vybral __Model registrován__, Model __nasazen ,__ Spustit __dokončeno__a __byl zjištěn posun datové sady__:

    ![typ události přidání](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Vyberte koncový bod, do který chcete událost publikovat. Na následujícím snímku obrazovky je centrem __událostí__ vybraný koncový bod:

    ![obslužná rutina události select-event](./media/how-to-use-event-grid/select-event-handler.png)

Po potvrzení výběru klepněte na tlačítko __Vytvořit__. Po konfiguraci budou tyto události posunuty do koncového bodu.


### <a name="configure-eventgrid-using-the-cli"></a>Konfigurace sítě EventGrid pomocí rozhraní příkazového příkazu

Můžete buď nainstalovat nejnovější [azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), nebo použít Azure Cloud Shell, který je k dispozici jako součást předplatného Azure.

Chcete-li nainstalovat rozšíření Mřížka událostí, použijte následující příkaz z příkazového příkazu příkazu příkazu:

```azurecli-interactive
az add extension --name eventgrid
```

Následující příklad ukazuje, jak vybrat předplatné Azure a vytvoří e nové předplatné událostí pro Azure Machine Learning:

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

## <a name="filter-events"></a>Filtrování událostí

Při nastavování událostí můžete filtry použít pouze na konkrétní data událostí. V níže uvedeném příkladu pro události změny stavu spuštění můžete filtrovat podle typů spuštění. Událost se aktivuje pouze v případě, že jsou splněna kritéria. Informace o datech událostí, podle kterých můžete filtrovat, najdete v [mřížce událostí Azure Machine Learning.](/azure/event-grid/event-schema-machine-learning) 

1. Přejděte na portál Azure, vyberte nové předplatné nebo existující předplatné. 

1. Vyberte kartu Filtry a posuňte se dolů na Upřesnit filtry. V **Klíč** a **hodnota** zadejte typy vlastností, které chcete filtrovat. Zde můžete vidět, že událost se spustí pouze v případě, že typ spuštění je spuštění kanálu nebo spuštění kroku kanálu.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="události filtru":::

## <a name="sample-scenarios"></a>Ukázkové scénáře

### <a name="use-a-logic-app-to-send-email-alerts"></a>Odesílání e-mailových upozornění pomocí aplikace logiky

Využijte [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) ke konfiguraci e-mailů pro všechny vaše události. Přizpůsobte si podmínky a určete příjemce, abyste umožnili spolupráci a povědomí mezi týmy, které spolupracují.

1. Na webu Azure Portal přejděte do pracovního prostoru Azure Machine Learning a na levém panelu vyberte kartu Události. Tady vyberte __Aplikace Logika__. 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Přihlaste se k ujmulovacímu uzponu logické aplikace a jako typ tématu vyberte službu Strojového učení. 

    ![typu select-topic](./media/how-to-use-event-grid/select-topic-type.png)

1. Vyberte události, na které chcete být upozorněni. Například následující snímek obrazovky __RunCompleted__.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Můžete použít metodu filtrování v části výše nebo přidat filtry pouze aktivovat aplikaci logiky na podmnožinu typů událostí. Na následujícím snímku obrazovky se používá __předponový filtr__ __/datadriftID/runs/.__

    ![filtr-události](./media/how-to-use-event-grid/filtering-events.png)

1. Dále přidejte krok, který tuto událost spotřebuje, a vyhledejte e-mail. Existuje několik různých e-mailových účtů, které můžete použít k přijímání událostí. Můžete také nakonfigurovat podmínky pro odeslání e-mailové výstrahy.

    ![select-email-akce](./media/how-to-use-event-grid/select-email-action.png)

1. Vyberte __Odeslat e-mail__ a vyplňte parametry. Do předmětu můžete zahrnout __typ události__ a __téma,__ které vám pomohou filtrovat události. Můžete také zahrnout odkaz na stránku pracovního prostoru pro spuštění v textu zprávy. 

    ![konfigurace-email-tělo](./media/how-to-use-event-grid/configure-email-body.png)

1. Chcete-li tuto akci uložit, v levém rohu stránky vyberte **Uložit jako.** Z pravého panelu, který se zobrazí, potvrďte vytvoření této akce.

    ![potvrdit-logika-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Použití aplikace logiky k aktivaci rekvalifikačních pracovních postupů, když dojde k posunu dat

Modely jít zastaralé v průběhu času a nezůstávají užitečné v kontextu, ve které běží. Jedním ze způsobů, jak zjistit, zda je čas přeškolit model, je detekce posunu dat. 

Tento příklad ukazuje, jak používat mřížku událostí s aplikací Azure Logic k aktivaci retraining. Příklad aktivuje kanál Azure Data Factory, když dojde k posunu dat mezi trénovací a obsluhou datových sad modelu.

Než začnete, proveďte následující akce:

* Nastavení monitorování datové sady pro [detekci posunu dat]( https://aka.ms/datadrift) v pracovním prostoru
* Vytvořte publikovaný [kanál Azure Data Factory](https://docs.microsoft.com/azure/data-factory/).

V tomto příkladu jednoduché data factory kanálu se používá ke kopírování souborů do úložiště objektů blob a spustit publikovaný kanál Machine Learning. Další informace o tomto scénáři najdete v tématu, jak nastavit [krok Machine Learning v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Začněte s vytvořením aplikace logiky. Přejděte na [portál Azure](https://portal.azure.com), vyhledejte logic apps a vyberte vytvořit.

    ![aplikace search-logic](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Vyplňte požadované informace. Chcete-li zjednodušit možnosti, použijte stejné předplatné a skupinu prostředků jako váš kanál Azure Data Factory a pracovní prostor Azure Machine Learning.

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po vytvoření aplikace logiky vyberte __při výskytu události prostředku Mřížka událostí__. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Přihlaste se a vyplňte podrobnosti o události. Nastavte __název prostředku__ na název pracovního prostoru. Nastavte __typ události__ na __DatasetDriftDetected__.

    ![přihlášení a přidat událost](./media/how-to-use-event-grid/login-and-add-event.png)

1. Přidejte nový krok a vyhledejte __Azure Data Factory__. Vyberte __možnost Vytvořit spuštění kanálu__. 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Přihlaste se a zadejte publikovaný kanál Azure Data Factory, který chcete spustit.

    ![zadat kanál adf](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Uložit a vytvořit aplikaci logiky pomocí tlačítka **uložit** v levém horním rohu stránky. Pokud chcete aplikaci zobrazit, přejděte do pracovního prostoru na [webu Azure Portal](https://portal.azure.com) a klikněte na **Události**.

    ![show-logika-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nyní je kanál datové továrny spuštěn, když dojde k posunu. Zobrazit podrobnosti o vašem běhu dat drift a strojového učení potrubí na [novém portálu pracovního prostoru](https://ml.azure.com). 

![zobrazení v pracovním prostoru](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Nasazení modelu na základě značek pomocí funkcí Azure

Objekt modelu Azure Machine Learning obsahuje parametry, které můžete kontočně nasazení například název modelu, verze, značka a vlastnost. Událost registrace modelu můžete aktivovat koncový bod a můžete použít funkci Azure k nasazení modelu na základě hodnoty těchto parametrů.

Například viz [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) úložiště a postupujte podle kroků v souboru **readme.**

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných událostech najdete v [tématu schéma událostí Azure Machine Learning.](/azure/event-grid/event-schema-machine-learning)

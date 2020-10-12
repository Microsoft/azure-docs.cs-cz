---
title: Monitorování a zobrazování protokolů spuštění ML & metriky
titleSuffix: Azure Machine Learning
description: Sledujte experimenty Azure ML a zobrazte metriky spuštění, abyste vylepšili proces vytváření modelů. Pomocí widgetů a portálu Studio můžete prozkoumat stav spuštění a zobrazit záznamy spuštění.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: aa4b55cb0700a47d9235a1d526ef1b1678d6db8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333813"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorování a zobrazování protokolů a metrik spuštění ML



V tomto článku se naučíte monitorovat Azure Machine Learning spouštění a zobrazovat jejich protokoly. Než budete moct zobrazit protokoly, musíte je nejdřív povolit. Další informace najdete v tématu [Povolení protokolování ve zkušebních běhůch Azure ml](how-to-track-experiments.md).

Protokoly vám můžou pomáhat při diagnostice chyb a upozornění nebo sledovat metriky výkonu, jako jsou parametry a přesnost modelu. V tomto článku se dozvíte, jak zobrazit protokoly pomocí následujících metod:

> [!div class="checklist"]
> * Monitorování spuštěných v nástroji Studio
> * Monitorování běhů pomocí widgetu Jupyter Notebook
> * Monitorovat automatizované běhy strojového učení
> * Zobrazit výstupní protokoly po dokončení
> * Zobrazit výstupní protokoly v studiu

Obecné informace o tom, jak spravovat experimenty, najdete v tématu [spuštění, monitorování a zrušení školicích běhů](how-to-manage-runs.md).

## <a name="monitor-runs-in-the-studio"></a>Monitorování spuštěných v nástroji Studio

Pokud chcete monitorovat běhy pro konkrétní výpočetní cíl z prohlížeče, použijte následující postup:

1. V [Azure Machine Learning Studiu](https://ml.azure.com/)vyberte svůj pracovní prostor a pak na levé straně stránky vyberte __COMPUTE__ .

1. Vyberte __školicí clustery__ , abyste zobrazili seznam cílových výpočetních prostředků, které se používají pro školení. Pak vyberte cluster.

    ![Vyberte školicí cluster.](./media/how-to-track-experiments/select-training-compute.png)

1. Vyberte __Spustit__. Zobrazí se seznam spuštění, která používají tento cluster. Chcete-li zobrazit podrobnosti o konkrétním spuštění, použijte odkaz ve sloupci __Spustit__ . Chcete-li zobrazit podrobnosti experimentu, použijte odkaz ve sloupci __experiment__ .

    ![Vybrat běhy pro cluster školení](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Vzhledem k tomu, že školení výpočetních cílů je sdíleným prostředkem, můžou mít v daném okamžiku více spuštění ve frontě nebo aktivních.
    > 
    > Běh může obsahovat podřízené spuštění, takže jedna školicí úloha může mít za následek několik položek.

Po dokončení běhu se již na této stránce nebude zobrazovat. Chcete-li zobrazit informace o dokončených spuštěních, přejděte k části __experimenty__ v nástroji Studio a vyberte experiment a spusťte. Další informace najdete v části [zobrazení metrik pro dokončená spuštění](#view-the-experiment-in-the-web-portal).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitorování běhů pomocí widgetu pro Poznámkový blok Jupyter

Když použijete metodu **ScriptRunConfig** k odeslání spuštění, můžete sledovat průběh běhu pomocí [widgetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true). Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.

Zobrazit widget Jupyter při čekání na dokončení běhu.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Snímek obrazovky s pomůckou pro Poznámkový blok Jupyter](./media/how-to-track-experiments/run-details-widget.png)

Můžete také získat odkaz na stejný displej v pracovním prostoru.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Monitorovat automatizované běhy strojového učení

Pro spuštění automatizovaného strojového učení pro přístup k grafům z předchozího běhu nahraďte `<<experiment_name>>` vhodným názvem experimentu:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widget pro Jupyter Poznámkový blok pro automatizované Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Zobrazit výstup po dokončení

Když použijete **ScriptRunConfig**, můžete použít ```run.wait_for_completion(show_output = True)``` k zobrazení po dokončení školení modelu. ```show_output```Příznak vám poskytne podrobný výstup. Další informace najdete v části ScriptRunConfig tématu [Jak povolit protokolování](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>Metriky spuštění dotazu

Metriky proučeného modelu můžete zobrazit pomocí ```run.get_metrics()``` . Můžete ho například použít s výše uvedeným příkladem k určení nejlepšího modelu tím, že vyhledáte model s nejnižší hodnotou čtverce chyby (MSE).

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Zobrazení záznamů spuštění v studiu

V [Azure Machine Learning Studiu](https://ml.azure.com)můžete procházet dokončené záznamy spuštění, včetně protokolovaných metrik.

Přejděte na kartu **experimenty** a vyberte svůj experiment. Na řídicím panelu experimentu můžete vidět sledované metriky a protokoly pro každé spuštění. 

Přechodem k podrobnostem konkrétního spuštění zobrazíte jeho výstupy nebo protokoly nebo si stáhněte snímek experimentu, abyste mohli sdílet složku experimentů s ostatními.

Můžete také upravit tabulku spustit seznam a vybrat vícenásobná spuštění a zobrazit buď hodnotu v poli Poslední, minimální nebo maximální hodnota protokolu. Přizpůsobte si grafy pro porovnání hodnot protokolovaných metrik a agregací napříč několika spuštěními.

![Podrobnosti o spuštění v Azure Machine Learning Studiu](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Formátování grafů v studiu

Pomocí následujících metod v rozhraních API protokolování můžete mít vliv na to, že Studio vizualizuje vaše metriky.

|Hodnota protokolu|Příklad kódu| Formátování na portálu|
|----|----|----|
|Protokolování pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Spojnicový graf s jednou proměnnou|
|Zaprotokoluje jednu číselnou hodnotu se stejným názvem metriky, který se opakovaně používá (například v rámci smyčky for).| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Spojnicový graf s jednou proměnnou|
|Opakované zaznamenání řádku se dvěma číselnými sloupci|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Spojnicový graf se dvěma proměnnými|
|Tabulka protokolu se dvěma číselnými sloupci|`run.log_table(name='Sine Wave', value=sines)`|Spojnicový graf se dvěma proměnnými|


## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak používat Azure Machine Learning, zkuste tyto další kroky:

* Naučte se [sledovat experimenty a protokoly povolit v návrháři Azure Machine Learning](how-to-track-designer-experiments.md).

* Příklad postupu při registraci nejvhodnějšího modelu a jeho nasazení najdete v kurzu [Trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).


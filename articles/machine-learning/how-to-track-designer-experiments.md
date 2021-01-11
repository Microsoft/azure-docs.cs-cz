---
title: Metriky protokolu v Návrháři
titleSuffix: Azure Machine Learning
description: Sledujte experimenty návrháře Azure ML. Povolte protokolování pomocí modulu spuštění skriptu Pythonu a podívejte se do protokolovaných výsledků v studiu.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: b940f5c9bd14bcec404827daaef666da802d969b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065248"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Povolit protokolování v Azure Machine Learningch kanálech návrháře


V tomto článku se dozvíte, jak přidat kód protokolování do kanálů návrháře. Naučíte se také, jak tyto protokoly zobrazit pomocí webového portálu Azure Machine Learning Studio.

Další informace o protokolování metrik pomocí prostředí SDK pro vytváření obsahu najdete v tématu [monitorování běhů experimentů Azure ml a metrik](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Povolit protokolování pomocí skriptu Pythonu pro spuštění

K povolení protokolování v kanálech návrháře použijte modul [spouštění skriptu Pythonu](./algorithm-module-reference/execute-python-script.md) . I když pomocí tohoto pracovního postupu můžete protokolovat libovolnou hodnotu, je zvláště užitečné protokolovat metriky z modulu __vyhodnocení modelu__ ke sledování výkonu modelu v průběhu spuštění.

Následující příklad ukazuje, jak protokolovat střední chybu dvou vyškolených modelů pomocí vyhodnocení modelu a spouštění modulů skriptu Pythonu.

1. Připojte modul __spuštění skriptu Pythonu__ k výstupu modulu __vyhodnocení modelu__ .

    ![Připojit modul spuštění skriptu Pythonu k vyhodnocení modulu modelu](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Vložte následující kód do editoru kódu __spouštěného v Pythonu__ , aby se protokoloval střední absolutní chyba pro vyškolený model. Podobný vzor můžete použít k zaznamenání jakékoli jiné hodnoty v Návrháři:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Tento kód používá sadu SDK Azure Machine Learning Python k protokolování hodnot. K získání kontextu aktuálního běhu používá Run.get_context (). Poté zaznamená hodnoty do tohoto kontextu pomocí metody Run. Parent. log (). Používá `parent` se k protokolování hodnot do nadřazeného spuštění kanálu namísto spuštění modulu.

Další informace o tom, jak použít sadu Python SDK k protokolování hodnot, najdete v tématu [Povolení protokolování ve školicích kurzech Azure ml](how-to-track-experiments.md).

## <a name="view-logs"></a>Zobrazení protokolů

Po dokončení kanálu můžete na stránce experimenty zobrazit *Mean_Absolute_Error* .

1. Přejděte do části **experimenty** .
1. Vyberte experiment.
1. Vyberte běh v experimentu, který chcete zobrazit.
1. Vyberte **Metriky**.

    ![Zobrazit metriky spuštění v studiu](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat protokoly v návrháři. Další postup najdete v těchto souvisejících článcích:


* Přečtěte si, jak řešit potíže s kanály návrháře, najdete v tématu [ladění & řešení potíží s kanály](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Přečtěte si, jak pomocí sady Python SDK protokolovat metriky v prostředí pro vytváření obsahu SDK, najdete v tématu [Povolení protokolování ve školicích kurzech Azure ml](how-to-track-experiments.md).
* Naučte se používat [skript spustit](./algorithm-module-reference/execute-python-script.md) v jazyce Python v návrháři.

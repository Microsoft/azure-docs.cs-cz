---
title: Ladění & řešení potíží s kanály ML
titleSuffix: Azure Machine Learning
description: Ladění kanálů Azure Machine Learning v Pythonu Seznamte se s běžnými nástrah pro vývoj kanálů a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: d369aafa7fdade93df1fe1706aa90c5135c75e79
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216959"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění kanálů strojového učení a řešení souvisejících potíží

V tomto článku se dozvíte, jak ladit a řešit potíže s [kanály strojového učení](concept-ml-pipelines.md) v sadě [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) a v [Návrháři Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer).

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Následující tabulka obsahuje běžné problémy při vývoji kanálů s potenciálními řešeními.

| Problém | Možné řešení |
|--|--|
| Nejde předat data do `PipelineData` adresáře. | Ujistěte se, že jste ve skriptu vytvořili adresář, který odpovídá tomu, kde váš kanál očekává výstupní data kroku. Ve většině případů vstupní argument definuje výstupní adresář a pak adresář vytvoří explicitně. Použijte `os.makedirs(args.output_dir, exist_ok=True)` k vytvoření výstupního adresáře. V tomto [kurzu](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) najdete příklad ukázkového skriptu, který ukazuje tento vzor návrhu. |
| Chyby závislostí | Pokud ve svém vzdáleném kanálu dojde k chybám závislostí, které nevznikly při místním testování, potvrďte, že závislosti a verze vzdáleného prostředí odpovídají hodnotám ve vašem testovacím prostředí. (Viz [sestavování prostředí, ukládání do mezipaměti a opakované použití](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Dvojznačné chyby s cíli výpočtů | Zkuste odstranit a znovu vytvořit výpočetní cíle. Opětovné vytváření výpočetních cílů je rychlé a může vyřešit některé přechodné problémy. |
| Kanál nepoužívá znovu postup | Použití tohoto kroku je ve výchozím nastavení povolené, ale ujistěte se, že jste ho neaktivovali v kroku kanálu. Pokud je opětovné použití zakázané, `allow_reuse` parametr v kroku se nastaví na `False` . |
| Nenutně funguje kanál. | Aby se zajistilo, že kroky se spustí znovu jenom v případě, že se změní jejich podkladová data nebo skripty, oddělte adresáře zdrojového kódu pro každý krok. Pokud používáte stejný zdrojový adresář pro více kroků, může docházet k zbytečnému opakovanému spuštění. Použijte `source_directory` parametr v objektu kroku kanálu, který odkazuje na izolovaný adresář pro daný krok, a ujistěte se, že nepoužíváte stejnou `source_directory` cestu pro více kroků. |
| Krok zpomalující epochs nebo jiné chování smyček | Zkuste přepnout všechny zápisy souborů, včetně protokolování, od `as_mount()` do `as_upload()` . Režim **připojení** používá vzdálený virtualizovaný systém souborů a nahrává celý soubor pokaždé, když je připojen k. |

## <a name="troubleshooting-parallelrunstep"></a>Při `ParallelRunStep` 

Skript pro `ParallelRunStep` *musí obsahovat* dvě funkce:
- `init()`: Tuto funkci použijte pro veškerou nákladný nebo běžnou přípravu pro pozdější odvození. Můžete ji například použít k načtení modelu do globálního objektu. Tato funkce bude volána pouze jednou na začátku procesu.
-  `run(mini_batch)`: Funkce se spustí pro každou `mini_batch` instanci.
    -  `mini_batch`: `ParallelRunStep` vyvolá metodu Run a předá `DataFrame` jako argument metody seznam nebo PANDAS. Každá položka v mini_batch bude cestou k souboru, pokud je vstupem `FileDataset` nebo PANDAS, `DataFrame` Pokud je vstupem `TabularDataset` .
    -  `response`: metoda Run () by měla vracet PANDAS `DataFrame` nebo Array. Pro append_row output_action jsou tyto vrácené prvky připojeny do společného výstupního souboru. V případě summary_only se obsah prvků ignoruje. U všech výstupních akcí každý vrácený element Output označuje jedno úspěšné spuštění vstupního prvku ve vstupní Mini-Batch. Ujistěte se, že je ve výsledku spuštění k dispozici dostatek dat pro mapování vstupu na výsledek výstupu. Výstup spuštění se zapíše do výstupního souboru a nebude zaručit, že bude v pořádku, abyste ho namapovali na vstup, měli byste použít nějaký klíč ve výstupu.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Pokud máte jiný soubor nebo složku ve stejném adresáři jako skript pro odvození, můžete na něj odkazovat pomocí hledání aktuálního pracovního adresáře.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametry pro ParallelRunConfig

`ParallelRunConfig` je hlavní konfigurací `ParallelRunStep` instance v rámci Azure Machine Learning kanálu. Použijete ho k zabalení skriptu a ke konfiguraci nezbytných parametrů, včetně všech následujících položek:
- `entry_script`: Uživatelský skript jako cesta k místnímu souboru, který bude spuštěn paralelně na více uzlech. Pokud `source_directory` je k dispozici, použijte relativní cestu. V opačném případě použijte jakoukoli cestu, která je přístupná v počítači.
- `mini_batch_size`: Velikost malé dávky předaná jednomu `run()` volání. (volitelné; výchozí hodnota je `10` soubory pro `FileDataset` a `1MB` pro `TabularDataset` .)
    - V případě je `FileDataset` to počet souborů s minimální hodnotou `1` . Můžete zkombinovat více souborů do jedné Mini-dávky.
    - Pro `TabularDataset` je to velikost dat. Příklady hodnot jsou `1024` , `1024KB` , `10MB` a `1GB` . Doporučená hodnota je `1MB` . Ze zkrácené dávky `TabularDataset` nebude nikdy mezi hranicemi souborů. Například pokud máte soubory. csv s různými velikostmi, nejmenší soubor je 100 KB a největší je 10 MB. Pokud nastavíte `mini_batch_size = 1MB` , budou se soubory s velikostí menší než 1 MB považovat za jednu miniickou dávku. Soubory o velikosti větší než 1 MB budou rozděleny do několika Mini-dávek.
- `error_threshold`: Počet selhání záznamu `TabularDataset` a selhání souborů pro `FileDataset` , které by měly být během zpracování ignorovány. Pokud se počet chyb pro celý vstup překročí k této hodnotě, bude úloha přerušena. Prahová hodnota chyby je pro celý vstup a nikoli pro jednotlivé Mini-dávky odeslané do `run()` metody. Rozsah je `[-1, int.max]` . `-1`Část indikuje ignorování všech selhání během zpracování.
- `output_action`: Jedna z následujících hodnot indikuje, jak bude uspořádán výstup:
    - `summary_only`: Uživatelský skript uloží výstup. `ParallelRunStep` použije výstup pouze pro výpočet prahové hodnoty chyby.
    - `append_row`: Pro všechny vstupy se ve výstupní složce vytvoří pouze jeden soubor pro připojení všech výstupů oddělených čárou.
- `append_row_file_name`: Chcete-li přizpůsobit název výstupního souboru pro append_row output_action (volitelné; výchozí hodnota je `parallel_run_step.txt` ).
- `source_directory`: Cesty ke složkám, které obsahují všechny soubory, které mají být spuštěny na cílovém výpočetním cíli (volitelné).
- `compute_target`: `AmlCompute` Podporuje se jenom.
- `node_count`: Počet výpočetních uzlů, které se mají použít ke spuštění skriptu uživatele.
- `process_count_per_node`: Počet procesů na uzel. Osvědčeným postupem je nastavit počet GPU nebo PROCESORového jednoho uzlu (volitelné; výchozí hodnota je `1` ).
- `environment`: Definice prostředí Pythonu. Můžete ji nakonfigurovat tak, aby používala existující prostředí Pythonu, nebo nastavit dočasné prostředí. Definice je také zodpovědná za nastavení požadovaných závislostí aplikace (volitelné).
- `logging_level`: Podrobnosti protokolu. Hodnoty při zvyšování podrobností jsou: `WARNING` , `INFO` a `DEBUG` . (volitelné; výchozí hodnota je `INFO` )
- `run_invocation_timeout`: `run()` Časový limit volání metody v sekundách. (volitelné; výchozí hodnota je `60` )
- `run_max_try`: Maximální počet testovaných položek `run()` pro Mini-Batch. V `run()` případě, že dojde k výjimce, se nezdařila nebo není vrácena žádná hodnota, pokud `run_invocation_timeout` je dosaženo (volitelné; výchozí hodnota je `3` ). 

Můžete zadat `mini_batch_size` , `node_count` ,, `process_count_per_node` `logging_level` , `run_invocation_timeout` a `run_max_try` jako, aby `PipelineParameter` při opakovaném odesílání kanálu mohli doladit hodnoty parametrů. V tomto příkladu použijete `PipelineParameter` pro `mini_batch_size` a `Process_count_per_node` a tyto hodnoty změníte, pokud je znovu spustíte později. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametry pro vytvoření ParallelRunStep

Vytvořte ParallelRunStep pomocí skriptu, konfigurace prostředí a parametrů. Určete výpočetní cíl, který jste už připojili k pracovnímu prostoru jako cíl provádění pro váš skript pro odvození. Použijte `ParallelRunStep` k vytvoření kroku kanálu odvození dávky, který převezme všechny následující parametry:
- `name`: Název kroku s následujícími omezeními pro pojmenovávání: jedinečné, 3-32 znaky a Regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: `ParallelRunConfig` Objekt, jak je definováno výše.
- `inputs`: Jedna nebo více Azure Machine Learning datových sad s jedním typem, které mají být rozděleny na oddíly pro paralelní zpracování.
- `side_inputs`: Jedno nebo více referenčních dat nebo datových sad, které se používají jako vstupy na straně, není nutné rozdělit na oddíly.
- `output`: `PipelineData` Objekt, který odpovídá výstupnímu adresáři.
- `arguments`: Seznam argumentů předaných uživatelskému skriptu. Pomocí unknown_args je načtěte v vstupním skriptu (volitelné).
- `allow_reuse`: Zda by měl krok při spuštění se stejnými nastaveními nebo vstupy znovu použít předchozí výsledky. Pokud je tento parametr `False` , nové spuštění bude pro tento krok vždy vygenerováno během provádění kanálu. (volitelné; výchozí hodnota je `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-techniques"></a>Techniky ladění

Existují tři hlavní techniky pro ladění kanálů: 

* Ladění jednotlivých kroků kanálu v místním počítači
* K izolaci a diagnostice zdroje problému použijte protokolování a Application Insights
* Připojení vzdáleného ladicího programu k kanálu běžícímu v Azure

### <a name="debug-scripts-locally"></a>Místní ladění skriptů

Jedním z nejběžnějších chyb v kanálu je, že se skript domény nespustí tak, jak má, nebo obsahuje běhové chyby ve vzdáleném výpočetním kontextu, které se obtížně ladí.

Samotné kanály se nedají spouštět místně, ale spuštěné skripty v izolaci na místním počítači vám umožní ladit rychleji, protože nemusíte čekat na proces sestavení výpočtů a prostředí. K tomu je potřeba nějaká vývojová práce:

* Pokud jsou vaše data v cloudovém úložišti dat, budete muset stáhnout data a zpřístupnit je vašemu skriptu. Použití malého vzorku vašich dat je dobrým způsobem, jak vyjímat modul runtime a rychle získat zpětnou vazbu k chování skriptu.
* Pokud se pokoušíte simulovat krok zprostředkujícího kanálu, možná budete muset ručně vytvořit typy objektů, které konkrétní skript očekává od předchozího kroku.
* Budete také muset definovat vlastní prostředí a replikovat závislosti definované ve vzdáleném výpočetním prostředí.

Jakmile budete mít Instalační program skriptu spuštěný v místním prostředí, je mnohem jednodušší provádět úlohy ladění, jako je:

* Připojení vlastní konfigurace ladění
* Pozastavení provádění a kontrola stavu objektu
* Zachycení typu nebo logických chyb, které se nezveřejňují do doby běhu

> [!TIP] 
> Jakmile ověříte, že je váš skript spuštěný podle očekávání, dobrým dalším krokem je spuštění skriptu v kanálu s jedním krokem předtím, než se pokusíte spustit v kanálu s více kroky.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurace, zápis a kontrola protokolů kanálu

Místní testování skriptů je skvělým způsobem, jak ladit hlavní fragmenty kódu a složitou logiku předtím, než začnete sestavovat kanál, ale v některých případech bude pravděpodobně nutné ladit skripty během samotného spuštění kanálu, zejména při diagnostice chování, ke kterému dojde během interakce mezi jednotlivými kroky kanálu. Doporučujeme, `print()` abyste ve svých skriptech použili možnost použití příkazů, abyste viděli stav objektu a očekávané hodnoty při vzdáleném spuštění, podobně jako při ladění kódu JavaScriptu.

### <a name="logging-options-and-behavior"></a>Možnosti a chování protokolování

Následující tabulka poskytuje informace o různých možnostech ladění pro kanály. Nejedná se o vyčerpávající seznam, protože další možnosti existují kromě pouze Azure Machine Learning, Pythonu a OpenCensus, které jsou zde uvedeny.

| Knihovna                    | Typ   | Příklad                                                          | Cíl                                  | Zdroje a prostředky                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Uživatelské rozhraní portálu Azure Machine Learning             | [Jak sledovat experimenty](how-to-track-experiments.md)<br>[AzureML. Core. Run – třída](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)                                                                                                                                                 |
| Tisk/protokolování v Pythonu    | Protokol    | `print(val)`<br>`logging.info(message)`                          | Protokoly ovladačů, Návrhář Azure Machine Learning | [Jak sledovat experimenty](how-to-track-experiments.md)<br><br>[Protokolování Pythonu](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Protokol    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – trasování                | [Ladění kanálů ve službě Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Nástroje pro export OpenCensus pro Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Kuchařka protokolování Pythonu](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Příklad možností protokolování

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Návrhář služby Azure Machine Learning

Pro kanály vytvořené v Návrháři můžete soubor **70_driver_log** najít na stránce pro vytváření obsahu nebo na stránce s podrobnostmi o spuštění kanálu.

### <a name="enable-logging-for-real-time-endpoints"></a>Povolit protokolování pro koncové body v reálném čase

Aby bylo možné řešit a ladit koncové body v reálném čase v návrháři, je nutné povolit protokolování Application Insight pomocí sady SDK. Protokolování umožňuje řešit a ladit nasazení modelu a problémy s využitím. Další informace najdete v tématu [protokolování pro nasazené modely](how-to-enable-logging.md#logging-for-deployed-models). 

### <a name="get-logs-from-the-authoring-page"></a>Získat protokoly ze stránky pro vytváření obsahu

Když odešlete spuštění kanálu a zůstanete na stránce vytváření obsahu, můžete najít soubory protokolu vygenerované pro každý modul, když se každý modul dokončí.

1. Vyberte modul, který se dokončil na plátně pro tvorbu.
1. V pravém podokně modulu otevřete kartu  **výstupy + protokoly** .
1. Rozbalte pravé podokno a vyberte **70_driver_log.txt** pro zobrazení souboru v prohlížeči. Protokoly také můžete stahovat místně.

    ![Rozšířené podokno výstup v Návrháři](./media/how-to-debug-pipelines/designer-logs.png)? zobrazení = Azure-ml-py&Preserve-View = true)? zobrazení = Azure-ml-py&Preserve-View = true)

### <a name="get-logs-from-pipeline-runs"></a>Získání protokolů z spuštění kanálu

Soubory protokolů pro konkrétní spuštění můžete najít na stránce s podrobnostmi o spuštění kanálu, kterou najdete v části **kanály** nebo **experimenty** v nástroji Studio.

1. Vyberte běh kanálu vytvořený v návrháři.

    ![Stránka spuštění kanálu](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Vyberte modul v podokně náhledu.
1. V pravém podokně modulu otevřete kartu  **výstupy + protokoly** .
1. Rozbalením pravého podokna zobrazte soubor **70_driver_log.txt** v prohlížeči nebo vyberte soubor pro místní stažení protokolů.

> [!IMPORTANT]
> Chcete-li aktualizovat kanál na stránce s podrobnostmi o spuštění kanálu, je nutné **naklonovat** spuštění kanálu do nové konceptu kanálu. Spuštění kanálu je snímek kanálu. Je podobný souboru protokolu a nedá se změnit. 

## <a name="application-insights"></a>Application Insights
Další informace o použití knihovny Pythonu OpenCensus tímto způsobem najdete v této příručce: [ladění a řešení potíží s kanály strojového učení v Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktivní ladění pomocí Visual Studio Code

V některých případech možná budete muset interaktivně ladit kód Pythonu, který se používá v kanálu ML. Pomocí Visual Studio Code (VS Code) a debugpy se můžete připojit ke kódu při jeho spuštění ve školicím prostředí. Další informace najdete [v příručce k interaktivnímu ladění v vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Další kroky

* Úplný kurz použití `ParallelRunStep` najdete v tématu [kurz: sestavení kanálu Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md).

* Úplný příklad automatizovaného strojového učení v kanálech ML najdete v tématu [Použití automatizovaného ml v kanálu Azure Machine Learning v Pythonu](how-to-use-automlstep-in-pipelines.md).

* Nápovědu k balíčku [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) a balíčku [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) najdete v referenčních informacích k sadě SDK.

* Podívejte se na seznam [výjimek návrháře a kódů chyb](algorithm-module-reference/designer-error-codes.md).

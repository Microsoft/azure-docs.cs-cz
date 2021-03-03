---
title: Monitorování a zobrazování protokolů spuštění ML & metriky
titleSuffix: Azure Machine Learning
description: Sledujte své experimenty ML a zobrazte metriky spuštění pomocí widgetů Jupyter a Azure Machine Learning studia.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8b2a61a92a25e1c0da9f85439438e75969fcfbf0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661014"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorování a zobrazování protokolů a metrik spuštění ML

Naučte se monitorovat Azure Machine Learning spouštění a zobrazovat jejich protokoly. 

Když spustíte experiment, protokoly a metriky se streamují za vás.  Kromě toho můžete přidat vlastní.  Další informace najdete v tématu [Povolení protokolování ve školicích kurzech Azure ml](how-to-track-experiments.md).

Protokoly vám můžou pomáhat při diagnostice chyb a upozornění pro vaši práci. Metriky výkonu, jako jsou parametry a přesnost modelu, vám pomůžou sledovat a monitorovat vaše běhy.

V tomto článku se dozvíte, jak zobrazit protokoly pomocí následujících metod:

> [!div class="checklist"]
> * Monitorování spuštěných v nástroji Studio
> * Monitorování běhů pomocí widgetu Jupyter Notebook
> * Monitorovat automatizované běhy strojového učení
> * Zobrazit výstupní protokoly po dokončení
> * Zobrazit výstupní protokoly v studiu

Obecné informace o tom, jak spravovat experimenty, najdete v tématu [spuštění, monitorování a zrušení školicích běhů](how-to-manage-runs.md).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitorování běhů pomocí widgetu pro Poznámkový blok Jupyter

Když použijete metodu **ScriptRunConfig** k odeslání spuštění, můžete sledovat průběh běhu pomocí [widgetu Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py). Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.

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

### <a name="logging-run-metrics"></a>Protokolování spuštění metrik 

Pomocí následujících metod v rozhraních API protokolování můžete ovlivnit vizualizace metrik. Poznamenejte si [omezení služby](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) pro tyto protokolované metriky. 

|Hodnota protokolu|Příklad kódu| Formátování na portálu|
|----|----|----|
|Protokolování pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Spojnicový graf s jednou proměnnou|
|Zaprotokoluje jednu číselnou hodnotu se stejným názvem metriky, který se opakovaně používá (například v rámci smyčky for).| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Spojnicový graf s jednou proměnnou|
|Opakované zaznamenání řádku se dvěma číselnými sloupci|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Spojnicový graf se dvěma proměnnými|
|Tabulka protokolu se dvěma číselnými sloupci|`run.log_table(name='Sine Wave', value=sines)`|Spojnicový graf se dvěma proměnnými|

## <a name="query-run-metrics"></a>Metriky spuštění dotazu

Metriky proučeného modelu můžete zobrazit pomocí ```run.get_metrics()``` . Můžete ho například použít s výše uvedeným příkladem k určení nejlepšího modelu tím, že vyhledáte model s nejnižší hodnotou čtverce chyby (MSE).

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>Zobrazení záznamů spuštění v studiu

V [Azure Machine Learning Studiu](https://ml.azure.com)můžete procházet dokončené záznamy spuštění, včetně protokolovaných metrik.

Přejděte na kartu **experimenty** . Pokud chcete zobrazit všechny vaše běhy v pracovním prostoru mezi experimenty, vyberte kartu **všechna spuštění** . Můžete přejít k podrobnostem o spuštění pro konkrétní experimenty pomocí filtru experimentů v horním řádku nabídek.

Pro jednotlivé zobrazení experimentů vyberte kartu **všechny experimenty** . Na řídicím panelu experimentu můžete vidět sledované metriky a protokoly pro každé spuštění. 

Můžete také upravit tabulku spustit seznam a vybrat vícenásobná spuštění a zobrazit buď hodnotu v poli Poslední, minimální nebo maximální hodnota protokolu. Přizpůsobte si grafy pro porovnání hodnot protokolovaných metrik a agregací napříč několika spuštěními. 

![Podrobnosti o spuštění v Azure Machine Learning Studiu](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-log-files-for-a-run"></a>Zobrazení souborů protokolu pro spuštění 

Soubory protokolu jsou zásadním prostředkem pro ladění úloh Azure ML. Přechod k podrobnostem konkrétního spuštění, aby se zobrazily jeho protokoly a výstupy:  

1. Přejděte na kartu **experimenty** .
1. Vyberte runID pro konkrétní spuštění.
1. V horní části stránky vyberte **výstupy a protokoly** .

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="Snímek obrazovky s částí výstupu a protokolů běhu":::

Níže uvedené tabulky zobrazují obsah souborů protokolu ve složkách, které vidíte v této části.

> [!NOTE]
> Každý soubor se pro každé spuštění nemusí zobrazovat. Například 20_image_build_log *. txt se zobrazí pouze v případě, že je vytvořen nový obrázek (například při změně prostředí).

#### <a name="azureml-logs-folder"></a>`azureml-logs` složky

|Soubor  |Popis  |
|---------|---------|
|20_image_build_log.txt     | Protokol pro vytváření imagí Docker pro školicí prostředí, volitelně, jeden za běhu. Platí pouze při aktualizaci vašeho prostředí. V opačném případě AML znovu použije obrázek uložený v mezipaměti. V případě úspěchu obsahuje podrobnosti o bitové kopii registru pro odpovídající obrázek.         |
|55_azureml-Execution-<node_id # C1.txt     | stdout/stderr protokol hostitelského nástroje, jeden pro každý uzel. Vyžádá si image do cílového výpočetního prostředí. Poznámka: Tento protokol se zobrazí jenom tehdy, když máte zabezpečené výpočetní prostředky.         |
|65_job_prep-<node_id # C1.txt     |   záznam stdout/stderr skriptu přípravy úlohy, jeden na uzel. Stáhněte si kód do výpočetního cíle a úložiště dat (Pokud se požaduje).       |
|70_driver_log (_x). txt      |  protokol stdout/stderr z skriptu ovládacího prvku AML a školicí skript zákazníka, jeden pro každý proces. **Toto je standardní výstup z vašeho skriptu. Toto je místo, kde se zobrazují protokoly kódu (například příkazy Print).** Ve většině případů tady budete monitorovat protokoly.       |
|70_mpi_log.txt     |   Protokol MPI Framework, volitelné, jeden za běhu. Pouze ke spuštění MPI.   |
|75_job_post-<node_id # C1.txt     |  stdout/stderr protokol skriptu pro vydání úlohy, jeden pro každý uzel. Odešlete protokoly a vyuvolněte výpočetní prostředky zpátky do Azure.        |
|process_info.jsna      |   zobrazí, který proces je spuštěný na kterém uzlu.  |
|process_status.jsna      | Zobrazit stav procesu, tj. Pokud proces není spuštěn, spuštěn nebo dokončen.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` složky

|Soubor  |Popis  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   systémový protokol pro přípravu úlohy        |
|job_release_azureml. log     | systémový protokol pro uvolnění úlohy        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` složky

Když je povolený postranní vozík, skripty pro přípravu úlohy a uvolnění úlohy se spustí v rámci kontejneru na vozíku.  Pro každý uzel existuje jedna složka. 

|Soubor  |Popis  |
|---------|---------|
|start_cms.txt     |  Protokol procesu, který se spustí při spuštění kontejneru postranního vozíku       |
|prep_cmd.txt      |   Protokol pro ContextManagers zadaný při `job_prep.py` spuštění (některé z nich budou streamované do `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Protokol pro ComtextManagers se ukončil při `job_release.py` spuštění.        |

#### <a name="other-folders"></a>Další složky

Pro úlohy školení ve více výpočetních clusterech jsou k dispozici protokoly pro každou IP adresu uzlu. Struktura pro každý uzel je stejná jako úloha s jedním uzlem. K dispozici je jedna další složka protokolů pro celkové spuštění, stderr a protokoly STDOUT.

Azure Machine Learning v průběhu školení protokolovat informace z nejrůznějších zdrojů, jako je například AutoML nebo kontejner Docker, který spouští školicí úlohu. Mnohé z těchto protokolů nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

## <a name="monitor-a-compute-cluster"></a>Monitorování výpočetního clusteru

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


## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak používat Azure Machine Learning, zkuste tyto další kroky:

* Naučte se [sledovat experimenty a protokoly povolit v návrháři Azure Machine Learning](how-to-track-designer-experiments.md).

* Příklad postupu při registraci nejvhodnějšího modelu a jeho nasazení najdete v kurzu [Trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

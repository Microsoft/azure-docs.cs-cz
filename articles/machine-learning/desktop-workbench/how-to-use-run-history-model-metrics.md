---
title: Jak používat spustit historie a metrik Model v Azure strojového učení Workbench | Microsoft Docs
description: Příručka pro použití funkce historii běhů a metriky Model Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: dddf6af506269c7f22cea69e39b55effa2f14d63
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Postup použití historie spouštění a metrik Model v Azure Machine Learning Workbench

Azure Machine Learning Workbench podporuje vědecké účely experimentování data přes jeho **historii běhů** a **metrik Model** funkce.
**Historie spouštění** poskytuje prostředky ke sledování výstupy vaší strojového učení experimenty a pak umožňuje filtrování a porovnání jejich výsledky.
**Model metriky** lze protokolovat z libovolného bodu skripty, sledování, ať hodnoty jsou nejdůležitější v experimentů vědecké účely data.
Tento článek popisuje, jak provádět efektivní použití těchto funkcí k zvýšit rychlost a kvalitu vědecké účely experimentování vaše data.

## <a name="prerequisites"></a>Požadavky
Na krok tímto průvodcem postupy, budete muset:
* [Vytvořit a nainstalovat Azure Machine Learning](../service/quickstart-installation.md)
- [Vytvoření projektu](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Přehled rozhraní API služby Azure ML protokolování
[Rozhraní API protokolování služby Azure ML](reference-logging-api.md) je k dispozici prostřednictvím **azureml.logging** modulu v Python (který je nainstalován pomocí nástroje Azure ML Workbench.) Po importu tento modul, můžete použít **get_azureml_logger** metoda pro vytvoření instance **protokolovač** objektu.
Pak můžete použít protokoly **protokolu** metodu pro uložení párů klíč hodnota vyprodukované skripty jazyka Python.
V současné době metrik model protokolování skalárních typů seznamů jsou podporovány a jak je znázorněno.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Je snadno použitelné protokolovacího nástroje v rámci vašich projektů Azure ML Workbench a v tomto článku se dozvíte, jak to udělat.

## <a name="create-a-project-in-azure-ml-workbench"></a>Vytvoření projektu v Azure ML Workbench
Pokud ještě nemáte projektu, můžete vytvořit jeden z [vytvořit a nainstalovat rychlý Start](../service/quickstart-installation.md) z **řídicího panelu Projekt**, můžete otevřít **iris_sklearn.py** skriptu () jak je znázorněno.)

![přístup k skript z na kartě soubory](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Tento skript můžete použít jako vodítko pro očekávané implementaci protokolování v Azure ML metrika modelu.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametrizace a protokolu modelu metriky ze skriptu
V **iris_sklearn.py** skriptu, očekávané vzor pro import a konstrukce protokolovacího nástroje v Pythonu, se může snížit na následující řádky kódu.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Po vytvoření můžete vyvolat **protokolu** metoda s žádným dvojice název hodnota.

Po dokončení vývoj je často užitečné Parametrizace skripty tak, aby hodnoty lze předat ve prostřednictvím příkazového řádku.
Následující ukázka ukazuje, jak tak, aby přijímal parametry příkazového řádku (pokud existuje) pomocí standardní knihovny jazyka Python.
Tento skript přijímá jeden parametr pro rychlost regulaci (*reg*) používá podle klasifikace modelu ve snaze zvýšit *přesnost* bez overfitting.
Tyto proměnné jsou poté protokolována jako *regulaci míra* a *přesnost* tak, aby model s optimální výsledky lze snadno identifikovat.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Provedení těchto kroků ve skriptech povolit je, aby optimální využití **historii běhů**.

## <a name="launch-runs-from-project-dashboard"></a>Spuštění se spouští z řídicího panelu Projekt
Vrácením **řídicího panelu Projekt**, můžete spustit **sledovaných spustit** výběrem **iris_sklearn.py** skript a zadáte **regulaci rychlost**  parametr ve **argumenty** textové pole.

![zadání parametrů a spuštění běží](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Vzhledem k tomu, že spuštění sledovaných spustí neblokuje Azure ML Workbench, může být několik spuštěn paralelně.
Stav každé sledovaných spuštění se zobrazí na **panelu úloh** jak je vidět.

![sledování běží v panelu úloh](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

To umožňuje optimálního využití prostředků, bez nutnosti Každá úloha běžela postupně.

## <a name="view-results-in-run-history"></a>Zobrazení výsledků v historie spouštění
Průběh a výsledky sledovaných spustí jsou k dispozici pro analýzu v Azure ML Workbench **historii běhů**.
**Historie spouštění** poskytuje tři různá zobrazení:
- Řídicí panel
- Podrobnosti
- Porovnání

**Řídicí panel** zobrazení ukazuje údaje o mezi všechny spustí skript dané vykresluje ve formulářích grafického rozhraní i tabulkových.
**Podrobnosti** zobrazení zobrazí všechny údaje získané z konkrétní spuštění dané skriptu, včetně protokolu metriky a výstupní soubory (například vykresluje znázorněním.) **Porovnání** zobrazení umožňuje výsledky dvě nebo tři běží jako zobrazenou-souběžného, také včetně zaznamenána metriky a výstupní soubory.

Mezi osm sledovat postupnými **iris_sklearn.py**, hodnoty **regulaci rychlost** parametr a **přesnost** výsledek byly zaznamenány pro ilustraci použití spustit Zobrazení historie.

### <a name="run-history-dashboard"></a>Řídicí panel Historie spouštění
Výsledky všech spuštění osm jsou viditelné v **spustit řídicí panel Historie**.
Jako **iris_sklearn.py** protokoly *regulaci míra* a *přesnost*, **spustit řídicí panel Historie** zobrazí grafy pro tyto hodnoty pomocí výchozí.

![Řídicí panel Historie spouštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

**Spustit řídicí panel Historie** lze přizpůsobit tak, aby zaznamenané hodnoty se zobrazí také v mřížce.  Kliknutím **přizpůsobit** , zobrazí se ikona **přizpůsobení zobrazení seznamu** dialog, jak je znázorněno.

![přizpůsobení historie spouštění řídicí panel mřížky](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Všechny hodnoty zaznamenána během sledovaného spustí jsou k dispozici pro zobrazení a vyberete **regulaci míra** a **přesnost** přidá je do mřížky.

![zaznamenané hodnoty v přizpůsobené mřížky](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Je snadno najít zajímavé spustí podržením ukazatele nad body v grafech.  V takovém případě spusťte 7 poskytuje dobrý přesnost kombinaci s nízkou doba trvání.

![hledání zajímavé spustit](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Kliknutím na bod přidružené spustit 7 v jakékoli grafu nebo odkaz na 7 spustit v zobrazení mřížky **spustit podrobnosti historie**.

### <a name="run-history-details"></a>Podrobnosti o historii spuštění
V tomto zobrazení se zobrazí úplné výsledky 7 spustit spolu s artefakty vyprodukované spustit 7.

![Podrobnosti o historii spuštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

**Spustit podrobnosti historie** zobrazení poskytuje taky možnost **Stáhnout** všechny soubory, zapisovat do **. / výstupy** složky (tyto soubory jsou zajišťované Azure ML Workbench cloudové úložiště pro historii běhů, který je předmětem jiný článek.)

Nakonec **spustit podrobnosti historie** poskytuje prostředky ke obnovení projektu jeho stav v době této spustit.
Kliknutím **obnovení** tlačítko zobrazí dialog potvrzení, jak je vidět.

![Potvrďte spustit obnovení](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Pokud potvrzen, soubory mohou být přepsány nebo odebrán, takže pomocí pečlivě této funkce.

### <a name="run-history-comparison"></a>Spustit porovnání historie
Výběr dva nebo tři běží v **spustit řídicí panel Historie** a kliknutím na **porovnat** vám umožňuje **spustit porovnání historie** zobrazení.
Můžete také kliknutím na tlačítko **porovnat** a výběrem příkazu Spustit v rámci **spustit podrobnosti historie** zobrazení také přesune na můžete **spustit porovnání historie** zobrazení.
V obou případech **spustit porovnání historie** zobrazení poskytuje prostředky ke naleznete v protokolu výsledky a artefakty dvě nebo tři spustí vedle sebe.

![Spustit porovnání historie](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Toto zobrazení je užitečné zejména pro porovnání pozemků, ale obecně všechny vlastnosti spustí je možné porovnávat sem.

### <a name="command-line-interface"></a>Command Line Interface
Azure Machine Learning Workbench také poskytuje přístup k historii běhů pomocí jeho **příkazového řádku rozhraní**.
Pro přístup k **příkazového řádku rozhraní**, klikněte na tlačítko **spusťte příkazový řádek** nabídky, jak je vidět.

![Otevřete příkazový řádek](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

K dispozici pro spuštění historie příkazů přistupuje prostřednictvím `az ml history`, s online nápovědy k dispozici přidáním `-h` příznak.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Tyto příkazy poskytovat stejné funkce a vrátit stejnou data zobrazená **spustit zobrazení historie**.
Můžete například zobrazit výsledky poslední spuštění jako objekt JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Také lze zobrazit seznam všech běží v tabulkovém formátu.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
**Příkazového řádku rozhraní** je alternativní cestu pro přístup k možnosti Azure Machine Learning Workbench.

## <a name="next-steps"></a>Další kroky
Tyto funkce jsou k dispozici pro pomoc s procesem vědecké účely experimentování data.
Věříme, že mají být užitečné najít a výrazně ocení váš názor.
Toto je právě naše počáteční implementace a budeme mít mnoho vylepšení plánované.
Těšíme se na nepřetržitě jejich doručování Azure Machine Learning Workbench. 

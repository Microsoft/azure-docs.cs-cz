---
title: Jak používat spustit historie a metrik modelů v Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Průvodce používáním funkce historie spuštění a metrik modelů Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 52b02460d444464211fc74c8982379424abebb5c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965492"
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Postup použití historie spouštění a metriky modelu v aplikaci Azure Machine Learning Workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Azure Machine Learning Workbench podporuje datové vědy experimentování ve službě prostřednictvím jeho **historie běhů** a **metrik Model** funkce.
**Historie spuštění** poskytuje prostředky ke sledování výstupů vašich se strojovým učením a pak povolí filtrování a porovnání jejich výsledky.
**Model metriky** můžete protokolovat z libovolného bodu skripty, sledování, ať hodnoty jsou nejdůležitější ve své experimenty datové vědy.
Tento článek popisuje, jak efektivně využívat tyto funkce, které zvyšují rychlost a kvalitu vaše data science experimentování.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
* [Vytvořte a nainstalujte Azure Machine Learning](quickstart-installation.md)
- [Vytvoření projektu](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Přehled rozhraní API pro Azure ML protokolování
[Rozhraní API protokolování služby Azure ML](reference-logging-api.md) je k dispozici prostřednictvím **azureml.logging** moduly v Pythonu (který je nainstalovaný v aplikaci Azure ML Workbench.) Po importování tento modul, můžete použít **get_azureml_logger** metoda pro vytvoření instance **protokolovací nástroj** objektu.
Potom můžete použít protokolovač **protokolu** metoda ukládají dvojice hodnot klíč/hodnota vytvářených skripty Python.
V současné době protokolování modelu metriky Skalární typy seznamu jsou podporovány a jak je znázorněno.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Je snadné použijte protokolovací nástroj v rámci vašich projektů Azure ML Workbench a v tomto článku se dozvíte, jak postupovat.

## <a name="create-a-project-in-azure-ml-workbench"></a>Vytvoření projektu v aplikaci Azure ML Workbench
Pokud projekt ještě nemáte, můžete vytvořit jeden z [vytvoření a instalace rychlý Start](quickstart-installation.md) z **řídicí panel projekt**, můžete otevřít **iris_sklearn.py** skriptu () jak je znázorněno.)

![na kartě souborů přístup ke skriptu](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Tento skript můžete použít jako vodítko pro očekávané implementace modelu metriky protokolování v Azure ML.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametrizovat a protokolování metrik Model ze skriptu
V **iris_sklearn.py** skriptu, očekávaný vzor k importu a konstrukce protokolovací nástroj v Pythonu můžete omezit na následující řádky kódu.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Po vytvoření můžete vyvolat **protokolu** metoda s žádným dvojice název/hodnota.

Jakmile je dokončen vývoj, často je užitečné k parametrizaci skripty tak, aby hodnoty mohou být předány do prostřednictvím příkazového řádku.
Následující ukázka ukazuje, jak přijmout parametry příkazového řádku (Pokud je k dispozici) pomocí standardní knihovny jazyka Python.
Tento skript přijímá jeden parametr pro míry Regularizace (*reg*) umožňuje přizpůsobit model klasifikace ve snaze zvýšit *přesnost* bez overfitting.
Tyto proměnné jsou poté protokolována jako *Regularizace* a *přesnost* tak, aby model s optimálních výsledků lze snadno identifikovat.

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

Provedení těchto kroků ve skriptech povolit, aby optimální využití **historie běhů**.

## <a name="launch-runs-from-project-dashboard"></a>Spuštění spustí z řídicího panelu Projekt
Vrácení **řídicí panel projekt**, můžete spustit **sledované spustit** tak, že vyberete **iris_sklearn.py** skript a zadáte **regularizace**  parametr **argumenty** textové pole.

![zadávání parametrů a po otevření spuštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Protože spuštění sledované spuštění Azure ML Workbench neblokuje, několik můžete spustit paralelně.
Stav jednotlivých sledovaných spuštění je viditelný v **úlohy na panelu** jak je znázorněno.

![sledování běží na panelu úloh](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

To umožňuje optimální využití bez nutnosti Každá úloha spuštění postupně.

## <a name="view-results-in-run-history"></a>Zobrazení výsledků v historii spuštění
Průběh a výsledky sledované spuštění jsou k dispozici pro analýzu v aplikaci Azure ML Workbench **historie běhů**.
**Historie spuštění** poskytuje tři různá zobrazení:
- Řídicí panel
- Podrobnosti
- porovnání

**Řídicí panel** zobrazení ukazuje údaje o napříč všechna spuštění daného skriptu, vykreslí grafické i tabulkovém formulářů.
**Podrobnosti** zobrazení ukazuje údaje o všech generovaných z konkrétního spuštění dané skripty, včetně zaznamenané metriky a výstupních souborů (jako je vykreslen vykreslí.) **Porovnání** view umožňuje výsledky dvě nebo tři spuštění bude zobrazené vedle sebe, včetně také metrikách zaznamenaných do protokolu a výstupní soubory.

Přes osm sledovat spuštění **iris_sklearn.py**, hodnoty **regularizace** parametr a **přesnost** výsledek vypsané si ukážeme, jak pomocí spustit Zobrazení historie.

### <a name="run-history-dashboard"></a>Řídicí panel Historie spuštění
V zobrazují výsledky všech osm spuštění **řídicí panel Historie spuštění**.
Jako **iris_sklearn.py** protokoly *Regularizace* a *přesnost*, **řídicí panel Historie spuštění** zobrazuje grafy pro tyto hodnoty Výchozí nastavení.

![řídicí panel Historie spuštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

**Řídicí panel Historie spuštění** můžete přizpůsobit tak, aby zaznamenané hodnoty se také zobrazí v mřížce.  Kliknutím **přizpůsobit** , zobrazí se ikona **vlastní nastavení zobrazení seznamu** dialog, jak je znázorněno.

![přizpůsobení řídicího panelu mřížky historie spuštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Všechny hodnoty zaznamenána během sledovaného spuštění jsou k dispozici k zobrazení a následným výběrem **Regularizace** a **přesnost** přidá je do mřížky.

![zaznamenané hodnoty v přizpůsobené mřížky](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Je snadné najít zajímavé spuštění podržením ukazatele nad body v grafech.  V takovém případě spusťte 7 poskytuje dobré přesnost s nízkou dobou trvání s velkou provázaností.

![hledání zajímavé spuštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Kliknutím na bod přidružený k 7 spustit libovolný graf nebo odkaz na spuštění 7 v zobrazení mřížky **podrobnosti o historii spuštění**.

### <a name="run-history-details"></a>Podrobnosti historie spuštění
V tomto zobrazení se zobrazí úplné výsledky spuštění 7 spolu s všechny artefakty vytvořené metodou spustit 7.

![Podrobnosti historie spuštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

**Podrobnosti o historii spuštění** zobrazení poskytuje taky možnost **Stáhnout** žádné soubory zapsané do **. / výstupy** složek (tyto soubory se zálohují na aplikaci Azure ML Workbench cloudové úložiště pro historie spuštění, což je předmětem jiného článku.)

Nakonec **podrobnosti o historii spuštění** poskytuje prostředky k obnovení vašeho projektu jeho stav v době toto spuštění.
Kliknutím **obnovení** tlačítku zobrazí dialog potvrzením, jak je znázorněno.

![Potvrdit obnovení spuštění](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Pokud potvrzené, soubory mohou být přepsány nebo odebrán, tedy tuto funkci používat opatrně.

### <a name="run-history-comparison"></a>Spustit porovnání historie
Vyberete dva nebo tři spuštění **řídicí panel Historie spuštění** a kliknete na **porovnání** vám přináší **spustit porovnání historie** zobrazení.
Můžete také kliknutím na **porovnání** a výběrem příkazu Spustit v rámci **podrobnosti o historii spuštění** zobrazení také přináší **spustit porovnání historie** zobrazení.
V obou případech **spustit porovnání historie** zobrazení umožňuje zobrazit zaznamenané výsledky a artefakty dvě nebo tři spuštění vedle sebe.

![Spustit porovnání historie](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Toto zobrazení je užitečné zejména pro porovnání vykreslení, ale obecně všechny vlastnosti spuštění lze porovnat tady.

### <a name="command-line-interface"></a>Command Line Interface
Azure Machine Learning Workbench také poskytuje přístup k historii spuštění prostřednictvím jeho **rozhraní příkazového řádku**.
Pro přístup **rozhraní příkazového řádku**, klikněte na tlačítko **otevřít příkazový řádek** nabídky, jak je znázorněno.

![Otevřete příkazový řádek](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Příkazy, které jsou k dispozici pro historie běhů se přistupuje prostřednictvím `az ml history`, s online nápovědy k dispozici tak, že přidáte `-h` příznak.
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
Tyto příkazy poskytují stejné funkce a vrátit se stejnou data zobrazená **zobrazení historie spuštění**.
Například lze zobrazit výsledky poslední spuštění jako objekt JSON.
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
Navíc lze zobrazit seznam všech spuštění ve formátu tabulky.
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
**Rozhraní příkazového řádku** je alternativní cesta pro přístup k sílu Azure Machine Learning Workbench.

## <a name="next-steps"></a>Další kroky
Tyto funkce jsou dostupné jako pomoc s procesem data science experimentování.
Věříme, že je vhodné vyhledat a výrazně ocení svůj názor.
Toto je jenom naše počáteční implementace a máme spoustu vylepšení, plánované.
Těšíme se průběžné doručování je k aplikaci Azure Machine Learning Workbench. 

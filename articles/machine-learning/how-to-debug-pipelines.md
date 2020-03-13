---
title: Ladění a řešení potíží s kanály strojového učení
titleSuffix: Azure Machine Learning
description: Ladit a řešit potíže s kanály strojového učení v sadě Azure Machine Learning SDK pro Python. Seznamte se s běžnými nástrah pro vývoj kanálů a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění. Naučte se používat Visual Studio Code k interaktivnímu ladění kanálů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: c81d4db5798c15327e06471f1cb0da4841bd61b2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283443"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění a řešení potíží s kanály strojového učení
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ladit a řešit potíže s [kanály strojového učení](concept-ml-pipelines.md) v sadě [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) a v [Návrháři Azure Machine Learning (Preview)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informace jsou k dispozici v tématu Postupy:

* Ladění pomocí sady Azure Machine Learning SDK
* Ladění pomocí návrháře Azure Machine Learning
* Ladění pomocí Application Insights
* Interaktivní ladění pomocí Visual Studio Code (VS Code) a Python Tools for Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Ladění a řešení potíží v sadě Azure Machine Learning SDK
Následující části poskytují přehled běžných nástrah při vytváření kanálů a různé strategie pro ladění kódu, který běží v kanálu. Následující tipy použijte, pokud máte potíže se spuštěním kanálu podle očekávání.

### <a name="testing-scripts-locally"></a>Místní testování skriptů

Jedním z nejběžnějších chyb v kanálu je to, že připojený skript (skript pro čištění dat, hodnoticí skript atd.) neběží tak, jak je zamýšlený, nebo obsahuje běhové chyby ve vzdáleném výpočetním kontextu, které se obtížně ladí ve vašem pracovním prostoru na Azure Machine. Learning Studio. 

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

### <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Místní testování skriptů je skvělým způsobem, jak ladit hlavní fragmenty kódu a složitou logiku předtím, než začnete sestavovat kanál, ale v některých případech bude pravděpodobně nutné ladit skripty během samotného spuštění kanálu, zejména při diagnostice chování, ke kterému dojde během interakce mezi jednotlivými kroky kanálu. Ve skriptech v kroku doporučujeme použití `print()`ch příkazů, abyste viděli stav objektu a očekávané hodnoty během vzdáleného spuštění, podobně jako při ladění kódu JavaScriptu.

Soubor protokolu `70_driver_log.txt` obsahuje: 

* Všechny tištěné příkazy během provádění skriptu
* Trasování zásobníku pro skript 

Chcete-li najít tento a další soubory protokolu na portálu, nejprve klikněte na spuštění kanálu ve vašem pracovním prostoru.

![Stránka seznamu spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-01.png)

Přejděte na stránku s podrobnostmi o spuštění kanálu.

![Stránka s podrobnostmi o spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-02.png)

Pro konkrétní krok klikněte na modul. Přejděte na kartu **protokoly** . Další protokoly obsahují informace o procesu sestavení image prostředí a kroku přípravy skriptu.

![Karta protokol stránky podrobností spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Spuštění *publikovaných kanálů* najdete na kartě **koncové body** v pracovním prostoru. Spuštění pro *nepublikované kanály* se dá najít v **experimentech** nebo **kanálech**.

### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Následující tabulka obsahuje běžné problémy při vývoji kanálů s potenciálními řešeními.

| Problém | Možné řešení |
|--|--|
| Nejde předat data do adresáře `PipelineData`. | Ujistěte se, že jste ve skriptu vytvořili adresář, který odpovídá tomu, kde váš kanál očekává výstupní data kroku. Ve většině případů vstupní argument definuje výstupní adresář a pak adresář vytvoří explicitně. K vytvoření výstupního adresáře použijte `os.makedirs(args.output_dir, exist_ok=True)`. V tomto [kurzu](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) najdete příklad ukázkového skriptu, který ukazuje tento vzor návrhu. |
| Chyby závislostí | Pokud jste vytvořili a otestovali skripty lokálně, ale při spuštění ve vzdálené výpočetní službě v kanálu zjistíte problémy se závislostmi, ujistěte se, že vaše závislosti a verze prostředí COMPUTE odpovídají vašemu testovacímu prostředí. |
| Dvojznačné chyby s cíli výpočtů | Odstranění a opětovné vytváření výpočetních cílů může vyřešit určité problémy s cíli výpočtů. |
| Kanál nepoužívá znovu postup | Použití tohoto kroku je ve výchozím nastavení povolené, ale ujistěte se, že jste ho neaktivovali v kroku kanálu. Pokud je zakázané opětovné použití, parametr `allow_reuse` v kroku se nastaví na `False`. |
| Nenutně funguje kanál. | Aby se zajistilo, že se kroky spustí znovu jenom v případě, že se změní jejich podkladová data nebo skripty, oddělte adresáře pro každý krok. Pokud používáte stejný zdrojový adresář pro více kroků, může docházet k zbytečnému opakovanému spuštění. Použijte parametr `source_directory` u objektu kroku kanálu, který odkazuje na izolovaný adresář pro daný krok, a ujistěte se, že nepoužíváte stejnou `source_directory` cestu pro více kroků. |

### <a name="logging-options-and-behavior"></a>Možnosti a chování protokolování

Následující tabulka poskytuje informace o různých možnostech ladění pro kanály. Nejedná se o vyčerpávající seznam, protože další možnosti existují kromě pouze Azure Machine Learning, Pythonu a OpenCensus, které jsou zde uvedeny.

| Knihovna                    | Typ   | Příklad                                                          | Cíl                                  | Prostředky                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sada Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Uživatelské rozhraní portálu Azure Machine Learning             | [Jak sledovat experimenty](how-to-track-experiments.md#available-metrics-to-track)<br>[AzureML. Core. Run – třída](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Tisk/protokolování v Pythonu    | Protokol    | `print(val)`<br>`logging.info(message)`                          | Protokoly ovladačů, Návrhář Azure Machine Learning | [Jak sledovat experimenty](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Protokolování Pythonu](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Protokol    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – trasování                | [Ladění kanálů v Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Nástroje pro export OpenCensus pro Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Kuchařka protokolování Pythonu](https://docs.python.org/3/howto/logging-cookbook.html) |

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Ladění a řešení potíží v Návrháři Azure Machine Learning (Preview)

Tato část poskytuje přehled o řešení potíží s kanály v návrháři.
Pro kanály vytvořené v Návrháři můžete najít **soubory protokolu** na stránce vytváření obsahu nebo na stránce s podrobnostmi o spuštění kanálu.

### <a name="access-logs-from-the-authoring-page"></a>Přístup k protokolům ze stránky pro vytváření obsahu

Když odešlete spuštění kanálu a zůstanete na stránce vytváření obsahu, můžete najít soubory protokolů vygenerované pro každý modul.

1. Vyberte libovolný modul na plátně pro tvorbu obsahu.
1. V podokně vlastnosti přejdete na kartu **protokoly** .
1. Vyberte soubor protokolu `70_driver_log.txt`

    ![Vytváření protokolů modulu stránky](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Přístup k protokolům z spuštění kanálu

Soubory protokolu konkrétních spuštění můžete také najít na stránce s podrobnostmi o spuštění kanálu v částech **kanály** nebo **experimenty** .

1. Vyberte běh kanálu vytvořený v návrháři.
    Stránka spuštění kanálu ![](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. V podokně náhledu vyberte libovolný modul.
1. V podokně vlastnosti přejdete na kartu **protokoly** .
1. Vyberte soubor protokolu `70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Ladění a řešení potíží v Application Insights
Další informace o použití knihovny Pythonu OpenCensus tímto způsobem najdete v této příručce: [ladění a řešení potíží s kanály strojového učení v Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Ladění a řešení potíží v Visual Studio Code

V některých případech možná budete muset interaktivně ladit kód Pythonu, který se používá v kanálu ML. Pomocí Visual Studio Code (VS Code) a Python Tools for Visual Studio (PTVSD) se můžete připojit ke kódu při jeho spuštění ve školicím prostředí.

### <a name="prerequisites"></a>Požadavky

* __Azure Machine Learning pracovní prostor__ , který je nakonfigurován pro použití __Virtual Network Azure__.
* __Kanál Azure Machine Learning__ , který jako součást postupu kanálu používá skripty Pythonu. Například PythonScriptStep.
* Azure Machine Learning výpočetní cluster, který je __ve virtuální síti__ a který je __používán kanálem pro školení__.
* __Vývojové prostředí__ , které je __ve virtuální síti__. Vývojové prostředí může mít jednu z těchto možností:

    * Virtuální počítač Azure ve virtuální síti
    * Výpočetní instance virtuálního počítače poznámkového bloku ve virtuální síti
    * Klientský počítač připojený k virtuální síti pomocí virtuální privátní sítě (VPN).

Další informace o použití Virtual Network Azure s Azure Machine Learning najdete v tématu [zabezpečení experimentů s Azure ml a odvozování úloh v rámci služby Azure Virtual Network](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Jak to funguje

Postup kanálu ML spouští skripty Pythonu. Tyto skripty jsou upraveny tak, aby prováděly následující akce:
    
1. Zaprotokolujte IP adresu hostitele, na kterém jsou spuštěná. Tuto IP adresu použijete k připojení ladicího programu ke skriptu.

2. Spusťte ladicí komponentu PTVSD a počkejte, než se ladicí program připojí.

3. Ve vašem vývojovém prostředí budete monitorovat protokoly vytvořené procesem školení a zjistit tak IP adresu, na které je skript spuštěný.

4. Určíte VS Code IP adresu pro připojení ladicího programu k pomocí souboru `launch.json`.

5. Ladicí program můžete připojit a interaktivně krokovat prostřednictvím skriptu.

### <a name="configure-python-scripts"></a>Konfigurace skriptů Pythonu

Pokud chcete povolit ladění, proveďte následující změny ve skriptech Pythonu používaných kroky v kanálu ML:

1. Přidejte následující výpisy importu:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Přidejte následující argumenty. Tyto argumenty umožňují povolit ladicí program podle potřeby a nastavit časový limit pro připojení ladicího programu:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Přidejte následující příkazy. Tyto příkazy načtou aktuální kontext spuštění, takže můžete protokolovat IP adresu uzlu, na kterém je kód spuštěný:

    ```python
    global run
    run = Run.get_context()
    ```

1. Přidejte příkaz `if`, který spustí PTVSD a počká, až se ladicí program připojí. Pokud se žádný ladicí program nepřipojí před časovým limitem, skript pokračuje jako normální.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

Následující příklad Pythonu ukazuje základní soubor `train.py`, který umožňuje ladění:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurovat kanál ML

Aby bylo možné poskytnout balíčky Pythonu potřebné ke spuštění PTVSD a získat kontext spuštění, vytvořte prostředí a nastavte `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`. Změňte verzi sady SDK tak, aby odpovídala hodnotě, kterou používáte. Následující fragment kódu ukazuje, jak vytvořit prostředí:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

V části [Konfigurace skriptů v Pythonu](#configure-python-scripts) byly do skriptů používaných vaším postupem kanálu ml přidány dva nové argumenty. Následující fragment kódu ukazuje, jak použít tyto argumenty pro povolení ladění pro komponentu a nastavení časového limitu. Také ukazuje, jak použít prostředí vytvořené dříve nastavením `runconfig=run_config`:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Po spuštění kanálu vytvoří každý krok podřízený běh. Pokud je povoleno ladění, upravené skripty protokolují informace podobné následujícímu textu v `70_driver_log.txt` pro podřízený běh:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Uložte hodnotu `ip_address`. Používá se v další části.

> [!TIP]
> IP adresu můžete najít také z protokolů spuštění pro tento krok kanálu pro podřízený běh. Další informace o tom, jak zobrazit tyto informace, najdete v tématu [monitorování běhů experimentů Azure ml a metrik](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

1. Chcete-li nainstalovat Python Tools for Visual Studio (PTVSD) do vývojového prostředí VS Code, použijte následující příkaz:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Další informace o použití PTVSD s VS Code najdete v tématu [vzdálené ladění](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Chcete-li nakonfigurovat VS Code ke komunikaci s Azure Machine Learning výpočetní prostředí, které spouští ladicí program, vytvořte novou konfiguraci ladění:

    1. Z VS Code vyberte nabídku __ladění__ a pak vyberte __otevřít konfigurace__. Otevře se soubor s názvem __Launch. JSON__ .

    1. V souboru __Launch. JSON__ Najděte řádek, který obsahuje `"configurations": [`a vložte za něj následující text. Změňte `"host": "10.3.0.5"` položku na IP adresu vrácenou v protokolech z předchozí části. Změňte položku `"localRoot": "${workspaceFolder}/code/step"` na místní adresář obsahující kopii laděného skriptu:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Pokud již existují další položky v oddílu konfigurace, přidejte čárku (,) za kód, který jste vložili.

        > [!TIP]
        > Osvědčeným postupem je udržovat prostředky pro skripty v samostatných adresářích, což je důvod, proč `localRoot` ukázkových hodnot odkazuje `/code/step1`.
        >
        > Pokud ladíte více skriptů, v různých adresářích Vytvořte samostatný konfigurační oddíl pro každý skript.

    1. Uložte soubor __Launch. JSON__ .

### <a name="connect-the-debugger"></a>Připojit ladicí program

1. Otevřete VS Code a otevřete místní kopii skriptu.
2. Nastavte zarážky, u kterých chcete, aby se skript zastavil, jakmile se připojíte.
3. I když je na podřízeném procesu spuštěn skript a `Timeout for debug connection` se zobrazí v protokolech, použijte klávesu F5 nebo vyberte __ladit__. Po zobrazení výzvy vyberte __Azure Machine Learning Compute: Konfigurace vzdáleného ladění__ . Můžete také vybrat ikonu ladění z bočního panelu, __Azure Machine Learning: položka vzdáleného ladění__ z rozevírací nabídky ladění a potom použít zelenou šipku pro připojení ladicího programu.

    V tomto okamžiku se VS Code připojí k PTVSD na výpočetním uzlu a zastaví se na zarážce, kterou jste předtím nastavili. Nyní můžete krokovat kód při spuštění, zobrazit proměnné atd.

    > [!NOTE]
    > Pokud protokol zobrazí položku informující `Debugger attached = False`, vypršel časový limit a skript pokračuje bez ladicího programu. Odešlete kanál znovu a připojte ladicí program za `Timeout for debug connection`ovou zprávou a před vypršením časového limitu.

## <a name="next-steps"></a>Další kroky

* Nápovědu k balíčku [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíčku [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) najdete v referenčních informacích k sadě SDK.

* Podívejte se na seznam [výjimek návrháře a kódů chyb](algorithm-module-reference/designer-error-codes.md).

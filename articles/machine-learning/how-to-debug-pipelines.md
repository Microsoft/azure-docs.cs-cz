---
title: Ladění kanálů strojového učení a řešení souvisejících potíží
titleSuffix: Azure Machine Learning
description: Ladění a řešení potíží s kanály strojového učení v Azure Machine Learning SDK pro Python. Seznamte se s běžnými náskali pro vývoj kanálů a tipy, které vám pomohou ladit skripty před a během vzdáleného spuštění. Naučte se používat visual studio kód interaktivně ladit vaše kanály strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 578e935ee742ad476aeafb53670f0a92035249e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064083"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění kanálů strojového učení a řešení souvisejících potíží
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ladit a řešit [problémy s kanály strojového učení](concept-ml-pipelines.md) v [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) a [Azure Machine Learning designer (preview)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Jsou poskytovány informace o tom, jak:

* Ladění pomocí sady Azure Machine Learning SDK
* Ladění pomocí Návrháře Azure Machine Learning Designer
* Ladění pomocí přehledů aplikací
* Interaktivní ladění pomocí kódu Sady Visual Studio (Kód VS) a nástrojů Pythonu pro Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Ladění a řešení potíží ve steku Azure Machine Learning SDK
Následující části poskytují přehled běžných úskalí při vytváření kanálů a různé strategie pro ladění kódu, který běží v kanálu. Následující tipy použijte, pokud máte potíže s spuštěním kanálu podle očekávání.

### <a name="testing-scripts-locally"></a>Testování skriptů místně

Jednou z nejběžnějších chyb v kanálu je, že připojený skript (skript pro čištění dat, skript pro vyhodnocování atd.) není spuštěn tak, jak má, nebo obsahuje chyby za běhu ve vzdáleném výpočetním kontextu, které je obtížné ladit ve vašem pracovním prostoru v Azure Machine Učící se studio. 

Samotné kanály nelze spustit místně, ale spuštění skriptů v izolaci v místním počítači umožňuje ladění rychleji, protože nemusíte čekat na výpočetní a environmentální proces sestavení. K tomu je nutná některá vývojová práce:

* Pokud jsou vaše data v cloudovém úložišti dat, budete muset stáhnout data a zpřístupnit je skriptu. Použití malého vzorku dat je dobrý způsob, jak zkrátit dobu běhu a rychle získat zpětnou vazbu o chování skriptu
* Pokud se pokoušíte simulovat mezilehlý krok kanálu, bude pravděpodobně nutné ručně vytvořit typy objektů, které konkrétní skript očekává od předchozího kroku
* Budete také muset definovat vlastní prostředí a replikovat závislosti definované ve vzdáleném výpočetním prostředí

Jakmile budete mít nastavení skriptu pro spuštění v místním prostředí, je mnohem jednodušší provést ladění úloh, jako je:

* Připojení vlastní konfigurace ladění
* Pozastavení provádění a kontrola stavu objektu
* Zachycení typu nebo logické chyby, které nebudou vystaveny až do běhu

> [!TIP] 
> Jakmile můžete ověřit, že váš skript je spuštěn podle očekávání, dobrým dalším krokem je spuštění skriptu v kanálu jednoho kroku před pokusem o spuštění v kanálu s více kroky.

### <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Testování skriptů místně je skvělý způsob, jak ladit hlavní fragmenty kódu a komplexní logiku před zahájením vytváření kanálu, ale v určitém okamžiku budete pravděpodobně muset ladit skripty během samotného spuštění kanálu, zejména při diagnostice chování, ke kterému dochází během interakce mezi kroky kanálu. Doporučujeme liberální `print()` použití příkazů ve skriptech kroku, abyste viděli stav objektu a očekávané hodnoty během vzdáleného spuštění, podobně jako byste ladili kód JavaScriptu.

Soubor `70_driver_log.txt` protokolu obsahuje: 

* Všechny vytištěné příkazy během provádění skriptu
* Trasování zásobníku pro skript 

Chcete-li najít tento a další soubory protokolu na portálu, nejprve klikněte na kanál spustit ve vašem pracovním prostoru.

![Stránka se seznamem spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-01.png)

Přejděte na stránku podrobností spuštění kanálu.

![Stránka podrobností spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-02.png)

Klikněte na modul pro konkrétní krok. Přejděte na kartu **Protokoly.** Další protokoly zahrnují informace o procesu sestavení image prostředí a skripty přípravy kroků.

![Karta protokolu podrobností spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Spuštění *publikovaných kanálů* najdete na kartě **Koncové body** ve vašem pracovním prostoru. Spuštění *nepublikovaných kanálů* naleznete v **experimentech** nebo **kanálech**.

### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Následující tabulka obsahuje běžné problémy během vývoje kanálu s potenciálními řešeními.

| Problém | Možné řešení |
|--|--|
| Nelze předat data `PipelineData` do adresáře. | Ujistěte se, že jste vytvořili adresář ve skriptu, který odpovídá místu, kde váš kanál očekává výstupní data kroku. Ve většině případů vstupní argument definuje výstupní adresář a potom vytvoříte adresář explicitně. Slouží `os.makedirs(args.output_dir, exist_ok=True)` k vytvoření výstupního adresáře. Podívejte se na [příklad bodovacího](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) skriptu, který ukazuje tento návrhový vzor. |
| Chyby závislostí | Pokud jste vyvinuli a otestovali skripty místně, ale najít problémy se závislostmi při spuštění na vzdálené výpočetní prostředky v kanálu, ujistěte se, že vaše výpočetní prostředí závislosti a verze odpovídají testovací prostředí. (Viz [Vytváření prostředí, ukládání do mezipaměti a opětovné použití](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Nejednoznačné chyby s výpočetními cíli | Odstranění a opětovné vytvoření výpočetních cílů může vyřešit určité problémy s výpočetními cíli. |
| Potrubí, které nepoužívají kroky opětovného použití | Opakované použití kroku je ve výchozím nastavení povoleno, ale ujistěte se, že jste ho nezakázali v kroku kanálu. Pokud je opakované použití `allow_reuse` zakázáno, parametr v `False`kroku bude nastaven na . |
| Potrubí se zbytečně přepáčuje | Chcete-li zajistit, aby se kroky znovu spouštěli pouze při změně podkladových dat nebo skriptů, odpojte adresáře pro každý krok. Pokud používáte stejný zdrojový adresář pro více kroků, může dojít k zbytečné opakování. Pomocí `source_directory` parametru na objektu kroku kanálu přejděte na izolovaný adresář pro tento `source_directory` krok a ujistěte se, že nepoužíváte stejnou cestu pro více kroků. |

### <a name="logging-options-and-behavior"></a>Možnosti a chování protokolování

Níže uvedená tabulka obsahuje informace o různých možnostech ladění pro kanály. Není to vyčerpávající seznam, protože existují i jiné možnosti kromě azure machine learningu, Pythonu a opencensusu, které jsou zobrazeny zde.

| Knihovna                    | Typ   | Příklad                                                          | Cíl                                  | Prostředky                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Azure Machine Learning Portal UI             | [Jak sledovat experimenty](how-to-track-experiments.md#available-metrics-to-track)<br>[třída azureml.core.Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python tisk/protokolování    | Protokol    | `print(val)`<br>`logging.info(message)`                          | Protokoly ovladačů, Azure Machine Learning Designer | [Jak sledovat experimenty](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Protokolování v Pythonu](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Protokol    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – trasování                | [Ladění kanálů ve službě Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Nástroje pro export OpenCensus pro Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python protokolování kuchařka](https://docs.python.org/3/howto/logging-cookbook.html) |

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Ladění a řešení potíží v návrháři Azure Machine Learning (preview)

Tato část obsahuje přehled o řešení potíží s kanály v návrháři.
U kanálů vytvořených v návrháři můžete **soubory protokolu** najít na stránce vytváření nebo na stránce podrobností spuštění kanálu.

### <a name="access-logs-from-the-authoring-page"></a>Přístup k protokolům ze stránky pro vytváření

Když odešlete spuštění kanálu a zůstanete na stránce vytváření, můžete najít soubory protokolu generované pro každý modul.

1. Vyberte libovolný modul ve vývojovém plátně.
1. V pravém podokně modulu přejděte na kartu **Výstupy+ogs.**
1. Výběr souboru protokolu`70_driver_log.txt`

    ![Vytváření protokolů modulů stránky](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Přístup k protokolům ze spuštění kanálu

Soubory protokolu konkrétních spuštění můžete také najít na stránce podrobností spuštění kanálu v **oddílech Kanály** nebo **Experimenty.**

1. Vyberte spuštění kanálu vytvořené v návrháři.
    ![Stránka spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. V podokně náhledu vyberte libovolný modul.
1. V pravém podokně modulu přejděte na kartu **Výstupy+ogs.**
1. Výběr souboru protokolu`70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Ladění a řešení potíží v přehledech aplikací
Další informace o použití knihovny OpenCensus Python tímto způsobem najdete v této příručce: [Ladění a řešení potíží s kanály strojového učení v Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Ladění a řešení potíží v kódu sady Visual Studio

V některých případech může být nutné interaktivně ladit kód Pythonu používaný v kanálu ML. Pomocí kódu Visual Studio (VS Code) a Python tools for Visual Studio (PTVSD), můžete připojit ke kódu, jak běží v tréninkovém prostředí.

### <a name="prerequisites"></a>Požadavky

* Pracovní __prostor Azure Machine Learning,__ který je nakonfigurovaný pro použití virtuální sítě __Azure__.
* Kanál __Azure Machine Learning,__ který používá skripty Pythonu jako součást kroků kanálu. Například PythonScriptStep.
* Cluster Azure Machine Learning Compute, který je __ve virtuální síti__ a používá se __kanálem pro školení__.
* __Vývojové prostředí,__ které je __ve virtuální síti__. Vývojové prostředí může být jedna z následujících možností:

    * Virtuální počítač Azure ve virtuální síti
    * Výpočetní instance virtuálního počítače poznámkového bloku ve virtuální síti
    * Klientský počítač připojený k virtuální síti virtuální privátní sítí (VPN).

Další informace o používání virtuální sítě Azure s Azure Machine Learning najdete v [tématu Zabezpečené úlohy experimentování azure ml a odvození v rámci virtuální sítě Azure](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Jak to funguje

Kroky kanálu ML spouštějí skripty Pythonu. Tyto skripty jsou upraveny tak, aby prováděly následující akce:
    
1. Protokolovat adresu IP hostitele, na které jsou spuštěny. Ip adresu slouží k připojení ladicího programu ke skriptu.

2. Spusťte součást ladění PTVSD a počkejte na připojení ladicího programu.

3. Z vývojového prostředí můžete sledovat protokoly vytvořené procesem školení najít IP adresu, kde je spuštěn skript.

4. Sdělte vs kód IP adresu pro připojení ladicího programu pomocí souboru. `launch.json`

5. Připojíte ladicí program a interaktivně krokování skriptu.

### <a name="configure-python-scripts"></a>Konfigurace skriptů Pythonu

Chcete-li povolit ladění, proveďte následující změny skriptů Pythonu používaných kroky v kanálu ML:

1. Přidejte následující příkazy importu:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Přidejte následující argumenty. Tyto argumenty umožňují povolit ladicí program podle potřeby a nastavit časový čas pro připojení ladicího programu:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Přidejte následující příkazy. Tyto příkazy načítají aktuální kontext spuštění, takže můžete protokolovat adresu IP uzlu, na který je kód spuštěn:

    ```python
    global run
    run = Run.get_context()
    ```

1. Přidejte `if` příkaz, který spustí PTVSD a čeká na připojení ladicího programu. Pokud žádný ladicí program připojí před časový mzda, skript pokračuje jako normální.

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

Následující příklad Pythonu `train.py` ukazuje základní soubor, který umožňuje ladění:

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

### <a name="configure-ml-pipeline"></a>Konfigurace kanálu ML

Chcete-li poskytnout balíčky Pythonu potřebné ke spuštění PTVSD `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`a získání kontextu spuštění, vytvořte prostředí a nastavte . Změňte verzi sady SDK tak, aby odpovídala verzi, kterou používáte. Následující fragment kódu ukazuje, jak vytvořit prostředí:

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

V části [Konfigurovat skripty Pythonu](#configure-python-scripts) byly přidány dva nové argumenty do skriptů používaných kroky kanálu ML. Následující fragment kódu ukazuje, jak použít tyto argumenty k povolení ladění komponenty a nastavení časového času. Ukazuje také, jak používat prostředí vytvořené `runconfig=run_config`dříve nastavením :

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

Při spuštění kanálu, každý krok vytvoří podřízené spuštění. Pokud je povoleno ladění, upravený skript protokoluje informace podobné následujícímu textu v podřízeném `70_driver_log.txt` spuštění:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Uložte `ip_address` hodnotu. Používá se v další části.

> [!TIP]
> Můžete také najít IP adresu z protokolů spuštění pro podřízené spustit pro tento krok kanálu. Další informace o zobrazení těchto informací naleznete v [tématu Sledování spuštění experimentu Azure ML a metriky](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

1. Chcete-li nainstalovat nástroje Pythonu pro Visual Studio (PTVSD) do vývojového prostředí Kódu VS, použijte následující příkaz:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Další informace o používání ptvsd s kódem VS naleznete [v tématu Vzdálené ladění](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Chcete-li nakonfigurovat kód VS pro komunikaci s výpočetními prostředky Azure Machine Learning, které běží ladicí program, vytvořte novou konfiguraci ladění:

    1. V nabídce VS Code vyberte nabídku __Ladění__ a pak __vyberte Otevřít konfigurace__. Otevře se soubor s názvem __launch.json.__

    1. V souboru __launch.json__ vyhledejte `"configurations": [`řádek, který obsahuje , a za něj vložte následující text. Změňte `"host": "10.3.0.5"` položku na IP adresu vrácenou v protokolech z předchozí části. Změňte `"localRoot": "${workspaceFolder}/code/step"` položku do místního adresáře, který obsahuje kopii laděného skriptu:

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
        > Pokud již existují další položky v části konfigurace, přidejte čárku (,) za kód, který jste vložili.

        > [!TIP]
        > Osvědčeným postupem je zachovat prostředky pro skripty v samostatných adresářích, což je důvod, proč `localRoot` příklad hodnoty odkazy `/code/step1`.
        >
        > Pokud ladíte více skriptů, vytvořte v různých adresářích samostatný oddíl konfigurace pro každý skript.

    1. Uložte soubor __launch.json.__

### <a name="connect-the-debugger"></a>Připojení ladicího programu

1. Otevřete Kód VS a otevřete místní kopii skriptu.
2. Po připojení nastavte zarážky na místo, kde má skript zastavit.
3. Zatímco podřízený proces je spuštěn `Timeout for debug connection` skript a je zobrazen v protokolech, použijte klávesu F5 nebo vyberte __ladění__. Po zobrazení výzvy vyberte __konfiguraci Azure Machine Learning Compute: remote debug.__ Můžete také vybrat ikonu ladění z postranního panelu, __Azure Machine Learning: vzdálená ladicí__ položka z rozbalovací nabídky Ladění a pak pomocí zelené šipky připojit ladicí program.

    V tomto okamžiku VS Kód se připojí k PTVSD na výpočetní uzel a zastaví na zarážku, kterou jste nastavili dříve. Nyní můžete krokovat kód při jeho spuštění, zobrazit proměnné atd.

    > [!NOTE]
    > Pokud se v protokolu `Debugger attached = False`zobrazí položka s uvedením , vypršel časový limit a skript pokračoval bez ladicího programu. Odešlete kanál znovu a připojte `Timeout for debug connection` ladicí program po zprávě a před vypršením časového limitu.

## <a name="next-steps"></a>Další kroky

* Podívejte se na odkaz na sdk nápovědu k balíčku [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíčku [azureml-pipelines-steps.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Podívejte se na seznam [výjimek návrháře a kódů chyb](algorithm-module-reference/designer-error-codes.md).

---
title: Interaktivní ladění pomocí Visual Studio Code
titleSuffix: Azure Machine Learning
description: Interaktivní ladění Azure Machine Learning kódu, kanálů a nasazení pomocí Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: e572f1f6a9452ccab9deddb62a5e219a81df5d47
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739990"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interaktivní ladění pomocí Visual Studio Code



Naučte se, jak interaktivně ladit Azure Machine Learning experimenty, kanály a nasazení s využitím Visual Studio Code (VS Code) a [debugpy](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Spustit a ladit experimenty místně

Pomocí rozšíření Azure Machine Learning můžete před odesláním do cloudu ověřit, spustit a ladit experimenty ve strojovém učení.

### <a name="prerequisites"></a>Požadavky

* Rozšíření Azure Machine Learning VS Code (Preview). Další informace najdete v tématu [nastavení rozšíření Azure Machine Learning vs Code](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop pro Mac a Windows
  * Modul Docker pro Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> V systému Windows se ujistěte, že jste [nakonfigurovali Docker pro použití kontejnerů systému Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> U Windows, i když to není nutné, se důrazně doporučuje [použít Docker v subsystému Windows pro Linux (WSL) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> Před místním spuštěním experimentu se ujistěte, že je Docker spuštěný.

### <a name="debug-experiment-locally"></a>Místní ladění experimentu

1. V VS Code otevřete zobrazení rozšíření Azure Machine Learning.
1. Rozbalte uzel předplatné obsahující váš pracovní prostor. Pokud ho ještě nemáte, můžete pomocí tohoto rozšíření [vytvořit pracovní prostor Azure Machine Learning](how-to-manage-resources-vscode.md#create-a-workspace) .
1. Rozbalte uzel pracovního prostoru.
1. Klikněte pravým tlačítkem na uzel **experimenty** a vyberte **vytvořit experiment**. Po zobrazení výzvy zadejte název experimentu.
1. Rozbalte uzel **experimenty** , klikněte pravým tlačítkem na experiment, který chcete spustit, a vyberte možnost **Spustit experiment**.
1. V seznamu možností pro spuštění experimentu vyberte možnost **místně**.
1. Při **prvním použití pouze v systému Windows**. Po zobrazení výzvy k povolení sdílení souborů vyberte **Ano**. Když povolíte sdílení souborů, umožní Docker připojit adresář obsahující váš skript do kontejneru. Kromě toho umožňuje Docker ukládat protokoly a výstupy z běhu do dočasného adresáře ve vašem systému.
1. Vyberte **Ano** , pokud chcete ladit experiment. Jinak vyberte **No** (Ne). Pokud vyberete Ne, váš experiment se spustí lokálně bez připojení k ladicímu programu.
1. Vyberte **vytvořit novou konfiguraci spuštění** a vytvořte tak konfiguraci spuštění. Konfigurace spuštění definuje skript, který chcete spustit, závislosti a použité datové sady. Případně, pokud už nějaký máte, vyberte ho z rozevíracího seznamu.
    1. Vyberte své prostředí. Můžete si vybrat z libovolného [Azure Machine Learning](resource-curated-environments.md) nebo si vytvořit vlastní.
    1. Zadejte název skriptu, který chcete spustit. Cesta je relativní vzhledem k adresáři otevřenému v VS Code.
    1. Vyberte, zda chcete použít Azure Machine Learning datovou sadu. Pomocí rozšíření můžete vytvořit [Azure Machine Learning datové sady](how-to-manage-resources-vscode.md#create-dataset) .
    1. Debugpy se vyžaduje, aby se ladicí program připojil ke kontejneru, na kterém běží experiment. Pokud chcete přidat debugpy jako závislost, vyberte **Přidat debugpy**. V opačném případě vyberte **Přeskočit**. Nepřidání debugpy jako závislost spustí experiment bez připojení k ladicímu programu.
    1. V editoru se otevře konfigurační soubor, který obsahuje nastavení konfigurace spuštění. Pokud jste s nastavením spokojeni, vyberte **Odeslat experiment**. Alternativně otevřete paletu příkazů (**zobrazení > paleta příkazů**) z řádku nabídek a zadejte `Azure ML: Submit experiment` příkaz do textového pole.
1. Po odeslání experimentu se vytvoří image Docker obsahující váš skript a konfigurace zadané v konfiguraci spuštění.

    Když se spustí proces sestavení image Docker, obsah `60_control_log.txt` datového proudu souboru do výstupní konzoly v vs Code.

    > [!NOTE]
    > Při prvním vytvoření image Docker může trvat několik minut.

1. Po vytvoření image se zobrazí výzva ke spuštění ladicího programu. Nastavte zarážky ve skriptu a vyberte **Spustit ladicí program** , až budete připraveni začít s laděním. Tím se připojí ladicí program VS Code ke kontejneru, na kterém běží experiment. Případně můžete v rozšíření Azure Machine Learning najeďte myší na uzel aktuálního běhu a výběrem ikony Přehrát spustit ladicí program.

    > [!IMPORTANT]
    > K jednomu experimentu nemůžete mít více relací ladění. Můžete ale ladit dva nebo více experimentů pomocí více instancí VS Code.

V tomto okamžiku byste měli být schopni krokovat a ladit kód pomocí VS Code.

Pokud v jakémkoli okamžiku chcete zrušit spuštění, klikněte pravým tlačítkem myši na uzel spustit a vyberte možnost **zrušit spuštění**.

Podobně jako u vzdálených experimentů můžete rozšířit uzel spuštění a zkontrolovat protokoly a výstupy.

> [!TIP]
> Image Docker, které používají stejné závislosti definované ve vašem prostředí, se znovu použijí mezi běhy. Pokud však spustíte experiment pomocí nového nebo jiného prostředí, vytvoří se nový obrázek. Vzhledem k tomu, že se tyto image ukládají do místního úložiště, doporučuje se odebrat staré nebo nepoužívané image Docker. K odebrání imagí ze systému použijte rozhraní [Docker CLI](https://docs.docker.com/engine/reference/commandline/rmi/) nebo [rozšíření vs Code Docker](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění kanálů strojového učení a řešení souvisejících potíží

V některých případech možná budete muset interaktivně ladit kód Pythonu, který se používá v kanálu ML. Pomocí VS Code a debugpy se můžete ke kódu připojit při jeho spuštění ve školicím prostředí.

### <a name="prerequisites"></a>Požadavky

* __Azure Machine Learning pracovní prostor__ , který je nakonfigurován pro použití __Virtual Network Azure__.
* __Kanál Azure Machine Learning__ , který jako součást postupu kanálu používá skripty Pythonu. Například PythonScriptStep.
* Azure Machine Learning výpočetní cluster, který je __ve virtuální síti__ a který je __používán kanálem pro školení__.
* __Vývojové prostředí__ , které je __ve virtuální síti__. Vývojové prostředí může mít jednu z těchto možností:

  * Virtuální počítač Azure ve virtuální síti
  * Výpočetní instance virtuálního počítače poznámkového bloku ve virtuální síti
  * Klientský počítač, který má připojení privátní sítě k virtuální síti, a to buď pomocí sítě VPN, nebo přes ExpressRoute.

Další informace o použití Virtual Network Azure s Azure Machine Learning najdete v tématu věnovaném [izolaci virtuální sítě a ochraně osobních údajů](how-to-network-security-overview.md).

> [!TIP]
> I když můžete pracovat s Azure Machine Learning prostředky, které nejsou za virtuální sítí, doporučuje se použít virtuální síť.

### <a name="how-it-works"></a>Jak to funguje

Postup kanálu ML spouští skripty Pythonu. Tyto skripty jsou upraveny tak, aby prováděly následující akce:

1. Zaprotokolujte IP adresu hostitele, na kterém jsou spuštěná. Tuto IP adresu použijete k připojení ladicího programu ke skriptu.

2. Spusťte ladicí komponentu debugpy a počkejte, než se ladicí program připojí.

3. Ve vašem vývojovém prostředí budete monitorovat protokoly vytvořené procesem školení a zjistit tak IP adresu, na které je skript spuštěný.

4. Určíte VS Code IP adresu pro připojení ladicího programu k nástroji pomocí `launch.json` souboru.

5. Ladicí program můžete připojit a interaktivně krokovat prostřednictvím skriptu.

### <a name="configure-python-scripts"></a>Konfigurace skriptů Pythonu

Pokud chcete povolit ladění, proveďte následující změny ve skriptech Pythonu používaných kroky v kanálu ML:

1. Přidejte následující výpisy importu:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Přidejte následující argumenty. Tyto argumenty umožňují povolit ladicí program podle potřeby a nastavit časový limit pro připojení ladicího programu:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Přidejte následující příkazy. Tyto příkazy načtou aktuální kontext spuštění, takže můžete protokolovat IP adresu uzlu, na kterém je kód spuštěný:

    ```python
    global run
    run = Run.get_context()
    ```

1. Přidejte `if` příkaz, který spustí debugpy a počká, až se ladicí program připojí. Pokud se žádný ladicí program nepřipojí před časovým limitem, skript pokračuje jako normální. Nezapomeňte nahradit `HOST` `PORT` hodnoty a hodnotou `listen` funkce vlastní.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

Následující příklad Pythonu ukazuje jednoduchý `train.py` soubor, který umožňuje ladění:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
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
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurovat kanál ML

Aby bylo možné poskytnout balíčky Pythonu potřebné ke spuštění debugpy a získat kontext spuštění, vytvořte prostředí a nastavte `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Změňte verzi sady SDK tak, aby odpovídala hodnotě, kterou používáte. Následující fragment kódu ukazuje, jak vytvořit prostředí:

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
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

V části [Konfigurace skriptů v Pythonu](#configure-python-scripts) byly do skriptů používaných vaším postupem kanálu ml přidány nové argumenty. Následující fragment kódu ukazuje, jak použít tyto argumenty pro povolení ladění pro komponentu a nastavení časového limitu. Také ukazuje, jak použít prostředí vytvořené dříve nastavením `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Po spuštění kanálu vytvoří každý krok podřízený běh. Pokud je zapnuté ladění, změněné skripty protokolují informace podobné následujícímu textu v části `70_driver_log.txt` pro podřízený běh:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Uložte `ip_address` hodnotu. Používá se v další části.

> [!TIP]
> IP adresu můžete najít také z protokolů spuštění pro tento krok kanálu pro podřízený běh. Další informace o tom, jak zobrazit tyto informace, najdete v tématu [monitorování běhů experimentů Azure ml a metrik](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

1. Pokud chcete nainstalovat debugpy do vývojového prostředí VS Code, použijte následující příkaz:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Další informace o použití debugpy s VS Code najdete v tématu [vzdálené ladění](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Chcete-li nakonfigurovat VS Code ke komunikaci s Azure Machine Learning výpočetní prostředí, které spouští ladicí program, vytvořte novou konfiguraci ladění:

    1. Z VS Code vyberte nabídku __ladění__ a pak vyberte __otevřít konfigurace__. Soubor s názvem __launch.jspři__ otevření.

    1. V __launch.jsv__ souboru vyhledejte řádek, který obsahuje `"configurations": [` , a vložte za něj následující text. Změňte `"host": "<IP-ADDRESS>"` položku na IP adresu vrácenou v protokolech z předchozí části. Změňte `"localRoot": "${workspaceFolder}/code/step"` položku na místní adresář, který obsahuje kopii laděného skriptu:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
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
        > Osvědčeným postupem je udržovat prostředky pro skripty v samostatných adresářích, aby kód byl relevantní jenom pro každý z těchto kroků. V tomto příkladu se `localRoot` odkazuje na příklad hodnoty `/code/step1` .
        >
        > Pokud ladíte více skriptů, v různých adresářích Vytvořte samostatný konfigurační oddíl pro každý skript.

    1. Uložte __launch.jsdo__ souboru.

### <a name="connect-the-debugger"></a>Připojit ladicí program

1. Otevřete VS Code a otevřete místní kopii skriptu.
2. Nastavte zarážky, u kterých chcete, aby se skript zastavil, jakmile se připojíte.
3. I když je na podřízeném procesu spuštěn skript a v `Timeout for debug connection` protokolech se zobrazí, použijte klávesu F5 nebo vyberte __ladit__. Po zobrazení výzvy vyberte __Azure Machine Learning Compute: Konfigurace vzdáleného ladění__ . Můžete také vybrat ikonu ladění z bočního panelu, __Azure Machine Learning: položka vzdáleného ladění__ z rozevírací nabídky ladění a potom použít zelenou šipku pro připojení ladicího programu.

    V tomto okamžiku se VS Code připojí k debugpy na výpočetním uzlu a zastaví se na zarážce, kterou jste předtím nastavili. Nyní můžete krokovat kód při spuštění, zobrazit proměnné atd.

    > [!NOTE]
    > Pokud se v protokolu zobrazí položka informující o tom `Debugger attached = False` , že vypršel časový limit a skript pokračuje bez ladicího programu. Odešlete kanál znovu a připojte ladicí program za `Timeout for debug connection` zprávu a před vypršením časového limitu.

## <a name="debug-and-troubleshoot-deployments"></a>Ladění a řešení potíží s nasazeními

V některých případech možná budete muset interaktivně ladit kód Pythonu obsažený v nasazení modelu. Například pokud se skript vstupu nezdařil a důvod nelze určit pomocí dalšího protokolování. Pomocí VS Code a debugpy můžete připojit k kódu běžícímu uvnitř kontejneru Docker.

> [!IMPORTANT]
> Tato metoda ladění nefunguje při použití `Model.deploy()` a `LocalWebservice.deploy_configuration` k nasazení modelu místně. Místo toho je nutné vytvořit bitovou kopii pomocí metody [model. Package ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-) .

Nasazení místních webových služeb vyžaduje pracovní instalaci do dokovacího prostředí v místním systému. Další informace o používání Docker najdete v [dokumentaci k Docker](https://docs.docker.com/). Všimněte si, že při práci s výpočetními instancemi je Docker již nainstalován.

### <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

1. Pokud chcete nainstalovat debugpy na místní vývojové prostředí VS Code, použijte následující příkaz:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Další informace o použití debugpy s VS Code najdete v tématu [vzdálené ladění](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Pokud chcete nakonfigurovat VS Code ke komunikaci s imagí Docker, vytvořte novou konfiguraci ladění:

    1. Z VS Code vyberte v části rozsah __spuštění__ nabídku __ladění__ a pak vyberte __otevřít konfigurace__. Soubor s názvem __launch.jspři__ otevření.

    1. V __launch.jsv__ souboru vyhledejte položku __"konfigurace"__ (řádek, který obsahuje `"configurations": [` ) a vložte následující text za něj. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        Po vložení by měl být __launch.jsv__ souboru podobný následujícímu:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Pokud již existují další položky v oddílu konfigurace, přidejte čárku ( __,__ ) za kód, který jste vložili.

        Tato část se připojuje k kontejneru Docker pomocí portu __5678__.

    1. Uložte __launch.jsdo__ souboru.

### <a name="create-an-image-that-includes-debugpy"></a>Vytvoření image, která zahrnuje debugpy

1. Upravte prostředí conda pro nasazení tak, aby zahrnovalo debugpy. Následující příklad ukazuje přidání pomocí `pip_packages` parametru:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Pokud chcete začít debugpy a po spuštění služby počkat na připojení, přidejte na začátek `score.py` souboru následující:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Vytvořte bitovou kopii založenou na definici prostředí a přetáhnout image do místního registru. 

    > [!NOTE]
    > V tomto příkladu se předpokládá, že `ws` odkazuje na váš pracovní prostor Azure Machine Learning a který `model` je modelem, který je nasazený. `myenv.yml`Soubor obsahuje závislosti conda vytvořené v kroku 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Po vytvoření a stažení Image (Tento proces může trvat déle než 10 minut, proto prosím vyčkejte), cesta k imagi (včetně úložiště, názvu a značky, která v tomto případě je také výtahem), se nakonec zobrazí v následující zprávě, která bude vypadat přibližně takto:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Aby bylo možné snadněji pracovat s imagí v místním prostředí, můžete k přidání značky pro tuto bitovou kopii použít následující příkaz. `myimagepath`V následujícím příkazu nahraďte hodnotou umístění z předchozího kroku.

    ```bash
    docker tag myimagepath debug:1
    ```

    V případě zbývajících kroků můžete `debug:1` místo hodnoty úplná cesta k imagi použít místní obrázek.

### <a name="debug-the-service"></a>Ladění služby

> [!TIP]
> Pokud nastavíte časový limit pro připojení debugpy v `score.py` souboru, je nutné před vypršením časového limitu připojit vs Code k ladicí relaci. Spusťte VS Code, otevřete místní kopii `score.py` , nastavte zarážku a před použitím kroků v této části Připravte ji na přechod.
>
> Další informace o ladění a nastavení zarážek naleznete v tématu [ladění](https://code.visualstudio.com/Docs/editor/debugging).

1. Pokud chcete spustit kontejner Docker pomocí Image, použijte následující příkaz:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Tím se připojíte `score.py` místně k portálu v kontejneru. Proto se všechny změny provedené v editoru automaticky projeví v kontejneru.

2. Pro lepší prostředí můžete přejít do kontejneru pomocí nového rozhraní VS Code. Vyberte `Docker` Rozsah z bočního panelu vs Code, Najděte svůj místní kontejner. v této dokumentaci je to `debug:1` . Klikněte pravým tlačítkem na tento kontejner a vyberte a `"Attach Visual Studio Code"` pak se automaticky otevře nové rozhraní vs Code a toto rozhraní zobrazí uvnitř vytvořeného kontejneru.

    ![Rozhraní VS Code kontejneru](./media/how-to-troubleshoot-deployment/container-interface.png)

3. V rámci kontejneru spusťte následující příkaz v prostředí.

    ```bash
    runsvdir /var/runit
    ```
    Pak můžete v prostředí zobrazit následující výstup v rámci svého kontejneru:

    ![Výstup konzoly pro spuštění kontejneru](./media/how-to-troubleshoot-deployment/container-run.png)

4. Pokud chcete připojit VS Code k debugpy uvnitř kontejneru, otevřete VS Code a použijte klávesu F5 nebo vyberte __ladit__. Po zobrazení výzvy vyberte __nasazení Azure Machine Learning: konfigurace ladění Docker__ . Můžete také vybrat ikonu rozsahu __spuštění__ z bočního panelu, __nasazení Azure Machine Learning: položku ladění Docker__ z rozevírací nabídky ladění a potom použít zelenou šipku pro připojení ladicího programu.

    ![Ikona ladění, tlačítko Spustit ladění a selektor konfigurace](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Po kliknutí na zelenou šipku a připojení ladicího programu do kontejneru VS Code rozhraní můžete zobrazit některé nové informace:
    
    ![Připojené informace ladicího programu kontejneru](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    V hlavním VS Code rozhraní můžete také sledovat následující informace:

    ![VS Code zarážku v score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

A nyní je místní, `score.py` který je připojen ke kontejneru, již zastaven na zarážekch, kde jste nastavili. V tomto okamžiku se VS Code připojí k debugpy uvnitř kontejneru Docker a zastaví kontejner Docker na zarážce, kterou jste předtím nastavili. Nyní můžete krokovat kód při spuštění, zobrazit proměnné atd.

Další informace o použití VS Code k ladění Pythonu najdete v tématu [ladění kódu Pythonu](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Zastavení kontejneru

Chcete-li zastavit kontejner, použijte následující příkaz:

```bash
docker stop debug
```

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili VS Code vzdálené, můžete použít výpočetní instanci jako vzdálenou výpočetní prostředky z VS Code k interaktivnímu ladění kódu. 

Další informace o řešení potíží:

* [Nasazení místního modelu](how-to-troubleshoot-deployment-local.md)
* [Nasazení vzdáleného modelu](how-to-troubleshoot-deployment.md)
* [Kanály Machine Learningu](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)


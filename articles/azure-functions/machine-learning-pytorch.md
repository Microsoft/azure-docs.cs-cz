---
title: Nasazení modelu PyTorch jako aplikace Azure Functions
description: Pomocí předem vycvičené hluboké neuronové sítě ResNet 18 od Společnosti PyTorch s funkcemi Azure můžete k image přiřadit 1 z 1000 popisků ImageNet.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.openlocfilehash: 17acb7e351d5f1c009a6a8a14717e987fae3e895
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78379896"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Kurz: Nasazení předem trénovaného modelu klasifikace bitových obrázků do funkcí Azure s PyTorch

V tomto článku se dozvíte, jak pomocí Pythonu, PyTorch a Azure funkce načíst předem trénovaný model pro klasifikaci image na základě jeho obsahu. Vzhledem k tomu, že všechny práce místně a vytvářet žádné prostředky Azure v cloudu, neexistuje žádné náklady na dokončení tohoto kurzu.

> [!div class="checklist"]
> * Inicializovat místní prostředí pro vývoj funkcí Azure v Pythonu.
> * Importujte předem vyškolený model strojového učení PyTorch do funkční aplikace.
> * Vytvořte rozhraní HTTP API bez serveru pro klasifikaci bitové kopie jako jedné z 1000 [tříd](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)ImageNet .
> * Využijte rozhraní API z webové aplikace.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 nebo vyšší](https://www.python.org/downloads/release/python-374/). (Python 3.8.x a Python 3.6.x jsou také ověřeny pomocí funkcí Azure.)
- [Základní nástroje Azure Functions](functions-run-local.md#install-the-azure-functions-core-tools)
- Editor kódu, jako je [například Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Kontrola předpokladů

1. V terminálu nebo příkazovém okně spusťte `func --version` a zkontrolujte, zda jsou základní nástroje Azure Functions core tools verze 2.7.1846 nebo novější.
1. Spusťte `python --version` (Linux/MacOS) nebo (Windows) a `py --version` zkontrolujte zprávy o verzi Pythonu 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Klonování výukového úložiště

1. V terminálu nebo příkazovém okně naklonovat následující úložiště pomocí Gitu:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Přejděte do složky a zkontrolujte její obsah.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* je vaše pracovní složka pro výukový program.
    - *konečný* výsledek a úplná implementace pro váš odkaz.
    - *obsahuje* model strojového učení a pomocné knihovny.
    - *frontend* je webová stránka, která volá aplikaci funkce.

## <a name="create-and-activate-a-python-virtual-environment"></a>Vytvoření a aktivace virtuálního prostředí Pythonu

Přejděte do *počáteční* složky a spusťte následující příkazy a vytvořte a aktivujte virtuální prostředí s názvem `.venv`.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Pokud Python nenainstaloval balíček venv do vaší distribuce Linuxu, spusťte následující příkaz:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

Spuštění všech následných příkazů v tomto aktivovaném virtuálním prostředí. (Chcete-li virtuální prostředí `deactivate`ukončit, spusťte .)


## <a name="create-a-local-functions-project"></a>Vytvoření projektu místních funkcí

V Azure Functions je projekt funkce kontejner pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní aktivační událost. Všechny funkce v projektu sdílejí stejné místní a hostitelské konfigurace. V této části vytvoříte projekt funkce, který obsahuje `classify` jednu často používaný funkci s názvem, který poskytuje koncový bod HTTP. Konkrétnější kód přidáte v pozdější části.

1. Ve složce *Start* použijte základní nástroje Azure Functions k inicializaci aplikace funkcí Pythonu:

    ```
    func init --worker-runtime python
    ```

    Po inicializaci obsahuje *počáteční* složka různé soubory pro projekt, včetně konfiguračních souborů s názvem [local.settings.json](functions-run-local.md#local-settings-file) a [host.json](functions-host-json.md). Vzhledem k tomu, *že local.settings.json* může obsahovat tajné klíče stažené z Azure, je soubor ve výchozím nastavení vyloučen ze správy zdrojového kódu v souboru *.gitignore.*

    > [!TIP]
    > Vzhledem k tomu, že projekt funkce je vázána na konkrétní runtime, všechny funkce v projektu musí být zapsány ve stejném jazyce.

1. Přidejte funkci do projektu pomocí následujícího `--name` příkazu, kde argument je `--template` jedinečný název funkce a argument určuje aktivační událost funkce. `func new`Vytvořte podsložku odpovídající názvu funkce, která obsahuje soubor kódu odpovídající zvolenému jazyku projektu a konfigurační soubor s názvem *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Tento příkaz vytvoří složku odpovídající názvu funkce, *klasifikovat*. V této složce jsou dva soubory: * \_ \_init\_\_.py*, který obsahuje kód funkce a *function.json*, který popisuje aktivační událost funkce a její vstupní a výstupní vazby. Podrobnosti o obsahu těchto souborů naleznete v [tématu Zkontrolujte obsah souboru](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) v pythonu rychlý start.


## <a name="run-the-function-locally"></a>Místní spuštění funkce

1. Spusťte funkci spuštěním místního hostitele runtime Azure Functions ve složce *start:*

    ```
    func start
    ```

1. Jakmile se `classify` koncový bod zobrazí ve výstupu, přejděte na adresu URL . ```http://localhost:7071/api/classify?name=Azure``` Zpráva "Hello Azure!" by se měl objevit ve výstupu.

1. K zastavení hostitele použijte **kombinaci kláves**-**C.**


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Import modelu PyTorch a přidání pomocového kódu

Chcete-li `classify` upravit funkci pro klasifikaci obrazu na základě jeho obsahu, použijte předem trénovaný model [ResNet.](https://arxiv.org/abs/1512.03385) Předem vycvičený model, který pochází z [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), klasifikuje obraz do 1 z 1000 [tříd ImageNet](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Potom přidáte některé pomocné kód a závislosti do projektu.

1. Ve složce *Start* spusťte následující příkaz a zkopírujte kód předpovědi a popisky do složky *klasifikovat.*

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Ověřte, zda složka *classifikace* obsahuje soubory s názvem *predict.py* a *labels.txt*. Pokud ne, zkontrolujte, zda jste příkaz spustili ve složce *Start.*

1. Otevřete *soubor start/requirements.txt* v textovém editoru a přidejte závislosti vyžadované kódem pomocníka, které by měly vypadat takto:

    ```txt
    azure-functions
    requests
    numpy==1.15.4
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-win_amd64.whl; sys_platform == 'win32' and python_version == '3.8'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.8'
    torchvision==0.5.0
    ```

1. Uložit *soubor requirements.txt*a potom spustit následující příkaz ze složky *Start* a nainstalovat závislosti.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

Instalace může trvat několik minut, během které můžete pokračovat v úpravě funkce v další části.
> [!TIP]
> >V systému Windows se může vyskytnat chyba "Nelze nainstalovat balíčky z důvodu EnvironmentError: [Errno 2] Žádný takový soubor nebo adresář:" následovaný dlouhým názvem cesty k souboru, jako *je sharded_mutable_dense_hashtable.cpython-37.pyc*. Obvykle k této chybě dochází, protože hloubka cesty ke složce se stane příliš dlouhá. V takovém případě nastavte `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` klíč `1` registru tak, aby povoluje dlouhé cesty. Případně zkontrolujte, kde je nainstalován váš překladač Pythonu. Pokud má toto umístění dlouhou cestu, zkuste přeinstalovat ve složce s kratší cestou.

## <a name="update-the-function-to-run-predictions"></a>Aktualizace funkce pro spuštění předpovědí

1. Otevřete *\_\_klasifikovat/ init\_\_.py* v textovém `import` editoru a přidejte následující řádky za existující příkazy pro import standardní knihovny JSON a *pomocníků predict:*

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Nahraďte celý `main` obsah funkce následujícím kódem:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Tato funkce obdrží adresu URL obrázku `img`v parametru řetězce dotazu s názvem . Potom volá `predict_image_from_url` z pomocné knihovny ke stažení a klasifikaci obrazu pomocí modelu PyTorch. Funkce pak vrátí odpověď HTTP s výsledky.

    > [!IMPORTANT]
    > Vzhledem k tomu, že tento koncový bod HTTP je volána webovou stránkou hostovanou v jiné doméně, obsahuje odpověď `Access-Control-Allow-Origin` záhlaví, které splňuje požadavky prohlížeče na sdílení prostředků mezi zdroji (CORS).
    >
    > V produkční aplikaci `*` změňte na konkrétní původ webové stránky pro zvýšení zabezpečení.

1. Uložte změny a za předpokladu, že se závislosti dokončily `func start`instalaci, spusťte hostitele místní funkce znovu pomocí aplikace . Nezapomeňte spustit hostitele ve složce *start* s aktivovaným virtuálním prostředím. V opačném případě se spustí hostitel, ale při vyvolání funkce se zobrazí chyby.

    ```
    func start
    ```

1. V prohlížeči otevřete následující adresu URL, abyste vyvolali funkci s adresou URL obrázku bernského horského psa a potvrďte, že vrácený JSON klasifikuje obrázek jako bernského horského psa.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Udržujte hostitele spuštěný, protože jej použijete v dalším kroku.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Spuštěním front-endu místní webové aplikace otestujte funkci

Chcete-li otestovat vyvolání koncového bodu funkce z jiné webové aplikace, je ve složce *front-end* úložiště jednoduchá aplikace.

1. Otevřete nový terminál nebo příkazový řádek a aktivujte virtuální prostředí (jak je popsáno výše v části [Vytvoření a aktivace virtuálního prostředí Pythonu).](#create-and-activate-a-python-virtual-environment)

1. Přejděte do *složky front-end* úložiště.

1. Spuštění http serveru s Pythonem:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. V prohlížeči přejděte na , `localhost:8000`zadejte do textového pole jednu z následujících fotografických adres URL nebo použijte adresu URL libovolného veřejně přístupného obrázku.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Vyberte **Odeslat,** chcete-li vyvolat koncový bod funkce pro klasifikaci obrazu.

    ![Snímek obrazovky s dokončeným projektem](media/machine-learning-pytorch/screenshot.png)

    Pokud prohlížeč při odeslání adresy URL obrázku nahlásí chybu, zkontrolujte terminál, ve kterém aplikaci funkce používáte. Pokud se zobrazí chyba jako "Žádný modul nebyl nalezen 'PIL'", možná jste spustili aplikaci funkce ve složce *start* bez předchozí aktivace virtuálního prostředí, které jste vytvořili dříve. Pokud se stále zobrazují `pip install -r requirements.txt` chyby, spusťte znovu s aktivovaným virtuálním prostředím a vyhledejte chyby.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vzhledem k tomu, že celý tento kurz běží místně na vašem počítači, neexistují žádné prostředky Azure nebo služby k vyčištění.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit a přizpůsobit koncový bod rozhraní HTTP API pomocí funkce Azure pro klasifikaci iobrazek pomocí modelu PyTorch. Také jste se naučili volat rozhraní API z webové aplikace. Pomocí technik v tomto kurzu můžete vytvořit všechna složitost, to vše při spuštění na výpočetním modelu bez serveru poskytovaném funkcemi Azure.

Viz také:

- [Nasazení funkce do Azure pomocí kódu Visual Studia](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Průvodce vývojářem Pythonu s funkcemi Azure](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Nasazení funkce do funkcí Azure pomocí průvodce příkazovým příkazem Azure](./functions-run-local.md#publish)

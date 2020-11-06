---
title: Nasazení modelu PyTorch jako aplikace Azure Functions
description: Použijte předem proučenou ResNet 18 neuronové síť od PyTorch a Azure Functions, abyste k obrázku přiřadili 1 z 1000 ImageNet popisků.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 8891c29e5d8d06df6292d06ec06e5e57fb9880e7
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422837"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Kurz: nasazení předem připraveného modelu klasifikace imagí pro Azure Functions s využitím PyTorch

V tomto článku se naučíte, jak pomocí Pythonu, PyTorch a Azure Functions načíst předučený model pro klasifikaci image na základě jejího obsahu. Vzhledem k tomu, že veškerou práci lokálně provedete místně a v cloudu nevytvoříte žádné prostředky Azure, nemusíte tento kurz dokončit.

> [!div class="checklist"]
> * Inicializujte místní prostředí pro vývoj Azure Functions v Pythonu.
> * Naimportujte předem trained PyTorch model strojového učení do aplikace Function App.
> * Sestavte rozhraní HTTP API bez serveru pro klasifikaci obrázku jako jednu z 1000 [tříd](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)ImageNet.
> * Využívání rozhraní API z webové aplikace

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 nebo novější](https://www.python.org/downloads/release/python-374/). (Pomocí Azure Functions se ověřují taky Python 3.8. x a Python 3.6. x.)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Editor kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

1. V terminálu nebo příkazovém okně spusťte příkaz `func --version` a ověřte, zda Azure Functions Core Tools verze 2.7.1846 nebo novější.
1. Spusťte `python --version` (Linux/MacOS) nebo `py --version` (Windows), abyste zkontrolovali, jestli verze Pythonu nahlásí verzi 3.7. x.

## <a name="clone-the-tutorial-repository"></a>Naklonujte úložiště kurzu.

1. V terminálu nebo příkazovém okně naklonujte následující úložiště pomocí Gitu:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Přejděte do složky a prověřte její obsah.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *Začínáme* je pracovní složka pro kurz.
    - *End* je konečný výsledek a plná implementace pro váš odkaz.
    - *prostředky* obsahují model strojového učení a pomocné knihovny.
    - *front-end* je web, který volá aplikaci Function App.

## <a name="create-and-activate-a-python-virtual-environment"></a>Vytvoření a aktivace virtuálního prostředí Pythonu

Přejděte do složky *Start* a spuštěním následujících příkazů vytvořte a aktivujte virtuální prostředí s názvem `.venv` .


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Pokud Python nenainstaloval balíček venv do distribuce systému Linux, spusťte následující příkaz:

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

V tomto aktivovaném virtuálním prostředí spustíte všechny následné příkazy. (Chcete-li virtuální prostředí ukončit, spusťte příkaz `deactivate` .)


## <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

V Azure Functions je projekt funkce kontejnerem pro jednu nebo více jednotlivých funkcí, které každý reaguje na konkrétní Trigger. Všechny funkce v projektu sdílejí stejné místní konfigurace a konfigurace hostování. V této části vytvoříte projekt funkce, který obsahuje jednu standardní funkci s názvem `classify` , která poskytuje koncový bod HTTP. Další konkrétní kód přidáte v pozdější části.

1. Ve složce *Start* použijte Azure Functions Core Tools k inicializaci aplikace funkcí Pythonu:

    ```
    func init --worker-runtime python
    ```

    Po inicializaci obsahuje složka *Start* různé soubory pro projekt, včetně konfiguračních souborů s názvem [local.settings.jsv](functions-run-local.md#local-settings-file) a [host.jsna](functions-host-json.md). Vzhledem k tomu, že *local.settings.json* může obsahovat tajné kódy stažené z Azure, soubor je ve výchozím nastavení vyloučený ze správy zdrojového kódu v souboru *. gitignore* .

    > [!TIP]
    > Vzhledem k tomu, že projekt funkce je svázán s konkrétním modulem runtime, všechny funkce v projektu musí být zapsány stejným jazykem.

1. Do projektu přidejte funkci pomocí následujícího příkazu, kde `--name` argument je jedinečný název vaší funkce a `--template` argument určuje Trigger funkce. `func new` Vytvořte podsložku odpovídající názvu funkce, která obsahuje soubor kódu, který je vhodný pro zvolený jazyk projektu, a konfigurační soubor s názvem *function.jsv*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Tento příkaz vytvoří složku, která odpovídá názvu funkce, *klasifikovat*. V této složce jsou dva soubory: *\_ \_ init \_ \_ . py* , který obsahuje kód funkce a *function.jsv* , které popisují Trigger funkce a její vstupní a výstupní vazby. Podrobnosti o obsahu těchto souborů najdete v tématu [prohlédnutí obsahu souboru](./create-first-function-cli-python.md#optional-examine-the-file-contents) v rychlém startu Pythonu.


## <a name="run-the-function-locally"></a>Místní spuštění funkce

1. Spusťte funkci spuštěním místního hostitele modulu runtime Azure Functions ve složce *Start* :

    ```
    func start
    ```

1. Jakmile se `classify` ve výstupu zobrazí koncový bod, přejděte na adresu URL, ```http://localhost:7071/api/classify?name=Azure``` . Zpráva "Hello Azure!" měl by se zobrazit ve výstupu.

1. K zastavení hostitele použijte **kombinaci kláves CTRL +** - **C** .


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Import modelu PyTorch a přidání kódu nápovědy

Chcete-li upravit `classify` funkci pro klasifikaci obrázku na základě jeho obsahu, použijte předem připravený [ResNet](https://arxiv.org/abs/1512.03385) model. Předučený model, který pochází z [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), klasifikuje obrázek na 1 z 1000 [tříd ImageNet](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Do projektu pak přidáte nějaký pomocný kód a závislosti.

1. Ve složce *Start* spusťte následující příkaz, který zkopíruje kód předpovědi a popisky do složky *klasifikovat* .

    # <a name="bash"></a>[bash](#tab/bash)

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

1. Ověřte, zda složka *klasifikovat* obsahuje soubory s názvem *PREDICT.py* a *labels.txt*. Pokud ne, ověřte, že jste spustili příkaz ve složce *Start* .

1. V textovém editoru otevřete *Start/requirements.txt* a přidejte závislosti vyžadované kódem pomocníka, který by měl vypadat takto:

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. Uložte *requirements.txt* a potom spuštěním následujícího příkazu z *úvodní* složky nainstalujte závislosti.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

Instalace může trvat několik minut. během této doby můžete pokračovat v úpravách funkce v další části.
> [!TIP]
> >V systému Windows se může zobrazit chyba, "nelze instalovat balíčky z důvodu EnvironmentError: [errno 2] žádný takový soubor nebo adresář": "následovaný dlouhým názvem cesty k souboru, například *sharded_mutable_dense_hashtable. CPython-37. PYC*. K této chybě obvykle dochází, protože hloubka cesty ke složce se zastává příliš dlouho. V takovém případě nastavte klíč registru tak, aby `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` `1` umožňoval dlouhé cesty. Alternativně ověřte, kde je váš překladač Pythonu nainstalovaný. Pokud má toto umístění dlouhou cestu, zkuste přeinstalovat ve složce s kratší cestou.

## <a name="update-the-function-to-run-predictions"></a>Aktualizace funkce pro spuštění předpovědi

1. V textovém editoru otevřete *klasifikovat/ \_ \_ init \_ \_ . py* a přidejte následující řádky za existující `import` příkazy pro import standardní knihovny JSON a sady pomocníků pro *předpověď* :

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Celý obsah funkce nahraďte `main` následujícím kódem:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Tato funkce obdrží adresu URL obrázku v parametru řetězce dotazu s názvem `img` . Pak volá `predict_image_from_url` z pomocné knihovny ke stažení a klasifikaci Image pomocí modelu PyTorch. Funkce pak vrátí odpověď HTTP s výsledky.

    > [!IMPORTANT]
    > Vzhledem k tomu, že je tento koncový bod HTTP volán webovou stránkou, která je hostována v jiné doméně, odpověď obsahuje `Access-Control-Allow-Origin` hlavičku pro splnění požadavků na sdílení prostředků mezi zdroji (CORS) v prohlížeči.
    >
    > V produkční aplikaci přejděte `*` na konkrétní zdroj webové stránky pro zvýšení zabezpečení.

1. Uložte změny a pak za předpokladu, že se dokončí instalace závislostí, spusťte znovu hostitele místní funkce `func start` . Nezapomeňte spustit hostitele ve složce *Start* s aktivovaným virtuálním prostředím. V opačném případě se hostitel spustí, ale při volání funkce se zobrazí chyby.

    ```
    func start
    ```

1. V prohlížeči otevřete následující adresu URL, která vyvolá funkci s adresou URL obrázku Bernese horského psa a potvrdí, že vrácená JSON klasifikuje obrázek jako Bernese horského psa.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Nechejte hostitele spuštěný, protože ho použijete v dalším kroku.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Spustí front-end místní webové aplikace a otestuje funkci.

Pokud chcete otestovat vyvolání koncového bodu funkce z jiné webové aplikace, je ve složce *front-endu* v úložišti jednoduchá aplikace.

1. Otevřete nový terminál nebo příkazový řádek a aktivujte virtuální prostředí (jak je popsáno výše v části [Vytvoření a aktivace virtuálního prostředí Pythonu](#create-and-activate-a-python-virtual-environment)).

1. Přejděte do složky front- *Endu* úložiště.

1. Spustit HTTP server s Pythonem:

    # <a name="bash"></a>[bash](#tab/bash)

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

1. V prohlížeči přejděte na adresu `localhost:8000` a potom do textového pole zadejte jednu z následujících adres URL fotek nebo použijte adresu URL jakékoli veřejně přístupné image.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Vyberte **Odeslat** pro vyvolání koncového bodu funkce pro klasifikaci obrázku.

    ![Snímek obrazovky dokončeného projektu](media/machine-learning-pytorch/screenshot.png)

    Pokud prohlížeč ohlásí chybu při odeslání adresy URL obrázku, podívejte se do terminálu, ve kterém je spuštěná aplikace Function App. Pokud se zobrazí chyba, například "nebyl nalezen žádný modul" PIL ", možná jste spustili aplikaci Function App ve složce *Start* , aniž byste nejdřív aktivovali virtuální prostředí, které jste vytvořili dříve. Pokud se stále zobrazují chyby, spusťte `pip install -r requirements.txt` znovu s aktivovaným virtuálním prostředím a vyhledejte chyby.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vzhledem k tomu, že se celá část tohoto kurzu spouští místně na vašem počítači, neexistují žádné prostředky nebo služby Azure, které by bylo potřeba vyčistit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit a přizpůsobit koncový bod rozhraní HTTP API pomocí Azure Functions pro klasifikaci imagí pomocí modelu PyTorch. Zjistili jste také, jak volat rozhraní API z webové aplikace. Techniky v tomto kurzu můžete použít k sestavení rozhraní API jakékoli složitosti, která se spustí na výpočetním modelu bez serveru, který poskytuje Azure Functions.

Viz také:

- [Nasaďte funkci do Azure pomocí Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Příručka pro vývojáře Azure Functions Pythonu](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Nasazení funkce pro Azure Functions pomocí Průvodce Azure CLI](./functions-run-local.md#publish)

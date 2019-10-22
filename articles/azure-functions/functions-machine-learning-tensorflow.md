---
title: 'Kurz: použití Pythonu a TensorFlow v Azure Functions k vytvoření odvození strojového učení | Microsoft Docs'
description: Tento kurz ukazuje, jak použít modely strojového učení TensorFlow v Azure Functions
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e243fd2f5c4a90e45f424ce39a97913df2332b2b
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677894"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Kurz: použití modelů strojového učení v Azure Functions pomocí Pythonu a TensorFlow

Tento článek ukazuje, jak Azure Functions umožňuje používat Python a TensorFlow s modelem strojového učení ke klasifikaci image na základě jejího obsahu.

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Inicializace místního prostředí pro vývoj Azure Functions v Pythonu
> * Import vlastního modelu Machine Learning ve TensorFlow do aplikace Function App
> * Sestavte rozhraní HTTP API bez serveru pro předpověď, jestli fotka obsahuje pes nebo kočka.
> * Využívání rozhraní API z webové aplikace

![Snímek obrazovky dokončeného projektu](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady 

Pokud chcete vytvořit Azure Functions v Pythonu, musíte nainstalovat několik nástrojů.

- [Python 3,6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Editor kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>Naklonujte úložiště kurzu.

Začněte tím, že otevřete terminál a naklonete následující úložiště pomocí Gitu:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

Úložiště obsahuje několik složek.

- *Začínáme*: Toto je pracovní složka pro kurz.
- *konec*: Toto je konečný výsledek a plná implementace pro váš odkaz.
- *prostředky*: obsahuje model strojového učení a pomocné knihovny.
- *front-end*: web, který volá aplikaci Function App

## <a name="create-and-activate-a-python-virtual-environment"></a>Vytvoření a aktivace virtuálního prostředí Pythonu

Azure Functions vyžaduje Python 3.6. x. Vytvoříte virtuální prostředí, abyste měli jistotu, že používáte požadovanou verzi Pythonu.

Změňte aktuální pracovní adresář na složku *Start* . Pak vytvořte a aktivujte virtuální prostředí s názvem *. venv*. V závislosti na instalaci Pythonu se můžou příkazy pro vytvoření virtuálního prostředí Python 3,6 lišit od následujících pokynů.

#### <a name="linux-and-macos"></a>Linux a macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

Výzva k zadání terminálu je nyní předpona `(.venv)`, která indikuje, že jste úspěšně aktivovali virtuální prostředí. Potvrďte, že `python` ve virtuálním prostředí jsou ve skutečnosti Python 3.6. x.

```console
python --version
```

> [!NOTE]
> Ve zbývající části kurzu spustíte ve virtuálním prostředí příkazy. Pokud potřebujete znovu aktivovat virtuální prostředí v terminálu, spusťte příslušný příkaz k aktivaci pro váš operační systém.

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

Ve složce *Start* použijte Azure Functions Core Tools k inicializaci aplikace funkcí Pythonu.

```console
func init --worker-runtime python
```

Aplikace Function App může obsahovat jeden nebo více Azure Functions. Otevřete složku *Start* v editoru a prověřte obsah.

- [*Local. Settings. JSON*](functions-run-local.md#local-settings-file): obsahuje nastavení aplikace používané pro místní vývoj.
- [*Host. JSON*](functions-host-json.md): obsahuje nastavení pro hostitele Azure functions a rozšíření.
- [*požadavky. txt*](functions-reference-python.md#python-version-and-package-management): obsahuje balíčky Pythonu, které vyžaduje tato aplikace.

## <a name="create-an-http-function"></a>Vytvoření funkce HTTP

Aplikace vyžaduje jeden koncový bod HTTP API, který jako vstup převezme adresu URL obrázku, a vrátí předpověď, zda obrázek obsahuje pes nebo kočka.

V terminálu použijte Azure Functions Core Tools k vytvoření nové funkce HTTP s názvem *klasifikovat*.

```console
func new --language python --template HttpTrigger --name classify
```

Vytvoří se nová složka s názvem *klasifikovat* , která obsahuje dva soubory.

- *\_ \_init \_ \_. py*: soubor pro funkci main
- *Function. JSON*: soubor popisující aktivační událost funkce a její vstupní a výstupní vazby

### <a name="run-the-function"></a>Spuštění funkce

V terminálu s aktivovaným virtuálním prostředím Python spusťte aplikaci Function App.

```console
func start
```

Otevřete prohlížeč a přejděte na následující adresu URL. Funkce by měla být spuštěná a vracet *Hello Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

K zastavení aplikace Function App použijte `Ctrl-C`.

## <a name="import-the-tensorflow-model"></a>Import modelu TensorFlow

Použijete předem sestavený TensorFlow model, který byl vyškolen a exportován z Azure Custom Vision Service.

> [!NOTE]
> Pokud chcete sestavit vlastní s využitím bezplatné úrovně Custom Vision Service, můžete postupovat podle [pokynů v tématu úložiště projektu Ukázka](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

Model se skládá ze dvou souborů ve složce *< REPOSITORY_ROOT >/Resources/model* : *model. pb* a *labels. txt*. Zkopírujte je do složky *klasifikovat* funkci.

#### <a name="linux-and-macos"></a>Linux a macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Nezapomeňte zahrnout \* do výše uvedeného příkazu. Potvrďte, že *klasifikace* nyní obsahuje soubory s názvem *model. pb* a *labels. txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>Přidání pomocných funkcí a závislostí

Některé pomocné funkce pro přípravu vstupní image a předpovědi s použitím TensorFlow jsou v souboru s názvem *PREDICT.py* ve složce *Resources* . Zkopírujte tento soubor do složky *klasifikovat* funkci.

#### <a name="linux-and-macos"></a>Linux a macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

Potvrďte, že *klasifikace* nyní obsahuje soubor s názvem *PREDICT.py*.

### <a name="install-dependencies"></a>Instalace závislostí

Pomocná knihovna obsahuje některé závislosti, které je potřeba nainstalovat. V editoru otevřete soubor *Start/požadavky. txt* a přidejte do něj následující závislosti.

```txt
tensorflow==1.15
Pillow
requests
```

Uložte soubor.

V terminálu s aktivovaným virtuálním prostředím spusťte následující příkaz pro instalaci závislostí ve složce *Start* . Dokončení některých kroků instalace může trvat několik minut.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Ukládání modelu do mezipaměti v globálních proměnných

V editoru otevřete *PREDICT.py* a podívejte se na funkci `_initialize` v horní části souboru. Všimněte si, že model TensorFlow je načten z disku při prvním spuštění funkce a uložení do globálních proměnných. Načítání z disku se při dalších spuštěních funkce `_initialize` přeskočí. Ukládání modelu do mezipaměti pomocí této techniky zrychlí pozdější předpovědi.

Další informace o globálních proměnných najdete v [příručce pro vývojáře Azure Functions Pythonu](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Funkce Update pro spuštění předpovědi

V editoru otevřete *\_ \_init \_ \_. py* . Importujte knihovnu *předpověď* , kterou jste přidali do stejné složky dříve. Následující příkazy `import` přidejte pod ostatní importy, které jsou již v souboru.

```python
import json
from .predict import predict_image_from_url
```

Kód šablony funkce nahraďte následujícím kódem.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Ujistěte se, že jste změny uložili.

Tato funkce obdrží adresu URL obrázku v parametru řetězce dotazu s názvem `img`. Volá `predict_image_from_url` z pomocné knihovny, která stáhne image a vrátí předpověď pomocí modelu TensorFlow. Funkce pak vrátí odpověď HTTP s výsledky.

Vzhledem k tomu, že je koncový bod HTTP volán webovou stránkou, která je hostována v jiné doméně, odpověď protokolu HTTP zahrnuje hlavičku `Access-Control-Allow-Origin` pro splnění požadavků na sdílení prostředků mezi zdroji (CORS) v prohlížeči.

> [!NOTE]
> V produkční aplikaci změňte `*` na specifický zdroj webové stránky pro zvýšení zabezpečení.

### <a name="run-the-function-app"></a>Spuštění aplikace Function App

Ujistěte se, že virtuální prostředí Pythonu je pořád aktivované, a spusťte aplikaci Function App pomocí následujícího příkazu.

```console
func start
```

V prohlížeči otevřete tuto adresu URL, která volá vaši funkci s adresou URL fotky Cat. Potvrďte, že je vrácen platný výsledek předpovědi.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Nechte aplikaci Function App spuštěnou.

### <a name="run-the-web-app"></a>Spuštění webové aplikace

Ve složce *front-endu* je jednoduchá webová aplikace, která využívá rozhraní API http ve Function App.

Otevřete *samostatný* terminál a přejděte do složky *front-endu* . Spusťte HTTP server s Pythonem 3,6.

#### <a name="linux-and-macos"></a>Linux a macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

V prohlížeči přejděte na adresu URL serveru HTTP, která se zobrazí v terminálu. Měla by se zobrazit webová aplikace. Do textového pole zadejte jednu z následujících adres URL fotek. Můžete použít také adresu URL veřejně přístupné fotografie Cat nebo pes.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Když kliknete na Odeslat, aplikace Function App se zavolá a na stránce se zobrazí výsledek.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Celý tento kurz běží místně na vašem počítači, takže neexistují žádné prostředky nebo služby Azure, které by bylo potřeba vyčistit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit a přizpůsobit rozhraní API HTTP pomocí Azure Functions předpovědi pomocí modelu TensorFlow. Zjistili jste také, jak volat rozhraní API z webové aplikace.

Techniky v tomto kurzu můžete použít k sestavení rozhraní API jakékoli složitosti, která se spustí na výpočetním modelu bez serveru, který poskytuje Azure Functions.

K nasazení aplikace Function App do Azure použijte [Azure Functions Core Tools](./functions-run-local.md#publish) nebo [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Příručka pro vývojáře Azure Functions Pythonu](./functions-reference-python.md)

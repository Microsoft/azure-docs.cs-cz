---
title: Vytvoření a nasazení Azure Functions v Pythonu s Visual Studio Code
description: Jak používat rozšíření Visual Studio Code pro Azure Functions k vytváření funkcí bez serveru v Pythonu a jejich nasazení do Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 587d8040039080e15c61292279c5e5637d296b08
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68351347"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Nasazení Pythonu pro Azure Functions s využitím Visual Studio Code

V tomto kurzu použijete Visual Studio Code a rozšíření Azure Functions k vytvoření koncového bodu HTTP bez serveru pomocí Pythonu a také k přidání připojení (neboli "vazby") do úložiště. Azure Functions spouští váš kód v prostředí bez serveru, aniž byste museli zřizovat virtuální počítač nebo publikovat webovou aplikaci. Rozšíření Azure Functions pro Visual Studio Code významně zjednodušuje proces používání funkcí automatickým zpracováním mnoha otázek týkajících se konfigurace.

Pokud narazíte na problémy s některým z kroků v tomto kurzu, máme rádi vědět o podrobnostech. K odeslání podrobné zpětné vazby použijte na konci každého oddílu tlačítko **jsem narazili na problém** .

## <a name="prerequisites"></a>Požadavky

- [Předplatného Azure](#azure-subscription).
- [Visual Studio Code s](#visual-studio-code-python-and-the-azure-functions-extension) rozšířením Azure Functions.
- [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Předplatné Azure

Pokud nemáte předplatné Azure, zaregistrujte si [nyní](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) bezplatný 30denní účet s $200 v kreditech Azure, abyste si vyzkoušeli libovolnou kombinaci služeb.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python a rozšíření Azure Functions

Nainstalujte následující software:

- Python 3.6. x podle požadavků Azure Functions. [Python 3.6.8](https://www.python.org/downloads/release/python-368/) je nejnovější verze 3.6. x.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python) popsané v [kurzu Visual Studio Code Pythonu – předpoklady](https://code.visualstudio.com/docs/python/python-tutorial).
- [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Obecné informace najdete v [úložišti GitHub VSCode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Při [práci s Azure Functions Core Tools](functions-run-local.md#v2)postupujte podle pokynů pro váš operační systém. Samotné nástroje jsou napsané v .NET Core a balíček Core Tools se nejlépe nainstaluje pomocí Správce balíčků Node. js, NPM, což je důvod, proč je potřeba nainstalovat .NET Core a Node. js v současnosti i v kódu Pythonu. Můžete ale obejít požadavek .NET Core pomocí "sady rozšíření", jak je popsáno v uvedené dokumentaci. Bez ohledu na to, že je potřeba nainstalovat tyto součásti, stačí, když Visual Studio Code automaticky vyzve k instalaci jakýchkoli aktualizací.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Jakmile se rozšíření Functions nainstaluje, přihlaste se k účtu Azure tak, že **přejdete na Azure: Průzkumník** funkcí, vyberte **Přihlásit se k Azure**a postupujte podle pokynů.

![Přihlaste se k Azure prostřednictvím Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

Po přihlášení ověřte, že se e-mailový účet vašeho předplatného Azure zobrazuje na stavovém řádku:

![Visual Studio Code stavového řádku ukazující účet Azure](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

Název, který jste přiřadili k vašemu předplatnému, **se zobrazí také v Azure: Průzkumník** funkcí ("primární") na obrázku níže:

![Visual Studio Code Azure App Service Explorer zobrazující odběry](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Pokud dojde k chybě **"nelze najít předplatné s názvem [ID předplatného]"** , může to být způsobeno tím, že jste za proxy a nemůžete získat přístup k rozhraní Azure API. Konfigurace `HTTP_PROXY` a`HTTPS_PROXY` proměnné prostředí s použitím informací o proxy serveru v terminálu:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Ověření požadavků

Chcete-li ověřit, zda jsou nainstalovány všechny nástroje Azure Functions, otevřete paletu příkazů Visual Studio Code (F1), **vyberte terminál: Vytvořte nový integrovaný příkaz** terminálu a po otevření terminálu spusťte příkaz: `func`

![Kontrola požadovaných součástí Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Výstup, který začíná logem Azure Functions (je třeba posunout výstup nahoru) znamená, že Azure Functions Core Tools k dispozici.

`func` Pokud příkaz není rozpoznán, ověřte, zda je složka, do které jste Azure Functions Core Tools nainstalovali, obsažena v proměnné prostředí PATH.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>Vytvoření funkce

1. Kód pro Azure Functions je spravován v rámci _projektu_Functions, který je třeba vytvořit před vytvořením kódu. V **Azure: Průzkumník** funkcí (otevřený pomocí ikony Azure na levé straně), vyberte ikonu příkazu **Nový projekt** nebo otevřete paletu příkazů (F1) a vyberte **Azure Functions: Vytvořte nový projekt**.

    ![Tlačítko vytvořit nový projekt v Průzkumníkovi funkcí](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. V následujících dotazech:

    | Výzva | Hodnota | Popis | 
    | --- | --- | --- |
    | Zadejte složku pro projekt | Aktuální otevřená složka | Složka, ve které se má projekt vytvořit. Můžete chtít vytvořit projekt v podsložce. |
    | Vyberte jazyk pro projekt Function App. | **Python** | Jazyk, který má být použit pro funkci, která určuje šablonu použitou pro kód. |
    | Vyberte šablonu pro funkci prvního projektu. | **Aktivační událost HTTP** | Funkce, která používá Trigger HTTP, se spustí pokaždé, když se na koncový bod funkce navede požadavek HTTP. (K dispozici jsou různé triggery Azure Functions. Další informace najdete v tématu [co se dá dělat s funkcemi?](functions-overview.md#what-can-i-do-with-functions).) |
    | Zadejte název funkce. | HttpExample | Název se používá pro podsložku, která obsahuje kód funkce spolu s konfiguračními daty, a definuje také název koncového bodu HTTP. Místo toho, abyste přijali výchozí hodnotu "HTTPTrigger", použijte "HttpExample", abyste rozlišili funkci přímo od triggeru. |
    | Úroveň autorizace | **Anonymous** | Anonymní autorizace umožňuje všem uživatelům přístup k této funkci. |
    | Vyberte, jak se má projekt otevřít. | **Otevřít v aktuálním okně** | Otevře projekt v aktuálním Visual Studio Codem okně. |

1. Po krátké době zpráva, která indikuje, že byl vytvořen nový projekt. V **Průzkumníkovi**je pro funkci vytvořena podsložka a Visual Studio Code otevře  *\_ \_soubor\_init\_. py* , který obsahuje výchozí kód funkce:

    [![Výsledek vytvoření nového projektu funkcí Pythonu](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Pokud vám Visual Studio Code upozorní, že při **otevření  *\_ \_příkazu\_init\_. py*nemáte vybraný překladač Pythonu, otevřete paletu příkazů (F1), vyberte Python: Vyberte** příkaz interpret a potom vyberte virtuální prostředí v místní `.env` složce (která byla vytvořena jako součást projektu). Prostředí musí být založené na Python 3.6 x konkrétně, jak bylo uvedeno dříve v části [požadavky](#prerequisites).
    >
    > ![Výběr virtuálního prostředí vytvořeného pomocí projektu](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> Kdykoli budete chtít vytvořit další funkci ve stejném projektu, použijte příkaz **Create Function** v **Azure: V** Průzkumníku funkcí nebo otevřete paletu příkazů (F1) a **vyberte Azure Functions: Příkaz Create** Function V obou příkazech se zobrazí výzva k zadání názvu funkce (což je název koncového bodu) a pak se vytvoří podsložka s výchozími soubory.
>
> ![Nový příkaz funkce v Azure: Průzkumník funkcí](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>Prověřte soubory kódu

V nově vytvořené podsložce funkce jsou tři soubory:  *\_ \_\_init\_. py* obsahuje kód funkce, *Function. JSON* , který popisuje funkci Azure functions a *Sample. dat* je ukázkový datový soubor. Pokud chcete, můžete soubor *Sample. dat* odstranit, protože existuje jenom k tomu, aby bylo možné přidat další soubory do podsložky.

Podívejme se nejprve na *Function. JSON* a pak na kód v  *\_ \_init\_\_. py*.

### <a name="functionjson"></a>function.json

Soubor Function. JSON poskytuje potřebné informace o konfiguraci pro Azure Functions koncový bod:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Vlastnost identifikuje spouštěcí soubor pro kód a tento kód musí obsahovat funkci Pythonu s názvem `main`. `scriptFile` Svůj kód můžete vyhodnotit do více souborů, pokud zde zadaný soubor obsahuje `main` funkci.

`bindings` Element obsahuje dva objekty, jeden pro popis příchozích požadavků a druhý pro popis odpovědi HTTP. U příchozích požadavků (`"direction": "in"`) funkce reaguje na požadavky HTTP GET nebo post a nevyžaduje ověřování. Odpověď (`"direction": "out"`) je odpověď HTTP, která vrací jakoukoli hodnotu `main` z funkce Pythonu.

### <a name="initpy"></a>\_\_init.py\_\_

Při vytváření nové funkce Azure Functions poskytuje výchozí kód Pythonu v  *\_ \_\_init\_. py*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Důležité části kódu jsou následující:

- Musíte importovat `func` z `azure.functions`: importující modul protokolování je volitelný, ale doporučený.
- Požadovaná `main` funkce Pythonu `func.request` obdrží objekt s názvem `req`a vrátí hodnotu typu `func.HttpResponse`. Další informace o možnostech těchto objektů najdete ve [Func. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) a [Func. ](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python)Odkazy na HttpResponse
- Tělo `main` pak zpracuje požadavek a vygeneruje odpověď. V tomto případě kód vyhledá `name` parametr v adrese URL. V `func.HttpRequest.get_json`takovém případě kontroluje, zda tělo požadavku obsahuje JSON (using) a zda kód JSON `name` obsahuje hodnotu (pomocí `get` metody objektu JSON vráceného funkcí `get_json`).
- Pokud je název nalezen, vrátí kód řetězec "Hello" se doplněným názvem; v opačném případě vrátí chybovou zprávu.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Místní ladění

1. Při vytváření projektu Functions rozšíření Visual Studio Code také vytvoří konfiguraci spuštění v `.vscode/launch.json` nástroji, která obsahuje jednu konfiguraci s názvem **připojit k funkcím Pythonu**. Tato konfigurace znamená, že můžete spustit projekt pouhým stisknutím klávesy F5 nebo pomocí Průzkumníka ladění:

    ![Průzkumník ladění zobrazující konfiguraci spuštění funkcí](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. Při spuštění ladicího programu se otevře terminál zobrazující výstup z Azure Functions, včetně shrnutí dostupných koncových bodů. Adresa URL může být odlišná, pokud jste použili jiný název než "HttpExample":

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Pomocí **kombinace kláves Ctrl + kliknutí** nebo **příkazu cmd + klikněte** na adresu URL v okně Visual Studio Code **výstup** , otevřete prohlížeč na tuto adresu nebo spusťte prohlížeč a vložte stejnou adresu URL. V obou případech je `api/<function_name>`koncový bod v tomto případě. `api/HttpExample` Vzhledem k tomu, že tato adresa URL neobsahuje parametr názvu, by mělo okno prohlížeče pouze zobrazit, "předejte prosím název do řetězce dotazu nebo v textu žádosti", jak je to vhodné pro cestu v kódu.

1. Teď zkuste přidat parametr Name pro použití, `http://localhost:7071/api/HttpExample?name=VS%20Code`jako je, a v okně prohlížeče by se měla zobrazit zpráva "Hello Visual Studio Code!", která představí, že jste tuto cestu ke kódu spustili.

1. Chcete-li předat hodnotu názvu v těle požadavku JSON, můžete použít nástroj, jako je například kudrlinkou s vloženým kódem JSON:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Případně vytvořte soubor jako *data. JSON* , který obsahuje `{"name":"Visual Studio Code"}` a použijte příkaz `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Chcete-li otestovat ladění funkce, nastavte zarážku na řádku, který `name = req.params.get('name')` čte a znovu vytvoří požadavek na adresu URL. Ladicí program Visual Studio Code by měl na tomto řádku zastavit, což vám umožní kontrolovat proměnné a krokovat kód. (Stručný návod základního ladění najdete v tématu [Visual Studio Code kurzu – konfigurace a spuštění ladicího programu](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger).)

1. Až budete spokojeni s tím, že jste důkladně otestovali funkci místně, ukončete ladicí program (pomocí příkazu nabídky **ladění** > **Zastavit ladění** nebo příkazu **Odpojit** na panelu nástrojů ladění).

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Nasazení do Azure Functions

V tomto postupu použijete rozšíření Functions k vytvoření aplikace Function App v Azure spolu s dalšími požadovanými prostředky Azure. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Vyžaduje taky Azure Storage účet pro data a [plán hostování](functions-scale.md#hosting-plan-support). Všechny tyto prostředky jsou uspořádány v rámci jedné skupiny prostředků.

1. **V Azure: Průzkumník** funkcí, vyberte příkaz **nasadit do Function App** nebo otevřete paletu příkazů (F1 **) a vyberte Azure Functions: Nasaďte do** Function App příkazu. Aplikace Function App je znovu v místě, kde se váš projekt Python spouští v Azure.

    ![Nasadit do Function App příkazu](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Po zobrazení výzvy vyberte **vytvořit novou Function App v Azure**a zadejte název, který je jedinečný v rámci Azure (obvykle se používá jako osobní nebo firemní název a jiné jedinečné identifikátory. můžete použít písmena, číslice a spojovníky). Pokud jste dříve vytvořili Function App, zobrazí se jeho název v tomto seznamu možností.

1. Rozšíření provádí následující akce, které můžete sledovat v Visual Studio Code automaticky otevírané zprávy a okně **výstup** (proces trvá několik minut):

    - Vytvořte skupinu prostředků pomocí názvu, který jste zadali (odeberou spojovníky).
    - V této skupině prostředků vytvořte účet úložiště, plán hostování a aplikaci Function App. Ve výchozím nastavení se vytvoří [plán spotřeby](functions-scale.md#consumption-plan) . Chcete-li spustit funkce ve vyhrazeném plánu, je třeba [Povolit publikování s možností pokročilého vytvoření](functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options).
    - Nasaďte svůj kód do aplikace Function App.

    **Azure: V** Průzkumníku funkcí se taky zobrazuje průběh:

    ![Indikátor průběhu nasazení v Azure: Průzkumník funkcí](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Po dokončení nasazení bude rozšíření Azure Functions zobrazovat zprávu s tlačítky pro tři další akce:

    ![Zpráva indikující úspěšné nasazení s dalšími akcemi](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    **Protokoly streamování** a **nastavení nahrávání**najdete v dalších částech. **Výstup zobrazení**najdete v následujícím kroku 5.

1. Po nasazení se v okně **výstup** zobrazuje také veřejný koncový bod v Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Pomocí tohoto koncového bodu můžete spustit stejné testy, které jste provedli místně, pomocí parametrů adresy URL nebo požadavků s daty JSON v textu žádosti. Výsledky veřejného koncového bodu by měly odpovídat výsledkům místního koncového bodu, který jste otestovali dříve.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Protokoly datových proudů

Podpora pro streamování protokolů je aktuálně ve vývoji, jak je popsáno v [tématu problém 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) pro rozšíření Azure Functions. Tlačítko **protokoly streamování** v místní nabídce se zprávou nasazení nakonec připojí výstup protokolu v Azure k Visual Studio Code. Datový proud protokolu můžete spustit a zastavit také v Průzkumníkovi **Azure Functions** kliknutím pravým tlačítkem na projekt functions a výběrem možnosti **Spustit protokoly streamování** nebo **zastavit streamování protokolů**.

V současnosti ale tyto příkazy ještě nejsou funkční. Streamování protokolů je místo k dispozici v prohlížeči spuštěním následujícího příkazu a nahrazením `<app_name>` názvem vaší aplikace Functions v Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Synchronizovat místní nastavení s Azure

Tlačítko **Nahrát nastavení** v místní nabídce zpráva nasazení aplikuje všechny změny provedené v souboru *Local. Settings. JSON* do Azure. Můžete také vyvolat příkaz v Průzkumníkovi **Azure Functions** , když rozbalíte uzel projektu Functions, kliknete pravým tlačítkem myši na **nastavení aplikace**a vyberete **Odeslat místní nastavení...** . Pomocí palety příkazů můžete také vybrat **Azure Functions: Odeslat místní nastavení** – příkaz

Nahrávání nastavení aktualizuje všechna existující nastavení a přidá všechna nová nastavení definovaná v *Local. Settings. JSON*. Při nahrávání se neodstraní žádná nastavení z Azure, která nejsou uvedená v místním souboru. Chcete-li tato nastavení odebrat, rozbalte uzel **nastavení aplikací** v Průzkumníkovi **Azure Functions** , klikněte pravým tlačítkem myši na nastavení a vyberte **Odstranit nastavení...** . Můžete také upravit nastavení přímo na Azure Portal.

Pokud chcete použít změny provedené prostřednictvím portálu nebo pomocí **Průzkumníka Azure** na soubor *Local. Settings. JSON* , klikněte pravým tlačítkem na uzel **nastavení aplikace** a vyberte příkaz **Stáhnout vzdálené nastavení...** . Pomocí palety příkazů můžete také vybrat **Azure Functions: Stažení příkazu pro** vzdálenou konfiguraci

## <a name="add-a-second-function"></a>Přidat druhou funkci

Po prvním nasazení můžete provádět změny kódu, jako je například přidání dalších funkcí a opětovné nasazení do stejné aplikace Functions.

1. **V Azure: V** Průzkumníku funkcí vyberte příkaz **vytvořit funkci** nebo použijte **Azure Functions: Vytvořte funkci** z palety příkazů. Zadejte následující podrobnosti funkce:

    - Šablona: Trigger HTTP
    - Název: "DigitsOfPi"
    - Úroveň autorizace: Anonymní

1. V Visual Studio Code Průzkumník souborů je podsložka s názvem funkce, která znovu obsahuje soubory s názvem  *\_ \_init\_\_. py*, *Function. JSON*a *Sample. dat*.

1. Nahraďte obsah  *\_ \_init.py,\_aby odpovídal následujícímu kódu, který generuje řetězec obsahující hodnotu pí na počet číslic zadaný v adrese URL (Tento kód používá pouze parametr URL).\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Vzhledem k tomu, že kód podporuje pouze HTTP GET, Modify *Function. JSON* , `"methods"` aby kolekce `"get"` obsahovala pouze (tj `"post"`. Remove). Celý soubor by měl vypadat takto:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. Spusťte ladicí program stisknutím klávesy F5 nebo **kliknutím na příkaz** > nabídky**Spustit ladění** pro ladění. Okno **výstup** by nyní mělo zobrazit oba koncové body v projektu:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. V prohlížeči nebo ze oblé žádosti si vyžádejte `http://localhost:7071/api/DigitsOfPi?digits=125` a Sledujte výstup. (Můžete si všimnout, že algoritmus kódu není zcela přesný, ale ponecháme vám vylepšení.) Až skončíte, zastavte ladicí program.

1. Znovu nasaďte kód pomocí **nasazení Function App** v **Azure: Průzkumník** funkcí Po zobrazení výzvy vyberte Function App vytvořili dříve.

1. Jakmile se nasazení dokončí (trvá několik minut), zobrazí se v okně **výstup** veřejné koncové body, pomocí kterých můžete testy opakovat.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Přidání vazby pro zápis zpráv do služby Azure Storage

_Vazba_ umožňuje připojit kód vaší funkce k prostředkům, jako je Azure Storage, bez psaní kódu pro přístup k datům. Vazba je definována v souboru *Function. JSON* a může představovat vstupní i výstupní. Funkce může používat víc vstupních a výstupních vazeb, ale jenom jednu Trigger. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

V této části přidáte vazbu úložiště k funkci HttpExample vytvořené dříve v tomto kurzu. Funkce používá tuto vazbu k zápisu zpráv do úložiště s každým požadavkem. Příslušné úložiště používá stejný výchozí účet úložiště, který používá aplikace Function App. Pokud ale plánujete, že budete chtít úložiště používat, měli byste zvážit vytvoření samostatného účtu.

1. Synchronizujte vzdálené nastavení projektu Azure Functions do souboru *Local. Settings. JSON* tak, že otevřete paletu příkazů a vyberete **Azure Functions: Stáhněte si vzdálená**nastavení. Otevřete *Local. Settings. JSON* a ověřte, zda obsahuje hodnotu pro `AzureWebJobsStorage`. Tato hodnota je připojovací řetězec pro účet úložiště.

1. Ve složce klikněte pravým tlačítkem myši na *Function. JSON*, vyberte **Přidat vazbu:** `HttpExample`

    ![Přidat příkaz vazby v Průzkumníkovi Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. V části výzvy, které následují v Visual Studio Code vyberte nebo zadejte následující hodnoty:

    | Výzva | Hodnota, která má být k dispozici |
    | --- | --- |
    | Nastavit směr vazby | mimo |
    | Výběr vazby s směrem ven | Azure Queue Storage |
    | Název, který slouží k identifikaci této vazby v kódu | msg |
    | Fronta, do které bude odeslána zpráva | outqueue |
    | Vyberte nastavení z *Local. Settings. JSON* (dotazování na připojení úložiště). | AzureWebJobsStorage |

1. Po provedení těchto výběrů ověřte, zda je do souboru *Function. JSON* přidána následující vazba:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Teď, když jste nakonfigurovali vazbu, ji můžete použít ve svém kódu funkce. Nově definovaná vazba se znovu zobrazí ve vašem kódu jako `main` argument funkce v  *\_ \_\_init\_. py*. Například můžete upravit `msg`  *\_ \_soubor init\_. py v HttpExample tak, aby odpovídal následujícímu, který ukazuje použití argumentu k zápisu zprávy s časovým razítkem s názvem použitým v\_* Request. Komentáře vysvětlují konkrétní změny:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Chcete-li tyto změny otestovat místně, spusťte ladicí program znovu v Visual Studio Code stisknutím klávesy F5 nebo výběrem příkazu **ladění** > **Spustit ladění** . Stejně jako předtím by měl okno **výstup** zobrazovat koncové body v projektu.

1. V prohlížeči přejděte na adresu URL `http://localhost:7071/api/HttpExample?name=VS%20Code` , abyste vytvořili požadavek na koncový bod HttpExample, který by také měl zapisovat zprávu do fronty.

1. Chcete-li ověřit, zda byla zpráva zapsána do fronty "fronta" (s názvem ve vazbě), můžete použít jednu ze tří metod:

    1. Přihlaste se k [Azure Portal](https://portal.azure.com)a přejděte do skupiny prostředků, která obsahuje projekt Functions. V této skupině prostředků klikněte na místní a přejděte do účtu úložiště projektu a pak přejděte do **fronty**. Na této stránce přejděte do části "fronta", která by měla zobrazovat všechny protokolované zprávy.

    1. Procházejte a prověřte frontu buď pomocí Průzkumník služby Azure Storage, která se integruje se sadou Visual Studio, jak je popsáno v části [připojení funkcí pro Azure Storage pomocí Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md), zejména v části prohlédnutí [výstupní fronty](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) .

    1. Použijte rozhraní příkazového řádku Azure pro dotazování fronty úložiště, jak je popsáno v tématu [dotazování fronty úložiště](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).
    
1. K testování v cloudu znovu nasaďte kód pomocí **nasazení Function App** v **Azure: Průzkumník** funkcí Po zobrazení výzvy vyberte Function App vytvořili dříve. Po dokončení nasazení (trvá několik minut) znovu zobrazí okno **výstup** veřejné koncové body, pomocí kterých můžete opakovat testy.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Function App, který jste vytvořili, zahrnuje prostředky, které můžou nabývat minimálními náklady (viz [ceny funkcí](https://azure.microsoft.com/pricing/details/functions/)). Prostředky vyčistíte tak, že kliknete pravým tlačítkem na **Function App v Azure: V** Průzkumníku funkcí a vyberte **Odstranit Function App**. Můžete také navštívit [Azure Portal](https://portal.azure.com), vybrat **skupiny prostředků** v navigačním podokně na levé straně, vybrat skupinu prostředků vytvořenou v procesu tohoto kurzu a pak použít příkaz **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další postup

Blahopřejeme k dokončení tohoto návodu k nasazení kódu Pythonu do Azure Functions! Nyní jste připraveni vytvořit mnoho dalších funkcí bez serveru.

Jak bylo uvedeno dříve, další informace o rozšíření Functions najdete v úložišti GitHub [VSCode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Jsou také k disúvodníku problémy a příspěvky.

Přečtěte si [přehled Azure Functions](functions-overview.md) a prozkoumejte různé triggery, které můžete použít.

Další informace o službách Azure, které můžete použít z Pythonu, včetně úložiště dat spolu s AI a Machine Learning službami, najdete v [centru pro vývojáře](/azure/python/?view=azure-python)v Pythonu pro Azure.

K dispozici jsou také další rozšíření Azure pro Visual Studio Code, které můžete najít užitečné. V Průzkumníkovi rozšíření stačí hledat "Azure":

![Rozšíření Azure pro Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Mezi oblíbená rozšíření patří:

- [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

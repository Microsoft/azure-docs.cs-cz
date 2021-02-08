---
title: Vytvoření funkce v cestách nebo Rust pomocí Visual Studio Code-Azure Functions
description: Naučte se, jak vytvořit funkci přejít jako vlastní obslužnou rutinu Azure Functions a pak publikovat místní projekt na hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: a3ef7bdd19badf7a7deaa8376440016e39e0d14b
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809558"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce přejít nebo Rust v Azure pomocí Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

V tomto článku pomocí Visual Studio Code vytvoříte [vlastní funkci obslužné rutiny](functions-custom-handlers.md) , která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.

Vlastní obslužné rutiny lze použít k vytvoření funkcí v libovolném jazyce nebo modulu runtime spuštěním procesu serveru HTTP. Tento článek podporuje [Rust](create-first-function-vs-code-other.md?tabs=rust). [](create-first-function-vs-code-other.md?tabs=go)

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

## <a name="configure-your-environment"></a>Konfigurace prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

# <a name="go"></a>[Přejít](#tab/go)

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code

+ [Azure Functions Core Tools](./functions-run-local.md#v2) verze 3. x. Pomocí `func --version` příkazu ověřte, zda je správně nainstalován.

+ [Přejít](https://golang.org/doc/install), doporučuje se nejnovější verze. Pomocí `go version` příkazu ověřte svou verzi.

# <a name="rust"></a>[Rust](#tab/rust)

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code

+ [Azure Functions Core Tools](./functions-run-local.md#v2) verze 3. x. Pomocí `func --version` příkazu ověřte, zda je správně nainstalován.

+ Rust sada nástrojů s použitím [rustup](https://www.rust-lang.org/tools/install). Pomocí `rustc --version` příkazu ověřte svou verzi.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions vlastní obslužné rutiny. Později v tomto článku publikujete kód funkce do Azure.

1. Zvolte ikonu Azure na panelu Aktivita a pak v oblasti **Azure: Funkce** vyberte ikonu **Vytvořit nový projekt**.

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `Custom`.

    + **Vyberte šablonu pro první funkci projektu:** Zvolte `HTTP trigger`.

    + **Zadejte název funkce:** Zadejte příkaz `HttpExample`.

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovni autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.

1. Pomocí těchto informací Visual Studio Code generuje projekt Azure Functions pomocí funkce triggeru HTTP. Můžete zobrazit soubory místních projektů v Průzkumníkovi. Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](functions-develop-vs-code.md#generated-project-files). 

## <a name="create-and-build-your-function"></a>Vytvoření a sestavení funkce

*function.jsv* souboru ve složce *HttpExample* DEKLARUJE funkci triggeru http. Funkci dokončíte přidáním obslužné rutiny a zkompilováním do spustitelného souboru.

# <a name="go"></a>[Přejít](#tab/go)

1. Stisknutím <kbd>kombinace kláves Ctrl + n</kbd> (<kbd>cmd + n</kbd> v MacOS) vytvořte nový soubor. Uložte ho jako *obslužnou rutinu.* v kořenovém adresáři aplikace Function App (ve stejné složce jako *host.jszapnuté*) použijte.

1. V *obslužné rutině. přejít* přidejte následující kód a soubor uložte. Toto je vlastní obslužná rutina přejít.

    ```go
    package main

    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )

    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }

    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. Stisknutím <kbd>kombinace kláves CTRL + SHIFT +</kbd> nebo vybráním *nového terminálu* z nabídky *terminálu* otevřete nový integrovaný terminál v vs Code.

1. Zkompilujte vlastní obslužnou rutinu pomocí následujícího příkazu. Spustitelný soubor s názvem `handler` ( `handler.exe` v systému Windows) je výstupem v kořenové složce aplikace Function App.

    ```bash
    go build handler.go
    ```

    ![Vlastní obslužná rutina VS Code-Build přejít](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. Stisknutím <kbd>kombinace kláves CTRL + SHIFT +</kbd> nebo vybráním *nového terminálu* z nabídky *terminálu* otevřete nový integrovaný terminál v vs Code.

1. V kořenovém adresáři aplikace Function App (stejná složka jako *host.json*) inicializujte projekt Rust s názvem `handler` .

    ```bash
    cargo init --name handler
    ```

1. Do pole *náklad. toml* přidejte následující závislosti, které jsou nezbytné k dokončení tohoto rychlého startu. V příkladu se používá architektura webového serveru v [křivce](https://docs.rs/warp/) .

    ```toml
    [dependencies]
    warp = "0.2"
    tokio = { version = "0.2", features = ["full"] }
    ```

1. V části *Src/Main. RS* přidejte následující kód a soubor uložte. Toto je vlastní obslužná rutina Rust.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Zkompilujte binární soubor pro vlastní obslužnou rutinu. Spustitelný soubor s názvem `handler` ( `handler.exe` v systému Windows) je výstupem v kořenové složce aplikace Function App.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![Vlastní obslužná rutina VS Code Rust sestavení](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>Konfigurace aplikace Function App

Aby bylo možné spustit vlastní binární soubor obslužné rutiny při spuštění, je nutné nakonfigurovat hostitele funkce.

1. Otevřete *host.js*.

1. V `customHandler.description` části nastavte hodnotu `defaultExecutablePath` na `handler` (v systému Windows, nastavte na hodnotu `handler.exe` ).

1. V `customHandler` části přidejte vlastnost s názvem `enableForwardingHttpRequest` a nastavte její hodnotu na `true` . U funkcí, které se skládají jenom z triggeru HTTP, toto nastavení zjednodušuje programování tím, že vám umožní pracovat s typickým požadavkem HTTP místo v [datové části žádosti](functions-custom-handlers.md#request-payload)o vlastní obslužnou rutinu.

1. Potvrďte, že `customHandler` oddíl vypadá jako v tomto příkladu. Soubor uložte.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

Aplikace Function App je nakonfigurována tak, aby spouštěla vlastní spustitelný soubor obslužné rutiny.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Tento projekt můžete spustit na místním vývojovém počítači před publikováním do Azure.

1. V integrovaném terminálu spusťte aplikaci Function App pomocí Azure Functions Core Tools.

    ```bash
    func start
    ```

1. Pokud běží základní nástroje, přejděte na následující adresu URL a spusťte požadavek GET, který zahrnuje `?name=Functions` řetězec dotazu.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Vrátí se odpověď, která v prohlížeči vypadá jako v následujících případech:

    ![Browser – ukázkový výstup pro localhost](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Informace o žádosti se zobrazí na panelu **terminálu** .

    ![Spuštění hostitele úkolu – výstup VS Code terminálu](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Stisknutím <kbd>kombinace kláves CTRL + C</kbd> zastavte základní nástroje.

Po ověření, že se funkce na místním počítači spustí správně, je čas použít Visual Studio Code k publikování projektu přímo do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>Kompilace vlastní obslužné rutiny pro Azure

V této části publikujete projekt do Azure v aplikaci Function App se systémem Linux. Ve většině případů je před publikováním do Azure nutné znovu zkompilovat binární soubor a upravit konfiguraci tak, aby odpovídala cílové platformě.

# <a name="go"></a>[Přejít](#tab/go)

1. V integrovaném terminálu zkompilujte obslužnou rutinu na Linux/x64. `handler`V kořenu aplikace Function App se vytvoří binární soubor s názvem.

    # <a name="macos"></a>[macOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build handler.go
    ```

    Změňte `defaultExecutablePath` v *host.js* v z `handler.exe` na `handler` . Tím se dá aplikaci Function App spustit binární soubor Linux.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. Vytvořte soubor v souboru *. náklad/config*. Přidejte následující obsah a uložte soubor.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. V integrovaném terminálu zkompilujte obslužnou rutinu na Linux/x64. Vytvoří se binární soubor s názvem `handler` . Zkopírujte ho do kořenového adresáře aplikace Function App.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. Pokud používáte systém Windows, změňte `defaultExecutablePath` v *host.js* v z `handler.exe` na `handler` . Tím se dá aplikaci Function App spustit binární soubor Linux.

1. Do souboru *. funcignore* přidejte následující řádek:

    ```
    target
    ```

    Tím se zabrání publikování obsahu *cílové* složky.

---

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

V této části vytvoříte aplikaci funkcí a související prostředky v předplatném Azure a pak svůj kód nasadíte. 

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure. 


1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** zvolte tlačítko **nasadit do aplikace Function App...** .

    ![Publikování projektu do Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte složku**: Zvolte složku z pracovního prostoru nebo přejděte k nějakému, který obsahuje vaši aplikaci Function App. Tato funkce se nezobrazuje, pokud už máte spuštěnou platnou aplikaci Function App.

    + **Vyberte předplatné**: zvolte předplatné, které chcete použít. Toto se nezobrazí, pokud máte jenom jedno předplatné.

    + **Výběr aplikace funkcí v Azure:** Zvolte `+ Create new Function App (advanced)`. 
    
        > [!IMPORTANT]
        > `advanced`Možnost umožňuje zvolit konkrétní operační systém, na kterém běží aplikace Function App v Azure, což je v tomto případě Linux.

        ![VS Code – výběr možnosti Upřesnit vytvoření nové aplikace Function App](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **Zadejte globálně jedinečný název aplikace Function App**: zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je v Azure Functions jedinečný.

    + **Vyberte zásobník modulu runtime**: zvolte `Custom Handler` .

    + **Vyberte operační systém**: zvolte `Linux` .

    + **Vyberte plán hostování**: zvolte `Consumption` .

    + **Vyberte skupinu prostředků**: zvolte `+ Create new resource group` . Zadejte název skupiny prostředků. Tento název musí být v rámci vašeho předplatného Azure jedinečný. Můžete použít název navržený v příkazovém řádku.

    + **Vyberte účet úložiště**: zvolte `+ Create new storage account` . Tento název musí být globálně jedinečný v rámci Azure. Můžete použít název navržený v příkazovém řádku.

    + **Vyberte prostředek Application Insights**: zvolte `+ Create Application Insights resource` . Tento název musí být globálně jedinečný v rámci Azure. Můžete použít název navržený v příkazovém řádku.

    + **Vyberte umístění pro nové prostředky**: pro lepší výkon zvolte [oblast](https://azure.microsoft.com/regions/) poblíž. Rozšíření zobrazuje stav jednotlivých prostředků, které se vytváří v Azure v oznamovací oblasti.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Oznámení o vytváření prostředků Azure":::

1. Po dokončení se ve vašem předplatném vytvoří následující prostředky Azure:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. 

4. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení. Pokud jste oznámení nezobrazili, vyberte ikonu zvonku v pravém dolním rohu, abyste ji viděli znovu.

    ![Vytvořit kompletní oznámení](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o Azure Functions vlastní obslužné rutiny](functions-custom-handlers.md)

---
title: Přidání rozhraní API do statického Web Apps Azure s Azure Functions
description: Začněte se statickým Web Apps Azure přidáním rozhraní API bez serveru do vaší statické webové aplikace pomocí Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598455"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Přidání rozhraní API do služby Azure static Web Apps Preview pomocí Azure Functions

Do služby Azure static Web Apps můžete přidat rozhraní API bez serveru prostřednictvím integrace s Azure Functions. Tento článek ukazuje, jak přidat a nasadit rozhraní API do webu Azure static Web Apps.

Informace o tom, jak zabezpečit trasy rozhraní API, najdete v [příručce k směrování](routes.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code
- Rozšíření pro [živý Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) .

## <a name="create-a-git-repository"></a>Vytvoření úložiště Git 

Následující kroky ukazují, jak vytvořit nové úložiště a klonovat soubory do počítače.

1. Pro https://github.com/staticwebdev/vanilla-basic/generate Vytvoření nového úložiště přejděte na.
1. Do pole _název úložiště_ zadejte **My-Vanilla-API**.
1. Klikněte na **vytvořit úložiště ze šablony**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

Po vytvoření projektu můžete použít Visual Studio Code k naklonování úložiště Git.

1. Stisknutím klávesy **F1** otevřete příkaz v paletě příkazů.
1. Vložte adresu URL do _Gitu: příkaz Clone_ a stiskněte klávesu **ENTER**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Klonování projektu GitHubu pomocí Visual Studio Code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Klonování projektu GitHubu pomocí Visual Studio Code":::


## <a name="create-your-local-project"></a>Vytvořit místní projekt

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions. Později publikujete aplikaci Functions do Azure.

1. V projektu _My-Vanilla-API_ vytvořte podsložku s názvem **rozhraní API**.

   > [!NOTE]
   > Této složce můžete dát libovolný název. Tento příklad používá `api` . 

2. Stisknutím **klávesy F1** otevřete paletu příkazů.
3. Zadejte **Azure Functions: vytvořit nový projekt...**
4. Stiskněte klávesu **ENTER**
5. Zvolit **Procházet**
6. Vyberte složku **rozhraní API** jako adresář pracovního prostoru projektu.
7. Zvolte **Vybrat**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Vytvoření nové Azure Functions pomocí Visual Studio Code":::

8. Zadejte následující informace na následujících dotazech:

    - _Vyberte jazyk pro váš projekt funkce_: zvolte **JavaScript** .
    - _Vyberte šablonu pro funkci prvního projektu_: zvolte **aktivační událost http** .
    - _Zadejte název funkce_: **GetMessage**
    - _Úroveň autorizace_: vyberte možnost **anonymní**, která umožňuje komukoli zavolat koncový bod funkce.
        - Další informace o úrovních autorizace najdete v tématu [autorizační klíče](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

9. Pomocí těchto informací Visual Studio Code generuje Azure Functions projekt pomocí triggeru protokolu HTTP.
    - Místní soubory projektu můžete zobrazit v okně Průzkumníka Visual Studio Code.
    - Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. Vaše aplikace by teď měla mít strukturu projektu podobnou tomuto příkladu.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Dále aktualizujte `GetMessage` funkci v části _API/GetMessage/index. js_ následujícím kódem.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Aktualizujte `GetMessage` konfiguraci `api/GetMessage/function.json` pomocí následujícího nastavení.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
U výše uvedených nastavení je koncovým bodem rozhraní API:

- Aktivovaná s požadavkem HTTP se provede na funkci.
- K dispozici pro všechny požadavky bez ohledu na stav ověřování
- Vystaveno prostřednictvím trasy _/API/Message_

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio Code se integruje s [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) a umožní vám spustit tento projekt na místním vývojovém počítači před publikováním do Azure.

1. Spusťte funkci stisknutím klávesy **F5** a spusťte aplikaci functions a zobrazí se základní výstup nástrojů na panelu _terminálu_ .

2. Pokud Azure Functions Core Tools ještě není nainstalovaná, vyberte na příkazovém řádku **nainstalovat** .

    Po instalaci základních nástrojů se vaše aplikace spustí na panelu _terminálu_ . Jako součást výstupu uvidíte koncový bod adresy URL vaší funkce aktivované protokolem HTTP spuštěnou místně.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Vytvoření nové Azure Functions pomocí Visual Studio Code":::

3. Se základními nástroji, které běží, přejděte na následující adresu URL a spusťte `GET` žádost.

   <http://localhost:7071/api/message>

   Vrátí se odpověď, která v prohlížeči vypadá jako v následujících případech:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Vytvoření nové Azure Functions pomocí Visual Studio Code":::

Po ověření, že funkce funguje správně, můžete volat rozhraní API z aplikace JavaScriptu.

### <a name="call-the-api-from-the-application"></a>Volání rozhraní API z aplikace

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Aktualizace souborů pro přístup k rozhraní API

1. Dále aktualizujte obsah souboru _index. html_ pomocí následujícího kódu, který načte text z funkce rozhraní API a zobrazí ho na obrazovce:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Se základními nástroji, které běží, použijte rozšíření [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code k obsluze souboru _index. html_ a otevřete ho v prohlížeči. 

2. Stiskněte **F1** a zvolte **živý Server: otevřít v Live serveru**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Vytvoření nové Azure Functions pomocí Visual Studio Code":::

   > [!NOTE]
   > K obsluze souboru můžete použít další servery HTTP nebo proxy `index.html` . Přístup k souboru `index.html` z `file:///` nebude fungovat.

### <a name="commit-and-push-your-changes-to-github"></a>Potvrzení a vložení změn do GitHubu

Pomocí Visual Studio Code proveďte potvrzení a vložení změn do vzdáleného úložiště Git.

1. Stisknutím **klávesy F1** otevřete paletu příkazů.
1. Zadejte **git: potvrdit vše**
1. Přidat potvrzovací zprávu
1. Typ v **Gitu: push**

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Vytvoření statické aplikace na Azure Portal obrazovce 1":::

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Klikněte na **vytvořit prostředek** .
1. Hledání **statického Web Apps**
1. Klikněte na **statické Web Apps (Preview)** .
1. Klikněte na **vytvořit** .
1. Výběr vašeho _předplatného Azure_
1. Vyberte nebo vytvořte novou _skupinu prostředků_ .
1. Pojmenujte aplikaci **My-Vanilla-API**.
1. Vyberte _oblast_ , která je pro vás nejblíže
1. Výběr **bezplatné** _SKU_
1. Klikněte na tlačítko **Přihlásit se pomocí GitHubu** a proveďte ověření pomocí GitHubu.
1. Vybrat upřednostňovanou _organizaci_
1. Z rozevíracího seznamu _úložiště_ vyberte **My-Vanilla-API** .
1. V rozevíracím seznamu _větev_ vyberte **Hlavní** .
1. Kliknutím na tlačítko **Další: >sestavení** upravíte konfiguraci sestavení

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Vytvoření statické aplikace na Azure Portal obrazovce 2":::

Dále přidejte následující podrobnosti o sestavení.

1. Jako _umístění aplikace_zadejte **./** .

1. Do pole _umístění rozhraní API_ zadejte **API** .

   Toto je název složky rozhraní API vytvořené v předchozím kroku.
   
1. Zrušte zaškrtnutí políčka Výchozí hodnota mimo _umístění artefaktu aplikace_a nechte pole prázdné.

1. Klikněte na **Zkontrolovat a vytvořit**.

| Nastavení | Popis             | Vyžadováno |
| -------- | ----------------------- |
|  Umístění aplikace | Umístění zdrojového kódu statické aplikace | Yes |
|  Umístění rozhraní API | Umístění back-endu rozhraní API. To odkazuje na kořenovou složku projektu aplikace Azure Functions. | No |
|  Umístění artefaktu aplikace | Umístění výstupní složky sestavení. Některé z front-endové rozhraní JavaScript mají krok sestavení, který umístí provozní soubory do složky. Toto nastavení odkazuje na výstupní složku sestavení. | No |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Vytvoření statické aplikace na Azure Portal – obrazovka 3":::

1. Klikněte na **vytvořit** .
1. Počkejte na dokončení nasazení (může to trvat několik minut).
1. Přejít na`https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Ujistěte se, že sestavení proběhlo úspěšně.

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="Pracovní postup GitHubu":::

Nasazené rozhraní API bude k dispozici na adrese `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` .

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="Pracovní postup GitHubu":::

Adresu URL aplikace můžete najít také z Azure Portal:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Přístup ke statické adrese URL aplikace z Azure Portal":::

Případně můžete přímo přistupovat ke službě Azure static Web App na portálu `https://<STATIC_APP_NAME>.azurestaticapps.net` a ověřit výsledek v prohlížeči.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nechcete, aby se tato aplikace nadále používala k dalšímu použití, můžete pomocí následujících kroků odstranit statickou webovou aplikaci Azure a její související prostředky.

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Do horního panelu vyhledávání zadejte **skupiny prostředků** .
1. Klikněte na **skupiny prostředků** . 
1. Vybrat **myResourceGroup**
1. Na stránce _myResourceGroup_ se ujistěte, že uvedené prostředky jsou ty, které chcete odstranit.
1. Vybrat **Odstranit**
1. Do textového pole zadejte **myResourceGroup** .
1. Vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace nastavení aplikace](./application-settings.md)

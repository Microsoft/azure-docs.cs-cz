---
title: Přidání rozhraní API do statického Web Apps Azure s Azure Functions
description: Začněte se statickým Web Apps Azure přidáním rozhraní API bez serveru do vaší statické webové aplikace pomocí Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: c84367a5e3ab57090f59196e8474c14cba87f32b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250143"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Přidání rozhraní API do služby Azure static Web Apps Preview pomocí Azure Functions

Do služby Azure static Web Apps můžete přidat rozhraní API bez serveru prostřednictvím integrace s Azure Functions. Tento článek ukazuje, jak přidat a nasadit rozhraní API do webu Azure static Web Apps.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným.
  - Pokud účet nemáte, můžete si [ho vytvořit zdarma](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code
- Rozšíření pro [živý Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) .
- [Node.js](https://nodejs.org/download/) pro místní spuštění aplikace API

## <a name="create-a-git-repository"></a>Vytvořte úložiště Git

Následující kroky ukazují, jak vytvořit nové úložiště a klonovat soubory do počítače.

1. Ujistěte se, že jste přihlášeni k GitHubu, a přejděte k https://github.com/staticwebdev/vanilla-basic/generate Vytvoření nového úložiště.
1. Do pole _název úložiště_ zadejte **My-Vanilla-API**.
1. Klikněte na **vytvořit úložiště ze šablony**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

Po vytvoření projektu zkopírujte adresu URL v prohlížeči pro nové úložiště. Tuto adresu URL použijete v Visual Studio Code ke klonování úložiště Git.

1. Stisknutím klávesy **F1** otevřete příkaz v paletě příkazů.
1. Vložte adresu URL do _Gitu: příkaz Clone_ a stiskněte klávesu **ENTER**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

    Podle pokynů vyberte umístění úložiště pro klonování projektu.

## <a name="create-the-api"></a>Vytvoření rozhraní API

V dalším kroku vytvoříte Azure Functions projekt jako rozhraní API aplikace. 

1. V projektu _My-Vanilla-API_ vytvořte podsložku s názvem **rozhraní API**.
1. Stisknutím **klávesy F1** otevřete paletu příkazů.
1. Zadejte **Azure Functions: vytvořit nový projekt...**
1. Stiskněte **Enter**.
1. Zvolit **Procházet**
1. Vyberte složku **rozhraní API** jako adresář pracovního prostoru projektu.
1. Zvolte **Vybrat**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

1. Zadejte následující informace na následujících dotazech:

    - _Vyberte jazyk_: zvolte **JavaScript** .
    - _Vyberte šablonu pro funkci prvního projektu_: zvolte **aktivační událost http** .
    - Zadejte _název funkce_: zadejte **GetMessage** .
    - _Úroveň autorizace_: vyberte možnost **anonymní**, která umožňuje komukoli zavolat koncový bod funkce.
        - Další informace o úrovních autorizace najdete v tématu [autorizační klíče](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

Visual Studio Code generuje projekt Azure Functions pomocí funkce aktivované protokolem HTTP.

Vaše aplikace teď má strukturu projektu podobnou následujícímu příkladu.

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

Dále změníte `GetMessage` funkci tak, aby vracela zprávu na front-end.

1. Aktualizujte `GetMessage` funkci v části _API/GetMessage/index.js_ s následujícím kódem.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Aktualizujte `GetMessage` konfiguraci `api/GetMessage/function.json` pomocí následujícího nastavení.

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

- Aktivuje se, když se ve funkci provede požadavek HTTP.
- K dispozici pro všechny požadavky bez ohledu na stav ověřování
- Vystaveno prostřednictvím trasy _/API/Message_

## <a name="run-the-api-locally"></a>Místní spuštění rozhraní API

Visual Studio Code se integruje s [Azure Functions Core Tools](../azure-functions/functions-run-local.md) a umožní vám spustit tento projekt na místním vývojovém počítači před publikováním do Azure.

> [!TIP]
> Než budete pokračovat, ujistěte se, že máte nainstalované všechny prostředky uvedené v oddílu [požadavky](#prerequisites) .

1. Spusťte funkci stisknutím klávesy **F5** a spusťte aplikaci Functions.

1. Pokud Azure Functions Core Tools ještě není nainstalovaná, vyberte na příkazovém řádku **nainstalovat** .

    Základní nástroje zobrazují výstup z běžící aplikace na panelu _terminálu_ . Jako součást výstupu uvidíte koncový bod adresy URL vaší funkce aktivované protokolem HTTP spuštěnou místně.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

1. Pokud běží základní nástroje, přejděte na následující adresu URL, abyste ověřili, že rozhraní API běží správně: `http://localhost:7071/api/message` .

   Odpověď v prohlížeči by měla vypadat podobně jako v následujícím příkladu:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

1. Stisknutím **SHIFT + F5** zastavíte relaci ladění.

### <a name="call-the-api-from-the-application"></a>Volání rozhraní API z aplikace

Při nasazení do Azure jsou požadavky na rozhraní API automaticky směrovány do aplikace Functions pro požadavky odeslané do `api` trasy. V místním prostředí je nutné nakonfigurovat nastavení aplikace na požadavky proxy na místní rozhraní API.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>Aktualizace souborů HTML pro přístup k rozhraní API

1. Dále aktualizujte obsah souboru _index.html_ pomocí následujícího kódu, který načte text z funkce rozhraní API a zobrazí ho na obrazovce:

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
       <p>Loading content from the API: <b id="name">...</b></p>
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

1. Stisknutím klávesy **F5** spusťte projekt rozhraní API.

1. Stiskněte **F1** a zvolte **živý Server: otevřít v Live serveru**.

    Nyní by se měla zobrazit zpráva rozhraní API na webové stránce.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

   > [!NOTE]
   > K obsluze souboru můžete použít další servery HTTP nebo proxy `index.html` . Přístup k souboru `index.html` z `file:///` nebude fungovat.

1. Stisknutím **SHIFT + F5** zastavte projekt rozhraní API.

### <a name="commit-and-push-your-changes-to-github"></a>Potvrzení a vložení změn do GitHubu

Pomocí Visual Studio Code proveďte potvrzení a vložení změn do vzdáleného úložiště Git.

1. Stisknutím **klávesy F1** otevřete paletu příkazů.
1. Zadejte **git: potvrdit vše**
1. Přidejte potvrzovací zprávu a stiskněte klávesu **ENTER** .
1. Stiskněte klávesu **F1**
1. Zadejte v **Gitu: push** a stiskněte **ENTER** .

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Klikněte na **Vytvořit prostředek**.
1. Vyhledat **statickou webovou aplikaci**
1. Klikněte na **statická webová aplikace (Preview)** .
1. Klikněte na **Vytvořit**.

Pak přidejte nastavení specifické pro aplikaci.

1. Výběr vašeho _předplatného Azure_
1. Vyberte nebo vytvořte novou _skupinu prostředků_ .
1. Pojmenujte aplikaci **My-Vanilla-API**.
1. Vyberte _oblast_ , která je pro vás nejblíže
1. Výběr **bezplatné** _SKU_
1. Klikněte na tlačítko **Přihlásit se přes GitHub** a ověřte se v této službě.
1. Vybrat upřednostňovanou _organizaci_
1. Z rozevíracího seznamu _úložiště_ vyberte **My-Vanilla-API** .
1. V rozevíracím seznamu _větev_ vyberte **Hlavní** .
1. Klikněte na tlačítko **Další: Build >** (Další: Sestavení) a upravte konfiguraci sestavení.

Dále přidejte následující podrobnosti o sestavení.

1. Jako **/** _umístění aplikace_zadejte.
1. Do pole _umístění rozhraní API_ zadejte **API** .
1. Zrušte zaškrtnutí políčka Výchozí hodnota mimo _umístění artefaktu aplikace_a nechte pole prázdné.
1. Klikněte na **Zkontrolovat a vytvořit**.
1. Klikněte na tlačítko **Create** (Vytvořit).

    Po kliknutí na tlačítko _vytvořit_ Azure provede dvě věci. Nejdřív se vytvoří základní cloudové služby, které budou podporovat aplikaci. Dále proces na pozadí začne sestavovat a nasazovat aplikaci.

1. Kliknutím na tlačítko **Přejít k prostředku** přejdete na stránku _Přehled_ webové aplikace.

    Po sestavení aplikace na pozadí můžete kliknout na banner, který obsahuje odkaz pro zobrazení stavu sestavení.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

1. Po dokončení nasazení může organizační jednotka přejít do webové aplikace kliknutím na odkaz _Adresa URL_ zobrazený na stránce _Přehled_ .

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Vytvoření nového úložiště z Vanilla – Basic":::

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

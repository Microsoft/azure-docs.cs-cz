---
title: Nastavení místního vývoje pro statickou Web Apps Azure
description: Naučte se nastavit místní vývojové prostředí pro statickou Web Apps Azure.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326163"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Nastavení místního vývoje pro Azure static Web Apps Preview

Instance statického Web Apps Azure se skládá ze dvou různých typů aplikací. První je webová aplikace pro váš statický obsah. Webové aplikace se často vytvářejí pomocí front-endové architektury a knihoven nebo se statickými generátory webů. Druhým aspektem je rozhraní API, což je Azure Functions aplikace, která poskytuje bohatší prostředí pro vývoj v back-endu.

Při spuštění v cloudu Azure static Web Apps hladce namapuje požadavky na `api` trasu z webové aplikace do aplikace Azure Functions, aniž by vyžadovala konfiguraci CORS. V místním prostředí je nutné nakonfigurovat aplikaci tak, aby napodobuje toto chování.

Tento článek popisuje doporučené osvědčené postupy pro místní vývoj, včetně následujících konceptů:

- Nastavení webové aplikace pro statický obsah
- Konfigurace aplikace Azure Functions pro rozhraní API vaší aplikace
- Ladění a spuštění aplikace
- Osvědčené postupy pro strukturu souborů a složek vaší aplikace

## <a name="prerequisites"></a>Požadavky

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code
- [Rozšíření pro živý Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) pro Visual Studio Code
  - Nutné jenom v případě, že nepoužíváte rozhraní příkazového řádku front-end JavaScript nebo statického generátoru webu.

## <a name="run-projects-locally"></a>Spustit projekty místně

Místní spuštění statické webové aplikace Azure zahrnuje tři procesy v závislosti na tom, jestli projekt obsahuje rozhraní API.

- Spuštění místního webového serveru
- Spuštění rozhraní API
- Připojení webového projektu k rozhraní API

V závislosti na tom, jak je web sestavený, může nebo nemusí být nutný k spuštění aplikace v prohlížeči místní webový server. Při použití front-end rozhraní JavaScript a generátorů statických webů je tato funkce integrovaná do příslušných rozhraní příkazového řádku (rozhraní příkazového řádku). Následující odkazy odkazují na odkaz CLI pro výběr platforem, knihoven a generátorů.

### <a name="javascript-frameworks-and-libraries"></a>JavaScriptové architektury a knihovny

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [Reagovat CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Generátory statických webů

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Pokud k obsluze svého webu používáte nástroj rozhraní příkazového řádku, můžete přeskočit na běžící část [rozhraní API](#run-api-locally) .

### <a name="running-a-local-web-server-with-live-server"></a>Spuštění místního webového serveru s živým serverem

Live Server Extension for Visual Studio Code poskytuje místní vývojový webový server, který obsluhuje statický obsah.

#### <a name="create-a-repository"></a>Vytvořte úložiště

1. Ujistěte se, že jste přihlášeni k GitHubu, a [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) pomocí této šablony přejděte do a vytvořte nový projekt GitHubu s názvem **Vanilla-API**.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="Nové okno úložiště GitHubu":::

1. Otevřete Visual Studio Code.

1. Stisknutím klávesy **F1** otevřete paletu příkazů.

1. Do vyhledávacího pole zadejte **Clone** a vyberte **git: Clone**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="možnost klonování Git v Visual Studio Code":::

1. Jako **adresu URL úložiště** zadejte následující hodnotu.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Vyberte umístění složky pro nový projekt.

1. Při zobrazení výzvy k otevření klonovaného úložiště vyberte **Otevřít**.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Otevřít v novém okně":::

Visual Studio Code otevře Klonovaný projekt v editoru.

### <a name="run-the-website-locally-with-live-server"></a>Místní spuštění webu pomocí živého serveru

1. Stisknutím klávesy **F1** otevřete paletu příkazů.

1. Do vyhledávacího pole zadejte **Live Server** a vyberte **živý Server: otevřít na Live serveru.**

    Otevře se karta prohlížeče pro zobrazení aplikace.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Jednoduchý statický web běžící v prohlížeči":::

    Tato aplikace provede požadavek HTTP na `api/message` koncový bod. V tuto chvíli se tato žádost nezdařila, protože část rozhraní API této aplikace musí být spuštěná.

### <a name="run-api-locally"></a>Spustit rozhraní API místně

Rozhraní API pro statické Web Apps služby Azure jsou napájená pomocí Azure Functions. Podrobnosti týkající se přidání rozhraní API do projektu statického Web Apps Azure najdete v tématu [Přidání rozhraní API do služby Azure static Web Apps s Azure Functions](add-api.md) .

V rámci procesu vytváření rozhraní API je pro Visual Studio Code vytvořena konfigurace spuštění. Tato konfigurace se nachází ve složce _. VSCode_ . Tato složka obsahuje všechna požadovaná nastavení pro místní sestavování a spouštění rozhraní API.

1. V Visual Studio Code spusťte rozhraní API stisknutím klávesy **F5** .

1. Otevře se nová instance Terminálové služby, která zobrazuje výstup z procesu sestavení rozhraní API.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="Rozhraní API běžící v Visual Studio Code terminálu":::

   Stavový řádek v Visual Studio Code je nyní oranžová. Tato barva indikuje, že rozhraní API je teď spuštěné a že je připojený ladicí program.

1. Potom stiskněte **kombinaci kláves Ctrl/Cmd** a kliknutím na adresu URL v terminálu otevřete okno prohlížeče, které volá rozhraní API.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Prohlížeč výsledek volání rozhraní API":::

### <a name="debugging-the-api"></a>Ladění rozhraní API

1. Otevřete soubor _API/GetMessage/index.js_ v Visual Studio Code.

1. Klikněte na levý okraj na řádku 2 a nastavte zarážku. Zobrazí se červená tečka, která indikuje, že je nastavená zarážka.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Zarážka v Visual Studio Code":::

1. V prohlížeči aktualizujte stránku běžící na <http://127.0.0.1:7071/api/message> .

1. Zarážka je v Visual Studio Code a spuštění programu je pozastavené.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Pozice zarážky v Visual Studio Code":::

   V Visual Studio Code pro vaše rozhraní API [je k dispozici kompletní prostředí ladění](https://code.visualstudio.com/Docs/editor/debugging) .

1. Pokračujte v provádění kliknutím na tlačítko **pokračovat** na panelu ladění.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Tlačítko pokračovat v Visual Studio Code":::

### <a name="calling-the-api-from-the-application"></a>Volání rozhraní API z aplikace

Po nasazení Azure static Web Apps automaticky mapuje tyto požadavky do koncových bodů ve složce _rozhraní API_ . Toto mapování zajišťuje, aby žádosti z aplikace do rozhraní API vypadaly jako v následujícím příkladu.

```javascript
let response = await fetch("/api/message");
```

V závislosti na tom, jestli je vaše aplikace sestavená pomocí rozhraní příkazového řádku jazyka JavaScript, existují dva způsoby, jak nakonfigurovat cestu k `api` trase při místním spuštění aplikace.

- Konfigurační soubory prostředí (doporučeno pro JavaScriptové architektury a knihovny)
- Místní proxy server

### <a name="environment-configuration-files"></a>Konfigurační soubory prostředí

Pokud vytváříte aplikaci s využitím front-endové architektury, které mají rozhraní příkazového řádku, měli byste použít konfigurační soubory prostředí. Jednotlivé architektury nebo knihovny mají jiný způsob zpracování těchto konfiguračních souborů prostředí. Je běžné mít konfigurační soubor pro vývoj, který se používá, když vaše aplikace běží místně, a jednu pro produkční prostředí, která se používá, když je vaše aplikace spuštěná v produkčním prostředí. Rozhraní příkazového řádku pro generátor JavaScriptu nebo statických webů, které používáte, bude automaticky znát použití vývojového souboru v místním počítači a produkční soubor, když je vaše aplikace sestavená pomocí statického Web Apps Azure.

V konfiguračním souboru pro vývoj můžete zadat cestu k rozhraní API, která odkazuje na místní umístění, `http:127.0.0.1:7071` kde rozhraní API vaší lokality běží místně.

```
API=http:127.0.0.1:7071/api
```

V konfiguračním souboru produkčního prostředí zadejte cestu k rozhraní API jako `api` . Tímto způsobem bude vaše aplikace při spuštění v produkčním prostředí volat rozhraní API prostřednictvím "yoursite.com/api".

```
API=api
```

Na tyto hodnoty konfigurace se dá odkazovat jako na proměnné prostředí uzlu v JavaScriptu pro webovou aplikaci.

```js
let response = await fetch(`${process.env.API}/message`);
```

Když se rozhraní příkazového řádku používá ke spuštění vašeho webu v režimu pro vývoj nebo při vytváření lokality pro produkční prostředí, `process.env.API` hodnota se nahradí hodnotou z příslušného konfiguračního souboru.

Další informace o konfiguraci souborů prostředí pro front-endové rozhraní JavaScript a knihovny najdete v těchto článcích:

- [Úhlové proměnné prostředí](https://angular.io/guide/build#configuring-application-environments)
- [Reagovat – přidávání vlastních proměnných prostředí](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue – režimy a proměnné prostředí](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Restartovat živý Server

1. Stisknutím **klávesy F1** otevřete paletu příkazů v Visual Studio Code.

1. Zadejte **živý Server** a vyberte **živý Server: zastavit živý Server**.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Příkaz Zastavit živý Server v paletě příkazů sady Visual Studio":::

1. Stisknutím klávesy **F1** otevřete paletu příkazů.

1. Zadejte **živý Server** a vyberte **živý Server: otevřít v Live serveru**.

1. Aktualizujte aplikaci běžící na `http://locahost:3000` . V prohlížeči se teď zobrazí zpráva vrácená z rozhraní API.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Hello z rozhraní API zobrazeného v prohlížeči":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace nastavení aplikace](application-settings.md)

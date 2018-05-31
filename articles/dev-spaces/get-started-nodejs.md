---
title: Vytvoření vývojového prostředí Kubernetes platformy Node.js v cloudu pomocí VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: deb651170b0fd58f8c89b591f3e42b5b629f4095
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361467"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Začínáme v Azure Dev Spaces s Node.js

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Teď můžete v Azure vytvořit vývojové prostředí založené na Kubernetes.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI
Služba Azure Dev Spaces vyžaduje minimální nastavení místního počítače. Většina konfigurace vývojového prostředí se ukládá do cloudu, aby bylo možné ji sdílet s ostatními uživateli. Napřed stáhněte a spusťte [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

> [!IMPORTANT]
> Pokud už máte Azure CLI nainstalované, ujistěte se, že používáte verzi 2.0.32 nebo vyšší.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Při čekání na vytvoření clusteru můžete začít psát kód.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Vytvoření kontejneru Node.js v Kubernetes

V této části vytvoříte webovou aplikaci Node.js a spustíte ji v kontejneru v Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Vytvoření webové aplikace Node.js
Stáhněte si kód z GitHubu. Přejděte na https://github.com/Azure/dev-spaces a vyberte **Clone or Download** (Klonovat nebo stáhnout). Úložiště GitHub se stáhne do vašeho místního prostředí. Kód tohoto průvodce je tady: `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Aktualizace souboru obsahu
Azure Dev Spaces neslouží jenom ke spuštění kódu v Kubernetes. Umožňuje také rychle opakovaně prohlížet změny kódu, které probíhají v prostředí Kubernetes v cloudu.

1. Najděte soubor `./public/index.html` a upravte kód HTML. Můžete třeba změnit barvu pozadí stránky na odstín modré:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Uložte soubor. Za chvilku se v okně terminálu zobrazí zpráva o aktualizaci souboru ve spuštěném kontejneru.
1. Přejděte do prohlížeče a aktualizujte stránku. Měli byste vidět novou barvu.

Co se stalo? Úpravy souborů obsahu, jako je HTML a CSS, nevyžadují restartování procesu Node.js. Aktivní příkaz `azds up` automaticky synchronizuje změněné soubory obsahu přímo se spuštěným kontejnerem v Azure, abyste si mohli rychle prohlédnout upravený obsah.

### <a name="test-from-a-mobile-device"></a>Testování na mobilním zařízení
Pokud webovou aplikaci otevřete na mobilním zařízení, můžete si všimnout, že na malém zařízení se uživatelské rozhraní nezobrazuje správně.

Opravíte to tak, že přidáte metaznačku `viewport`:
1. Otevřete soubor `./public/index.html`.
1. Do stávajícího prvku `head` přidejte metaznačku `viewport`:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Uložte soubor.
1. Aktualizujte prohlížeč zařízení. Webová aplikace by se měla vykreslit správně. 

Na tomto příkladu je vidět, že některé problémy nezjistíte, dokud aplikaci neotestujete na zařízení, pro které je určená. V Azure Dev Spaces můžete rychle iterovat kód a ověřovat změny na cílových zařízeních.

### <a name="update-a-code-file"></a>Aktualizace souboru s kódem
Aktualizace souborů s kódem na straně serveru je pracnější, protože aplikaci Node.js je potřeba restartovat.

1. V okně terminálu stiskněte `Ctrl+C` (kvůli zastavení `azds up`).
1. Otevřete soubor s kódem nazvaný `server.js` a změňte úvodní zprávu služby: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Uložte soubor.
1. Spusťte `azds up` v okně teminálu. 

Tím znovu sestavíte image kontejneru a znovu nasadíte Helm chart. Načtěte znovu stránku prohlížeče a podívejte se, jestli se promítly změny kódu.

Existuje ještě *rychlejší způsob* vývoje kódu, který si ukážeme v další části. 

## <a name="debug-a-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Výběr konfigurace AZDS pro ladění
1. Pokud chcete zobrazit ladění, klikněte na boku editoru VS Code na **panelu aktivit** na ikonu Ladění.
1. Jako aktivní konfiguraci ladění vyberte **Launch Program (AZDS)**.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> Pokud na paletě příkazů nevidíte příkazy Azure Dev Spaces, ověřte, že máte [nainstalované rozšíření VS Code pro Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes
Když chcete v Kubernetes ladit kód, stiskněte **F5**.

Podobně jako při příkazu `up` se na začátku ladění synchronizuje kód s vývojovým prostředím a sestaví se kontejner, který se nasadí v Kubernetes. Tentokrát se ale ladicí program připojí ke vzdálenému kontejneru.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

V serverovém souboru s kódem nastavte zarážku, třeba ve funkci `app.get('/api'...` v souboru `server.js`. Aktualizujte stránku prohlížeče nebo stiskněte aktualizační tlačítko. Měli byste se dostat k zarážce, abyste mohli procházet kód.

Máte plný přístup k informacím o ladění, jako je zásobník volání, místní proměnné, informace o výjimkách apod., úplně stejně jako při lokálním spuštění kódu.

### <a name="edit-code-and-refresh-the-debug-session"></a>Úprava kódu a aktualizace ladicí relace
V aktivním ladicím programu změňte kód. Můžete třeba znovu změnit text úvodní zprávy:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Uložte soubor a v **podokně akcí ladicího programu** klikněte na tlačítko **Aktualizovat**. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Místo opětovného sestavení a nasazení nové image kontejneru po každé provedené změně, což často dlouho trvá, restartuje služba Azure Dev Spaces mezi ladicími relacemi proces Node.js, aby se zrychlil cyklus úprav a ladění.

Aktualizujte webovou aplikaci v prohlížeči nebo stiskněte tlačítko *Aktualizovat*. V uživatelském rozhraní by se měla zobrazit vaše upravená zpráva.

### <a name="use-nodemon-to-develop-even-faster"></a>Zrychlení vývoje nástrojem NodeMon
*Nodemon* je oblíbený nástroj, který používají vývojáři aplikací Node.js k rychlejšímu vývoji. Místo ručního restartování procesu Node po každé úpravě kódu na straně serveru vývojáři často nakonfigurují projekt Node tak, aby změny souboru monitoroval nástroj *nodemon*, který automaticky restartuje procesy na serveru. Při tomto stylu práce vývojář po úpravě kódu jenom aktualizuje prohlížeč.

V Azure Dev Spaces můžete používat řadu stejných vývojových pracovních postupů, jaké používáte při místním vývoji. Abychom to ilustrovali, je ukázkový projekt `webfrontend` nakonfigurovaný tak, aby používal nástroj *nodemon* (je nakonfigurovaný jako vývojová závislost v souboru `package.json`).

Vyzkoušejte následující kroky:
1. Zastavte ladicí program VS Code.
1. Na boku editoru VS Code na **panelu aktivit** klikněte na ikonu Ladění. 
1. Jako aktivní konfiguraci ladění vyberte **Attach (AZDS)** (Připojit (AZDS)).
1. Stiskněte F5.

Při této konfiguraci kontejner spustí *nodemon*. Po úpravě kódu na straně serveru nástroj *nodemon* automaticky restartuje proces Node úplně stejně, jako při místním vývoji. 
1. Znovu upravte úvodní zprávu v souboru `server.js` a pak soubor uložte.
1. Aktualizujte prohlížeč nebo klikněte na tlačítko *Aktualizovat*, aby se změny promítly.

**Teď máte metodu, jak rychle provádět iteraci kódu a jeho ladění v Kubernetes.** Příště si ukážeme, jak vytvářet a volat druhý kontejner.

## <a name="call-a-service-running-in-a-separate-container"></a>Volání služby spuštěné v samostatném kontejneru

V této části vytvoříte druhou službu `mywebapi`, kterou bude `webfrontend` volat. Každá služba poběží v samostatném kontejneru. Ladění pak provedete v obou kontejnerech.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Otevření ukázkového kódu *mywebapi*
Ve složce `samples` už byste měli mít k tomuto průvodci ukázkový kód pro `mywebapi` (pokud ne, přejděte na https://github.com/Azure/dev-spaces a vyberte **Clone or Download** (Klonovat nebo stáhnout), abyste stáhli úložiště GitHubu). Kód k této části je tady: `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. V *samostatném okně editoru VS Code* otevřete složku `mywebapi`.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Připravenou službu poznáte podle toho, že se zobrazí panel ladění editoru VS Code.
1. Poznamenejte si adresu URL koncového bodu, která by měla vypadat zhruba takto http://localhost:\<portnumber\>. **Tip: Na stavovém řádku editoru VS Code se zobrazí adresa URL, na kterou můžete kliknout.** I když to vypadá, že kontejner je spuštěný v místním prostředí, ve skutečnosti je spuštěný ve vývojovém prostředí v Azure. Důvodem adresy místního hostitele je, že služba `mywebapi` nemá definované veřejné koncové body, a proto je přístupná jenom z instance Kubernetes. Kvůli jednodušší práci a interakci se soukromou službou z místního počítače vytvoří Azure Dev Spaces do kontejneru se spuštěným Azure dočasný tunel SSH.
1. Až bude služba `mywebapi` připravená, otevřete v prohlížeči adresu místního hostitele. Měla by se zobrazit odpověď služby `mywebapi` („Hello from mywebapi“).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Vytvoření požadavku z *webfrontend* do *mywebapi*
Teď napíšeme kód v projektu `webfrontend`, který vygeneruje požadavek do `mywebapi`.
1. Přepněte se do okna editoru VS Code s projektem `webfrontend`.
1. Na začátek souboru `server.js` přidejte tyto řádky kódu:
    ```javascript
    var request = require('request');
    var propagateHeaders = require('./propagateHeaders');
    ```

3. *Nahraďte* kód obslužné rutiny GET `/api`. Tato rutina při zpracování požadavku zase volá službu `mywebapi` a následně vrátí výsledky oběma službám.

    ```javascript
    app.get('/api', function (req, res) {
        request({
            uri: 'http://mywebapi',
            headers: propagateHeaders.from(req) // propagate headers to outgoing requests
        }, function (error, response, body) {
            res.send('Hello from webfrontend and ' + body);
        });
    });
    ```

Všimněte si, jak se v Kubernetes používá zjišťování služby DNS, aby odkaz na službu vypadal takto: `http://mywebapi`. **Ve vývojovém prostředí běží kód stejně jako v produkčním prostředí**.

V předchozí ukázce kódu je použit pomocný modul s názvem `propagateHeaders`. Tento pomocný modul byl do kódu přidaný v okamžiku spuštění `azds prep`. Funkce `propagateHeaders.from()` rozšíří konkrétní hlavičky ze stávajícího objektu http.IncomingMessage do objektu hlaviček odchozího požadavku. Později si ukážeme, jak to týmům pomáhá při společném vývoji.

### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. Nastavte zarážku ve výchozí obslužné rutině GET `/`.
1. V projektu `webfrontend` nastavte zarážku těsně před odesláním požadavku GET do `http://mywebapi`.
1. V projektu `webfrontend` stiskněte klávesu F5.
1. Otevřete webovou aplikaci a projděte kód obou služeb. Ve webové aplikaci by se měla zobrazit zpráva vytvořená spojením řetězců dvou služeb: „Hello from webfrontend and Hello from mywebapi.“

Hotovo! Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

[!INCLUDE[](includes/team-development-1.md)]

Podívejte se, jak to funguje:
1. Přejděte do okna editoru VS Code s projektem `mywebapi` a upravte kód výchozí obslužné rutiny GET `/` například takto:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]





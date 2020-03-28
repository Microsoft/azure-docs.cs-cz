---
title: 'Spuštění více závislých služeb: Soubor Node.js & kódu sady Visual Studio'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Tento kurz ukazuje, jak pomocí Azure Dev Spaces a Visual Studio Code ladit vícestránkovou aplikaci Node.js ve službě Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
ms.openlocfilehash: a5fa0aae3a966dd96ee95e6bcafc3b2eec4e6837
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438297"
---
# <a name="running-multiple-dependent-services-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Spuštění více závislých služeb: Node.js a Visual Studio Code s Azure Dev Spaces

V tomto kurzu se dozvíte, jak vyvíjet aplikace s více službami pomocí Azure Dev Spaces, spolu s některými dalšími výhodami, které dev Spaces poskytuje.

## <a name="call-a-service-running-in-a-separate-container"></a>Volání služby spuštěné v samostatném kontejneru

V této části vytvoříte druhou službu `mywebapi`, kterou bude `webfrontend` volat. Každá služba poběží v samostatném kontejneru. Ladění pak provedete v obou kontejnerech.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Otevření ukázkového kódu *mywebapi*
Vzorový kód `mywebapi` pro tuto příručku byste již `samples` měli mít pod https://github.com/Azure/dev-spaces složky s názvem (pokud ne, přejděte na možnost **Klonovat nebo Stáhnout** a stáhnout úložiště GitHub.) Kód pro tuto část `samples/nodejs/getting-started/mywebapi`je v .

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. V *samostatném okně editoru VS Code* otevřete složku `mywebapi`.
1. Otevřete **paletu příkazů** (pomocí nabídky **Zobrazit | Paleta příkazů**) a pomocí automatického dokončování zadejte a vyberte tento příkaz: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Nezaměňujte tento příkaz s příkazem `azds prep`, který projekt nakonfiguruje pro nasazení.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Budete vědět, že je připraven, když *naslouchání na portu 80* zpráva se zobrazí v ladicí konzole.
1. Poznamenejte si adresu URL koncového bodu, která by měla vypadat zhruba takto `http://localhost:<portnumber>`. **Tip: Stavový řádek kódu VS se změní na oranžovou barvu a zobrazí adresu URL, na kterou lze kliknout.** I když to vypadá, že kontejner je spuštěný v místním prostředí, ve skutečnosti je spuštěný ve vývojovém prostředí v Azure. Důvodem adresy místního hostitele je, že služba `mywebapi` nemá definované veřejné koncové body, a proto je přístupná jenom z instance Kubernetes. Kvůli jednodušší práci a interakci se soukromou službou z místního počítače vytvoří Azure Dev Spaces do kontejneru se spuštěným Azure dočasný tunel SSH.
1. Až bude služba `mywebapi` připravená, otevřete v prohlížeči adresu místního hostitele. Měla by se zobrazit odpověď služby `mywebapi` („Hello from mywebapi“).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Vytvoření požadavku z *webfrontend* do *mywebapi*
Teď napíšeme kód v projektu `webfrontend`, který vygeneruje požadavek do `mywebapi`.
1. Přepněte se do okna editoru VS Code s projektem `webfrontend`.
1. Na začátek souboru `server.js` přidejte tyto řádky kódu:
    ```javascript
    var request = require('request');
    ```

3. *Nahraďte* kód obslužné rutiny GET `/api`. Tato rutina při zpracování požadavku zase volá službu `mywebapi` a následně vrátí výsledky oběma službám.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Odeberte*`server.close()` řádek na konci `server.js`

Předchozí příklad kódu předává hlavičku `azds-route-as` z příchozího požadavku do odchozího požadavku. Později si ukážeme, jak to týmům pomáhá při společném vývoji.

### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. Nastavte zarážku uvnitř `/` výchozí obslužné rutiny GET [na řádku 8 aplikace `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. V projektu `webfrontend` nastavte zarážku těsně před odesláním požadavku GET do `http://mywebapi`.
1. V projektu `webfrontend` stiskněte klávesu F5.
1. Otevřete webovou aplikaci a projděte kód obou služeb. Ve webové aplikaci by se měla zobrazit zpráva vytvořená spojením řetězců dvou služeb: „Hello from webfrontend and Hello from mywebapi.“

### <a name="well-done"></a>Hotovo!
Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o vývoji týmu v Dev Spaces](team-development-nodejs.md)

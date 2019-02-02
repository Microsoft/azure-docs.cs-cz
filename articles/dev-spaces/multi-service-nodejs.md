---
title: Spuštění více závislých služeb pomocí Node.js a VS Code | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: f3dbe8c62cb1fcc0585b5abccc51a620ea713543
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666383"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Vývoj pro víc služeb prostory Azure Dev

V tomto kurzu se naučíte vyvíjet víc služeb aplikací s použitím Azure Dev mezery, společně s dalšími výhodami, které poskytuje vývoje prostorů.

## <a name="call-a-service-running-in-a-separate-container"></a>Volání služby spuštěné v samostatném kontejneru

V této části vytvoříte druhou službu `mywebapi`, kterou bude `webfrontend` volat. Každá služba poběží v samostatném kontejneru. Ladění pak provedete v obou kontejnerech.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Otevření ukázkového kódu *mywebapi*
Ve složce `samples` už byste měli mít k tomuto průvodci ukázkový kód pro `mywebapi` (pokud ne, přejděte na https://github.com/Azure/dev-spaces a vyberte **Clone or Download** (Klonovat nebo stáhnout), abyste stáhli úložiště GitHubu). Kód k této části je tady: `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. V *samostatném okně editoru VS Code* otevřete složku `mywebapi`.
1. Otevřete **paletu příkazů** (pomocí nabídky **Zobrazit | Paleta příkazů**) a pomocí automatického dokončování zadejte a vyberte tento příkaz: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Nezaměňujte tento příkaz s příkazem `azds prep`, který projekt nakonfiguruje pro nasazení.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Připravenou službu poznáte podle toho, že se zobrazí panel ladění editoru VS Code.
1. Poznamenejte si adresu URL koncového bodu, která by měla vypadat zhruba takto http://localhost:\<portnumber\>. **Tip: Stavový řádek VS Code se zobrazí po kliknutí adresy URL.** I když to vypadá, že kontejner je spuštěný v místním prostředí, ve skutečnosti je spuštěný ve vývojovém prostředí v Azure. Důvodem adresy místního hostitele je, že služba `mywebapi` nemá definované veřejné koncové body, a proto je přístupná jenom z instance Kubernetes. Kvůli jednodušší práci a interakci se soukromou službou z místního počítače vytvoří Azure Dev Spaces do kontejneru se spuštěným Azure dočasný tunel SSH.
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
 4. *Odeberte* `server.close()` řádek na konci `server.js`

Předchozí příklad kódu předává hlavičku `azds-route-as` z příchozího požadavku do odchozího požadavku. Později si ukážeme, jak to týmům pomáhá při společném vývoji.

### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. Nastavte zarážku ve výchozí obslužné rutině GET `/`.
1. V projektu `webfrontend` nastavte zarážku těsně před odesláním požadavku GET do `http://mywebapi`.
1. V projektu `webfrontend` stiskněte klávesu F5.
1. Otevřete webovou aplikaci a projděte kód obou služeb. Webová aplikace by se zobrazit zpráva zřetězit dvě služby: "Hello webfrontend a Hello z mywebapi."

### <a name="automatic-tracing-for-http-messages"></a>Automatické trasování pro zprávy protokolu HTTP
Mohli jste si všimnout, že i když *webfrontend* neobsahuje všechny speciální kód, který vytiskne provádí volání HTTP *mywebapi*, můžete zobrazit zprávy v okně výstup trasování protokolu HTTP:
```
// The request from your browser
webfrontend.<id>.<region>.aksapp.io --hyh-> webfrontend:
   GET /api?_=1544485357627 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --1b1-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-1b1-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
webfrontend.<id>.<region>.aksapp.io <-hyh-- webfrontend:
   HTTP/1.1 200 OK
   Hello from webfrontend and Hello from mywebapi
```
Toto je jednou z výhod "free", který jste získali z instrumentace Dev mezery. Jsme vložit součásti, které sledují požadavky HTTP, které procházejí systému zjednodušit sledování hovorů komplexní víc služeb během vývoje.

### <a name="well-done"></a>Hotovo!
Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o vývoj v týmu v prostorách vývoj](team-development-nodejs.md)
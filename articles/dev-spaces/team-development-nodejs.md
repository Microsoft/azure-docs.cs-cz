---
title: Týmový vývoj se službou Azure Dev Spaces pomocí VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 8a7200723b39c40fe9596bbab0acaf752acbbaf8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451389"
---
# <a name="team-development-with-azure-dev-spaces"></a>Týmový vývoj se službou Azure Dev Spaces

V tomto kurzu zjistíte, jak pomocí vývojových prostorů pracovat v různých vývojových prostředích současně. Různou práci přitom budete uchovávat v samostatných vývojových prostorech na stejném clusteru.

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

Hotovo! Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

[!INCLUDE [](../../includes/team-development-1.md)]

Podívejte se, jak to funguje:
1. Přejděte do okna editoru VS Code s projektem `mywebapi` a upravte kód výchozí obslužné rutiny GET `/` například takto:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí

Teď když jste prozkoumali Azure Dev Spaces, [podělte se o svůj vývojářský prostor se členy týmu](how-to/share-dev-spaces.md) a ukažte jim, jak snadná je vzájemná spolupráce.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```





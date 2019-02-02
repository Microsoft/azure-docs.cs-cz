---
title: Spuštění více závislé služby pomocí Javy a VS Code | Dokumentace Microsoftu
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
manager: yuvalm
ms.openlocfilehash: 0bc680b47a85834886b8d7875968eb4b9b12a870
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666399"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Vývoj pro víc služeb prostory Azure Dev

V tomto kurzu se naučíte vyvíjet víc služeb aplikací s použitím Azure Dev mezery, společně s dalšími výhodami, které poskytuje vývoje prostorů.

## <a name="call-a-service-running-in-a-separate-container"></a>Volání služby spuštěné v samostatném kontejneru

V této části vytvoříte druhou službu `mywebapi`, kterou bude `webfrontend` volat. Každá služba poběží v samostatném kontejneru. Ladění bude probíhat v obou kontejnerech.

![Více kontejnerů](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Stažení ukázkového kódu pro *mywebapi*
Kvůli úspoře času si můžete ukázkový kód stáhnout z úložiště GitHub. Přejděte na https://github.com/Azure/dev-spaces a stáhněte si úložiště GitHub výběrem možnosti **Clone or Download** (Klonovat nebo stáhnout). Kód k této části je tady: `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. V *samostatném okně editoru VS Code* otevřete složku `mywebapi`.
1. Otevřete **paletu příkazů** (pomocí nabídky **Zobrazit | Paleta příkazů**) a pomocí automatického dokončování zadejte a vyberte tento příkaz: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Připravenou službu poznáte podle toho, že se zobrazí panel ladění editoru VS Code.
1. Adresa URL koncového bodu bude přibližně takto: http://localhost:\<portnumber\>. **Tip: Stavový řádek VS Code se zobrazí po kliknutí adresy URL.** I když to vypadá, že kontejner je spuštěný lokálně, ve skutečnosti je spuštěný ve vývojovém prostoru v Azure. Důvodem adresy místního hostitele je, že služba `mywebapi` nemá definované veřejné koncové body, a proto je přístupná jenom z instance Kubernetes. Kvůli jednodušší práci a interakci se soukromou službou z místního počítače vytvoří Azure Dev Spaces do kontejneru se spuštěným Azure dočasný tunel SSH.
1. Až bude služba `mywebapi` připravená, otevřete v prohlížeči adresu místního hostitele.
1. Pokud byly všechny kroky úspěšné, měla by se zobrazit odpověď služby `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Vytvoření požadavku z *webfrontend* do *mywebapi*
Teď napíšeme kód v projektu `webfrontend`, který vygeneruje požadavek do `mywebapi`.
1. Přepněte se do okna editoru VS Code s projektem `webfrontend`.
1. V rámci příkazu `package` *přidejte* následující příkazy `import`:

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Nahraďte* kód metody pozdravu:

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

Předchozí příklad kódu předává hlavičku `azds-route-as` z příchozího požadavku do odchozího požadavku. Později si ukážeme, jak to týmům pomáhá při společném vývoji.

### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. V metodě `index()` projektu `webapi` nastavte zarážku.
1. V projektu `webfrontend` na řádku začínajícím `try` nastavte zarážku těsně před odesláním požadavku GET do `mywebapi`.
1. V projektu `webfrontend` stiskněte F5 (nebo restartujte ladicí program, pokud je aktuálně spuštěný).
1. Otevřete webovou aplikaci a projděte kód obou služeb.
1. Ve webové aplikaci na stránce o se zobrazí zpráva zřetězit dvě služby: "Hello webfrontend a Hello z mywebapi."

### <a name="automatic-tracing-for-http-messages"></a>Automatické trasování pro zprávy protokolu HTTP
Mohli jste si všimnout, že i když *webfrontend* neobsahuje všechny speciální kód, který vytiskne provádí volání HTTP *mywebapi*, můžete zobrazit zprávy v okně výstup trasování protokolu HTTP:
```
// The request from your browser
webfrontend.856bb3af715744c6810b.eastus.aksapp.io --ytv-> webfrontend:8080:
   GET /greeting?_=1544503627515 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --ve4-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-ve4-- mywebapi:
   HTTP/1.1 200
   Hello from mywebapi

// Response from *webfrontend* to your browser
webfrontend.856bb3af715744c6810b.eastus.aksapp.io <-ytv-- webfrontend:8080:
   HTTP/1.1 200
   Hello from webfrontend and
   Hello from mywebapi
```
Toto je jednou z výhod "free", který jste získali z instrumentace Dev mezery. Jsme vložit součásti, které sledují požadavky HTTP, které procházejí systému zjednodušit sledování hovorů komplexní víc služeb během vývoje.

### <a name="well-done"></a>Hotovo!
Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o vývoj v týmu v prostorách vývoj](team-development-java.md)
---
title: 'Spuštění více závislých služeb: & .NET Core Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: V tomto kurzu se dozvíte, jak pomocí Azure Dev Spaces a Visual Studio Code ladit aplikaci .NET Core s více službami ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.openlocfilehash: 0bbb1aefe517c45207160b83b89f7207e8909666
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438316"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>Spuštění více závislých služeb: .NET Core a Visual Studio Code s Azure Dev Spaces

V tomto kurzu se naučíte vyvíjet aplikace s více službami pomocí Azure Dev Spaces společně s dalšími výhodami, které poskytují vývojové prostory.

## <a name="call-a-service-running-in-a-separate-container"></a>Volání služby spuštěné v samostatném kontejneru

V této části vytvoříte druhou službu, `mywebapi`a budete ji `webfrontend` volat. Každá služba poběží v samostatném kontejneru. Ladění pak provedete v obou kontejnerech.

![Více kontejnerů](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Stažení ukázkového kódu pro *mywebapi*
Kvůli úspoře času si můžete ukázkový kód stáhnout z úložiště GitHub. Přejděte na https://github.com/Azure/dev-spaces a stáhněte si úložiště GitHub výběrem možnosti **Clone or Download** (Klonovat nebo stáhnout). Kód k této části je tady: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. V *samostatném okně editoru VS Code* otevřete složku `mywebapi`.
1. Otevřete **paletu příkazů** (pomocí nabídky **Zobrazit | Paleta příkazů**) a pomocí automatického dokončování zadejte a vyberte tento příkaz: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Nezaměňujte tento příkaz s příkazem `azds prep`, který projekt nakonfiguruje pro nasazení.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Budete mít jistotu, že je připravená, jakmile se *aplikace spustí. Pro vypnutí stiskněte kombinaci kláves CTRL + C.* zpráva se zobrazí v konzole ladění.
1. Adresa URL koncového bodu bude přibližně takto: `http://localhost:<portnumber>`. **Tip: VS Code stavový řádek se změní na oranžová a zobrazí se adresa URL pro kliknutí.** I když to vypadá, že kontejner je spuštěný lokálně, ve skutečnosti je spuštěný ve vývojovém prostoru v Azure. Důvodem adresy místního hostitele je, že služba `mywebapi` nemá definované veřejné koncové body, a proto je přístupná jenom z instance Kubernetes. Kvůli jednodušší práci a interakci se soukromou službou z místního počítače vytvoří Azure Dev Spaces do kontejneru se spuštěným Azure dočasný tunel SSH.
1. Až bude služba `mywebapi` připravená, otevřete v prohlížeči adresu místního hostitele. Pokud chcete volat výchozí GET API pro `ValuesController`, připojte k URL kontroler `/api/values`.
1. Pokud byly všechny kroky úspěšné, měla by se zobrazit odpověď služby `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Vytvoření požadavku z *webfrontend* do *mywebapi*
Teď napíšeme kód v projektu `webfrontend`, který vygeneruje požadavek do `mywebapi`.
1. Přepněte se do okna editoru VS Code s projektem `webfrontend`.
1. *Nahraďte* kód metody About v `HomeController.cs`:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

        return View();
    }
    ```

Předchozí příklad kódu předává hlavičku `azds-route-as` z příchozího požadavku do odchozího požadavku. Později uvidíte, jak to pomáhá týmům s [vývojem ve spolupráci](team-development-netcore.md).

### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. Nastavte zarážku uvnitř metody `Get(int id)`, která zpracovává žádosti `api/values/{id}` GET. Je to kolem [řádku 23 v souboru *Controllers/ValuesController. cs* ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23).
1. V projektu `webfrontend` nastavte zarážku těsně před odesláním požadavku GET do `mywebapi/api/values`. To je přibližně řádek 32 v souboru [ *Controllers/HomeController. cs* ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs) , který jste upravili v předchozí části.
1. V projektu `webfrontend` stiskněte klávesu F5.
1. Otevřete webovou aplikaci a projděte kód obou služeb.
1. Ve webové aplikaci se na stránce O aplikaci bude zobrazovat zpráva vzniklá zřetězením ze dvou služeb: „Hello from webfrontend and Hello from mywebapi.“

### <a name="well-done"></a>Hotovo!
Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o vývoji týmu ve vývojových prostorech](team-development-netcore.md)

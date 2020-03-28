---
title: 'Spuštění více závislých služeb: .NET Core & Visual Studio'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Tento kurz ukazuje, jak pomocí Azure Dev Spaces a Visual Studio ladit víceslužbou .NET Core aplikace ve službě Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
ms.openlocfilehash: 7f95c21c2cf5b7adcdb34d7bbe2b1f8314c20333
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438390"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>Spuštění více závislých služeb: .NET Core a Visual Studio s Azure Dev Spaces

V tomto kurzu se dozvíte, jak vyvíjet aplikace s více službami pomocí Azure Dev Spaces, spolu s některými dalšími výhodami, které dev Spaces poskytuje.

## <a name="call-another-container"></a>Volání jiného kontejneru
V této části vytvoříte druhou službu `mywebapi`a budete `webfrontend` ji mít zavolejte. Každá služba poběží v samostatném kontejneru. Ladění pak provedete v obou kontejnerech.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Stažení ukázkového kódu pro *mywebapi*
Kvůli úspoře času si můžete ukázkový kód stáhnout z úložiště GitHub. Přejděte na https://github.com/Azure/dev-spaces a stáhněte si úložiště GitHub výběrem možnosti **Clone or Download** (Klonovat nebo stáhnout). Kód k této části je tady: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. Otevřete službu `mywebapi` projektu v *samostatném okně sady Visual Studio*.
1. Vyberte v rozevíracím okně nastavení spouštění **Azure Dev Spaces**, jak jste to udělali už dříve pro projekt `webfrontend`. Tentokrát místo vytváření nového clusteru AKS vyberte cluster, který jste už vytvořili. Stejně jako dříve nechte v rozevíracím seznamu Space (Prostor) vybranou položku `default` a klikněte na **OK**. V okně Výstup můžete zaznamenat, že Visual Studio začne "zahřívat" tuto novou službu v prostoru pro spuštění, aby se urychlily věci při spuštění ladění.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Až se barva stavového řádku sady Visual Studio změní na oranžovou, bude služba připravená.
1. Poznamenejte si adresu URL koncového bodu zobrazenou v podokně **Azure Dev Spaces for AKS** v okně **Výstup.** Bude vypadat přibližně takto: `http://localhost:<portnumber>`. I když to může vypadat, že kontejner je spuštěný lokálně, ve skutečnosti je spuštěný ve vývojovém prostoru v Azure.
2. Až se `mywebapi` připraví, otevřete v prohlížeči adresu místního hostitele a k adrese URL připojte `/api/values`, aby se vyvolalo výchozí rozhraní GET API pro `ValuesController`. 
3. Pokud byly všechny kroky úspěšné, měla by se zobrazit odpověď ze služby `mywebapi`, která vypadá takto:

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Vytvoření požadavku z *webfrontend* do *mywebapi*
Teď napíšeme kód v projektu `webfrontend`, který vygeneruje požadavek do `mywebapi`. Přejděte do okna sady Visual Studio, ve kterém je projekt `webfrontend`. V `HomeController.cs` souboru *nahraďte* kód metody About následujícím kódem:

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

Předchozí příklad kódu předává hlavičku `azds-route-as` z příchozího požadavku do odchozího požadavku. Později uvidíte, jak to usnadňuje produktivnější vývoj v [týmových scénářích](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. V souboru `Controllers/ValuesController.cs`, který zpracovává požadavky GET `api/values/{id}`, nastavte zarážku v metodě `Get(int id)`.
1. V projektu `webfrontend`, do kterého jste vložili výše uvedený kód, nastavte zarážku těsně před odesláním požadavku GET do `mywebapi/api/values`.
1. V projektu `webfrontend` stiskněte klávesu F5. Visual Studio znovu otevře v prohlížeči příslušný port místního hostitele a zobrazí se daná webová aplikace.
1. Zarážku v projektu `webfrontend` aktivujte kliknutím na odkaz **O aplikaci** v horní části stránky. 
1. Pokračujte stisknutím klávesy F10. Zarážka v projektu `mywebapi` teď bude aktivovaná.
1. Pokračujte stisknutím klávesy F5, abyste se vrátili ke kódu v projektu `webfrontend`.
1. Dalším stisknutím klávesy F5 se dokončí daný požadavek a vrátí se stránka v prohlížeči. Ve webové aplikaci se na stránce O aplikaci bude zobrazovat zpráva vzniklá zřetězením ze dvou služeb: „Hello from webfrontend and Hello from mywebapi.“

### <a name="well-done"></a>Hotovo!
Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o vývoji týmu v Dev Spaces](team-development-netcore-visualstudio.md)

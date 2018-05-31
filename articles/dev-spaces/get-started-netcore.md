---
title: Vytvoření vývojového prostředí Kubernetes v cloudu pomocí .NET Core a VS Code| Microsoft Docs
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
ms.openlocfilehash: a57118feb85a010e38d73b758ebfb84d1cc463fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361246"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Začínáme s .NET Core v Azure Dev Spaces

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Teď můžete v Azure vytvořit vývojové prostředí založené na Kubernetes.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI
Azure Dev Spaces vyžaduje minimální nastavení místního počítače. Většina konfigurace vývojového prostředí se ukládá do cloudu, aby bylo možné ji sdílet s ostatními uživateli. Nejdřív si stáhněte a spusťte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Pokud už máte rozhraní příkazového řádku Azure nainstalované, ujistěte se, že používáte verzi 2.0.32 nebo vyšší.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Při čekání na vytvoření clusteru můžete začít s vývojem kódu.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core
Pokud jste si nainstalovali [.NET Core](https://www.microsoft.com/net), můžete ve složce `webfrontend` rychle vytvořit webovou aplikaci ASP.NET Core.
    
```cmd
dotnet new mvc --name webfrontend
```

Nebo si **stáhněte ukázkový kód z GitHubu**. Přejděte na https://github.com/Azure/dev-spaces a vyberte **Clone or Download** (Klonovat nebo stáhnout), abyste úložiště GitHub stáhli do místního prostředí. Kód tohoto průvodce je tady: `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>Aktualizace souboru obsahu
Azure Dev Spaces neslouží jenom ke spuštění kódu v prostředí Kubernetes. Umožňuje také rychle opakovaně prohlížet změny kódu, ke kterým dochází v prostředí Kubernetes v cloudu.

1. Najděte soubor `./Views/Home/Index.cshtml` a upravte kód HTML. Můžete změnit řádek 70, na kterém je `<h2>Application uses</h2>`, třeba takto: `<h2>Hello k8s in Azure!</h2>`
1. Uložte soubor. Za chvilku se v okně terminálu zobrazí zpráva o aktualizaci souboru ve spuštěném kontejneru.
1. Přejděte do prohlížeče a aktualizujte stránku. Na webové stránce by se měl zobrazit aktualizovaný kód HTML.

Co se stalo? Úpravy obsahových souborů jako HTML a CSS nevyžadují rekompilaci ve webové aplikaci .NET Core. Aktivní příkaz `azds up` totiž automaticky synchronizuje všechny upravené soubory obsahu do kontejneru spuštěného v Azure, abyste změny obsahu viděli okamžitě.

## <a name="update-a-code-file"></a>Aktualizace souboru s kódem
Aktualizace souborů s kódem je o něco pracnější, protože aplikace .NET Core musí znovu sestavit a vytvořit aktualizované binární soubory aplikace.

1. V okně terminálu stiskněte `Ctrl+C`, abyste zastavili `azds up`.
1. Otevřete soubor s kódem, který se jmenuje `Controllers/HomeController.cs`, a upravte zprávu, která se zobrazí na stránce O aplikaci: `ViewData["Message"] = "Your application description page.";`
1. Uložte soubor.
1. V okně terminálu spusťte `azds up`. 

Tento příkaz znovu sestaví image kontejneru a znovu nasadí Helm chart. Pokud chcete vidět, jak se změny kódu projevily v běžící aplikaci, přejděte do nabídky O aplikaci webové aplikace.


Existuje ještě *rychlejší způsob* vývoje kódu, který si ukážeme v další části. 

## <a name="debug-a-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>Výběr konfiguraci AZDS pro ladění
1. Pokud chcete zobrazit ladění, klikněte na boku editoru VS Code na **panelu aktivit** na ikonu Ladění.
1. Jako aktivní konfiguraci ladění vyberte **.NET Core Launch (AZDS)** (Spustit .NET Core (AZDS)).

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Pokud na paletě příkazů nevidíte příkazy Azure Dev Spaces, ověřte, že máte nainstalované rozšíření VS Code pro Azure Dev Spaces. Ujistěte se, že pracovní prostor, který jste otevřeli ve VS Code, je složka obsahující soubor azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes
Když chcete v Kubernetes ladit kód, stiskněte **F5**.

Stejně jako u příkazu `up` se kód synchronizuje s vývojovým prostředím a sestaví se kontejner, který se nasadí v Kubernetes. Ladicí program se tentokrát samozřejmě připojí ke vzdálenému kontejneru.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

V serverovém souboru s kódem nastavte zarážku, třeba ve funkci `Index()` ve zdrojovém souboru `Controllers/HomeController.cs`. Aktualizace stránky prohlížeče způsobí aktivaci zarážky.

Máte plný přístup k informacím o ladění, jako je zásobník volání, místní proměnné, informace o výjimkách apod., stejně jako při lokálním spuštění kódu.

### <a name="edit-code-and-refresh"></a>Úprava a aktualizace kódu
V aktivním ladicím programu upravte kód. Můžete třeba změnit zprávu na stránce O aplikaci v `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Uložte soubor a v **podokně akcí ladicího programu** klikněte na tlačítko **Aktualizovat**. 

![](media/get-started-netcore/debug-action-refresh.png)

Místo opětovného sestavení a nasazení nové image kontejneru po každé provedené změně, což často dlouho trvá, rekompiluje služba Azure Dev Spaces kód po přírůstcích ve stávajícím kontejneru, aby se zrychlil cyklus úprav/ladění.

Aktualizujte webovou aplikaci v prohlížeči a přejděte na stránku O aplikaci. V uživatelském rozhraní by se měla zobrazit vaše upravená zpráva.

**Teď máte metodu, jak rychle provádět iteraci kódu a jeho ladění v Kubernetes.** Příště si ukážeme, jak vytvořit a volat druhý kontejner.

## <a name="call-a-service-running-in-a-separate-container"></a>Volání služby spuštěné v samostatném kontejneru

V této části vytvoříte druhou službu `mywebapi`, kterou bude `webfrontend` volat. Každá služba poběží v samostatném kontejneru. Ladění bude probíhat v obou kontejnerech.

![Více kontejnerů](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Stažení ukázkového kódu pro *mywebapi*
Kvůli úspoře času si můžete ukázkový kód stáhnout z úložiště GitHub. Přejděte na https://github.com/Azure/dev-spaces a stáhněte si úložiště GitHub výběrem možnosti **Clone or Download** (Klonovat nebo stáhnout). Kód k této části je tady: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. V *samostatném okně editoru VS Code* otevřete složku `mywebapi`.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Připravenou službu poznáte podle toho, že se zobrazí panel ladění editoru VS Code.
1. Poznamenejte si adresu URL koncového bodu, která by měla vypadat zhruba takto http://localhost:\<portnumber\>. **Tip: Na stavovém řádku editoru VS Code se zobrazí adresa URL, na kterou můžete kliknout.** I když to vypadá, že kontejner je spuštěný lokálně, ve skutečnosti je spuštěný ve vývojovém prostředí Azure. Důvodem adresy místního hostitele je, že služba `mywebapi` nemá definované veřejné koncové body, a proto je přístupná jenom z instance Kubernetes. Kvůli jednodušší práci a interakci se soukromou službou z místního počítače vytvoří Azure Dev Spaces do kontejneru se spuštěným Azure dočasný tunel SSH.
1. Až bude služba `mywebapi` připravená, otevřete v prohlížeči adresu místního hostitele. Pokud chcete volat výchozí GET API pro `ValuesController`, připojte k URL kontroler `/api/values`. 
1. Pokud byly všechny kroky úspěšné, měla by se zobrazit odpověď služby `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Vytvoření požadavku z *webfrontend* do *mywebapi*
Teď napíšeme kód v projektu `webfrontend`, který vygeneruje požadavek do `mywebapi`.
1. Přepněte se do okna editoru VS Code s projektem `webfrontend`.
1. *Nahraďte* kód metody About:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
        // headers in the incoming request to any outgoing requests
        using (var client = new HeaderPropagatingHttpClient(this.Request))
        {
            // Call *mywebapi*, and display its response in the page
            var response = await client.GetAsync("http://mywebapi/api/values/1");
            ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
        }

        return View();
    }
    ```

Všimněte si, jak se v Kubernetes používá zjišťování služby DNS, aby odkaz na službu vypadal takto: `http://mywebapi`. **Ve vývojovém prostředí běží kód stejně jako v produkčním prostředí**.

V předchozí ukázce kódu se také používá třída `HeaderPropagatingHttpClient`. Tato pomocná třída byla do složky kódu přidána v okamžiku spuštění `azds prep`. Třída `HeaderPropagatingHttpClient` je odvozená ze známé třídy `HttpClient` a přidává funkce potřebné k šíření určitých hlaviček z existujícího objektu ASP.NET HttpRequest do odchozího objektu HttpRequestMessage. Dále si ukážeme, jak tato odvozená třída přispívá při týmových scénářích k větší produktivitě vývojářů.


### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. Nastavte zarážku v metodě `Get(int id)`, která zpracovává požadavky GET `api/values/{id}`.
1. V projektu `webfrontend` nastavte zarážku těsně před odesláním požadavku GET do `mywebapi/api/values`.
1. V projektu `webfrontend` stiskněte klávesu F5.
1. Otevřete webovou aplikaci a projděte kód obou služeb.
1. Ve webové aplikaci se na stránce O aplikaci bude zobrazovat zpráva vytvořená spojením řetězců dvou služeb: „Hello from webfrontend and Hello from mywebapi“.


Hotovo! Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

[!INCLUDE[](includes/team-development-1.md)]

Podívejte se, jak to funguje: Přejděte do okna editoru VS Code s projektem `mywebapi` a upravte kód metody `string Get(int id)` například takto:

    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]

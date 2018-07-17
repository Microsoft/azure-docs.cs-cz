---
title: Týmový vývoj pro Azure Dev Spaces pomocí .NET Core a sady Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: 93c9f42df1136f242a5a04dc3056e0b1595cfb02
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37933438"
---
# <a name="team-development-with-azure-dev-spaces"></a>Týmový vývoj se službou Azure Dev Spaces

V tomto kurzu zjistíte, jak pomocí vývojových prostorů pracovat v různých vývojových prostředích současně. Různou práci přitom budete uchovávat v samostatných vývojových prostorech na stejném clusteru.

## <a name="call-another-container"></a>Volání jiného kontejneru
V této části vytvoříte druhou službu `mywebapi`, kterou bude `webfrontend` volat. Každá služba poběží v samostatném kontejneru. Ladění pak provedete v obou kontejnerech.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Stažení ukázkového kódu pro *mywebapi*
Kvůli úspoře času si můžete ukázkový kód stáhnout z úložiště GitHub. Přejděte na https://github.com/Azure/dev-spaces a stáhněte si úložiště GitHub výběrem možnosti **Clone or Download** (Klonovat nebo stáhnout). Kód k této části je tady: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Spuštění *mywebapi*
1. Otevřete službu `mywebapi` projektu v *samostatném okně sady Visual Studio*.
1. Vyberte v rozevíracím okně nastavení spouštění **Azure Dev Spaces**, jak jste to udělali už dříve pro projekt `webfrontend`. Tentokrát místo vytváření nového clusteru AKS vyberte cluster, který jste už vytvořili. Stejně jako dříve nechte v rozevíracím seznamu Space (Prostor) vybranou položku `default` a klikněte na **OK**. V okně Výstup si můžete všimnout, že sada Visual Studio začíná tuto novou službu „připravovat“ ve vašem vývojovém prostoru, aby se při spuštění ladění postup urychlil.
1. Stiskněte F5 a počkejte na sestavení a nasazení služby. Až se barva stavového řádku sady Visual Studio změní na oranžovou, bude služba připravená.
1. Poznamenejte si adresu URL koncového bodu, která se zobrazí v okně **Výstup** v podokně **Azure Dev Spaces for AKS** (Azure Dev Spaces pro AKS). Bude vypadat přibližně takto: http://localhost:\<portnumber\>. I když to může vypadat, že kontejner je spuštěný lokálně, ve skutečnosti je spuštěný ve vývojovém prostoru v Azure.
2. Až se `mywebapi` připraví, otevřete v prohlížeči adresu místního hostitele a k adrese URL připojte `/api/values`, aby se vyvolalo výchozí rozhraní GET API pro `ValuesController`. 
3. Pokud byly všechny kroky úspěšné, měla by se zobrazit odpověď ze služby `mywebapi`, která vypadá takto:

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Vytvoření požadavku z *webfrontend* do *mywebapi*
Teď napíšeme kód v projektu `webfrontend`, který vygeneruje požadavek do `mywebapi`. Přejděte do okna sady Visual Studio, ve kterém je projekt `webfrontend`. V souboru `HomeController.cs` *nahraďte* kód pro metodu About následujícím kódem:

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

Předchozí příklad kódu předává hlavičku `azds-route-as` z příchozího požadavku do odchozího požadavku. Později se dozvíte, jak se tím v týmových scénářích zvyšuje produktivita vývojového prostředí.

### <a name="debug-across-multiple-services"></a>Ladění více služeb
1. V této fázi byste měli mít spuštěnou službu `mywebapi` s připojeným ladicím programem. Pokud tomu tak není, stiskněte v projektu `mywebapi` klávesu F5.
1. V souboru `Controllers/ValuesController.cs`, který zpracovává požadavky GET `api/values/{id}`, nastavte zarážku v metodě `Get(int id)`.
1. V projektu `webfrontend`, do kterého jste vložili výše uvedený kód, nastavte zarážku těsně před odesláním požadavku GET do `mywebapi/api/values`.
1. V projektu `webfrontend` stiskněte klávesu F5. Visual Studio znovu otevře v prohlížeči příslušný port místního hostitele a zobrazí se daná webová aplikace.
1. Zarážku v projektu `webfrontend` aktivujte kliknutím na odkaz **O aplikaci** v horní části stránky. 
1. Pokračujte stisknutím klávesy F10. Zarážka v projektu `mywebapi` teď bude aktivovaná.
1. Pokračujte stisknutím klávesy F5, abyste se vrátili ke kódu v projektu `webfrontend`.
1. Dalším stisknutím klávesy F5 se dokončí daný požadavek a vrátí se stránka v prohlížeči. Ve webové aplikaci se na stránce O aplikaci bude zobrazovat zpráva vzniklá zřetězením ze dvou služeb: „Hello from webfrontend and Hello from mywebapi.“

Hotovo! Teď máte aplikaci s více kontejnery, kde můžete každý kontejner vyvíjet a nasazovat odděleně.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Umožňuje týmu vývojářů podle potřeby pracovat ve stejném prostředí, ať už ve sdíleném pracovním prostoru nebo v oddělených pracovních prostorech.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
V tuto chvíli není ukázková aplikace moc složitá. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy.

Předpokládejme, že pracujete na službě, která komunikuje s řadou dalších služeb.

- Může se stát, že spouštění všech služeb místně nebude během vývoje reálné. Počítač používaný pro vývoj nemusí mít dostatek prostředků ke spuštění celé aplikace. Anebo třeba vaše aplikace obsahuje koncové body, které musí být veřejně dostupné (aplikace například odpovídá na webhook z aplikace SaaS).
- Můžete se pokusit spustit jenom služby, na kterých jste závislí, ale to znamená, že byste museli podrobně znát všechny závislosti (třeba i závislosti závislostí). Může také nastat situace, že vytvoření a spuštění vašich závislostí nebude snadné, protože jste na nich nepracovali.
- Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. To někdy může pomoct, ale správa těchto napodobení může brzy vyžadovat svůj vlastní náročný vývoj. Navíc to vede k tomu, že vývojové prostředí vypadá jinak než produkční prostředí a můžou se tak vloudit drobné chyby.
- Z toho vyplývá, že jakékoli komplexní testování kódu bude obtížné. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

Pomocí následujícího postupu se ujistěte, že služby `webfrontend` a `mywebapi` jsou spuštěné **ve vývojovém prostoru `default`**.
1. Zavřete všechny ladicí relace (otevřené pomocí F5) pro obě služby, ale nechte projekty v oknech sady Visual Studio otevřené.
2. Přejděte do okna sady Visual Studio s projektem `mywebapi` a stisknutím Ctrl+F5 spusťte tuto službu bez připojeného ladicího programu.
3. Přejděte do okna sady Visual Studio s projektem `webfrontend` a stisknutím Ctrl+F5 spusťte také tuto službu.

> [!Note]
> Někdy je při stisknutí Ctrl+F5 nutné po prvním zobrazení webové stránky v prohlížeči danou stránku aktualizovat.

Kdokoli, kdo otevře veřejnou adresu URL a přejde k webové aplikaci, vyvolá zapsanou cestu kódu, která spouští obě služby ve výchozím prostoru `default`. Předpokládejme teď, že chcete pokračovat ve vývoji `mywebapi` – jak to uděláte bez toho, že byste narušili práci jiných vývojářů, kteří používají tento vývojový prostor? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-new-dev-space"></a>Vytvoření nového vývojového prostoru
V sadě Visual Studio můžete vytvářet další prostory, které se použijí, když pro danou službu stisknete F5 nebo Ctrl+F5. Prostor můžete pojmenovat libovolně (např. `sprint4` nebo `demo`).

K vytvoření nového prostoru použijte následující postup:
1. Přejděte do okna sady Visual Studio, ve kterém je projekt `mywebapi`.
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
3. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
4. Odtud můžete změnit nebo vytvořit cluster nebo prostor, který se použije po stisknutí F5 nebo Ctrl+F5. *Zkontrolujte, že je vybraný vývojový prostor Azure, který jste vytvořili dříve*.
5. V rozevírací nabídce Space (Prostor) vyberte **<Create New Space…>** <Vytvořit nový prostor...>.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. V dialogovém okně **Add Space** (Přidat prostor) zadejte název pro daný prostor a klikněte na **OK**. Můžete pro nový prostor použít svoje jméno (například „scott“), aby bylo vašim kolegům jasné, že se jedná o prostor, ve kterém pracujete vy.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Teď by se měl zobrazit váš cluster AKS a na stránce vlastností projektu by měl být vybraný nový prostor.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aktualizace kódu pro *mywebapi*

1. V projektu `mywebapi` změňte kód v souboru `Controllers/ValuesController.cs` v metodě `string Get(int id)` následujícím způsobem:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Nastavte zarážku v tomto aktualizovaném bloku kódu (jednu už můžete mít nastavenou z dřívějška).
3. Stisknutím F5 spusťte službu `mywebapi`. Služba se spustí v clusteru s využitím vybraného prostoru, což je v tomto případě `scott`.

Následující diagram vám pomůže porozumět tomu, jak různé prostory fungují. Fialová cesta znázorňuje požadavek prostřednictvím prostoru `default`. Je to výchozí cesta, která se použije, když se žádný prostor nepřidá jako předpona k adrese URL. Růžová cesta znázorňuje požadavek prostřednictvím prostoru `default/scott`.

![](media/common/Space-Routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje komplexní testování kódu ve sdíleném prostředí bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-running-in-the-defaultscott-space"></a>Testování kódu spuštěného v prostoru `default/scott`
Pokud chcete otestovat novou verzi `mywebapi` ve spojení s projektem `webfrontend`, otevřete v prohlížeči adresu URL veřejného přístupového bodu pro `webfrontend` (například http://webfrontend.123456abcdef.eastus.aksapp.io) a přejděte na stránku O aplikaci. Měla by se zobrazit původní zpráva: „Hello from webfrontend and Hello from mywebapi.“

Teď k adrese URL přidejte „scott.s.“. Adresa URL tedy bude vypadat nějak podobně jako http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io. Aktualizujte stránku v prohlížeči. Mělo by dojít k zastavení na zarážce, kterou jste nastavili v projektu `mywebapi`. Pokračujte kliknutím na F5. V prohlížeči by se teď měla zobrazit nová zpráva „Hello from webfrontend and mywebapi now says something new.“ Důvodem je skutečnost, že v prostoru `default/scott` je spuštěná cesta k aktualizovanému kódu v `mywebapi`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]

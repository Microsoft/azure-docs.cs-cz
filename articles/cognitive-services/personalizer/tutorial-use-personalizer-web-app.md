---
title: Použití aplikace Web App-personalizovat
description: Přizpůsobení webové aplikace v jazyce C# .NET pomocí smyčky vlastního nastavení pro poskytnutí správného obsahu uživateli na základě akcí (s funkcemi) a funkcí kontextu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c004887e3883ae711974b544510dff16a98d4ef9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363914"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Kurz: Přidání přizpůsobení do webové aplikace .NET

Přizpůsobení webové aplikace v jazyce C# .NET pomocí smyčky vlastního nastavení pro poskytnutí správného obsahu uživateli na základě akcí (s funkcemi) a funkcí kontextu.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Nastavení klíče a koncového bodu přizpůsobeného přizpůsobování
> * Shromažďování funkcí
> * Rozhraní API pro řazení a měnu volání
> * Zobrazit horní akci určenou jako _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Výběr nejlepšího obsahu pro webovou aplikaci

Webová aplikace by měla používat přizpůsobený modul, pokud je na webové stránce seznam _akcí_ (nějaký typ obsahu), které je potřeba přizpůsobit na jednu nejvyšší položku (rewardActionId), která se má zobrazit. Příklady seznamů akcí zahrnují články s příspěvky, umístění umístění tlačítek a možnosti Wordu pro názvy produktů.

Seznam akcí spolu s funkcemi kontextu můžete odeslat do smyčky přizpůsobené. Individuální nastavení vybere jednu nejlepší akci, kterou vaše webová aplikace zobrazí.

V tomto kurzu jsou akce typy jídla:

* těstovin
* zmrzlina
* vyrábí
* salát
* popcorn
* internetových
* neobsahuje

Chcete-li lépe přizpůsobovat informace o vašich akcích, odešlete obě _akce s_ funkcemi a _kontextovou funkcí_ s každou žádostí rozhraní API pro řazení.

**Funkce** modelu je informace o akci nebo kontextu, které mohou být agregovány (seskupeny) mezi členy vaší uživatelské základny webové aplikace. Funkce _není_ individuálně specifická (například ID uživatele) nebo má vysokou specifickou hodnotu (například přesný denní čas).

### <a name="actions-with-features"></a>Akce s funkcemi

Každá akce (položka obsahu) obsahuje funkce, které usnadňují rozlišení položky v potravinách.

Funkce nejsou nakonfigurované jako součást konfigurace smyčky v Azure Portal. Místo toho jsou odesílány jako objekt JSON s každým voláním rozhraní API řazení. To umožňuje flexibilitu při akcích a jejich funkcích v průběhu času zvětšovat, měnit a zmenšovat, což umožňuje přizpůsobovat sledování trendů.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Kontextové funkce

Kontextové funkce přizpůsobují usnadnění pro pochopení kontextu akcí. Kontext této ukázkové aplikace zahrnuje:

* čas od dne do ráno, odpoledne, večer, noc
* preference uživatele pro chuti – sůl, sladké, hořký, zdrojová nebo Savory
* kontext prohlížeče – uživatelský agent, zeměpisné umístění, odkazujícího uživatele

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Jak Web App používá přizpůsobování?

Webová aplikace používá přidaný nástroj k výběru nejlepší akce ze seznamu možností potravinové služby. Odešle následující informace s každým voláním rozhraní API řazení:
* **Akce** s jejich funkcemi, jako jsou `taste` a `spiceLevel`
* **kontextové** funkce, jako jsou například uživatelské `time` `taste` Předvolby a informace o uživatelském agentovi prohlížeče a funkce kontextu
* **akce, které se mají vyloučit** , jako je například šťáva
* **ID události** , která se liší pro každé volání rozhraní API řazení.

## <a name="personalizer-model-features-in-a-web-app"></a>Funkce modelu přizpůsobení ve webové aplikaci

Přizpůsobování vyžaduje funkce pro akce (obsah) a aktuální kontext (uživatel a prostředí). Funkce se používají k zarovnávání akcí s aktuálním kontextem v modelu. Tento model představuje informace o akcích, kontextu a jejich funkcích přizpůsobeného přizpůsobování, které umožňuje provádět kvalifikovaná rozhodnutí.

Model, včetně funkcí, se aktualizuje podle plánu na základě nastavení **četnosti aktualizace modelu** v Azure Portal.

> [!CAUTION]
> Funkce v této aplikaci jsou určeny k ilustraci funkcí a hodnot funkcí, ale ne nutně k nejlepším funkcím, které je potřeba použít ve webové aplikaci.

### <a name="plan-for-features-and-their-values"></a>Plánování funkcí a jejich hodnot

Funkce by se měly vybrat se stejným plánováním a návrhem, který byste použili pro jakékoli schéma nebo model v technické architektuře. Hodnoty funkcí je možné nastavit pomocí obchodní logiky nebo systémů třetích stran. Hodnoty funkcí by neměly být tak vysoce specifické, aby se neprojevily v rámci skupiny nebo třídy funkcí.

### <a name="generalize-feature-values"></a>Generalizace hodnot funkcí

#### <a name="generalize-into-categories"></a>Generalizace do kategorií

Tato aplikace používá `time` jako funkci, ale seskupuje čas do kategorií, jako jsou `morning` ,, `afternoon` `evening` a `night` . To je příklad použití informací o čase, ale ne vysoce specifických způsobem, jako je `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>Generalizace do částí

Tato aplikace používá funkce požadavku HTTP z prohlížeče. Začíná velmi specifickým řetězcem, který má všechna data, například:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

Knihovna tříd **HttpRequestFeatures** generalizuje tento řetězec do objektu **userAgentInfo** s použitím individuálních hodnot. Všechny hodnoty, které jsou příliš specifické, jsou nastaveny na prázdný řetězec. Po odeslání funkcí kontextu pro požadavek má následující formát JSON:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Použití ukázkové webové aplikace

Ukázková webová aplikace založená na prohlížeči (veškerý kód je k dispozici) vyžaduje, aby následující aplikace byly nainstalovány ke spuštění aplikace.

Nainstalujte následující software:

* [.NET core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) – ukázkový back-end Server používá .NET Core
* [Node.js](https://nodejs.org/) – klient/front-end závisí na této aplikaci.
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)nebo [.NET Core CLI](/dotnet/core/tools/) – pro sestavení a spuštění aplikace použijte vývojové prostředí sady visual Studio 2019 nebo .NET Core CLI.

### <a name="set-up-the-sample"></a>Nastavení ukázky
1. Naklonujte úložiště ukázek pro Azure pro přizpůsobení.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Přejděte na _Samples/HttpRequestFeatures_ a otevřete řešení `HttpRequestFeaturesExample.sln` .

    Pokud je požadováno, umožněte aplikaci Visual Studio aktualizovat balíček .NET pro přizpůsobení.

### <a name="set-up-azure-personalizer-service"></a>Nastavení služby přizpůsobování Azure

1. [Vytvořte prostředek](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) přizpůsobeného v Azure Portal.

1. V Azure Portal vyhledejte na `Endpoint` `Key1` `Key2` kartě **klíče a koncové body** a buď nebo (jak budou fungovat). Jsou to vaše `PersonalizerServiceEndpoint` a vaše `PersonalizerApiKey` .
1. Vyplňte `PersonalizerServiceEndpoint` **appsettings.js** v části.
1. Nakonfigurujte `PersonalizerApiKey` jako [tajné klíče aplikace](/aspnet/core/security/app-secrets) jedním z následujících způsobů:

    * Pokud používáte .NET Core CLI, můžete použít `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` příkaz.
    * Pokud používáte aplikaci Visual Studio, můžete kliknout pravým tlačítkem myši na projekt a vybrat možnost **spravovat tajné klíče uživatele** pro konfiguraci klíčů přizpůsobeného nástroji. Tím se v aplikaci Visual Studio otevře soubor, `secrets.json` kde můžete klíče přidat následujícím způsobem:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Sestavte a spusťte HttpRequestFeaturesExample pomocí jedné z následujících metod:

* Visual Studio 2019: stiskněte klávesu **F5**
* .NET Core CLI: `dotnet build` pak `dotnet run`

Přes webový prohlížeč můžete odeslat žádost o zařazení a žádost o odměnu, zobrazit jejich odpovědi a také funkce žádosti HTTP extrahované z vašeho prostředí.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky ukazuje příklad funkce požadavku HTTP ve webovém prohlížeči.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Ukázka smyčky přizpůsobeného přizpůsobování

1. Kliknutím na tlačítko **Generovat novou žádost o** zařazení vytvořte nový objekt JSON pro volání rozhraní API řazení. Tím se vytvoří akce (s funkcemi) a kontextové funkce a zobrazí se hodnoty, abyste viděli, jak JSON vypadá.

    Pro vaši vlastní budoucí aplikaci může generování akcí a funkcí probíhat na klientovi, na serveru, v kombinaci obou nebo s voláními jiných služeb.

1. Vyberte **Odeslat požadavek na řazení** pro odeslání objektu JSON na server. Server volá rozhraní API pro řazení pro přizpůsobení. Server obdrží odpověď a vrátí nejvyšší seřazenou akci, kterou má klient zobrazit.

1. Nastavte hodnotu odměňování a pak vyberte tlačítko pro **odeslání žádosti o Nemotivaci** . Pokud hodnotu neměníte, klientská aplikace vždy pošle hodnotu do přizpůsobeného `1` .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se zobrazí v oddílu žádosti o odměnu.](./media/tutorial-web-app/reward-score-api-call.png)

    Pro vaši vlastní budoucí aplikaci může generování skóre odměňování nastat po shromáždění informací z chování uživatele v klientovi, spolu s obchodní logikou na serveru.

## <a name="understand-the-sample-web-app"></a>Pochopení ukázkové webové aplikace

Ukázková webová aplikace má server **C# .NET** , který spravuje shromažďování funkcí a posílání a přijímání volání http do koncového bodu přizpůsobeného rozhraní.

Ukázková webová aplikace používá **vyseknutí front-endové klientské aplikace** pro zachycení funkcí a zpracování akcí uživatelského rozhraní, jako je kliknutí na tlačítka a odesílání dat na server .NET.

V následujících částech se vysvětlují části serveru a klienta, které vývojář potřebuje pochopit, aby mohl používat přizpůsobené součásti.

## <a name="rank-api-client-application-sends-context-to-server"></a>Rozhraní API pro řazení: klientská aplikace odesílá kontext na server.

Klientská aplikace shromažďuje _uživatelský agent_ prohlížeče uživatele.

> [!div class="mx-imgBorder"]
> ![Sestavte a spusťte projekt HTTPRequestFeaturesExample. Otevře se okno prohlížeče, ve kterém se zobrazí jedna stránková aplikace.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Rozhraní API řazení: Serverová aplikace Přizpůsob volání

Toto je typická webová aplikace .NET s klientskou aplikací, takže většina kódu kotlového talíře je k dispozici za vás. Jakýkoli kód, který není specifický pro přizpůsobení, je odebrán z následujících fragmentů kódu, takže se můžete soustředit na kód specifický pro přizpůsobení.

### <a name="create-personalizer-client"></a>Vytvořit klienta přizpůsobeného pro přizpůsobování

V **Startup.cs** serveru se pomocí koncového bodu a klíče přizpůsobeného objektu vytvoří klient přizpůsobeného pro přizpůsobení. Klientská aplikace nemusí v této aplikaci komunikovat s přizpůsobením, místo toho, aby tato volání sady SDK spoléhala na server.

Spouštěcí kód pro .NET webového serveru je:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Vybrat nejlepší akci

V **PersonalizerController.cs** serveru rozhraní API serveru **GenerateRank** shrnuje přípravu pro volání rozhraní API Rank.

* Vytvořit nový `eventId` pro volání pořadí
* Získá seznam akcí.
* Získat seznam funkcí od uživatele a vytvořit kontextové funkce
* Volitelně můžete nastavit všechny vyloučené akce.
* Rozhraní API pro řazení volání, vrácení výsledků klientovi

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

JSON odeslaný do přizpůsobeného, který obsahuje obě akce (s funkcemi) a aktuální kontextové funkce, vypadá takto:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Vrácení přizpůsobení rewardActionId klientovi

Rozhraní API řazení vrátí vybranou nejlepší akci **rewardActionId** serveru.

Zobrazí akci vrácenou v **rewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Klient zobrazuje akci rewardActionId

V tomto kurzu se `rewardActionId` zobrazí hodnota.

Ve vaší vlastní budoucí aplikaci může být nějaký přesný text, tlačítko nebo oddíl zvýrazněné webové stránky. Seznam se vrátí pro všechny následné analýzy skóre, nikoli pro objednání obsahu. `rewardActionId`Měl by se zobrazit jenom obsah.

## <a name="reward-api-collect-information-for-reward"></a>API pro odměnu: shromažďování informací pro odměnu

[Skóre odměňování](concept-rewards.md) by mělo být pečlivě plánováno, stejně jako funkce jsou plánovány. Skóre odměňování by obvykle mělo být hodnota od 0 do 1. Hodnotu _lze_ v klientské aplikaci vypočítat částečně, na základě chování uživatelů a částečně na serveru, a to na základě obchodní logiky a cílů.

Pokud server nevolá rozhraní API pro odměnu v **době čekání na odměnu** nakonfigurovanou v Azure Portal pro váš prostředek pro přizpůsobování, pak se pro tuto událost použije **Výchozí doba odměna** (také nakonfigurovaná v Azure Portal).

V této ukázkové aplikaci můžete vybrat hodnotu, abyste viděli, jak se u výběru ovlivňuje.

## <a name="additional-ways-to-learn-from-this-sample"></a>Další způsoby, jak se naučit z této ukázky

Ukázka používá několik událostí založených na čase, které jsou nakonfigurovány v Azure Portal pro váš prostředek přizpůsobeného pro přizpůsobení. Pomocí těchto hodnot se pak vraťte do této ukázkové webové aplikace, abyste viděli, jak změny ovlivní volání funkcí pořadí a odměňování:

* Doba čekání na odměnu
* Frekvence aktualizace modelu

Další nastavení, která se mají přehrát, zahrnují:
* Výchozí odměna
* Procento průzkumu


## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto kurzem hotovi, vyčistěte následující prostředky:

* Odstraňte ukázkový adresář projektu.
* Odstraňte prostředek přizpůsobeného přizpůsobeného prostředku, který je vyhrazený pro akce a kontext – pouze znovu použijte prostředek, pokud stále používáte potraviny jako akce, které jsou v doméně předmětu.


## <a name="next-steps"></a>Další kroky
* [Jak služba Personalizace funguje](how-personalizer-works.md)
* [Funkce](concepts-features.md): Naučte se koncepty funkcí pomocí akcí a kontextu.
* [Ceny](concept-rewards.md): informace o výpočtu neprospěchu
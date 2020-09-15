---
title: Použít přizpůsobování v programu chat robot – Přizpůsobte
description: Přizpůsobte si robota chatu v C# .NET se smyčkou pro přizpůsobení a poskytněte tak správnému obsahu uživatele na základě akcí (s funkcemi) a funkcí kontextu.
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d95a6999186b964e59ff8f287d917b1f93e1813
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089883"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Kurz: použití přizpůsobeného prostředí v rozhraní .NET chat bot

K poskytnutí správného obsahu uživateli použijte robota chatu v C# .NET se smyčkou pro přizpůsobení. Tento robot pro chat navrhuje konkrétní káva nebo čaj pro uživatele. Uživatel může tento návrh přijmout nebo odmítnout. Tím získáte informace o přizpůsobování, které vám pomůžou udělat další návrh lépe.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Nastavení prostředků Azure
> * Konfigurace a spuštění robota
> * Interakce s robotem pomocí emulátoru bot Frameworku
> * Informace o tom, kde a jak Robot používá přizpůsobování


## <a name="how-does-the-chat-bot-work"></a>Jak robot chat funguje?

Robotem chatu je obvykle konverzace s uživatelem. Tato specifická konverzační Robot používá přizpůsobený nástroj k výběru nejlepší akce (kávy nebo čaj) k nabídnutí uživateli. Individuální nastavení používá k provedení tohoto výběru posílení učení.

Robot pro chat musí spravovat konverzaci. Robotská Robot používá [rozhraní bot Framework](https://github.com/microsoft/botframework-sdk) ke správě architektury a konverzace robota a používá službu pro rozpoznávání [Language Understanding](../LUIS/index.yml) (Luis) k pochopení záměru přirozeného jazyka od uživatele.

Robotem chat je web, který má konkrétní trasu dostupnou k zodpovězení žádostí `http://localhost:3978/api/messages` . Emulátor bot můžete použít k vizuální interakci s robotem běžícího chatu, zatímco lokálně vyvíjíte robot.

### <a name="user-interactions-with-the-bot"></a>Interakce uživatele s robotem

Toto je jednoduchý robot pro chat, který umožňuje zadat textové dotazy.

|Uživatel zadá text|Robot odpoví textem|Popis akce robota provede určení textu odpovědi|
|--|--|--|
|Nebyl zadán text – robot zahájí konverzaci.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|Robot zahájí konverzaci s instruktážní textem a informuje o tom, co kontext je: `Tuesday` , `Snowy` .|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Určení záměru dotazu pomocí LUIS a zobrazení možností nabídky pro kávové a čaj položky. Funkce akcí jsou |
|`What do you suggest`|`How about Latte?`|Určete záměr dotazů pomocí LUIS, pak zavolejte **rozhraní API pro řazení**a jako otázku si zvolte horní volbu `How about {response.RewardActionId}?` . Zobrazuje také volání JSON a odpověď pro ilustraci.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Určete záměr dotazů pomocí LUIS a pak zavolejte **API** pro odměnu s odměňováním `1` , zobrazí volání JSON a odpověď pro ilustraci.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Určete záměr dotazů pomocí LUIS a pak zavolejte **API** pro odměnu s odměňováním `0` , zobrazí volání JSON a odpověď pro ilustraci.|
|`Reset`|Vrátí instruktážní text.|Určete záměr dotazu pomocí LUIS a pak zobrazí text s pokyny a obnoví kontext.|


### <a name="personalizer-in-this-bot"></a>Přizpůsobení v této robotu

Tato robotka chatu používá přizpůsobený nástroj k výběru horní akce (konkrétní kávy nebo čaj) na základě seznamu _akcí_ (nějaký typ obsahu) a funkcí kontextu.

Robot pošle seznam akcí společně s funkcemi kontextu do smyčky pro přizpůsobení. Přizpůsobování vrátí jednu nejlepší akci pro robota, kterou zobrazí robot.

V tomto kurzu jsou **Akce** typy kávy a čaj:

|Internetových|Čaj|
|--|--|
|Cappuccino<br>Espresso<br>Latte<br>Mocha|GreenTea<br>Rooibos|

**Rozhraní API pro řazení:** Aby se lépe přizpůsobené informace o vašich akcích, robot při každém požadavku rozhraní API pro hodnocení pošle následující:

* Akce _s funkcemi_
* Kontextové funkce

**Funkce** modelu je informace o akci nebo kontextu, které se dají agregovat (seskupovat) mezi členy vaší uživatelské základny robota v chatu. Funkce _není_ individuálně specifická (například ID uživatele) nebo má vysokou specifickou hodnotu (například přesný denní čas).

Funkce se používají k zarovnávání akcí s aktuálním kontextem v modelu. Tento model představuje informace o akcích, kontextu a jejich funkcích přizpůsobeného přizpůsobování, které umožňuje provádět kvalifikovaná rozhodnutí.

Model, včetně funkcí, se aktualizuje podle plánu na základě nastavení **četnosti aktualizace modelu** v Azure Portal.

Funkce by se měly vybrat se stejným plánováním a návrhem, který byste použili pro jakékoli schéma nebo model v technické architektuře. Hodnoty funkcí je možné nastavit pomocí obchodní logiky nebo systémů třetích stran.

> [!CAUTION]
> Funkce v této aplikaci jsou k disukázce a nemusí být nezbytně nejlepší funkce pro použití ve vaší webové aplikaci pro váš případ použití.

#### <a name="action-features"></a>Funkce akcí

Každá akce (položka obsahu) obsahuje funkce, které pomůžou odlišit položku kávy nebo čaj.

Funkce nejsou nakonfigurované jako součást konfigurace smyčky v Azure Portal. Místo toho jsou odesílány jako objekt JSON s každým voláním rozhraní API řazení. To umožňuje flexibilitu při akcích a jejich funkcích v průběhu času zvětšovat, měnit a zmenšovat, což umožňuje přizpůsobovat sledování trendů.

Mezi funkce pro káva a čaj patří:

* Zdrojové umístění kávy, například Keni a Brazílie
* Je organická nebo čaj organická?
* Světlá nebo tmavá Roast kávy

I když káva obsahuje tři funkce v předchozím seznamu, má čaj pouze jeden. Dodejte jenom funkce pro přizpůsobování, které pro akci mají smysl. Nemusíte předat prázdnou hodnotu pro funkci, pokud se na akci nevztahuje.

#### <a name="context-features"></a>Kontextové funkce

Funkce kontextu usnadňuje přizpůsobení a pochopení kontextu prostředí, jako je zobrazovací zařízení, uživatel, umístění a další funkce, které jsou relevantní pro váš případ použití.

Kontext pro tuto robota chatu zahrnuje:

* Typ počasí (sněhové, deště, Slunečné)
* Den v týdnu

Výběr funkcí je v této robotice pro chat náhodný. V reálném bot použijte skutečná data pro funkce kontextu.

### <a name="design-considerations-for-this-bot"></a>Faktory návrhu pro tento robot

Poznámka k této konverzaci je popsána v několika upozorněních:
* **Interakce robota**: konverzace je velmi jednoduchá, protože je v jednoduchém případu použití demonstruje rozsah a měna. Nejedná se o plnou funkčnost sady robot Framework SDK nebo emulátoru.
* **Přizpůsobování**: funkce jsou náhodně vybrány pro simulaci využití. Ve scénáři přizpůsobení produkčního prostředí nepoužívejte náhodné funkce.
* **Language Understanding (Luis)**: několik příkladů projevy modelu Luis jsou určeny pouze pro tuto ukázku. Nepoužívejte k tomu projevy ve vaší produkční aplikaci LUIS několik příkladů.


## <a name="install-required-software"></a>Nainstalovat požadovaný software
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Úložiště ukázek ke stažení obsahuje pokyny, pokud chcete použít .NET Core CLI.
- [Emulátor Microsoft bot Framework](https://aka.ms/botframeworkemulator) je desktopová aplikace, která umožňuje vývojářům robotům testovat a ladit své roboty na místním hostiteli nebo vzdáleně spouštět prostřednictvím tunelu.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Stáhnout vzorový kód robota pro chat

Robot pro chat je k dispozici v úložišti ukázek pro přizpůsobení. Naklonujte nebo [Stáhněte](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) úložiště a pak otevřete ukázku v `/samples/ChatbotExample` adresáři pomocí sady Visual Studio 2019.

K naklonování úložiště použijte následující příkaz Git v prostředí bash (terminálu).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Vytvoření a konfigurace přizpůsobeného a LUIS prostředků

### <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Pro použití tohoto robota chatu potřebujete vytvořit prostředky Azure pro přizpůsobování a Language Understanding (LUIS).

* [Vytvoření prostředků Luis](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal). V kroku vytvoření vyberte **obojí** , protože potřebujete prostředky pro vytváření i předpovědi.
* [Vytvořte prostředek pro přizpůsobení](how-to-create-resource.md) a pak zkopírujte klíč a koncový bod z Azure Portal. Tyto hodnoty budete muset nastavit v `appsettings.json` souboru projektu .NET.

### <a name="create-luis-app"></a>Vytvoření aplikace LUIS

Pokud s LUIS začínáte, musíte se [Přihlásit](https://www.luis.ai) a hned migrovat svůj účet. Nemusíte vytvářet nové prostředky, místo toho vyberte prostředky, které jste vytvořili v předchozí části tohoto kurzu.

1. Pokud chcete vytvořit novou aplikaci LUIS, vyberte na [portálu Luis](https://www.luis.ai)svůj odběr a prostředek pro vytváření obsahu.
1. Pak pořád na stejné stránce vyberte **+ Nová aplikace pro konverzaci**a pak **importovat jako JSON**.
1. V místním dialogovém okně vyberte **zvolit soubor** a pak vyberte `/samples/ChatbotExample/CognitiveModels/coffeebot.json` soubor. Zadejte název `Personalizer Coffee bot` .
1. Vyberte tlačítko **výuka** v pravém horním rohu portálu Luis.
1. Vyberte tlačítko **publikovat** a publikujte aplikaci do **produkčního slotu** pro předpověď modulu runtime.
1. Vyberte **Spravovat**a pak **Nastavení**. Zkopírujte hodnotu **ID aplikace**. Tuto hodnotu budete muset nastavit v `appsettings.json` souboru projektu .NET.
1. Pořád v části **Spravovat** vyberte **prostředky Azure**. Tím se zobrazí přidružené prostředky v aplikaci.
1. Vyberte **Přidat prostředek předpovědi**. V místním dialogovém okně vyberte vaše předplatné a prostředek předpovědi vytvořený v předchozí části tohoto kurzu a potom vyberte **Hotovo**.
1. Zkopírujte hodnoty **primárního klíče** a **adresy URL koncového bodu**. Tyto hodnoty budete muset nastavit v `appsettings.json` souboru projektu .NET.

### <a name="configure-bot-with-appsettingsjson-file"></a>Nakonfigurovat robota s appsettings.jsem v souboru

1. Otevřete soubor řešení robota pro konverzaci, v `ChatbotSamples.sln` rámci sady Visual Studio 2019.
1. Otevřete `appsettings.json` v kořenovém adresáři projektu.
1. Nastavte všechna pět nastavení, která jste zkopírovali v předchozí části tohoto kurzu.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Sestavení a spuštění robota

Jakmile nakonfigurujete `appsettings.json` , budete připraveni sestavit a spustit robota chatu. Když to uděláte, otevře se prohlížeč na běžícím webu `http://localhost:3978` .

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Snímek obrazovky prohlížeče se zobrazením webu chat bot":::

Nechejte web spuštěný, protože kurz vysvětluje, co dělá robot, takže můžete s robotem pracovat.


## <a name="set-up-the-bot-emulator"></a>Nastavení emulátoru bot

1. Otevřete emulátor rozhraní bot Framework a vyberte **otevřít robot**.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Snímek obrazovky s úvodní obrazovkou emulátoru bot":::


1. Nakonfigurujte robota s následující **adresou URL robota** a pak vyberte **připojit**:

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Snímek obrazovky s emulátorem bot otevřete nastavení robota.":::

    Emulátor se připojí ke robotovi chatu a zobrazí Návodný text spolu s informacemi o protokolování a ladění, které jsou užitečné pro místní vývoj.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Snímek obrazovky s emulátorem bot v prvním zapnutí konverzace":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Použití robota v emulátoru bot

1. Požádejte o zobrazení nabídky zadáním `I would like to see the menu` . Robot pro chat zobrazí položky.
1. Umožněte, aby robot navrhl položku zadáním `Please suggest a drink for me.` emulátoru. v okně chatu se zobrazí požadavek a odpověď pořadí, takže uvidíte úplný formát JSON. A robot vytvoří nějaký návrh, např. `How about Latte?`
1. Podle toho, co byste chtěli přijmout, což znamená, že přijměte horní seřazený výběr, `I like it.` emulátor zobrazí požadavek na odměnu s hodnotou 1 a odpověď v okně chatu, takže uvidíte úplný formát JSON. A robot odpoví `That’s great! I’ll keep learning your preferences over time.` a `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Pokud odpovíte na výběr, bude do přizpůsobeného přizpůsobené `no` skóre pro odměnu 0.


## <a name="understand-the-net-code-using-personalizer"></a>Pochopení kódu .NET pomocí přizpůsobení

Řešení .NET je jednoduchý robot na chatu pro robot Framework. Kód související s přizpůsobením je v následujících složkách:
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs` soubor pro interakci mezi robotem a přizpůsobením
* `/samples/ChatbotExample/ReinforcementLearning` – spravuje akce a funkce pro model přizpůsobeného přizpůsobování.
* `/samples/ChatbotExample/Model` – soubory pro akce a funkce přizpůsobeného přizpůsobování a pro LUIS záměry

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs – práce s přizpůsobením

`PersonalizerChatbot`Třída je odvozena z `Microsoft.Bot.Builder.ActivityHandler` . Má tři vlastnosti a metody pro správu toku konverzace.

> [!CAUTION]
> Nekopírujte kód z tohoto kurzu. Použijte vzorový kód v [úložišti ukázek pro přizpůsobení](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

Metody s předponou `Send` Spravovat konverzaci s robotem a Luis. Metody `ChooseRankAsync` a `RewardAsync` interakce s přizpůsobením.

#### <a name="calling-rank-api-and-display-results"></a>Volání rozhraní API pořadí a zobrazení výsledků

Metoda `ChooseRankAsync` sestaví data JSON pro odeslání do rozhraní API řazení přizpůsobeného sestavením akcí s funkcemi a funkcemi kontextu.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Volání rozhraní API pro odměnu a zobrazení výsledků

Metoda `RewardAsync` sestaví data JSON pro odeslání do aplikačního rozhraní API pro přizpůsobení pomocí určení skóre. Skóre je určeno z záměru LUIS identifikovaného v textu uživatele a z `OnTurnAsync` metody odeslané.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Faktory návrhu pro robota

Tato ukázka je určena k předvedení jednoduchého uceleného řešení v robotu. Váš případ použití může být složitější.

Pokud se rozhodnete použít přizpůsobeného přizpůsobování v rámci výrobního robota, naplánujte:
* Přístup k přizpůsobenému aplikaci v reálném čase _pokaždé_ , když budete potřebovat seřazený výběr. Rozhraní API pro řazení není možné dávkovat ani ukládat do mezipaměti.  Volání odchodu může být zpožděné nebo převedené na samostatný proces, a Pokud nevrátíte v časovém limitu žádnou odměnu, bude pro událost nastavena výchozí hodnota pro odměnu.
* Výpočet na základě případu použití: Tento příklad ukázal dvě odměny nula a jedna bez rozsahu mezi a žádná záporná hodnota pro skóre. Váš systém potřebuje přesnější bodování.
* Kanály robota: Tato ukázka používá jeden kanál, ale pokud máte v úmyslu použít více než jeden kanál nebo variace roboty v jednom kanálu, může být nutné považovat za součást kontextových funkcí modelu přizpůsobeného objektem pro přizpůsobení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto kurzem hotovi, vyčistěte následující prostředky:

* Odstraňte ukázkový adresář projektu.
* Odstraňte prostředek přizpůsobeného a LUIS v Azure Portal.

## <a name="next-steps"></a>Další kroky
* [Jak služba Personalizace funguje](how-personalizer-works.md)
* [Funkce](concepts-features.md): Naučte se koncepty funkcí pomocí akcí a kontextu.
* [Ceny](concept-rewards.md): informace o výpočtu neprospěchu

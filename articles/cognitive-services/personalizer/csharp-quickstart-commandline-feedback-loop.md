---
title: Smyčka zpětné vazby - Personalizer
titleSuffix: Azure Cognitive Services
description: Přizpůsobení obsahu v tomto C# rychlý start ke službě Personalizer.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/08/2019
ms.author: edjez
ms.openlocfilehash: a7f6c6fe25bf9dff2f102080f93fc1e0024ac660
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478617"
---
# <a name="quickstart-personalize-content-using-c"></a>Rychlý start: Přizpůsobení obsahu pomocíC# 

Zobrazit přizpůsobený obsah v tomto C# rychlý start ke službě Personalizer.

Tento příklad ukazuje, jak použít Personalizer klientskou knihovnu pro C# provádět následující akce: 

 * Řadit seznam akcí pro přizpůsobení.
 * Sestavy reward přidělit nahoru seřazené akce na základě výběru uživatelem zadané události.

Začínáme s Personalizer zahrnuje následující kroky:

1. Odkazování na sadu SDK 
1. Zápis kódu pro akce, které chcete zobrazit uživatelům, pořadí
1. Zápis kódu pro odesílání odměny tak moct trénovat smyčky.

## <a name="prerequisites"></a>Požadavky

* Je nutné [Personalizer služby](how-to-settings.md) získat adresu url předplatné key a koncového bodu služby. 
* [Visual Studio 2015 nebo 2017](https://visualstudio.microsoft.com/downloads/).
* [Microsoft.Azure.CognitiveServices.Personalizer](https://go.microsoft.com/fwlink/?linkid=2092272) balíček NuGet sady SDK. Pokyny k instalaci jsou uvedené dál.

## <a name="change-the-model-update-frequency"></a>Změna frekvence aktualizace modelu

V prostředku Personalizer na webu Azure Portal, přejděte **četnost aktualizace modelu** na 10 sekund. To bude trénování služby rychle, což vám umožní podívat, jak se mění hlavní akce pro každou iteraci.

Při vytváření smyčku Personalizer první instance, není žádný model, protože nepřichází žádná volání rozhraní API Reward tak moct trénovat z. Pořadí volání vrátí stejné pravděpodobnosti pro každou položku. Vaše aplikace by měla stále vždy řadit obsahu pomocí výstupu RewardActionId.

![Změna frekvence aktualizace modelu](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Vytváření nových konzolovou aplikaci a odkazování na sadu SDK Personalizer 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet Personalizer klienta knihovny. V nabídce vyberte **nástroje**vyberte **Nuget package Manager**, pak **spravovat balíčky NuGet pro řešení**.
1. Vyberte **Procházet** kartu a **hledání** zadejte `Microsoft.Azure.CognitiveServices.Personalizer`.
1. Vyberte **Microsoft.Azure.CognitiveServices.Personalizer** při zobrazí.
1. Zaškrtněte políčko vedle názvu vašeho projektu a vyberte **nainstalovat**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Přidejte kód a vložit vaše klíče Personalizer a Azure

1. Soubor Program.cs nahraďte následujícím kódem. 
1. Nahraďte `serviceKey` hodnotu s klíči předplatného Personalizer platný.
1. Nahraďte `serviceEndpoint` s vašeho koncového bodu služby. Příklad: `https://westus2.api.cognitive.microsoft.com/`.
1. Spusťte program.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Přidejte kód, který řadit akce, které chcete zobrazit uživatelům

Následující C# kód je kompletní výpis k předání informací o uživateli, _features a informace o obsahu, _akce_, k Personalizer pomocí sady SDK. Personalizer vrátí horní seřazené akce se zobrazí uživateli.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            // Get the actions list to choose from personalizer with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalizer client.
            PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

                float reward = 0.0f;
                string answer = GetKey();

                if (answer == "Y")
                {
                    reward = 1;
                    Console.WriteLine("\nGreat! Enjoy your food.");
                }
                else if (answer == "N")
                {
                    reward = 0;
                    Console.WriteLine("\nYou didn't like the recommended food choice.");
                }
                else
                {
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
                }

                Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Spuštění programu

Sestavte program a spusťte ho. Rychlý start program zeptá na několik otázek k získání uživatelských předvoleb, označované jako funkce, pak poskytuje hlavní akce.

![Rychlý start program zeptá na několik otázek k získání uživatelských předvoleb, označované jako funkce, pak poskytuje hlavní akce.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile tento rychlý start dokončíte, odeberte všechny soubory, které jste v něm vytvořili. 

## <a name="next-steps"></a>Další postup

[Jak funguje Personalizer](how-personalizer-works.md)



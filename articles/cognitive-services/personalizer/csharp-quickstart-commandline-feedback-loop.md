---
title: 'Rychlý start: Vytvoření vlastní smyčky zpětné vazby'
titleSuffix: Azure Cognitive Services
description: Přizpůsobení obsahu v tomto C# rychlém startu pomocí služby pro přizpůsobení.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 54aa23071fef09058a1702218d6b7fc920363518
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662794"
---
# <a name="quickstart-personalize-content-using-c"></a>Rychlý start: Přizpůsobení obsahu pomocíC# 

Zobrazit přizpůsobený obsah C# v tomto rychlém startu pomocí služby pro přizpůsobení.

Tato ukázka předvádí, jak použít klientskou knihovnu pro přizpůsobování pro C# k provedení následujících akcí: 

 * Seřadit seznam akcí pro přizpůsobení.
 * Odkázat odměnu pro přidělení k horní seřazené akci na základě výběru uživatele pro zadanou událost.

Začínáme s přizpůsobením nástroje zahrnuje následující kroky:

1. Odkazování na sadu SDK 
1. Psaní kódu pro řazení akcí, které chcete uživatelům zobrazit,
1. Psaní kódu pro odeslání neprospěchu ke výukě smyčky.

## <a name="prerequisites"></a>Požadavky

* Abyste získali klíč předplatného a adresu URL služby koncového bodu, potřebujete [službu](how-to-settings.md) pro přizpůsobování. 
* [Visual Studio 2015 nebo 2017](https://visualstudio.microsoft.com/downloads/).
* Balíček NuGet sady SDK pro [Microsoft. Azure. cognitiveservices Account. personalizovat](https://go.microsoft.com/fwlink/?linkid=2092272) . Pokyny k instalaci jsou uvedené dál.

## <a name="change-the-model-update-frequency"></a>Změna frekvence aktualizace modelu

V prostředku přizpůsobeného nástroji v Azure Portal změňte **Četnost aktualizace modelu** na 10 sekund. Tím se služba bude vytvářet rychle a umožní vám to zjistit, jak se hlavní akce mění pro každou iteraci.

Když se poprvé vytvoří instance smyčky pro přizpůsobení, neexistuje žádný model, protože neexistovala žádná Neplatní volání rozhraní API pro vlak. Volání Rank budou pro každou položku vracet stejné pravděpodobnosti. Vaše aplikace by měla stále vždy Seřadit obsah pomocí výstupu RewardActionId.

![Změna frekvence aktualizace modelu](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Vytvoření nové aplikace konzoly a odkazování na sadu SDK pro přizpůsobování 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci Visual C#.
1. Nainstalujte balíček NuGet klientské knihovny pro přizpůsobování. V nabídce vyberte **nástroje**, vyberte **Správce balíčků NuGet**a pak **spravujte balíčky NuGet pro řešení**.
1. Podívejte se na **zahrnout předběžné verze**.
1. Vyberte kartu **Procházet** a do pole **Hledat** zadejte `Microsoft.Azure.CognitiveServices.Personalizer`.
1. Po zobrazení vyberte **Microsoft. Azure. cognitiveservices Account. personalizovat** .
1. Zaškrtněte políčko vedle názvu projektu a vyberte **instalovat**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Přidejte kód a vložte ho do přizpůsobené aplikace a klíčů Azure.

1. Soubor Program.cs nahraďte následujícím kódem. 
1. Nahraďte `serviceKey` hodnotu platným klíčem předplatného pro přizpůsobování.
1. Nahraďte `serviceEndpoint` koncovým bodem služby. Příklad: `https://westus2.api.cognitive.microsoft.com/`.
1. Spusťte program.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Přidejte kód, který bude seřadit akce, které chcete uživatelům zobrazit.

Následující C# kód je úplný seznam pro předání informací o uživateli, _features a informace o vašem obsahu, akcích, přizpůsobování pomocí sady SDK. Přizpůsobování vrátí nejvyšší seřazenou akci pro zobrazení uživatele.  

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

Sestavte program a spusťte ho. V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile tento rychlý start dokončíte, odeberte všechny soubory, které jste v něm vytvořili. 

## <a name="next-steps"></a>Další kroky

[Jak přizpůsobovat práci](how-personalizer-works.md)



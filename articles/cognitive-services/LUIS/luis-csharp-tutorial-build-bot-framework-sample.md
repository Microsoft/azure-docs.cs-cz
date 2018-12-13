---
title: Bot - C# – v3
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pomocí jazyka C# vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Tuto chatovací robot používá předem připravených HomeAutomation doménu k rychlé implementaci řešení robota.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: b4b074de483d4a1ccd4c934a9fa7254e0f656a1b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133942"
---
# <a name="luis-bot-in-c-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Služba LUIS robotů v C# pomocí rozhraní Bot Framework 3.x a použijete Azure Web app bot

Pomocí jazyka C# vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Tuto chatovací robot používá předem připravených HomeAutomation doménu k rychlé implementaci řešení robota. Robot využívá rozhraní Bot Framework 3.x a použijete Azure Web app bot.

## <a name="prerequisite"></a>Požadavek

* [Aplikace LUIS HomeAutomation](luis-get-started-create-app.md). Záměry z této mapy aplikace LUIS rutinám bodu robotů také dialogového okna. 

## <a name="luis-homeautomation-intents"></a>Služba LUIS HomeAutomation záměrů

| Záměr | Ukázková promluva | Funkce robota |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Vypnul světla. | Když záměr LUIS `HomeAutomation.TurnOn` zjistí, vyvolá robota `OnIntent` obslužná rutina dialogové okno. Toto dialogové okno je, kde by volat služby IoT můžete zapnout v zařízení a informace pro uživatele, že zařízení je zapnutý. |
| HomeAutomation.TurnOff | Vypněte ložnici světla. | Když záměr LUIS `HomeAutomation.TurnOff` zjistí, vyvolá robota `OffIntent` obslužná rutina dialogové okno. Toto dialogové okno je, kde by volat služby IoT, vypněte zařízení a řekněte mu, že zařízení jsou vypnuté. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Vytváření robotů Language Understanding s využitím služby Bot

1. V [webu Azure portal](https://portal.azure.com)vyberte **vytvořit nový prostředek** v nabídce vlevo nahoře.

    ![Vytvořit nový prostředek na webu Azure portal](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. Do vyhledávacího pole vyhledejte **Web App Bot**. 

    ![Vyberte web app bot jako typ prostředku](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. V okně s Web App Bot, klikněte na tlačítko **vytvořit**.

4. V **Bot Service**, zadejte požadované informace a klikněte na tlačítko **vytvořit**. Tím se vytvoří a nasadí bot service a LUIS aplikace do Azure. Pokud chcete použít [řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), zkontrolujte [požadavků oblasti](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) před vytvořením svého robota. 
    * Nastavte **název aplikace** na název svého robota. Název se používá jako subdoménu svého robota nasazené do cloudu (například mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Vyberte předplatné, [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plán služby App service, a [umístění](https://azure.microsoft.com/regions/).
    * Pro **Bot šablony**vyberte:
        * **Sada SDK v3**
        * **C#**
        * **Rozpoznávání jazyka**
    * Vyberte **umístění aplikace LUIS**. Toto je vytváření [oblasti](luis-reference-regions.md) vytvoření aplikace v.
    * Zaškrtněte políčko potvrzení právních upozornění. Podmínky právních upozornění jsou níže na zaškrtávací políčko.

    ![Bot Service](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Potvrďte, že byla nasazena bot service.
    * Klikněte na oznámení (ikona zvonku umístěné podél horního okraje na webu Azure portal). Oznámení se změní z **nasazení začalo** k **nasazení bylo úspěšné**.
    * Po oznámení změn **nasazení bylo úspěšné**, klikněte na tlačítko **přejít k prostředku** na toto oznámení.

> [!Note]
> Tento proces vytváření robota webové aplikace vytvoří také novou aplikaci LUIS za vás. Má se školení a publikovat za vás. 

## <a name="try-the-default-bot"></a>Zkuste bot výchozí

Potvrďte, že byla nasazena robota tak, že vyberete **oznámení** zaškrtávací políčko. Oznámení se změní z **probíhá nasazení...**  k **nasazení bylo úspěšné**. Klikněte na tlačítko **přejít k prostředku** tlačítko Otevřít bodu robotů také prostředky.

Po nasazení se robota, klikněte na tlačítko **testování ve Web Chat** a otevřete tak podokno Web Chat. Zadejte "hello" v Web Chat.

  ![Testování ve Web Chat robota](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Robot reaguje vyslovením příkazu "dosáhli pozdrav. Je ale nutné dodat: hello ".  Tato odpověď potvrdí, že robota obdržel zprávu a předat aplikaci LUIS, který je vytvořen výchozí. Toto výchozí nastavení aplikace LUIS zjistil záměr pozdrav. V dalším kroku připojíte aplikaci LUIS, kterou jste dříve vytvořili místo výchozího aplikace LUIS robota.

## <a name="connect-your-luis-app-to-the-bot"></a>Připojte svou aplikaci LUIS robotu

Otevřít **nastavení aplikace** a upravit **LuisAppId** pole obsahující ID aplikace pro vaši aplikaci LUIS. Pokud jste vytvořili aplikaci HomeAutomation LUIS v jiné oblasti, než USA – Západ, budete muset změnit **LuisAPIHostName** také. **LuisAPIKey** je aktuálně nastavený na klíč pro vytváření obsahu. Můžete to změnit klíče koncového bodu při provozu překročí kvótu na úrovni free. 

  ![Aktualizovat ID aplikace LUIS v Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Pokud nemáte ID aplikace LUIS [Domů automatizace aplikace](luis-get-started-create-app.md), přihlaste se k [LUIS](luis-reference-regions.md) webu pomocí stejného účtu, který používáte k přihlášení k Azure. 
> 1. Klikněte na **Moje aplikace**. 
> 2. Najdete LUIS aplikaci, kterou jste dříve vytvořili, a která obsahuje záměry a entity z HomeAutomation domény.
> 3. V **nastavení** stránky pro aplikaci LUIS, najít a zkopírovat ID aplikace. Ujistěte se, že je [trénovaného](luis-interactive-test.md) a [publikované](luis-how-to-publish-app.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Úprava kódu robota

1. Klikněte na tlačítko **sestavení** a potom klikněte na tlačítko **otevřít online editor kódu**.

   ![Otevřít online editor kódu](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Klikněte pravým tlačítkem myši `build.cmd` a zvolte **spustit z konzoly** k sestavení aplikace. Existuje několik kroků sestavení, které služba provede automaticky za vás. Sestavení je dokončená, jakmile bylo dokončeno s "Byl úspěšně dokončen."

3. V editoru kódu, otevřete `/Dialogs/BasicLuisDialog.cs`. Obsahuje následující kód:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Změňte kód na HomeAutomation záměrů


1. Odebrat tři atributy záměru a metody pro **pozdrav**, **zrušit**, a **pomáhají**. V doméně předem připravených HomeAutomation nejsou použity tyto záměry. Ujistěte se, aby **žádný** záměru atribut a metody. 

2. Přidáte závislosti do horní části souboru s další závislosti:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Přidejte konstanty pro správu řetězce v horní části `BasicLuisDialog ` třídy:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Přidejte kód pro nové příkazů `HomeAutomation.TurnOn` a `HomeAutomation.TurnOff` uvnitř `BasicLuisDialog ` třídy:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Přidejte kód k získání všech entit objevila aplikace LUIS uvnitř `BasicLuisDialog ` třídy:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Změna **ShowLuisResult** metodu `BasicLuisDialog ` třídy zaokrouhlí skóre, shromáždění entity a zobrazení zprávy s odpovědí v chatovací robot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Sestavit robota
V editoru kódu, klikněte pravým tlačítkem na `build.cmd` a vyberte **spustit z konzoly**.

![Sestavit robota Web ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

Zobrazení kódu se nahradí okno terminálu znázorňující průběh a výsledky sestavení.

![Úspěch sestavení Web bot](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Vyberte název robota na začátek modrém panelu a vyberte je alternativní metoda pro vytvoření robota **otevřete konzoly Kudu**. Otevře se konzola pro **D:\home**. 
> 
> Změňte adresář na **D:\home\site\wwwroot** zadáním: `cd site\wwwroot`
>
> Spusťte skript sestavení zadáním: `build.cmd`

## <a name="test-the-bot"></a>Testování robota

Na webu Azure Portal, klikněte na **testování ve Web Chat** otestovat robota. Typ zprávy like "vypnul světla" a "vypnout Moje Ohřívač" k vyvolání příkazů, které jste přidali do něj.

   ![Testování ve Web Chat HomeAutomation bot](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Mohou uchovávat aplikace LUIS bez jakékoli změny kódu vašeho robota. Zobrazit [přidání projevů příklad](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) a [trénování a testování vaší aplikace LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test). 

## <a name="download-the-bot-to-debug"></a>Stáhněte si robota, aby ladění
Pokud váš robot nefunguje, stáhněte si projekt do svého místního počítače a pokračovat [ladění](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-a-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Další informace o Bot Frameworku
Další informace o [Bot Framework](https://dev.botframework.com/) a [3.x](https://github.com/Microsoft/BotBuilder) a [4.x](https://github.com/Microsoft/botbuilder-dotnet) sady SDK.

## <a name="next-steps"></a>Další postup

Přidání záměrů LUIS a Bot service dialogová okna pro zpracování **pomáhají**, **zrušit**, a **pozdrav** záměry. Nezapomeňte si školení, publikovat a [sestavení](#build-the-bot) použijete web app bot. Služba LUIS a robot by měl mít stejné záměry.

> [!div class="nextstepaction"]
> [Přidání záměrů](./luis-how-to-add-intents.md)
> [dočištění řeči](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


<!-- Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-cs-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/csharp
[Github-LUIS-Samples-cs-hotel-bot-readme]: https://github.com/Microsoft/LUIS-Samples/blob/master/bot-integration-samples/hotel-finder/csharp/README.md
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[VisualStudio]: https://www.visualstudio.com/

<!-- tested on Win10 -->

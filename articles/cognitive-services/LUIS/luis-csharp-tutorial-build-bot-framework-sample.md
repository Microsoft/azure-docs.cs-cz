---
title: Integrovat LEOŠ robotu pomocí robota Tvůrce SDK pro jazyk C# v Azure | Microsoft Docs
description: Sestavení robotu integrovat LEOŠ aplikace pomocí rozhraní robota.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: 273fca51d83f25e2657b51b68179e46f4295f92b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263876"
---
# <a name="web-app-bot-using-the-luis-template-for-c"></a>Robota webové aplikace pomocí šablony LEOŠ pro jazyk C#

Sestavení chatbot s principy integrované jazyk.

## <a name="prerequisite"></a>Požadavek

* [Aplikace LEOŠ HomeAutomation](luis-get-started-create-app.md). Záměry z této aplikace mapy LEOŠ obslužné rutiny robota dialogové okno. 

## <a name="luis-homeautomation-intents"></a>LEOŠ HomeAutomation záměry

| Záměr | Příklad utterance | Funkce robota |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Zapněte světla. | Když záměr LEOŠ `HomeAutomation.TurnOn` je zjištěn, vyvolá robota `OnIntent` obslužná rutina dialogové okno. Toto dialogové okno je, kde by volání služby IoT můžete zapnout v zařízení a informace pro uživatele, který zařízení je zapnutý. |
| HomeAutomation.TurnOff | Indikátory ložnici vypněte. | Když záměr LEOŠ `HomeAutomation.TurnOff` je zjištěn, vyvolá robota `OffIntent` obslužná rutina dialogové okno. Toto dialogové okno je, kde by volání služby IoT vypněte zařízení a informace pro uživatele, že zařízení je vypnutá. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Vytvořit znalosti jazyka robota službou robota

1. V [portál Azure](https://portal.azure.com), vyberte **vytvořit nový prostředek** v levé nabídce nahoře.

    ![Vytvořit nový prostředek](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. Do vyhledávacího pole Hledat **webové aplikace robota**. 

    ![Vytvořit nový prostředek](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. V okně robota webové aplikace klikněte na **vytvořit**.

4. V **robota služby**, zadejte požadované informace a klikněte na **vytvořit**. Tím se vytvoří a nasadí robota služby a aplikace LEOŠ do Azure. Pokud chcete použít [řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), zkontrolujte [požadavků oblasti](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) před vytvořením vaší robota. 
    * Nastavit **název aplikace** na název vaší robota. Název se používá jako subdoménou, když vaše robota je nasazená do cloudu (například mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Vyberte předplatné, [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plán služby App service, a [umístění](https://azure.microsoft.com/regions/).
    * Vyberte **znalosti jazyka (C#)** šablonu **robota šablony** pole.
    * Vyberte **umístění aplikace LEOŠ**. Toto je pro tvorbu [oblast] [ LUIS] vytvoří se v aplikaci.
    * Zaškrtněte políčko potvrzení právní upozornění. Právní upozornění. podmínky jsou uvedeny níže políčko.

    ![Služba Bot](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Potvrďte, že byla nasazena službu robota.
    * Kliknutím na oznámení (na ikonu zvonku umístěné podél horního okraje portálu Azure). Oznámení se změní z **nasazení začalo** k **nasazení bylo úspěšné**.
    * Po oznámení změn na **nasazení bylo úspěšné**, klikněte na tlačítko **přejděte k prostředku** na tomto oznámení.

> [!Note]
> Tento proces vytvoření robota webové aplikace pro můžete také vytvořit novou aplikaci LEOŠ. Bylo vycvičena a publikovaná za vás. 

## <a name="try-the-default-bot"></a>Zkuste robota výchozí

Potvrďte, že byla nasazena robota výběrem **oznámení** zaškrtávací políčko. Oznámení se změní z **nasazení probíhá...**  k **nasazení bylo úspěšné**. Klikněte na tlačítko **přejděte k prostředku** tlačítko Otevřít robota prostředky.

Jakmile robota je nasazena, klikněte na tlačítko **Test v webového chatu** otevřete podokno webového chatu. Zadejte "hello" v webového chatu.

  ![Testování robota v webového chatu](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Odpoví robota vyslovením "bylo dosaženo pozdrav. Jste uvedli: hello ".  Tato odezva potvrdí, že má robota přijal zprávu a předaný výchozí LEOŠ aplikaci, která ji vytvořit. Toto výchozí nastavení aplikace LEOŠ zjištěna pozdravu záměr. V dalším kroku připojíte robota LEOŠ aplikaci, kterou jste dříve vytvořili místo výchozího LEOŠ aplikace.

## <a name="connect-your-luis-app-to-the-bot"></a>Připojení aplikace LEOŠ k robota

Otevřete **nastavení aplikace** a upravit **LuisAppId** pole tak, aby obsahovala ID aplikace LEOŠ aplikace. Pokud jste vytvořili vaší aplikaci HomeAutomation LEOŠ v jiné oblasti, než západní USA, budete muset změnit **LuisAPIHostName** také. **LuisAPIKey** je nastaveno na klíč pro vytváření obsahu. Můžete to změnit na svůj klíč předplatného při provozu překročí kvótu úroveň free. 

  ![Aktualizovat LEOŠ ID aplikace v Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Pokud nemáte ID aplikace LEOŠ [Domů automatizace aplikace](luis-get-started-create-app.md), přihlaste se k [LEOŠ](luis-reference-regions.md) webu pomocí stejného účtu, který používáte k přihlášení do Azure. 
> 1. Klikněte na **Moje aplikace**. 
> 2. Najděte LEOŠ aplikaci, kterou jste dříve vytvořili, obsahující tříd Intent a entity z domény HomeAutomation.
> 3. V **nastavení** stránku pro aplikaci LEOŠ, najít a zkopírovat ID aplikace. Zkontrolujte, zda je [trained](interactive-test.md) a [publikovaná](PublishApp.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Upravit kód robota

1. Klikněte na tlačítko **sestavení** a pak klikněte na **editor otevřete online kódu**.

   ![Otevřít online editor kódu](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Klikněte pravým tlačítkem na `build.cmd` a zvolte **spustit z konzoly** k sestavení aplikace. Existuje několik kroků sestavení, které služba dokončení automaticky za vás. Sestavení je dokončená, když se dokončila "Bylo úspěšně dokončeno."

3. V editoru kódu otevřete `/Dialogs/BasicLuisDialog.cs`. Obsahuje následující kód:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Změňte kód HomeAutomation záměry


1. Odebrat tři záměrné atributy a metody pro **s pozdravem**, **zrušit**, a **pomoci**. Tyto záměry nejsou v doméně předem HomeAutomation používat. Ujistěte se, aby **žádné** záměrné atribut a metoda. 

2. Přidejte závislosti do horní části souboru, s další závislosti:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Přidejte konstanty ke správě řetězce v horní části `BasicLuisDialog ` třídy:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Přidat kód pro nové záměry z `HomeAutomation.TurnOn` a `HomeAutomation.TurnOff` uvnitř `BasicLuisDialog ` třídy:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Přidejte kód k načtení všech entit nalezena LEOŠ uvnitř `BasicLuisDialog ` třídy:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Změna **ShowLuisResult** metoda v `BasicLuisDialog ` třída zaokrouhleno skóre, shromažďování entity a zobrazí se zpráva odpovědi v chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Sestavení robota
V editoru kódu, klikněte pravým tlačítkem na `build.cmd` a vyberte **spustit z konzoly**.

![Sestavení webové robota ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

Zobrazení kódu se nahradí okno terminálu zobrazující průběh a výsledky sestavení.

![Úspěch sestavení webové robota](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Alternativní metodu sestavení robota je vyberte název robota na horním panelu modré a vyberte **otevřít konzolu Kudu**. Otevře se konzola pro **D:\home**. 
> 
> Změňte adresář na **D:\home\site\wwwroot** zadáním: `cd site\wwwroot`
>
> Spusťte skript sestavení zadáním: `build.cmd`

## <a name="test-the-bot"></a>Testovací robota

Na portálu Azure klikněte na **testů ve webové Chat** k testování robota. Typ zprávy například "Zapnout indikátory" a "vypnout Moje topení" k vyvolání záměry, které jste přidali do ní.

   ![Testování HomeAutomation robota v webového chatu](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Můžete přeučování LEOŠ aplikace bez nutnosti jakékoli úpravy kódu vaší robota. V tématu [přidat příklad utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) a [trénování a testování aplikace s LEOŠ](https://docs.microsoft.com/azure/cognitive-services/LUIS/interactive-test). 

## <a name="download-the-bot-to-debug"></a>Stáhnout robota k ladění
Pokud vaše robota nefunguje, stáhněte projektu do místního počítače a pokračovat [ladění](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-an-azure-app-service-web-app-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Další informace o robota Framework
Další informace o [robota Framework](https://dev.botframework.com/) a [3.x](https://github.com/Microsoft/BotBuilder) a [4.x](https://github.com/Microsoft/botbuilder-dotnet) sady SDK.

## <a name="next-steps"></a>Další postup

Přidejte LEOŠ záměry a robota služby dialogová okna pro zpracování **pomoci**, **zrušit**, a **s pozdravem** tříd Intent. Nezapomeňte cvičení, publikovat a [sestavení](#build-the-bot) robota aplikaci web. LEOŠ i robota by měl mít stejné tříd Intent.

> [!div class="nextstepaction"]
> [Přidat tříd Intent](./luis-how-to-add-intents.md)
> [dočištění řeči](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


<!-- Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-cs-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/csharp
[Github-LUIS-Samples-cs-hotel-bot-readme]: https://github.com/Microsoft/LUIS-Samples/blob/master/bot-integration-samples/hotel-finder/csharp/README.md
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[AssignedEndpointDoc]: https://docs.microsoft.com/azure/cognitive-services/LUIS/manage-keys
[VisualStudio]: https://www.visualstudio.com/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
<!-- tested on Win10 -->

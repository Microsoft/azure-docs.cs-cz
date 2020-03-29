---
title: 'Kurz: Hlasy umožňují botovi používat řečOvou sdk - službu řeči'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte Echo Bot pomocí Microsoft Bot-Framework, nasadit do Azure a zaregistrovat pomocí bot-framework přímé linky řeči kanálu. Pak nakonfigurujete ukázkovou klientskou aplikaci pro Windows, která vám umožní mluvit s vaším robotem a slyšet, jak vám reaguje.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 96d2c2e5e3772575e681d2db079ab0122b7014e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348551"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Kurz: Hlasové povolení robota pomocí sady Speech SDK

Nyní můžete využít sílu služby Řeč snadno hlasově povolit chatovacího robota.

V tomto kurzu vytvoříte Echo Bot pomocí Microsoft Bot-Framework, nasadit do Azure a zaregistrovat pomocí bot-framework přímé linky řeči kanálu. Pak nakonfigurujete ukázkovou klientskou aplikaci pro Windows, která vám umožní mluvit s vaším robotem a slyšet, jak vám reaguje.

Tento kurz je určen pro vývojáře, kteří právě začínají svou cestu s Azure, Bot-Framework roboty, Direct Line Speech nebo Speech SDK a chtějí rychle vytvořit pracovní systém s omezeným kódováním. Žádné zkušenosti nebo obeznámenost s těmito službami je potřeba.

Na konci tohoto cvičení budete mít nastaven systém, který bude fungovat takto:

1. Ukázková klientská aplikace je nakonfigurována pro připojení ke kanálu Direct Line Speech a Echo Bot
1. Zvuk je zaznamenán z výchozího mikrofonu na stisknutí tlačítka (nebo nepřetržitě zaznamenán, pokud je aktivováno vlastní klíčové slovo)
1. Volitelně dojde k vlastní detekci klíčových slov, která streamuje zvuk do cloudu
1. Pomocí sady Speech SDK se aplikace připojuje ke kanálu Direct Line Speech a streamuje zvuk
1. Volitelně se ve službě stane ověření klíčového slova s vyšší přesností.
1. Zvuk je předán službě rozpoznávání řeči a přepsán na text
1. Rozpoznaný text je předán Echo-Bot jako Bot Framework Activity 
1. Text odpovědi je službou Převod textu na řeč převeden na zvuk a vysílaný zpět do klientské aplikace pro přehrávání

![značka diagramu](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Tok řečového kanálu")

> [!NOTE]
> Kroky v tomto kurzu nevyžadují placenou službu. Jako nový uživatel Azure budete moct k dokončení tohoto kurzu použít kredity z bezplatného zkušebního předplatného Azure a bezplatnou úroveň služby Speech.

Zde je to, co tento výukový program pokrývá:
> [!div class="checklist"]
> * Vytvoření nových prostředků Azure
> * Sestavení, testování a nasazení ukázky Echo Botu do služby Azure App Service
> * Zaregistrujte svého robota pomocí kanálu Direct Line Speech
> * Vytvoření a spuštění klienta hlasového asistenta systému Windows pro interakci s robotem Echo
> * Přidání vlastní aktivace klíčových slov
> * Naučte se měnit jazyk uznávané a mluvené řeči

## <a name="prerequisites"></a>Požadavky

Zde je to, co budete potřebovat k dokončení tohoto kurzu:

- Počítač s Windows 10 s funkčním mikrofonem a reproduktory (nebo sluchátky)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) nebo vyšší
- [Sada .NET Core SDK](https://dotnet.microsoft.com/download) verze 2.1 nebo novější
- Účet Azure. [Zaregistrujte se zdarma](https://azure.microsoft.com/free/ai/).
- Účet [GitHub](https://github.com/)
- [Git pro Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Klientská aplikace, kterou vytvoříte v tomto kurzu, používá několik služeb Azure. Chcete-li zkrátit dobu odezvy od vašeho robota, měli byste se ujistit, že tyto služby jsou umístěny ve stejné oblasti Azure. V této části vytvoříte skupinu prostředků v oblasti **Západní USA.** Tato skupina prostředků se použije při vytváření jednotlivých prostředků pro rozhraní Bot-Framework, kanál řeči přímé čáry a službu Řeč.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Vytvoření skupiny prostředků<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. Budete vyzváni k zadání některých informací:
   * Nastavte **předplatné** na **bezplatnou zkušební verzi** (můžete také použít stávající předplatné).
   * Zadejte název **skupiny Prostředků**. Doporučujeme **SpeechEchoBotTutorial-ResourceGroup**.
   * V rozevíracím souboru **Oblast** vyberte **možnost Západní USA**.
1. Klikněte na **Zkontrolovat a vytvořit**. Měli byste vidět banner, který četl **Ověření prošel**.
1. Klikněte na **Vytvořit**. Vytvoření skupiny prostředků může trvat několik minut.
1. Stejně jako u prostředků, které vytvoříte později v tomto kurzu, je vhodné připnout tuto skupinu prostředků na řídicí panel pro snadný přístup. Pokud chcete tuto skupinu prostředků připnout, klikněte na ikonu špendlíku v pravém horním panelu.

### <a name="choosing-an-azure-region"></a>Výběr oblasti Azure

Pokud chcete pro tento kurz použít jinou oblast, mohou tyto faktory omezit vaše volby:

* Ujistěte se, že používáte [podporovanou oblast Azure](regions.md#voice-assistants).
* Kanál řeči přímé čáry používá službu převodu textu na řeč, která má standardní a neurální hlasy. Neurální hlasy jsou [omezené na konkrétní oblasti Azure](regions.md#standard-and-neural-voices).
* Bezplatné zkušební klávesy mohou být omezeny na určitou oblast.

Další informace o oblastech najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Vytvoření prostředků

Teď, když máte skupinu prostředků v podporované oblasti, dalším krokem je vytvoření jednotlivých prostředků pro každou službu, kterou budete používat v tomto kurzu.

### <a name="create-a-speech-service-resource"></a>Vytvoření prostředku služby rozpoznávání řeči

Podle těchto pokynů vytvořte zdroj řeči:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku služby rozpoznávání řeči<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Budete vyzváni k zadání některých informací:
   * Zadejte svému zdroji **název**. Doporučujeme **SpeechEchoBotTutorial-Speech**
   * V **případě předplatného**zkontrolujte, zda je **vybraná bezplatná zkušební verze.**
   * V **popřípadě Umístění**vyberte **možnost Západní USA**.
   * V **případě Cenové úrovně**vyberte možnost **F0**. Toto je bezplatná úroveň.
   * Ve **skupině Resource vyberte** **možnost SpeechEchoBotTutorial-ResourceGroup**.
5. Po zadání všech požadovaných informací klepněte na **tlačítko Vytvořit**. Vytvoření prostředku může trvat několik minut.
6. Dále v tomto kurzu budete potřebovat klíče předplatného pro tuto službu. K těmto klíčům můžete kdykoli přistupovat z **přehledu** prostředků (Spravovat klíče) nebo **klíče**.

V tomto okamžiku zkontrolujte, zda vaše skupina prostředků (**SpeechEchoBotTutorial-ResourceGroup**) má prostředek řeči:

| Name (Název) | Typ  | Umístění |
|------|-------|----------|
| SpeechEchoBotTutorial-Řeč | Cognitive Services | USA – západ |

### <a name="create-an-azure-app-service-plan"></a>Vytvoření plánu služby Azure App Service

Dalším krokem je vytvoření plánu služby App Service. Plán služby App Service definuje sadu výpočetních prostředků pro provozování webové aplikace.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Vytvoření plánu služby Azure App Service<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Budete vyzváni k zadání některých informací:
   * Nastavte **předplatné** na **bezplatnou zkušební verzi** (můžete také použít stávající předplatné).
   * Ve **skupině Resource vyberte** **možnost SpeechEchoBotTutorial-ResourceGroup**.
   * Zadejte svému zdroji **název**. Doporučujeme **SpeechEchoBotTutorial-AppServicePlan**
   * V **programu Operační systém**vyberte **položku Windows**.
   * V **popřípadě Oblast**vyberte **možnost Západní USA**.
   * U **cenové úrovně**se ujistěte, že je **vybránstandard S1.** To by měla být výchozí hodnota. Pokud tomu tak není, ujistěte se, že jste **nastavili operační systém** na **Windows,** jak je popsáno výše.
5. Klikněte na **Zkontrolovat a vytvořit**. Měli byste vidět banner, který četl **Ověření prošel**.
6. Klikněte na **Vytvořit**. Vytvoření skupiny prostředků může trvat několik minut.

V tomto okamžiku zkontrolujte, zda vaše skupina prostředků (**SpeechEchoBotTutorial-ResourceGroup**) má dva prostředky:

| Name (Název) | Typ  | Umístění |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plán služby App Service | USA – západ |
| SpeechEchoBotTutorial-Řeč | Cognitive Services | USA – západ |

## <a name="build-an-echo-bot"></a>Postavte Echo Bot

Nyní, když jste vytvořili nějaké zdroje, pojďme vytvořit bot. Začneme se vzorkem Echo Bot, který, jak název napovídá, odráží text, který jste zadali jako odpověď. Nebojte se, ukázkový kód je připraven k použití bez jakýchkoli změn. Je nakonfigurovaný pro práci s kanálem Direct Line Speech, který připojíme po nasazení robota do Azure.

> [!NOTE]
> Následující pokyny a další informace o Echo Botu jsou k dispozici v [readme ukázky na GitHubu](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Spusťte ukázku robota na vašem počítači

1. Klonovat úložiště ukázek:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Spusťte Visual Studio.
3. Na panelu nástrojů vyberte **Soubor** > **otevřít** > **projekt/řešení**a otevřete řešení projektu Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Po načtení projektu vytvořte a spusťte projekt stisknutím <kbd>klávesy F5.</kbd>
5. Prohlížeč by se měl spustit a zobrazí se obrazovka, která vypadá podobně jako tato.
    > [!div class="mx-imgBorder"]
    > [![echobot-running-on-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot běží na localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Otestujte ukázku robota pomocí emulátoru Framework Bot

[Bot Framework Emulátor](https://github.com/microsoft/botframework-emulator) je desktopová aplikace, která umožňuje vývojářům botů testovat a ladit své roboty lokálně nebo vzdáleně tunelem. Emulátor podporuje zadaný text jako vstup (ne hlas). Bot bude reagovat s textem. Chcete-li pomocí emulátoru Framework botů otestovat robota Echo, který běží místně, pomocí textového a textového výstupu. Po nasazení robota do Azure ho otestujeme pomocí hlasového vstupu a hlasového výstupu.

1. Instalace [emulátoru Rozhraní Bot](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) verze 4.3.0 nebo vyšší
2. Spusťte Robot Framework Emulátor a otevřete bot:
   * **Soubor** -> **Open Bot**.
3. Zadejte adresu URL svého robota. Například:

   ```
   http://localhost:3978/api/messages
   ```
   a stiskněte tlačítko "Connect".
4. Bot by vás měl okamžitě pozdravit s "Ahoj a vítejte!" zpráva. Zadejte libovolnou textovou zprávu a potvrďte, že dostanete odpověď od robota.
5. To je to, co výměna komunikace s instancí Echo Bot může vypadat: [ ![bot-framework-emulátor](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Emulátor rozhraní Bot Framework")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Nasazení robota do služby Azure App Service

Dalším krokem je nasazení Echo Bot do Azure. Existuje několik způsobů, jak nasadit robota, ale v tomto kurzu se zaměříme na publikování přímo z Visual Studia.

> [!NOTE]
> Případně můžete nasadit robota pomocí [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) a [šablony nasazení](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. V sadě Visual Studio otevřete Echo Bot, který byl nakonfigurován pro použití s kanálem Přímé řeči:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na projekt **EchoBot** a vyberte **publikovat...**
1. Otevře se nové okno s názvem **Vyskladnění cíle publikování.**
1. V navigační maješce **služby Azure** vyberte **App Service,** vyberte **Vytvořit nový**a klikněte na **Vytvořit profil**.
1. Když se zobrazí okno **Vytvořit službu app service:**
   * Klikněte na **Přidat účet**a přihlaste se pomocí přihlašovacích údajů k účtu Azure. Pokud jste již přihlášeni, vyberte v rozevíracím seznamu požadovaný účet.
   * Pro **název aplikace**budete muset zadat globálně jedinečný název pro vašeho robota. Tento název se používá k vytvoření jedinečné adresy URL robota. Výchozí hodnota bude naplněna včetně data a času (například: "EchoBot20190805125647"). Pro tento kurz můžete použít výchozí název.
   * U **předplatného**nastavte zkušební **verzi zdarma.**
   * V **případě skupiny prostředků**vyberte možnost **SpeechEchoBotTutorial-ResourceGroup .**
   * Pro **hostování plánu**, vyberte **SpeechEchoBotTutorial-AppServicePlan**
   * Pro **Application Insights**, ponechte jako **žádný**
1. Klikněte na **Vytvořit.**
1. V sadě Visual Studio by se měla zobrazit zpráva o úspěchu, která vypadá takto:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Výchozí prohlížeč by měl otevřít a zobrazit stránku s nápisem: "Váš bot je připraven!".
1. V tomto okamžiku zkontrolujte skupinu prostředků **SpeechEchoBotTutorial-ResourceGroup** na webu Azure portal a potvrďte, že existují tři prostředky:

| Name (Název) | Typ  | Umístění |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA – západ |
| SpeechEchoBotTutorial-AppServicePlan | Plán služby App Service | USA – západ |
| SpeechEchoBotTutorial-Řeč | Cognitive Services | USA – západ |

## <a name="enable-web-sockets"></a>Povolení webových soketů

Budete muset provést malou změnu konfigurace, aby váš robot mohl komunikovat s kanálem Direct Line Speech pomocí webových soketů. Chcete-li povolit webové sokety, postupujte takto:

1. Přejděte na [portál Azure](https://portal.azure.com)a vyhledejte službu App Service. Prostředek by měl být pojmenován podobně jako **EchoBot20190805125647** (název jedinečné aplikace).
2. V navigaci **služeb Azure** klikněte v části **Nastavení**na **Konfigurace**.
3. Vyberte kartu **Obecné nastavení.**
4. Vyhledejte přepínač **pro webové sokety** a nastavte jej **na zapnuto**.
5. Klikněte na **Uložit**.

> [!TIP]
> Ovládací prvky v horní části stránky služby Azure App Service můžete službu zastavit nebo restartovat. To se může hodit při řešení problémů.

## <a name="create-a-channel-registration"></a>Vytvoření registrace kanálu

Teď, když jste vytvořili službu Azure App Service pro hostování vašeho robota, je dalším krokem vytvoření **registrace kanálů botů**. Vytvoření registrace kanálu je předpokladem pro registraci vašeho robota pomocí kanálů Bot-Framework, včetně kanálu Direct Line Speech.

> [!NOTE]
> Pokud se chcete dozvědět více o tom, jak roboti využívají kanály, přečtěte si informace [o připojení robota ke kanálům](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Vytvoření registrace kanálů Azure Bot<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. Budete vyzváni k zadání některých informací:
   * Pro **rukojeť Bot**, zadejte **SpeechEchoBotTutorial-BotRegistration**.
   * V **případě předplatného**vyberte **bezplatnou zkušební verzi**.
   * Ve **skupině Resource vyberte** **možnost SpeechEchoBotTutorial-ResourceGroup**.
   * V **popřípadě Umístění**vyberte **možnost Západní USA**.
     * V **případě Cenové úrovně**vyberte možnost **F0**.
     * Do **koncového bodu zasílání zpráv**zadejte adresu `/api/messages` URL webové aplikace s připojenou cestou na konci. Například: Pokud byl globálně jedinečný název aplikace **EchoBot20190805125647**, koncový `https://EchoBot20190805125647.azurewebsites.net/api/messages/`bod zasílání zpráv bude: .
     * U **přehledů aplikací**můžete tuto možnost nastavit na **vypnuto**. Další informace naleznete v [tématu Bot analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignorovat **Automatické vytváření ID aplikace a heslo**.
5. V dolní části okna **Registrace kanálů robota** klepněte na **tlačítko Vytvořit**.

V tomto okamžiku zkontrolujte skupinu prostředků **SpeechEchoBotTutorial-ResourceGroup** na webu Azure Portal. Nyní by měl yukázat čtyři zdroje:

| Name (Název) | Typ  | Umístění |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA – západ |
| SpeechEchoBotTutorial-AppServicePlan | Plán služby App Service | USA – západ |
| SpeechEchoBotTutorial-BotRegistration | Registrace botových kanálů | global |
| SpeechEchoBotTutorial-Řeč | Cognitive Services | USA – západ |

> [!IMPORTANT]
> Prostředek Registrace kanálů robota zobrazí globální oblast, i když jste vybrali západní USA. To se očekává.

## <a name="register-the-direct-line-speech-channel"></a>Registrace kanálu řeči přímé čáry

Nyní je čas zaregistrovat svého robota pomocí kanálu Direct Line Speech. Tento kanál je to, co se používá k vytvoření spojení mezi echo bot a klientské aplikace zkompilované s sadou Speech SDK.

1. Vyhledejte a otevřete prostředek **SpeechEchoBotTutorial-BotRegistration** na [webu Azure Portal](https://portal.azure.com).
1. V navigaci **služeb Azure** vyberte **Kanály**.
   * Vyhledejte **další kanály**, vyhledejte a klepněte na **položku Přímá řeč**.
   * Zkontrolujte text na stránce s názvem **Konfigurovat přímou řeč a**rozbalte rozevírací nabídku s názvem Účet kognitivní služby.
   * Vyberte prostředek řeči, který jste vytvořili dříve (např. **SpeechEchoBotTutorial-Speech**) z nabídky a přidružte svého robota ke klíči předplatného řeči.
   * Klikněte na **Uložit**.

1. V navigaci **pro správu botů** klepněte na tlačítko **Nastavení**.
   * Zaškrtněte políčko **Povolit koncový bod streamování**. To je potřeba k povolení komunikačního protokolu postaveného na webových soketech mezi robotem a kanálem Direct Line Speech.
   * Klikněte na **Uložit**.

> [!TIP]
> Pokud se chcete dozvědět více, přečtěte si informace [o připojení robota k přímé muzice .](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) Tato stránka obsahuje další informace a známé problémy.

## <a name="build-the-windows-voice-assistant-client"></a>Vytvoření klienta hlasového asistenta systému Windows

V tomto kroku vytvoříte klienta hlasového asistenta systému Windows. Klient je aplikace WPF (Windows Presentation Foundation) v C# , která používá [sadu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) ke správě komunikace s robotem pomocí kanálu Direct Line Speech. Použijte jej k interakci s a testování robota před napsáním vlastní klientské aplikace.

Klient hlasového asistenta systému Windows má jednoduché rozhraní, které umožňuje konfigurovat připojení k robotovi, zobrazit textovou konverzaci, zobrazit aktivity Bot-Framework ve formátu JSON a zobrazit adaptivní karty. Podporuje také používání vlastních klíčových slov. Budete používat tento klient mluvit s bot a přijímat hlasovou odpověď.

Než budeme pokračovat, ujistěte se, že máte zapnutý mikrofon a reproduktory a že fungují.

1. Přejděte do úložiště GitHub pro [klienta hlasového asistenta Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
2. Postupujte podle pokynů k klonování úložiště, sestavení projektu, konfiguraci klienta a spuštění klienta.
3. Klikněte na **Znovu připojit** a ujistěte se, že se zobrazí zpráva **Stiskněte tlačítko mikrofonu nebo zadejte, abyste začali mluvit se svým robotem**.
4. Pojďme to otestovat. Klikněte na tlačítko mikrofonu a promluvte několik slov v angličtině. Rozpoznaný text se zobrazí během mluví. Až budete hotovi mluvit, bot odpoví svým vlastním hlasem, řka: "echo" následovaný rozpoznaná slova.
5. Můžete také použít text pro komunikaci s robotem. Stačí zadat text v dolní liště. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Poradce při potížích s chybami v klientovi hlasového asistenta systému Windows

Pokud se v hlavním okně aplikace zobrazí chybová zpráva, pomocí této tabulky můžete chybu identifikovat a řešit:

| Chyba | Co byste měli udělat? |
|-------|----------------------|
|Chyba authenticationFailure: Upgrade websocketu se nezdařil s chybou ověřování (401). Kontrola správného klíče předplatného (nebo autorizačního tokenu) a názvu oblasti| Na stránce Nastavení aplikace zkontrolujte, zda jste správně zadali klíč Předplatného řeči a jeho oblast.<br>Zkontrolujte, zda byl klíč pro řeč a oblast klíče zadánsprávně. |
|Chyba ConnectionFailure: Připojení bylo ukončeno vzdáleným hostitelem. Kód chyby: 1011. Podrobnosti o chybě: Před odesláním zprávy jsme se nemohli připojit k robotovi. | Zkontrolujte, zda jste [zaškrtli políčko Povolit koncový bod streamování](#register-the-direct-line-speech-channel) nebo [přepnuté **webové sokety** na Zapnuto.](#enable-web-sockets)<br>Ujistěte se, že vaše azure app service běží. Pokud ano, zkuste službu App Service restartovat.|
|Chyba ConnectionFailure: Připojení bylo ukončeno vzdáleným hostitelem. Kód chyby: 1011. Podrobnosti o chybě: Stavový kód odpovědi neoznačuje úspěch: 500 (InternalServerError)| Váš robot zadal neurální hlas ve svém výstupním poli [Aktivita Speak,](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) ale oblast Azure přidružená k klíči předplatného řeči nepodporuje neurální hlasy. Viz [Standardní a neurální hlasy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Chyba ConnectionFailure: Připojení bylo ukončeno vzdáleným hostitelem. Kód chyby: 1000. Podrobnosti o chybě: Překročená maximální doba trvání nečinnosti připojení webového soketu (> 300000 ms)| Jedná se o očekávanou chybu, pokud je připojení ke kanálu ponecháno otevřené a neaktivní po dobu delší než pět minut. |

Pokud váš problém v tabulce neřeší, přečtěte si téma [Hlasoví asistenti: Nejčastější dotazy](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Zobrazit aktivity robota

Každý robot odesílá a přijímá zprávy **aktivity.** V okně **protokol aktivit** klienta hlasového asistenta systému Windows se zobrazí protokoly s časovým razítkem s každou aktivitou, kterou klient obdržel od robota. Můžete také zobrazit aktivity, které klient odeslal robotovi [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) pomocí metody. Když vyberete položku protokolu, zobrazí podrobnosti o přidružené aktivity jako JSON.

Zde je ukázka json aktivity klient obdržel:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Další informace o tom, co se vrátilo ve výstupu JSON, najdete [v polích aktivita](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Pro účely tohoto kurzu se můžete zaměřit na pole [Text](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) a [Mluvit.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Zobrazení zdrojového kódu klienta pro volání sady Speech SDK

Klient hlasového asistenta systému Windows používá balíček NuGet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), který obsahuje sadu Speech SDK. Dobrým místem pro zahájení kontroly ukázkového kódu je metoda [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)InitSpeechConnector() v souboru , která vytváří tyto dva objekty sady Speech SDK:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- Pro nastavení konfigurace (např. klíč předplatného řeči, oblast klíče)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- Chcete-li spravovat připojení kanálu a události předplatného klienta pro zpracování rozpoznaných odpovědí řeči a robota.

## <a name="add-custom-keyword-activation"></a>Přidání vlastní aktivace klíčových slov

Sada Speech SDK podporuje aktivaci vlastního klíčového slova. Podobně jako "Hey Cortana" pro Asistenta společnosti Microsoft můžete napsat aplikaci, která bude nepřetržitě poslouchat klíčové slovo podle vašeho výběru. Mějte na paměti, že klíčové slovo může být jedno slovo nebo fráze s více slovy.

> [!NOTE]
> Termín *klíčové slovo* je často používán zaměnitelně s termínem *wake word*a může se zobrazit oba použité v dokumentaci společnosti Microsoft.

Zjišťování klíčových slov se provádí v klientské aplikaci. Pokud používáte klíčové slovo, zvuk je vysílán do kanálu Řeči přímé čáry pouze v případě, že je klíčové slovo rozpoznáno. Kanál Řeči přímé čáry obsahuje komponentu nazvanou *ověření klíčových slov (KWV),* která provádí složitější zpracování v cloudu, aby ověřila, že zvolené klíčové slovo je na začátku zvukového proudu. Pokud klíčové slovo ověření úspěšné, pak kanál bude komunikovat s robotem.

Následujícím postupem vytvořte model klíčových slov, nakonfigurujte klienta hlasového asistenta systému Windows tak, aby tento model používal, a nakonec jej otestujte pomocí robota.

1. Podle těchto pokynů [vytvořte vlastní klíčové slovo pomocí služby Řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Rozbalte soubor modelu, který jste stáhli v předchozím kroku. Mělo by být pojmenováno podle klíčového slova. Hledáte soubor s názvem `kws.table`.
3. V klientovi hlasového asistenta systému Windows vyhledejte nabídku **Nastavení** (vpravo nahoře vyhledejte ikonu ozubeného kola). Vyhledejte **cestu k souboru modelu** `kws.table` a zadejte úplný název cesty pro soubor z kroku 2.
4. Nezapomeňte zaškrtnout políčko označené **Povoleno**. Měli byste vidět tuto zprávu vedle zaškrtávacího políčka: "Bude poslouchat klíčové slovo při dalším připojení". Pokud jste zadali nesprávný soubor nebo neplatnou cestu, měla by se zobrazit chybová zpráva.
5. Zadejte **klíč předplatného**řeči , **oblast klíče předplatného**a klepnutím na tlačítko **OK** zavřete nabídku **Nastavení.**
6. Klepněte na tlačítko **Znovu připojit**. Měli byste vidět zprávu, která zní: "Nová konverzace byla zahájena - zadejte, stiskněte tlačítko mikrofonu nebo řekněte klíčové slovo". Aplikace nyní neustále naslouchá.
7. Vyslovte jakoukoli frázi, která začíná klíčovým slovem. Například:**"{vaše klíčové slovo}**, kolik je hodin?". Není nutné pozastavit po vyslovení klíčového slova. Po dokončení se stanou dvě věci:
   * Uvidíte přepis toho, co jste mluvil
   * Krátce poté uslyšíte odpověď robota
8. Pokračujte v experimentování se třemi typy vstupů, které váš robot podporuje:
   * Zadaný text v dolním pruhu
   * Stisknutí ikony mikrofonu a mluvení
   * Vyslovení fráze, která začíná klíčovým slovem

### <a name="view-the-source-code-that-enables-keyword"></a>Zobrazení zdrojového kódu, který umožňuje klíčové slovo

Ve zdrojovém kódu klienta hlasového asistenta systému Windows se podívejte na tyto soubory a zkontrolujte kód, který se používá k povolení zjišťování klíčových slov:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)Obsahuje volání metody [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)Speech SDK , která slouží k vytvoření instance modelu z místního souboru na disku.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)obsahuje metodu [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)volání sady Speech SDK , která aktivuje průběžné zjišťování klíčových slov.

## <a name="optional-change-the-language-and-bot-voice"></a>(Nepovinné) Změna jazyka a hlasu robota

Bot, který jste vytvořili, bude naslouchat a reagovat v angličtině, s výchozím hlasem pro převod textu na řeč v angličtině. Nejste však omezeni na používání angličtiny nebo výchozího hlasu. V této části se dozvíte, jak změnit jazyk, který bude váš robot poslouchat a reagovat. Dozvíte se také, jak vybrat jiný hlas pro tento jazyk.

### <a name="change-the-language"></a>Změna jazyka

Můžete si vybrat z libovolného jazyka uvedeného v tabulce [převodřeči na text.](language-support.md#speech-to-text) V níže uvedeném příkladu změníme jazyk na němčinu.

1. Otevřete klientskou aplikaci Windows Voice Assistant, klikněte na tlačítko `de-de` nastavení (ikona pravého horního ozubeného kola) a zadejte do pole Jazyk (toto je hodnota národního prostředí uvedená v tabulce [převodu řeči na text).](language-support.md#speech-to-text) Tím nastavíte rozpoznávání mluveného jazyka, který přepíše výchozí hodnotu `en-us`. To také dává pokyn Direct Line řeči kanál použít výchozí německý hlas pro odpověď Bot.
2. Zavřete stránku nastavení a kliknutím na tlačítko Znovu se připojit navážete na nové připojení k echo botu.
3. Klikněte na tlačítko mikrofonu a řekněte frázi v němčině. Uvidíte rozpoznaný text a echo bot odpověď s výchozím německým hlasem.

### <a name="change-the-default-bot-voice"></a>Změna výchozího hlasu robota

Výběr hlasu převodu textu na řeč a ovládání výslovnosti lze provést, pokud bot určuje odpověď ve formě [jazyka SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) (SSML) namísto jednoduchého textu. Echo bot nepoužívá SSML, ale můžeme snadno upravit kód k tomu, že. V níže uvedeném příkladu přidáme SSML do odpovědi echo bot, takže německý hlas Stefan Apollo (mužský hlas) bude použit namísto výchozího ženského hlasu. Podívejte se na seznam [standardních a](language-support.md#standard-voices) [neurálních zvuků podporovaných](language-support.md#neural-voices) pro váš jazyk.

1. Začněme otevřením `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Vyhledejte tyto dva řádky:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Nahraďte je:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Sestavte si své řešení v sadě Visual Studio a opravte všechny chyby sestavení.

Druhý argument v metodě MessageFactory.Text nastaví [aktivitu mluvit pole](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) v odpovědi robota. S výše uvedenou změnou byl nahrazen z jednoduchého textu na SSML, aby bylo možné určit nevýchozí německý hlas.

### <a name="redeploy-your-bot"></a>Znovu nasadit svého robota

Teď, když jste provedli potřebnou změnu robota, je dalším krokem znovu publikovat do služby Azure App Service a vyzkoušet si to:

1. V okně Průzkumník řešení klikněte pravým tlačítkem myši na projekt **EchoBot** a vyberte **publikovat**.
2. Předchozí konfigurace nasazení již byla načtena jako výchozí. Jednoduše klepněte na tlačítko **Publikovat** vedle **položky EchoBot20190805125647 - Web Deploy**.
3. Zpráva **Úspěšné publikování** se zobrazí ve výstupním okně sady Visual Studio a spustí se webová stránka se zprávou "Váš robot je připraven!".
4. Otevřete klientskou aplikaci Windows Voice Assistant, klikněte na tlačítko nastavení (ikona pravého horního ozubeného kola) a ujistěte se, že máte `de-de` stále v poli Jazyk.
5. Podle pokynů v [části Sestavení klienta hlasového asistenta systému Windows](#build-the-windows-voice-assistant-client) se znovu připojte k nově nasazenému robotovi, mluvte v novém jazyce a uslyšíte odpověď robota v tomto jazyce novým hlasem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v používání echo-bot uplatní v tomto kurzu, můžete odebrat a všechny jeho přidružené prostředky Azure jednoduše odstraněním skupiny prostředků Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. Na [webu Azure Portal](https://portal.azure.com)klikněte na **Skupiny prostředků** z navigace služeb **Azure.**
2. Vyhledejte skupinu prostředků s názvem **SpeechEchoBotTutorial-ResourceGroup**. Klikněte na tři tečky (...).
3. Vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření vlastní klientské aplikace pomocí sady Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Viz také

* Nasazení do [oblasti Azure ve vašem okolí,](https://azure.microsoft.com/global-infrastructure/locations/) abyste viděli zlepšení doby odezvy robota
* Nasazení do [oblasti Azure, která podporuje vysoce kvalitní hlasy TTS neural](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Ceny spojené s kanálem Přímé řeči linky:
  * [Ceny služby Bot Service](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Služba Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Vytváření a nasazování vlastního robota s podporou hlasu:
  * Sestavte [bot-framework bot](https://dev.botframework.com/). Zaregistrujte jej pomocí [kanálu Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) a [přizpůsobte si robota pro hlasové](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Prozkoumejte stávající [řešení Bot-Framework:](https://microsoft.github.io/botframework-solutions/index)Sestavte [si virtuálního asistenta](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) a [rozšiřte ho na direct line speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)

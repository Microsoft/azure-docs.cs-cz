---
title: 'Kurz: hlasy umožňují robotům používat sadu Speech SDK – Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte robota s odezvou s použitím Microsoft bot-Frameworku, nasadíte ho do Azure a zaregistrujete ho pomocí kanálu s přímým odkazem na rozhraní robota. Potom nakonfigurujete ukázkovou klientskou aplikaci pro Windows, která vám umožní mluvit na robota a slyšet na vás odpověď.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: dcohen
ms.openlocfilehash: b42314d1c8c1bd734181f02c36ae3f43507e9b79
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815202"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Kurz: hlas – povolení robota pomocí sady Speech SDK

Nyní můžete použít sílu služby řeči ke snadnému hlasu – povolte robota chatu.

V tomto kurzu vytvoříte robota s odezvou s použitím Microsoft bot-Frameworku, nasadíte ho do Azure a zaregistrujete ho pomocí kanálu s přímým odkazem na rozhraní robota. Potom nakonfigurujete ukázkovou klientskou aplikaci pro Windows, která vám umožní mluvit na robota a slyšet na vás odpověď.

Tento kurz je určený pro vývojáře, kteří právě spouštějí svou cestu pomocí Azure, robot-Framework roboty, Direct line Speech nebo sady Speech SDK a chcete rychle vytvořit funkční systém s omezeným kódováním. Tyto služby nevyžadují žádné zkušenosti ani obeznámení s nimi.

Na konci tohoto cvičení jste nastavili systém, který bude fungovat následujícím způsobem:

1. Ukázková klientská aplikace je nakonfigurovaná tak, aby se připojovala k přímému line kanálu a robotovi s odezvou
1. Zvuk se zaznamenává z výchozího mikrofonu při stisknutí tlačítka (nebo nepřetržitě zaznamenaný, pokud je aktivované vlastní klíčové slovo).
1. Volitelně se detekce vlastního klíčového slova stane, když se do cloudu zavede audio streamování.
1. Pomocí sady Speech SDK se aplikace připojuje k přímému line kanálu řeči a zvuk streamů.
1. Volitelně se u služby stane ověření klíčového slova s vyšší přesností.
1. Zvuk se předává službě rozpoznávání řeči a přepisu se na text.
1. Rozpoznaný text se předává funkci echo-bot jako aktivity bot Frameworku. 
1. Text odpovědi je převedený na zvuk ve službě převod textu na mluvené slovo (TTS) a streamuje se zpět do klientské aplikace pro přehrávání.

![Diagram – značka](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Tok kanálu řeči")

> [!NOTE]
> Kroky v tomto kurzu nevyžadují placenou službu. Jako nový uživatel Azure budete moct k dokončení tohoto kurzu použít kredity ze bezplatného zkušebního předplatného Azure a bezplatné úrovně služby Speech.

V tomto kurzu se dozvíte, co tento kurz popisuje:
> [!div class="checklist"]
> * Vytvoření nových prostředků Azure
> * Sestavování, testování a nasazování vzorku echo bot na Azure App Service
> * Zaregistrujte si robot s přímým line Speech Channel.
> * Sestavení a spuštění klienta Direct line Speech pro interakci s vaším robotem ozvěny
> * Přidat vlastní aktivaci klíčového slova
> * Naučte se změnit jazyk rozpoznaného a mluveného řeči.

## <a name="prerequisites"></a>Předpoklady

Tady je postup, který budete potřebovat k dokončení tohoto kurzu:

- POČÍTAČ s Windows 10 s pracovním mikrofonem a reproduktory (nebo sluchátka)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) nebo vyšší
- [.NET Core SDK](https://dotnet.microsoft.com/download) verze 2,1 nebo novější
- Účet Azure. [Zaregistrujte se zdarma](https://azure.microsoft.com/free/ai/).
- Účet [GitHub](https://github.com/)
- [Git pro Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Klientská aplikace, kterou vytvoříte v tomto kurzu, používá několik služeb Azure. Aby se snížila doba odezvy pro odpovědi z bot, měli byste se ujistit, že tyto služby jsou umístěné ve stejné oblasti Azure. V této části vytvoříte skupinu prostředků v oblasti **západní USA** . Tato skupina prostředků se použije při vytváření individuálních prostředků pro robot-Framework, kanál přímého zpracování řeči a službu pro rozpoznávání řeči.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V levém navigačním panelu vyberte **skupiny prostředků**. Pak kliknutím na **Přidat** přidejte novou skupinu prostředků.
1. Zobrazí se výzva k zadání některých informací:
   * Nastavte **předplatné** na **bezplatnou zkušební verzi** (můžete také použít stávající předplatné).
   * Zadejte název vaší **skupiny prostředků**. Doporučujeme **SpeechEchoBotTutorial-Resource**.
   * V rozevíracím seznamu **oblast** vyberte možnost **západní USA**.
1. Klikněte na tlačítko **zkontrolovat a vytvořit**. Měla by se zobrazit informační zpráva s **potvrzením**, že čtení bylo úspěšné.
1. Klikněte na **Vytvořit**. Vytvoření skupiny prostředků může trvat několik minut.
1. Stejně jako u prostředků, které vytvoříte později v tomto kurzu, je vhodné Připnout tuto skupinu prostředků na řídicí panel a získat tak snadný přístup. Pokud chcete tuto skupinu prostředků připnout, klikněte na ikonu připnutí v pravém horním rohu řídicího panelu.

### <a name="choosing-an-azure-region"></a>Výběr oblasti Azure

Pokud chcete pro tento kurz použít jinou oblast, můžou tyto faktory omezit vaše možnosti:

* Ujistěte se, že používáte [podporovanou oblast Azure](regions.md#voice-assistants).
* Kanál Direct line Speech používá službu pro převod textu na mluvené slovo, která má standardní a neuronové hlasy. Hlasy neuronové jsou [omezené na konkrétní oblasti Azure](regions.md#standard-and-neural-voices).
* Bezplatné zkušební klíče můžou být omezené na konkrétní oblast.

Další informace o oblastech najdete v tématu věnovaném [umístěním Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Vytvoření prostředků

Teď, když máte skupinu prostředků v oblasti **západní USA** , je dalším krokem vytvoření jednotlivých prostředků pro každou službu, kterou použijete v tomto kurzu.

### <a name="create-a-speech-service-resource"></a>Vytvoření prostředku služby Speech

Pomocí těchto pokynů vytvořte prostředek řeči:

1. Přejít na [Azure Portal](https://portal.azure.com) a v levém navigačním panelu vyberte **vytvořit prostředek** .
2. Na panelu hledání zadejte **Speech**.
3. Vyberte **řeč**a pak klikněte na **vytvořit**.
4. Zobrazí se výzva k zadání některých informací:
   * Zadejte **název**prostředku. Doporučujeme **SpeechEchoBotTutorial** řeč.
   * U **předplatného**se ujistěte, že je vybraná možnost **bezplatná zkušební verze** .
   * V **oblasti umístění**vyberte **západní USA**.
   * V případě **cenové úrovně**vyberte **F0**. Toto je úroveň Free.
   * V případě **skupiny prostředků**vyberte **SpeechEchoBotTutorial-Resource**.
5. Po zadání všech požadovaných informací klikněte na **vytvořit**. Vytvoření prostředku může trvat několik minut.
6. Později v tomto kurzu budete pro tuto službu potřebovat klíče předplatného. K těmto klíčům můžete kdykoli přistupovat z **přehledu** svého prostředku (Správa klíčů) nebo **klíčů**.

V tomto okamžiku ověřte, že skupina prostředků (**SpeechEchoBotTutorial-resourceName**) má prostředek řeči:

| JMÉNO | TYP  | UMÍSTĚNÍ |
|------|-------|----------|
| SpeechEchoBotTutorial – řeč | Cognitive Services | Západní USA |

### <a name="create-an-azure-app-service-plan"></a>Vytvoření plánu služby Azure App Service

Dalším krokem je vytvoření plánu App Service. Plán služby App Service definuje sadu výpočetních prostředků pro provozování webové aplikace.

1. Přejít na [Azure Portal](https://portal.azure.com) a v levém navigačním panelu vyberte **vytvořit prostředek** .
2. Do panelu hledání zadejte **App Service plán**. Potom ve výsledcích hledání vyhledejte a vyberte kartu **App Service plán** .
3. Klikněte na **Vytvořit**.
4. Zobrazí se výzva k zadání některých informací:
   * Nastavte **předplatné** na **bezplatnou zkušební verzi** (můžete také použít stávající předplatné).
   * V případě **skupiny prostředků**vyberte **SpeechEchoBotTutorial-Resource**.
   * Zadejte **název**prostředku. Doporučujeme **SpeechEchoBotTutorial-AppServicePlan** .
   * V případě **operačního systému**vyberte možnost **Windows**.
   * V **oblasti oblast**vyberte **západní USA**.
   * V případě **cenové úrovně**se ujistěte, že je vybraná **úroveň Standard S1** . Mělo by se jednat o výchozí hodnotu. Pokud ne, ujistěte se, že jste nastavili **operační systém** na **Windows** , jak je popsáno výše.
5. Klikněte na tlačítko **zkontrolovat a vytvořit**. Měla by se zobrazit informační zpráva s **potvrzením**, že čtení bylo úspěšné.
6. Klikněte na **Vytvořit**. Vytvoření skupiny prostředků může trvat několik minut.

V tomto okamžiku ověřte, že vaše skupina prostředků (**SpeechEchoBotTutorial-resourceName**) má dva prostředky:

| JMÉNO | TYP  | UMÍSTĚNÍ |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plán služby App Service | Západní USA |
| SpeechEchoBotTutorial – řeč | Cognitive Services | Západní USA |

## <a name="build-an-echo-bot"></a>Sestavení robota s odezvou

Teď, když jste vytvořili nějaké prostředky, pojďme vytvořit robota. Budeme začít s ukázkou pro echo bot, který jako název napovídá, vrátí text, který jste zadali jako odpověď. Nedělejte si starosti, vzorový kód je připravený k použití bez jakýchkoli změn. Je nakonfigurovaná tak, aby fungovala s přímým kanálem pro rozpoznávání řeči, ke kterému se připojíme po nasazení robota do Azure.

> [!NOTE]
> Následující pokyny a další informace o službě echo bot jsou k dispozici v [souboru Readme ukázky na GitHubu](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Spuštění ukázky robota na počítači

1. Naklonujte úložiště ukázek:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Spusťte sadu Visual Studio.
3. Na panelu nástrojů vyberte **soubor** > **otevřete** > **projekt/řešení**a otevřete řešení pro odezvu na ozvěnu.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Po načtení projektu stiskněte `F5` a sestavte a spusťte projekt.

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Test ukázky robota pomocí emulátoru bot Framework

[Emulátor systému robot Framework](https://github.com/microsoft/botframework-emulator) je desktopová aplikace, která umožňuje vývojářům robotům testovat a ladit roboty místně nebo vzdáleně prostřednictvím tunelu. Emulátor podporuje zadaný text jako vstup (ne hlas). Robot bude reagovat na text. Postupujte podle těchto kroků a pomocí emulátoru robot Framework otestujte vaši aplikaci echo robot spuštěnou lokálně s textovým vstupem a textovým výstupem. Po nasazení bot Azure ho budeme testovat pomocí hlasového vstupu a hlasového výstupu.

1. Nainstalujte [emulátor systému bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) verze 4.3.0 nebo novější.
2. Spusťte emulátor rozhraní bot Framework a otevřete robota:
   * **Soubor** -> **otevřete robot**.
3. Zadejte adresu URL pro robota. Například:

   ```
   http://localhost:3978/api/messages
   ```
   a stiskněte klávesu "připojit".
4. Robot by vás měl okamžitě popozdravit "Hello a Welcome!" . Zadejte jakoukoli textovou zprávu a potvrďte, že od robota dostanete odpověď.

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Nasazení robota na Azure App Service

Dalším krokem je nasazení nástroje echo bot do Azure. Existuje několik způsobů, jak nasadit robot, ale v tomto kurzu se zaměříme na publikování přímo ze sady Visual Studio.

> [!NOTE]
> Alternativně můžete nasadit robota pomocí [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) a [šablon nasazení](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. V aplikaci Visual Studio otevřete robota s odezvou, která je nakonfigurovaná pro použití s přímým linkovým kanálem řeči:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. V **Průzkumník řešení**klikněte pravým tlačítkem na řešení **EchoBot** a vyberte **publikovat...**
1. Otevře se nové okno s názvem **Vyberte cíl publikování** .
1. V levém navigačním panelu vyberte **App Service** , vyberte **vytvořit novou**a potom klikněte na **publikovat**.
1. Po zobrazení okna **vytvořit App Service** :
   * Klikněte na **Přidat účet**a přihlaste se pomocí svých přihlašovacích údajů k účtu Azure. Pokud jste už přihlášení, v rozevíracím seznamu vyberte požadovaný účet.
   * Pro **název aplikace**budete muset pro robot zadat globálně jedinečný název. Tento název se používá k vytvoření jedinečné adresy URL robota. Bude naplněna výchozí hodnota včetně data a času (například: "EchoBot20190805125647"). Pro tento kurz můžete použít výchozí název.
   * U **předplatného**nastavte na **bezplatnou zkušební verzi** .
   * V případě **skupiny prostředků**vyberte **SpeechEchoBotTutorial-Resource** .
   * V případě **plánu hostování**vyberte **SpeechEchoBotTutorial-AppServicePlan**
1. Klikněte na **Vytvořit**
1. V aplikaci Visual Studio by se měla zobrazit zpráva o úspěchu, která vypadá takto:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Výchozí prohlížeč by měl otevřít a zobrazit stránku, která čte: "vaše robot je připravený!".
1. V tomto okamžiku zkontrolujte skupinu prostředků **SpeechEchoBotTutorial-source** ve Azure Portal a potvrďte, že existují tři prostředky:

| JMÉNO | TYP  | UMÍSTĚNÍ |
|------|-------|----------|
| EchoBot20190805125647 | Aplikační služba | Západní USA |
| SpeechEchoBotTutorial-AppServicePlan | Plán služby App Service | Západní USA |
| SpeechEchoBotTutorial – řeč | Cognitive Services | Západní USA |

## <a name="enable-web-sockets"></a>Povolit webové sokety

Budete muset udělat malou změnu konfigurace, aby robot mohl komunikovat s přímým řádkem řeči pomocí webových soketů. Pokud chcete povolit webové sokety, postupujte podle těchto kroků:

1. Přejděte do [Azure Portal](https://portal.azure.com)a vyhledejte App Service. Prostředek by měl mít název podobný jako **EchoBot20190805125647** (jedinečný název aplikace).
2. V levém navigačním panelu v části **Nastavení**klikněte na **Konfigurace**.
3. Vyberte kartu **Obecné nastavení** .
4. Vyhledejte přepínač pro **webové sokety** a nastavte jej na **zapnuto**.
5. Klikněte na **Uložit**.

> [!TIP]
> Pomocí ovládacích prvků v horní části stránky Azure App Service můžete službu zastavit nebo restartovat. To může být užitečné při řešení potíží.

## <a name="create-a-channel-registration"></a>Vytvoření registrace kanálu

Teď, když jste vytvořili Azure App Service pro hostování robota, je dalším krokem vytvoření **registrace kanálů robota**. Vytvoření registrace kanálu je předpokladem pro registraci robota pomocí kanálů robot-Framework, včetně kanálu Direct line Speech.

> [!NOTE]
> Pokud se chcete dozvědět víc o tom, jak roboty využívají kanály, přečtěte si téma [připojení robota k kanálům](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

1. Prvním krokem je vytvoření nového prostředku pro registraci. Na portálu [Azure portal](https://portal.azure.com) klikněte na možnost **Vytvořit prostředek**.
2. Na panelu hledání napište **robot**, po zobrazení výsledků vyberte možnost **registrace kanálů robota**.
3. Klikněte na **Vytvořit**.
4. Zobrazí se výzva k zadání některých informací:
   * Jako **název bot**zadejte **SpeechEchoBotTutorial-BotRegistration**.
   * V případě **předplatného**vyberte **bezplatná zkušební verze**.
   * V případě **skupiny prostředků**vyberte **SpeechEchoBotTutorial-Resource**.
   * V **oblasti umístění**vyberte **západní USA**.
     * V případě **cenové úrovně**vyberte **F0**.
     * Do pole **koncový bod pro zasílání zpráv**zadejte adresu URL webové aplikace s cestou k `/api/messages`, která je připojena na konci. Příklad: Pokud byl váš globálně jedinečný název aplikace **EchoBot20190805125647**, koncový bod pro zasílání zpráv by byl: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * V případě služby **Application Insights**můžete tuto možnost nastavit na **vypnuto**. Další informace najdete v tématu věnovaném [analýze robotů](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignoruje **Automatické vytvoření ID a heslo aplikace**.
5. Přejděte zpátky do **registrace kanálů robota** a klikněte na **vytvořit**.

V tomto okamžiku ověřte skupinu prostředků **SpeechEchoBotTutorial-Resource** v Azure Portal. Teď by se měly zobrazit čtyři prostředky:

| JMÉNO | TYP  | UMÍSTĚNÍ |
|------|-------|----------|
| EchoBot20190805125647 | Aplikační služba | Západní USA |
| SpeechEchoBotTutorial-AppServicePlan | Plán služby App Service | Západní USA |
| SpeechEchoBotTutorial-BotRegistration | Bot Channels Registration | Globální |
| SpeechEchoBotTutorial – řeč | Cognitive Services | Západní USA |

> [!IMPORTANT]
> Prostředek registrace kanálů robota zobrazí globální oblast, i když jste vybrali Západní USA. To se očekává.

## <a name="register-the-direct-line-speech-channel"></a>Registrace kanálu Direct line Speech

Teď je čas zaregistrovat robota pomocí kanálu Direct line Speech. Tento kanál slouží k vytvoření propojení mezi robotem ozvěny a klientskou aplikací zkompilovanou sadou Speech SDK.

1. Vyhledejte a otevřete prostředek **SpeechEchoBotTutorial-BotRegistration** v [Azure Portal](https://portal.azure.com).
1. V levém navigačním panelu vyberte **kanály**.
   * Podívejte se na **Další kanály**, najděte a klikněte na **Direct line Speech**.
   * Projděte si text na stránce s názvem **Konfigurace přímého řádku řeč**a pak rozbalte rozevírací nabídku s názvem "účet služby pro rozpoznávání".
   * Vyberte prostředek řeči, který jste předtím vytvořili (např. **SpeechEchoBotTutorial-Speech**) z nabídky a přidružte svůj robot k vašemu klíči předplatného řeči.
   * Klikněte na **Uložit**.

1. V levém navigačním panelu klikněte na **Nastavení**.
   * Zaškrtněte políčko s popiskem **Povolit koncový bod streamování**. Tato možnost je nutná k povolení komunikačního protokolu postaveného na webových soketech mezi robotem a kanálem Direct line Speech.
   * Klikněte na **Uložit**.

> [!TIP]
> Pokud se chcete dozvědět víc, přečtěte si téma [připojení robota k přímému line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Tato stránka obsahuje další informace a známé problémy.

## <a name="build-the-direct-line-speech-client"></a>Vytvoření klienta Direct line Speech

V tomto kroku sestavíte klienta Direct line Speech. Klient je aplikace Windows Presentation Foundation (WPF) v C# systému, která používá [sadu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) ke správě komunikace s robotem pomocí kanálu Direct line Speech. Použijte ho k interakci s a otestujte bot před psaním vlastní klientské aplikace.

Klient Direct line Speech má jednoduché uživatelské rozhraní, které umožňuje nakonfigurovat připojení k robotovi, zobrazit textovou konverzaci, zobrazit aktivity robota v rámci formátu JSON a zobrazit adaptivní karty. Podporuje také použití vlastních klíčových slov. Tohoto klienta použijete ke mluvit s robotem a dostanete hlasovou reakci.

Než začneme, ujistěte se, že mikrofon a reproduktory jsou povolené a fungují.

1. Přejděte do úložiště GitHub pro [klienta Direct line Speech](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md).
2. Postupujte podle pokynů uvedených k naklonování úložiště, sestavte projekt, nakonfigurujte klienta a spusťte klienta.
3. Klikněte na **znovu připojit** a ujistěte se, že se zobrazí zpráva **stiskněte tlačítko mikrofonu, nebo napište, abyste mohli začít mluvit s robotem**.
4. Pojďme si to vyzkoušet. Klikněte na tlačítko mikrofon a mluvte několik slov v angličtině. Rozpoznaný text se zobrazí při mluvení. Až budete hotovi, robot odpoví ve svém vlastním hlasu a říká "echo" následovaný rozpoznanými slovy.
5. K komunikaci s robotem můžete také použít text. Stačí zadat text na dolním panelu. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Řešení chyb v klientovi Direct line Speech

Pokud se zobrazí chybová zpráva v hlavním okně aplikace, použijte tuto tabulku k identifikaci a řešení potíží s chybou:

| Chyba | Co byste měli udělat? |
|-------|----------------------|
|Chyba AuthenticationFailure: upgrade protokolu WebSocket se nezdařil s chybou ověřování (401). Vyhledat správný klíč předplatného (nebo autorizační token) a název oblasti| Na stránce nastavení aplikace se ujistěte, že jste zadali správný klíč předplatného pro rozpoznávání řeči a jeho oblast.<br>Ujistěte se, že klíč řeči a klíčová oblast byly zadány správně. |
|Chyba ConnectionFailure: připojení bylo ukončeno vzdáleným hostitelem. Kód chyby: 1011. Podrobnosti o chybě: před odesláním zprávy se nepovedlo připojit k robotovi. | Ujistěte se, že jste [zaškrtli políčko Povolit koncový bod streamování](#register-the-direct-line-speech-channel) nebo na zapnuté [ **webové zásuvky** ](#enable-web-sockets) .<br>Ujistěte se, že je spuštěný Azure App Service. Pokud je to, zkuste restartovat App Service.|
|Chyba ConnectionFailure: připojení bylo ukončeno vzdáleným hostitelem. Kód chyby: 1011. Podrobnosti o chybě: kód stavu odpovědi neindikuje úspěch: 500 (Nenalezeno)| Vaše robot zadal neuronové hlas do výstupního pole [speaking](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) Activity, ale oblast Azure přidružená k vašemu klíči předplatného pro rozpoznávání řeči nepodporuje hlasy neuronové. Viz [hlasy Standard a neuronové](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Chyba ConnectionFailure: připojení bylo ukončeno vzdáleným hostitelem. Kód chyby: 1000. Podrobnosti o chybě: překročení maximální doby nečinnosti připojení webového soketu (> 300000 MS)| Jedná se o očekávanou chybu, pokud je připojení k kanálu otevřené a neaktivní po dobu delší než 5 minut. |

Pokud se problém nevyřeší v tabulce, přečtěte si téma [asistenti hlasu: nejčastější dotazy](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Zobrazit aktivity robota

Každý robot odesílá a přijímá zprávy o **aktivitách** . V okně **Protokol aktivit** klienta Direct line Speech se zobrazí protokoly s časovým razítkem s každou aktivitou, kterou klient přijal od robota. Můžete také zobrazit aktivity, které klient poslal do robota pomocí metody [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) . Když vyberete položku protokolu, zobrazí se podrobnosti související aktivity jako JSON.

Tady je ukázkový JSON aktivity, kterou klient přijal:
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

Další informace o tom, co se vrátilo ve výstupu JSON, najdete v tématu [pole v aktivitě](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Pro účely tohoto kurzu se můžete zaměřit na pole [text](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) a [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) .

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Zobrazit zdrojový kód klienta pro volání sady Speech SDK

Klient nástroje Direct line Speech používá balíček NuGet [Microsoft. cognitiveservices Account. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), který obsahuje sadu Speech SDK. Dobrým místem, kde začít kontrolovat vzorový kód, je metoda InitSpeechConnector () v souboru [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs), který vytváří tyto dva objekty sady Speech SDK:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) – nastavení konfigurace (například klíč předplatného pro řeč, klíčová oblast)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) – Správa připojení kanálu a událostí odběru klienta pro zpracování rozpoznaných odpovědí na řeč a robotů.

## <a name="add-custom-keyword-activation"></a>Přidat vlastní aktivaci klíčového slova

Sada Speech SDK podporuje aktivaci vlastního klíčového slova. Podobně jako "Hey Cortana" pro pomocníka společnosti Microsoft můžete napsat aplikaci, která bude průběžně naslouchat klíčovému slovu podle vašeho výběru. Mějte na paměti, že klíčové slovo může být jedno slovo nebo fráze více slov.

> [!NOTE]
> *Klíčové slovo* Term se často používá k zaznamenání na *probouzení slova*a může se zobrazit v dokumentaci Microsoftu.

Rozpoznávání klíčových slov se provádí v klientské aplikaci. Při použití klíčového slova je zvuk streamování do kanálu s přímým přístupem, pokud je klíčové slovo rozpoznáno. Kanál Direct line Speech zahrnuje komponentu s názvem *ověřování klíčovým slovem (KWV)* , která provádí složitější zpracování v cloudu, aby se ověřilo, že klíčové slovo, které jste zvolili, je na začátku zvukového streamu. Pokud se ověření pomocí klíčového slova zdaří, kanál bude komunikovat s robotem.

Pomocí těchto kroků vytvoříte model klíčových slov, nakonfigurujete klienta Direct line Speech pro použití tohoto modelu a nakonec ho otestujete pomocí robota.

1. Podle těchto pokynů [vytvořte vlastní klíčové slovo pomocí služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Rozbalte soubor modelu, který jste stáhli v předchozím kroku. Měla by být pojmenována pro vaše klíčové slovo. Hledáte soubor s názvem `kws.table`.
3. V klientovi Direct line Speech Vyhledejte nabídku **Nastavení** (v pravém horním rohu najděte ikonu ozubeného kolečka). Vyhledejte **cestu k souboru modelu** a zadejte úplnou cestu k souboru `kws.table` z kroku 2.
4. Nezapomeňte zaškrtnout políčko označený jako **povolené**. Tato zpráva by se měla zobrazit vedle zaškrtávacího políčka: "při dalším připojení naslouchat klíčové slovo." Pokud jste zadali nesprávný soubor nebo neplatnou cestu, měla by se zobrazit chybová zpráva.
5. Zadejte **klíč předplatného**pro rozpoznávání řeči, **oblast klíče předplatného**a potom kliknutím na tlačítko **OK** zavřete nabídku **Nastavení** .
6. Klikněte na **znovu připojit**. Měla by se zobrazit zpráva, že "nová konverzace byla spuštěna – typ, stiskněte tlačítko mikrofonu, nebo řekněte klíčové slovo". Aplikace teď neustále naslouchá.
7. Mluvte všechny fráze začínající vaším klíčovým slovem. Například: " **{Your klíčové slovo}** , jaký je čas?". Po umístění klíčového slova se nemusíte porušit. Po dokončení budou provedeny dvě věci:
   * Uvidíte si, co jste paprskový
   * Krátce po vás uslyšíte odpověď robota.
8. Pokračujte v experimentování se třemi vstupními typy, které vaše robot podporuje:
   * Zadaný text na dolním panelu
   * Stisknutí ikony mikrofonu a mluvení
   * Říká se fráze začínající vaším klíčovým slovem.

### <a name="view-the-source-code-that-enables-keyword"></a>Zobrazit zdrojový kód, který povoluje klíčové slovo

Ve zdrojovém kódu klienta Direct line Speech si podíváte se na tyto soubory a zkontrolujte kód, který se používá k povolení rozpoznávání klíčových slov:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) obsahuje volání metody sady Speech SDK [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-), která se používá k vytvoření instance modelu z místního souboru na disku.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) obsahuje volání metody sady Speech SDK [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync), která aktivuje funkci zjišťování nepřetržitého klíčového slova.

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>Volitelné Změna jazyka a opětovné nasazení robota

Robot, který jste vytvořili, bude naslouchat v angličtině a reagovat na ně. Nejste však omezeni na použití angličtiny. V této části se dozvíte, jak změnit jazyk, ve kterém bude robot naslouchat a reagovat na a znovu nasaďte robot.

### <a name="change-the-language"></a>Změnit jazyk

1. Pojďme začít otevřením `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. V dalším kroku vyhledejte SSML. Hledání je snadné, protože je uzavřeno v `<speak></speak>` značek.
3. V řetězci SSML vyhledejte značku `<voice name>`, nahraďte ji `<voice name='de-DE-Stefan-Apollo'>`a uložte. Tento formátovaný řetězec oznamuje službě převod textu na mluvené slovo, aby vracela hlasovou odpověď pomocí hlasového `de-DE-Stefan-Apollo`, která je optimalizována pro němčinu.

>[!NOTE]
> Nejste omezeni na němčinu a můžete si vybrat ze seznamu dostupných hlasů od [služby Speech](language-support.md#text-to-speech).

### <a name="redeploy-your-bot"></a>Opětovné nasazení robota

Teď, když jste udělali potřebnou změnu robota, je dalším krokem jeho opětovné publikování do Azure App Service a pokus o to:

1. Sestavte řešení v aplikaci Visual Studio a opravte všechny chyby sestavení.
2. V okně Průzkumník řešení klikněte pravým tlačítkem na projekt **EchoBot** a vyberte **publikovat**.
3. Předchozí konfigurace nasazení již byla načtena jako výchozí. Jednoduše klikněte na **publikovat** vedle **EchoBot20190805125647-nasazení webu**.
4. Zpráva o **úspěšném publikování** se zobrazí v okně výstupu sady Visual Studio a otevře se webová stránka se zprávou "robot je připravený!".
5. Otevřete klientskou aplikaci Direct line Speech, klikněte na tlačítko nastavení (ikona pravého ozubeného kolečka) a zadejte `de-de` do pole jazyk. Tím se nastaví mluvený jazyk, který se má rozpoznat, a přepíše výchozí `en-us`.
6. Postupujte podle pokynů v tématu [Vytvoření klienta Direct line Speech](#build-the-direct-line-speech-client) pro opětovné připojení k nově nasazenému robotovi, mluvte v novém jazyce a sledujte, jak se v tomto jazyku v tomto jazyce nachází odpověď robota s novým hlasem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat službu echo-bot nasazenou v tomto kurzu, můžete ji odebrat a všechny její přidružené prostředky Azure odstranit pouhým odstraněním skupiny prostředků Azure **SpeechEchoBotTutorial-Resource**.

1. V [Azure Portal](https://portal.azure.com)klikněte na **skupiny prostředků** v levém navigačním panelu.
2. Vyhledejte skupinu prostředků s názvem: **SpeechEchoBotTutorial-Resource**. Klikněte na tři tečky (...).
3. Vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření vlastní klientské aplikace pomocí sady Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Další informace najdete v tématech

* Nasazení do [oblasti Azure v blízkosti,](https://azure.microsoft.com/global-infrastructure/locations/) abyste viděli vylepšení doby odezvy robota
* Nasazení do [oblasti Azure, která podporuje hlasy vysoké kvality neuronové TTS](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Ceny spojené s kanálem Direct line Speech:
  * [Ceny služby bot Service](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Služba řeči](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Sestavování a nasazování vlastního robota s povoleným hlasem:
  * Sestavte [robota v rozhraní robota](https://dev.botframework.com/). Zaregistrujte si ho pomocí [kanálu Direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) a [Přizpůsobte si robota pro hlas](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0) .
  * Seznamte se s existujícími [řešeními pro roboty](https://microsoft.github.io/botframework-solutions/index): vytvoření [virtuálního asistenta](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) a [jeho roztažení do přímého lineového rozpoznávání](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/) .

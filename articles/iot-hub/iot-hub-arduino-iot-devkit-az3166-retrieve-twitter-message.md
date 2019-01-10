---
title: Načtení zprávy Twitteru s využitím Azure Functions | Dokumentace Microsoftu
description: Využijte snímač pohybu ke zjišťování, přičemž a pomocí služby Azure Functions k vyhledání náhodné tweet s hashtagem, který zadáte
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: f488f8aa991b3d4baae05097af9b6e2f2db481cc
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158920"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Zatřeste, zatřeste tweetu – načtení zprávy Twitteru s využitím Azure Functions

V tomto projektu se dozvíte, jak použít snímač pohybu aktivovat události pomocí služby Azure Functions. Aplikace načte náhodné tweet s #hashtag, které nakonfigurujete v vaše sketch Arduino. Tweet se zobrazí na obrazovce DevKit.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Vaše DevKit připojení k Wi-Fi.
* Příprava vývojového prostředí.

Aktivní předplatné Azure. Pokud ho nemáte, můžete zaregistrovat pomocí jedné z těchto metod:

* Aktivovat [bezplatné 30denní zkušební verzi účtu Microsoft Azure](https://azure.microsoft.com/free/)
* Deklarace identity vaší [kredit Azure ve výši](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Pokud jste předplatitelem MSDN nebo Visual Studio

## <a name="open-the-project-folder"></a>Otevřít složku projektu

Začněte otevřením složky projektu. 

### <a name="start-vs-code"></a>Spusťte VS Code

* Zajistěte, aby že vaše DevKit je připojený k počítači.

* Spusťte VS Code.

* Připojení DevKit k vašemu počítači.

   > [!NOTE]
   > Při spuštění VS Code, můžete obdržet chybovou zprávu, která rozhraním Arduino IDE nebo související Rady balíčku nemůže být nalezen. Pokud k této chybě dochází, zavřete VS Code a znovu spusťte rozhraním Arduino IDE. VS Code teď by měl správně najít cestu rozhraním Arduino IDE.

### <a name="open-the-arduino-examples-folder"></a>Otevřete složku příklady Arduino

Rozbalte na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **ShakeShake**. Otevře se nové okno VS Code, zobrazení složky projektu. Není-li v části MXCHIP AZ3166, ujistěte se, že vaše zařízení správně připojené a restartujte Visual Studio Code.  
![mini solution příklady](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Ukázkový projekt lze také otevřít z palety příkazů. Klikněte na tlačítko `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

## <a name="provision-azure-services"></a>Zřízení služby Azure

V okně řešení spustit úlohu `Ctrl+P` (macOS: `Cmd+P`) tak, že zadáte `task cloud-provision`.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadovaných služeb Azure:

![zřizování cloudu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Pokud na stránce přestane reagovat ve stavu načítání při pokusu o přihlášení k Azure, přečtěte si ["přestane reagovat přihlašovací stránku" krok v části Nejčastější dotazy IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Upravit #hashtag

Otevřít `ShakeShake.ino` a vyhledejte tento řádek kódu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Nahraďte řetězec `iot` uvnitř složených závorek s upřednostňované hashtagem. DevKit později použije náhodné tweet, který obsahuje hashtag, který zadáte v tomto kroku.

## <a name="deploy-azure-functions"></a>Nasazení služby Azure Functions

Použití `Ctrl+P` (macOS: `Cmd+P`) ke spuštění `task cloud-deploy` spustit nasazení kódu funkce Azure:

![nasazení cloudu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Funkce Azure Functions v některých případech nemusí fungovat správně. Chcete-li tento problém vyřešit, když k ní dojde, zkontrolujte ["Chyba kompilace" část Nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

V dalším kroku sestavení a nahrajte kód zařízení.

### <a name="windows"></a>Windows

1. Použití `Ctrl+P` spuštění `task device-upload`.

2. Terminálu zobrazí výzvu k zadání režim konfigurace. Postupujte následovně:

   * Podržte tlačítko A

   * Push a uvolněte tlačítko Obnovit.

3. Na obrazovce se zobrazí DevKit ID a "Configuration".

### <a name="macos"></a>macOS

1. Přepněte DevKit do režimu konfigurace:

   Podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí "Configuration".

2. Použití `Cmd+P` spuštění `task device-upload` nastavit připojovací řetězec, který je načten z `task cloud-provision` kroku.

### <a name="verify-upload-and-run"></a>Ověřit, uložit a spustit

Nyní je nastaven připojovací řetězec, ověří a nahraje aplikace pak ji spustí. 

1. VS Code začne ověřování a odesílání Arduino sketch vaše DevKit:

   ![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit restartuje a spustí kód.

Může se zobrazit "Chyba: AZ3166 SE: Neznámý balíček"chybová zpráva. Tato chyba nastane, pokud index panelu balíček není správně aktualizovat. Chcete-li tento problém vyřešit, zkontrolujte ["Neznámý balíček" chyby v nejčastějších Dotazech IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace klikněte na tlačítko a uvolněte tlačítko A pak jemně zatřesením panelu DevKit. Tato akce načte náhodné tweet, který obsahuje hashtag, který jste zadali dříve. Během několika sekund tweet zobrazí na obrazovce DevKit:

### <a name="arduino-application-initializing"></a>Arduino inicializace aplikace...

![Inicializace aplikace Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Stisknutím A zatřesením...

![Stisknutím klávesy A k zatřesením](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Připraveno k zatřesením...

![Připravené zatřesením](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Zpracovává se...

![Zpracování](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Stiskněte klávesu B ke čtení...

![Stiskněte klávesu B pro čtení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Zobrazení náhodných tweet...

![Zobrazení náhodných tweetu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Stiskněte tlačítko A znovu a poté zatřesením pro nový tweet.
- Kliknutím na tlačítko B procházet rest tweetu.

## <a name="how-it-works"></a>Jak to funguje

![Diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Náčrt Arduino odešle událost pro službu Azure IoT Hub. Tato událost se aktivuje aplikace Azure Functions. Aplikaci Azure Functions, která obsahuje logiku pro připojení k rozhraní API na Twitteru a načíst tweet. Tento algoritmus pak zabalí text tweetu do C2D zprávu (Cloud zařízení) a odešle ho zpátky do zařízení.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Volitelné: Použít vlastní nosný token služby Twitter.

Pro účely testování, tento ukázkový projekt používá předem nakonfigurované nosné tokeny služby Twitter. Existuje ale [četnosti](https://dev.twitter.com/rest/reference/get/search/tweets) pro každý účet na Twitteru. Pokud budete chtít zvážit použití vlastní token, postupujte podle těchto kroků:

1. Přejděte na [portál pro vývojáře na Twitteru](https://dev.twitter.com/) a zaregistrujte novou aplikaci Twitter.

2. [Získat uživatelský klíč a tajných kódů uživatelů](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) vaší aplikace.

3. Použití [některé nástroje](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) k vygenerování tokenu nosiče Twitteru z těchto dvou klíčů.

4. V [webu Azure portal](https://portal.azure.com/){: target = "_blank"}, dostat do **skupiny prostředků** a najít funkce Azure Functions (typ: App Service) pro váš projekt "Zatřesením, zatřesením". Název obsahuje vždy "zatřesením..." řetězec.

   ![Funkce Azure functions](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Aktualizace kódu pro `run.csx` v rámci **funkce > shakeshake cs** s vlastní token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
  ```
  
  ![token služby Twitter.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Uložte soubor a klikněte na tlačítko **spustit**.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Postup řešení problémů nebo poskytnout zpětnou vazbu. 

### <a name="problems"></a>Problémy

Jeden problém je možné, uvidíte v případě, že na obrazovce se zobrazí "Tweety č' při každém kroku proběhla úspěšně. K tomuto stavu obvykle dochází při prvním nasazení a spuštění ukázky, protože aplikace function app vyžaduje kdekoli z několik sekund, na co jednu minutu na úplné spuštění aplikace. 

Nebo při spuštění kódu, existují některé blips, které způsobí restartování aplikace. Pokud k tomuto stavu dochází, můžete získat aplikace pro zařízení s vypršení časového limitu pro načítání tweetu. V takovém případě můžete zkusit jedno nebo obě tyto metody k vyřešení problému:

1. Klikněte na tlačítko Obnovit v DevKit ke spuštění aplikace pro zařízení znovu.

2. V [webu Azure portal](https://portal.azure.com/), najít aplikaci Azure Functions vytvoříte a restartujte ji:

   ![restartování počítače Azure – funkce](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Váš názor

Pokud máte jiné potíže, přečtěte si [nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujete nás prostřednictvím následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit k akcelerátor řešení vzdáleného monitorování Azure IoT DevKit zařízení a načíst tweet, tady jsou další navrhované kroky:

* [Azure IoT vzdálené monitorování přehled akcelerátorů řešení](https://docs.microsoft.com/azure/iot-suite/)
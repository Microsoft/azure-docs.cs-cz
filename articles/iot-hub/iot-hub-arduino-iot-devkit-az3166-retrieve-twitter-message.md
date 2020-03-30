---
title: Načtení zprávy na Twitteru pomocí funkcí Azure | Dokumenty společnosti Microsoft
description: Pomocí snímače pohybu zjišťujte třepání a pomocí funkce Azure najděte náhodný tweet s hashtagem, který zadáte.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60779740"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake pro tweet - Načíst zprávu Twitter s Funkcemi Azure

V tomto projektu se dozvíte, jak pomocí snímače pohybu spustit událost pomocí funkce Azure. Aplikace načte náhodný tweet s #hashtag, kterou nakonfigurujete ve skice Arduino. Tweet se zobrazí na obrazovce DevKit.

## <a name="what-you-need"></a>Co potřebujete

Dokončete [příručku Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) s tímto účelem:

* Připojení devKitu k Wi-Fi.
* Připravte vývojové prostředí.

Aktivní předplatné Azure. Pokud ji nemáte, můžete se zaregistrovat jednou z těchto metod:

* Aktivace [bezplatného 30denního zkušebního účtu Microsoft Azure](https://azure.microsoft.com/free/)
* Nárokujte si [kredit Azure,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) pokud jste předplatitelem MSDN nebo Visual Studia

## <a name="open-the-project-folder"></a>Otevření složky projektu

Začněte otevřením složky projektu. 

### <a name="start-vs-code"></a>Spustit vs kód

* Zkontrolujte, zda je devKit připojený k počítači.

* Spusťte VS Code.

* Připojte devKit k počítači.

   > [!NOTE]
   > Při spouštění VS Code se může zobrazit chybová zpráva, že arduino IDE nebo související balíček desky nelze nalézt. Pokud k této chybě dojde, zavřete Kód VS a znovu spusťte Arduino IDE. VS kód by nyní najít Arduino IDE cestu správně.

### <a name="open-the-arduino-examples-folder"></a>Otevření složky Příklady Arduina

Rozbalte levou stranu **ARDUINO EXAMPLES** sekce, přejděte na **příklady pro MXCHIP AZ3166 > AzureIoT**, a vyberte **ShakeShake**. Otevře se nové okno VS Code, které zobrazí složku projektu. Pokud část MXCHIP AZ3166 nevidíte, zkontrolujte, jestli je zařízení správně připojené, a restartujte kód Sady Visual Studio.  
příklady ![mini-řešení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Ukázkový projekt můžete také otevřít z palety příkazů. Klepnutím `Ctrl+Shift+P` na tlačítko `Cmd+Shift+P`(macOS: ) otevřete paletu příkazů, zadejte **Arduino**a pak najděte a vyberte **Arduino: Příklady**.

## <a name="provision-azure-services"></a>Poskytování služeb Azure

V okně řešení spusťte `Ctrl+P` úlohu přes `Cmd+P`(macOS: ) zadáním `task cloud-provision`.

V terminálu VS Code vás interaktivní příkazový řádek provede zřízením požadovaných služeb Azure:

![poskytování cloudu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Pokud se stránka při pokusu o přihlášení k Azure zasekne ve stavu načítání, přečtěte si [krok "přihlašovací stránka se zasekne" v nejčastějších dotazech k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Úprava #hashtag

Otevřete `ShakeShake.ino` a vyhledejte tento řádek kódu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Nahraďte `iot` řetězec v rámci složených závorek preferovaným hashtagem. DevKit později načte náhodný tweet, který obsahuje hashtag, který zadáte v tomto kroku.

## <a name="deploy-azure-functions"></a>Nasazení služby Azure Functions

Ke `Ctrl+P` spuštění `task cloud-deploy` a `Cmd+P`nasazení kódu Azure Functions použijte (macOS: ) :

![nasazení v cloudu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> V některých případě azure funkce nemusí fungovat správně. Chcete-li tento problém vyřešit, když k němu dojde, zkontrolujte [část "chyba kompilace" v nejčastějších dotazech k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

Dále vytvořte a nahrajte kód zařízení.

### <a name="windows"></a>Windows

1. Slouží `Ctrl+P` ke `task device-upload`spuštění .

2. Terminál vás vyzve k přepání konfiguračního režimu. Postupujte následovně:

   * Podržte tlačítko A

   * Stiskněte a uvolněte resetovací tlačítko.

3. Na obrazovce se zobrazí ID DevKit u a "Konfigurace".

### <a name="macos"></a>macOS

1. Přeptolit DevKit do konfiguračního režimu:

   Podržte tlačítko A a poté stiskněte a uvolněte tlačítko reset. Na obrazovce se zobrazí "Konfigurace".

2. Slouží `Cmd+P` ke `task device-upload` spuštění k nastavení připojovacího řetězce, který je načten z `task cloud-provision` kroku.

### <a name="verify-upload-and-run"></a>Ověření, nahrání a spuštění

Nyní je připojovací řetězec nastaven, ověří a nahraje aplikaci a spustí ji. 

1. VS Code začne ověřovat a nahrávat skicu Arduino do vašeho DevKitu:

   ![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit restartuje a spustí kód.

Může se zobrazí chybová zpráva "Chyba: AZ3166: Neznámý balíček". K této chybě dochází, když index balíčku desky není aktualizován správně. Chcete-li tento problém vyřešit, zkontrolujte [chybu "neznámý balíček" v nejčastějších dotazech k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace klikněte na tlačítko A a uvolněte tlačítko A a pak jemně protřepejte desku DevKit. Tato akce načte náhodný tweet, který obsahuje hashtag, který jste zadali dříve. Během několika sekund se na obrazovce DevKitzobrazí tweet:

### <a name="arduino-application-initializing"></a>Arduino aplikace inicializace...

![Arduino-aplikace-inicializace](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Stisknutím tlačítka A se zatřesete...

![Tisková-A-pro třepat](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Připraven k otřesu...

![Připraveno k otřesu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Zpracování...

![Zpracování](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Stisknutím tlačítka B si přečtete...

![Tisk-B-to-číst](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Zobrazit náhodný tweet...

![Zobrazit náhodný tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Stiskněte tlačítko A znovu, pak se třást pro nový pípání.
- Stisknutím tlačítka B projděte zbytek tweetu.

## <a name="how-it-works"></a>Jak to funguje

![Diagramu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Skica Arduino odešle událost do služby Azure IoT Hub. Tato událost aktivuje aplikaci Azure Functions. Aplikace Azure Functions obsahuje logiku pro připojení k rozhraní API Twitteru a načtení tweetu. Potom zalomí text tweet do zprávy C2D (Cloud-to-device) a odešle jej zpět do zařízení.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Volitelné: Použijte svůj vlastní Twitter nosič token

Pro účely testování tento ukázkový projekt používá předem nakonfigurovaný twitterový nosný token. Nicméně, tam je [limit sazby](https://dev.twitter.com/rest/reference/get/search/tweets) pro každý účet Twitter. Pokud chcete zvážit použití vlastního tokenu, postupujte takto:

1. Přejděte na [portál pro vývojáře Twitteru](https://dev.twitter.com/) a zaregistrujte novou aplikaci Twitter.

2. [Získejte spotřebitelský klíč a tajemství spotřebitelů](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) vaší aplikace.

3. Použijte [nějaký nástroj](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) pro generování Twitter nosičtoken z těchto dvou klíčů.

4. Na [webu Azure Portal](https://portal.azure.com/){:target="_blank"} se dostanete do **skupiny prostředků** a najděte funkci Azure (typ: služba App Service) pro váš projekt "Shake, Shake". Název vždy obsahuje 'shake...' Řetězec.

   ![azurová funkce](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Aktualizujte kód `run.csx` pro funkce **> shakeshake-cs** s vlastním tokenem:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Uložte soubor a klepněte na tlačítko **Spustit**.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Jak řešit problémy nebo poskytnout zpětnou vazbu. 

### <a name="problems"></a>Problémy

Jeden problém byste mohli vidět, pokud se na obrazovce zobrazí 'No Tweets', zatímco každý krok úspěšně běží. Tato podmínka se obvykle stane při prvním nasazení a spuštění ukázky, protože aplikace funkce vyžaduje kdekoli od několika sekund až po jednu minutu studené spuštění aplikace. 

Nebo při spuštění kódu, existují některé chyby, které způsobují restartování aplikace. Když se stane tato podmínka, aplikace zařízení může získat časový čas pro načítání pípání. V takovém případě můžete zkusit jednu nebo obě tyto metody k vyřešení problému:

1. Dalším spuštěním aplikace zařízení klikněte na tlačítko Obnovit na aplikaci DevKit.

2. Na [webu Azure Portal](https://portal.azure.com/)najděte aplikaci Azure Functions, kterou jste vytvořili, a restartujte ji:

   ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Váš názor

Pokud narazíte na jiné problémy, podívejte se na [Nejčastější dotazy k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo nás kontaktujte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit zařízení DevKit k akcelerátoru řešení vzdáleného monitorování Azure IoT a načíst tweet, tady jsou navrhované další kroky:

* [Přehled akcelerátoru řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
---
title: Načtení služby Twitter zprávu s Azure Functions | Microsoft Docs
description: Použijte snímač pohybu a zjistit, že zatřesete používat Azure Functions najít náhodných tweet s hashtagu, který určíte.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: 50ce34cb3289e346e7b637d917730dac8e8a097b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Zatřesením, zatřesením pro Tweet – načíst zprávu Twitter s funkcemi Azure!

V tomto projektu zjistěte, jak použít snímač pohybu aktivovat událost pomocí Azure Functions. Aplikace načte náhodných tweet s které konfigurujete ve vašem nákresu Arduino #hashtag. Tweet zobrazí na obrazovce DevKit.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Vaše DevKit připojení k Wi-Fi.
* Příprava vývojového prostředí.

Aktivní předplatné Azure. Pokud nemáte, můžete zaregistrovat pomocí jedné z těchto metod:

* Aktivovat [Bezplatný zkušební účet Microsoft Azure 30 dnů](https://azure.microsoft.com/free/)
* Deklarace identity vaší [kreditu Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jste předplatitelem MSDN nebo v sadě Visual Studio

## <a name="open-the-project-folder"></a>Otevřete složku projektu

### <a name="start-vs-code"></a>Kód pro spuštění VS

- Zkontrolujte, zda je vaše DevKit **není** připojené k vašemu počítači.
- Spustí kód VS.
- Připojení DevKit k vašemu počítači.

> [!NOTE]
> Při spuštění VS Code, obdržíte chybovou zprávu, která Arduino IDE nebo balíček Příbuzná Tabule nelze najít. Pokud k této chybě dojde, zavřete VS Code a znovu spusťte Arduino IDE. VS Code by měl nyní vyhledejte cestu Arduino IDE správně.

### <a name="open-arduino-examples-folder"></a>Otevřít složku Arduino příklady

Rozbalte levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **ShakeShake**. Otevře se nové okno VS Code s složce projektu v ní.

![Mini solution příklady](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

> [!NOTE]
> Příklad můžete také otevřít z palety příkaz. Použití `Ctrl+Shift+P` (systému macOS: `Cmd+Shift+P`) Chcete-li spustit příkaz palety, zadejte **Arduino**a potom najděte a vyberte **Arduino: Příklady**.

## <a name="provision-azure-services"></a>Zřídit služby Azure

V okně řešení spuštění úkolu prostřednictvím `Ctrl+P` (systému macOS: `Cmd+P`) tak, že zadáte `task cloud-provision`.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadované služby Azure:

![zřizování cloudové](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Pokud při pokusu o přihlášení k Azure, přestane reagovat stav načítání stránky, podívejte se na to [– nejčastější dotazy krok](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Změnit #hashtag

Otevřete `ShakeShake.ino` a vyhledejte tento řádek kódu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Nahraďte řetězec `iot` do složených závorek s vaší upřednostňované hashtag. DevKit později načte náhodných tweet, která zahrnuje hashtagu, které určíte v tomto kroku.

## <a name="deploy-azure-functions"></a>Nasazení služby Azure Functions

Použití `Ctrl+P` (systému macOS: `Cmd+P`) ke spuštění `task cloud-deploy` zahájíte nasazení kód Azure Functions:

![nasazení cloudu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Funkce Azure v některých případech nemusí fungovat správně. Chcete-li vyřešit tento problém, pokud k ní dojde, zaškrtněte toto políčko [– nejčastější dotazy krok](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Vytvořit a odeslat kód zařízení

### <a name="windows"></a>Windows

1. Použití `Ctrl+P` ke spuštění `task device-upload`.
2. Terminálu zobrazí výzvu k zadání režim konfigurace. Postupujte následovně:

   * Podržte tlačítko A
   * Push a verzí na tlačítko Obnovit.

3. Na obrazovce zobrazí DevKit ID a "Konfigurace".
4. Toto nastaví připojovací řetězec, který je načten z `task cloud-provision` krok.
5. VS Code pak spustí ověření a odeslání Arduino načrtnout k vaší DevKit: ![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. DevKit restartuje a spuštění kódu.

> [!NOTE]
> Může dojít "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, když není správně aktualizovat index balíčků panelu. Chcete-li vyřešit tento problém, zaškrtněte toto políčko [– nejčastější dotazy krok](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. Přepnout do režimu konfigurace, DevKit: podržte tlačítko A pak nabízené a verze na tlačítko Obnovit. Na obrazovce zobrazí "Konfigurace".
2. Použití `Cmd+P` ke spuštění `task device-upload` nastavit připojovací řetězec, který je načten z `task cloud-provision` krok.
3. VS Code pak spustí ověření a odeslání Arduino načrtnout k vaší DevKit: ![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. DevKit restartuje a spuštění kódu.

> [!NOTE]
> Může dojít "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, když není správně aktualizovat index balíčků panelu. Chcete-li vyřešit tento problém, zaškrtněte toto políčko [– nejčastější dotazy krok](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace klikněte na tlačítko a verze A tlačítko a pak jemně zatřesením DevKit panelu. Tato akce načte náhodných tweet, který obsahuje hashtagu, které jste zadali dříve. Během pár sekund tweet zobrazí na obrazovce DevKit:

### <a name="arduino-application-initializing"></a>Arduino inicializace aplikace...
![Inicializace aplikace Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Stisknutím A zatřesením...
![Stiskněte klávesu A k zatřesením](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Připraveno k zatřesením...
![Připraveno k zatřesením](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Zpracovává se...
![Zpracování](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Stiskněte klávesu B číst...
![Stiskněte klávesu B pro čtení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Zobrazte náhodných tweet...
![Zobrazení náhodné tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Stisknutím tlačítka A znovu a potom zatřesením pro nové tweet.
- Stisknutím tlačítka B posuňte zbytek tweet.

## <a name="how-it-works"></a>Jak to funguje

![Diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Nákresu Arduino odešle událost do služby Azure IoT Hub. Tato událost se aktivuje aplikaci Azure Functions. Aplikace Azure funkce obsahuje logiku pro připojení k rozhraní API na Twitteru a načíst tweet. Potom zabalí tweet text do C2D zprávu (Cloud zařízení) a odešle ho zpátky do zařízení.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Volitelné: Použít vlastní token nosiče služby Twitter.

Pro účely testování používá tento ukázkový projekt předem nakonfigurovaná nosný token služby Twitter. Je však [limit rychlosti](https://dev.twitter.com/rest/reference/get/search/tweets) pro každý účet služby Twitter. Pokud chcete zvažte použití vlastního tokenu, postupujte takto:

1. Přejděte na [portál pro vývojáře Twitter](https://dev.twitter.com/) zaregistrujte novou aplikaci služby Twitter.

2. [Získat uživatelský klíč a tajné klíče příjemce](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) vaší aplikace.

3. Použití [některé nástroj](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) pro vygenerování tokenu nosiče Twitter z těchto dvou klíčů.

4. V [portál Azure](https://portal.azure.com/){: cílový = "_blank"}, získat **skupiny prostředků** a najít funkce Azure (typ: služby App Service) pro svůj projekt "Zatřesením, zatřesením". Název obsahuje vždy 'zatřesením...' řetězec.
  ![Funkce Azure](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Aktualizujte kód pro `run.csx` v rámci **funkce > shakeshake cs** s vlastními token:
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![token služby Twitter.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Uložte tento soubor a klikněte na tlačítko **spustit**.


## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>Na obrazovce zobrazí 'Tweetů č' při každé fázi byla úspěšně spuštěna.

Tento stav se stane obvykle při prvním nasazení a spuštění ukázky, protože funkce aplikace vyžaduje kdekoli z několika sekund až o jednu minutu studený start aplikace. Nebo při spuštění kódu, jsou některé blips, které způsobí restartování aplikace. Pokud k tomuto stavu dochází, aplikace zařízení můžete získat vypršení časového limitu pro načítání tweet. V takovém případě můžete zkusit akci jednoho nebo obou těchto metod, jak tento problém vyřešit:

1. Klepněte na tlačítko Obnovit v DevKit a znovu spusťte aplikaci zařízení.

2. V [portál Azure](https://portal.azure.com/), najít aplikaci Azure Functions jste vytvořili a restartujte ji: ![azure. funkce restartování](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Váš názor

Pokud dochází k problémům, podívejte se na [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás z následující kanály:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili DevKit zařízení připojit k vaší akcelerátoru řešení Azure IoT vzdálené monitorování a načtení tweet, zde jsou navrhované další kroky:

* [Azure IoT vzdálené monitorování přehled řešení akcelerátoru](https://docs.microsoft.com/azure/iot-suite/)

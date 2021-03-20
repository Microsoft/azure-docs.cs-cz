---
title: Načtěte zprávu Twitteru s Azure Functions | Microsoft Docs
description: Pomocí snímače pohybu zjistíte protřepání a použijte Azure Functions k vyhledání náhodného seznamu s hashtagem, který zadáte.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147902"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Protřepení a protřepávání na vyžádání – načtení zprávy Twitteru pomocí Azure Functions

V tomto projektu se naučíte, jak pomocí snímače pohybu aktivovat událost pomocí Azure Functions. Aplikace načte náhodný disk s #hashtag, kterou nakonfigurujete v náčrtu Arduino. Na obrazovce DevKit se zobrazí.

## <a name="what-you-need"></a>Co budete potřebovat

Dokončete [průvodce Začínáme](./iot-hub-arduino-iot-devkit-az3166-get-started.md) :

* Připojte DevKit k Wi-Fi.
* Připravte vývojové prostředí.

Musíte mít aktivní předplatné Azure. Pokud ho nemáte, můžete se zaregistrovat prostřednictvím jedné z těchto metod:

* Aktivujte si [bezplatný 30denní zkušební účet Microsoft Azure](https://azure.microsoft.com/free/) .
* Pokud jste předplatitelem MSDN nebo Visual studia, můžete svůj [kredit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) uplatnit.

## <a name="open-the-project-folder"></a>Otevření složky projektu

Začněte tím, že otevřete složku projektu. 

### <a name="start-vs-code"></a>Spustit VS Code

* Ujistěte se, že je váš DevKit připojený k vašemu počítači.

* Spusťte VS Code.

* Připojte DevKit k počítači.

   > [!NOTE]
   > Při spuštění VS Code se může zobrazit chybová zpráva, že se nepovedlo najít integrované vývojové prostředí (IDE) Arduino nebo balíček související desky. Pokud k této chybě dojde, zavřete VS Code a znovu spusťte prostředí IDE Arduino. VS Code by teď měl správně najít cestu IDE Arduino.

### <a name="open-the-arduino-examples-folder"></a>Otevřete složku příklady Arduino

Rozbalte část s **Příklady Arduino** na levé straně, přejděte k **příkladům pro MXCHIP AZ3166 > AzureIoT** a vyberte **ShakeShake**. Otevře se nové okno VS Code, ve kterém se zobrazí složka projektu. Pokud nevidíte část MXCHIP AZ3166, ujistěte se, že je zařízení správně připojené, a restartujte Visual Studio Code.  
![Příklady řešení Mini-Solution](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Vzorový projekt lze také otevřít z palety příkazů. Kliknutím na `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) otevřete paletu příkazů, zadáte **Arduino** a pak vyhledáte a vyberete **Arduino: příklady**.

## <a name="provision-azure-services"></a>Zřizování služeb Azure

V okně řešení spusťte úlohu prostřednictvím `Ctrl+P` (MacOS: `Cmd+P` ) zadáním `task cloud-provision` .

V terminálu VS Code vás interaktivní příkazový řádek provede zřízením požadovaných služeb Azure:

![Snímek obrazovky se zobrazí jako interaktivní příkazový řádek Visual Studio Code terminálu.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Pokud se stránka při pokusu o přihlášení k Azure přestane načítat, podívejte se do části [Nejčastější dotazy ke službě IoT DevKit v kroku "přihlášení přihlašovací stránky"](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Úprava #hashtag

Otevřete `ShakeShake.ino` a vyhledejte tento řádek kódu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Nahraďte řetězec `iot` v složených závorkách preferovaným hashtagem. DevKit později načte náhodný disk, který obsahuje hashtag, který zadáte v tomto kroku.

## <a name="deploy-azure-functions"></a>Nasazení služby Azure Functions

Pro spuštění `Ctrl+P` `Cmd+P` `task cloud-deploy` nasazení Azure Functions kódu použijte (MacOS:):

![Snímek obrazovky ukazuje Visual Studio Code, kde můžete spustit úlohu Cloud – nasazení a nasadit Azure Functions kód.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> V některých případech nemusí funkce Azure fungovat správně. Pokud chcete tento problém vyřešit, přečtěte si [část "Chyba kompilace" v tématu Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Sestavení a nahrání kódu zařízení

Dále Sestavte a nahrajte kód zařízení.

### <a name="windows"></a>Windows

1. Použijte `Ctrl+P` ke spuštění `task device-upload` .

2. Terminál vás vyzve k zadání režimu konfigurace. Postupujte následovně:

   * Podržte tlačítko A.

   * Vložení a uvolnění tlačítka obnovit

3. Na obrazovce se zobrazí ID DevKit a konfigurace.

### <a name="macos"></a>macOS

1. Vložte DevKit do režimu konfigurace:

   Podržte tlačítko a, potom klikněte na tlačítko Resetovat a uvolněte. Obrazovka zobrazí ' Configuration '.

2. Použijte `Cmd+P` ke spuštění `task device-upload` k nastavení připojovacího řetězce, který se načte z `task cloud-provision` kroku.

### <a name="verify-upload-and-run"></a>Ověření, nahrání a spuštění

Připojovací řetězec je teď nastavený, ověří a nahraje aplikaci a pak ji spustí. 

1. VS Code spustí ověřování a nahrání Arduino náčrtu do vaší DevKit:

   ![Snímek obrazovky ukazuje Visual Studio Code ověřování a nahrávání náčrtu Arduino.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit se restartuje a začne používat kód.

Může se zobrazit chybová zpráva "Chyba: AZ3166: neznámý balíček". K této chybě dojde, pokud se index balíčku panelu není správně aktualizovat. Pokud chcete tento problém vyřešit, podívejte se na [chybu "Neznámý balíček" v tématu Nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace klikněte na tlačítko a uvolněte tlačítko A a pak jemně zatřeste panel DevKit. Tato akce načte náhodný příkaz, který obsahuje hashtag, který jste zadali dříve. Během několika sekund se na obrazovce DevKit zobrazí obrazovka:

### <a name="arduino-application-initializing"></a>Inicializuje se aplikace Arduino...

![Arduino – inicializace aplikace](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Stiskněte A a protřepe se...

![Tisk a zatřesení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Připraveno k protřepání...

![Připraveno k protřepání](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Zpracovává se...

![Zpracování](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Stisknutím klávesy B si přečtěte...

![Stiskněte – B-pro čtení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Zobrazit náhodný stav...

![Zobrazení-a-náhodný-přístavení](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Stiskněte znovu tlačítko A potom protřepejte pro nový.
- Stisknutím tlačítka B můžete procházet zbytek.

## <a name="how-it-works"></a>Jak to funguje

![Diagram znázorňuje mobilní zařízení odesílající událost do služby Azure I o-T rozbočovače, která spustí aplikaci Azure Functions, aby požádala o prostředcích, která se pošle zpátky do aplikace a předalo do centra a do mobilního zařízení.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Náčrt Arduino odešle událost do Azure IoT Hub. Tato událost aktivuje aplikaci Azure Functions. Aplikace Azure Functions obsahuje logiku pro připojení k rozhraní API Twitteru a načtení. Pak tento text zalomí do zprávy C2D (Cloud-zařízení) a pošle je zpátky do zařízení.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Volitelné: použijte vlastní token pro Twitter nosiče.

Pro účely testování používá tento ukázkový projekt předem nakonfigurovaný token nosiče pro Twitter. Pro každý účet na Twitteru ale platí [omezení přenosové rychlosti](https://dev.twitter.com/rest/reference/get/search/tweets) . Pokud chcete zvážit použití vlastního tokenu, postupujte podle následujících kroků:

1. Pokud chcete zaregistrovat novou aplikaci pro Twitter, navštivte [portál pro vývojáře na Twitter](https://dev.twitter.com/) .

2. [Získejte klíč příjemce a příjemce tajných klíčů](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) vaší aplikace.

3. Pomocí [některého nástroje](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) vygenerujte z těchto dvou klíčů token nosiče z Twitteru.

4. V [Azure Portal](https://portal.azure.com/){: target = "_blank"} se přečtěte do **skupiny prostředků** a vyhledejte funkci Azure Functions (typ: App Service) pro projekt "protřepání". Název vždy obsahuje ' protřepání... ' řetezce.

   ![Snímek obrazovky Azure Portal zobrazuje aplikační službu pro projekt.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Aktualizujte kód `run.csx` v rámci **funkcí > shakeshake-cs** s vlastním tokenem:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Snímek obrazovky zobrazuje kód jazyka C# pro funkci, do které můžete zadat svůj token.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Uložte soubor a klikněte na **Spustit**.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Jak řešit problémy nebo poskytovat zpětnou vazbu. 

### <a name="problems"></a>Problémy

Jedním z problémů, které vidíte, pokud se při úspěšném spuštění každého kroku zobrazí obrazovka ' No tweety '. K tomuto stavu obvykle dochází při prvním nasazení a spuštění ukázky, protože aplikace Function App vyžaduje pár sekund až po dobu jedné minuty, aby bylo možné aplikaci studeny spustit. 

Nebo při spuštění kódu existují některé výkyvů, které způsobují restartování aplikace. Pokud k tomuto stavu dojde, může aplikace zařízení získat časový limit pro načtení zařízení. V takovém případě se můžete pokusit vyřešit problém jedním z těchto způsobů:

1. Kliknutím na tlačítko obnovit v DevKit spusťte aplikaci zařízení znovu.

2. V [Azure Portal](https://portal.azure.com/)Najděte vytvořenou aplikaci Azure functions a restartujte ji:

   ![Snímek obrazovky ukazuje Azure Portal s aplikací Azure Functions a tlačítkem restartovat.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

Pokud se setkáte s dalšími problémy, přečtěte si [Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit zařízení DevKit k akcelerátoru řešení vzdáleného monitorování Azure IoT a načíst provedený disk, je tady doporučený následující postup:

* [Přehled akcelerátoru řešení vzdáleného monitorování Azure IoT](/azure/iot-suite/)
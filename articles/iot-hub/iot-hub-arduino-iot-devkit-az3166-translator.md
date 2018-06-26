---
title: Překladač IoT DevKit pomocí funkce Azure a kognitivní služby | Microsoft Docs
description: Použít mikrofon na IoT DevKit přijímat hlasové zprávy a kognitivní služby Azure pro zpracování do přeložený text v angličtině
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: ba2325272552a13d6e464797b1fb523415393100
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945934"
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Používat IoT DevKit AZ3166 se funkce Azure a kognitivní Services k vytvoření překladač jazyk

V tomto článku, zjistíte, jak zajistit IoT DevKit jako jazyk překladač pomocí [kognitivní služeb Azure](https://azure.microsoft.com/services/cognitive-services/). Se zaznamenává váš hlas a převede jej na angličtinu text uvedené na obrazovce DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní fórum s bohatou periferní zařízení a senzory. Můžete vytvořit pomocí programu [Visual Studio Code rozšíření pro Arduino](https://aka.ms/arduino). A dodává se s rozšiřujících se [projekty katalogu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) na požadované prototypu řešení Internetu věcí (IoT), která využít výhod služby Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mít vaše DevKit připojení Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud jeden nemáte, můžete zaregistrovat pomocí jedné z těchto dvou metod:

* Aktivovat [Bezplatný zkušební účet Microsoft Azure 30 dnů](https://azure.microsoft.com/free/)
* Deklarace identity vaší [kreditu Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jste předplatitelem MSDN nebo v sadě Visual Studio

## <a name="step-1-open-the-project-folder"></a>Krok 1. Otevřete složku projektu

### <a name="a-start-vs-code"></a>A. Kód pro spuštění VS

- Ujistěte se, že vaše DevKit je připojen k vašemu počítači.
- Kód pro spuštění VS
- Připojení DevKit k vašemu počítači.

### <a name="b-open-the-arduino-examples-folder"></a>B. Otevřete složku Arduino příklady

Rozbalte levé straně **ARDUINO příklady > Příklady MXCHIP AZ3166 > AzureIoT**a vyberte **DevKitTranslator**. Otevře nové okno VS Code s složce projektu DEVKITTRANSLATOR v ní.  

> [!NOTE]
> Pokud nevidíte příklady pro oddíl MXCHIP AZ3166, zajistěte, aby vaše zařízení správně připojené a restartujte Visual Studio Code.  

![Ukázky IoT DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

> [!NOTE]
> Příklad můžete také otevřít z palety příkaz. Použití `Ctrl+Shift+P` (systému macOS: `Cmd+Shift+P`) Chcete-li spustit příkaz palety, zadejte **Arduino**a potom najděte a vyberte **Arduino: Příklady**.

## <a name="step-2-provision-azure-services"></a>Krok 2. Zřídit služby Azure

V okně řešení zadejte `Ctrl+P` (systému macOS: `Cmd+P`) a zadejte `task cloud-provision`.

V produktu VS Code terminálu bude průvodce interaktivního příkazového řádku můžete zřídit všechny nezbytné služby Azure:

![Úloha zřídit cloudu](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Krok 3. Nasazení služby Azure Functions

Použití `Ctrl+P` (systému macOS: `Cmd+P`) ke spuštění `task cloud-deploy` nasazení kód Azure Functions. Tento proces trvá obvykle 2 až 5 minut na dokončení:

![Úloha nasazení cloudu](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Po funkce Azure nasadí úspěšně, vyplňte azure_config.h soubor s názvem aplikace funkce. Můžete přejít na [portál Azure](https://portal.azure.com/) ji najít:

![Najít název aplikace Azure – funkce](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Pokud funkci Azure nefunguje správně, zaškrtněte toto políčko [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) část jeho řešení.

## <a name="step-4-build-and-upload-the-device-code"></a>Krok 4. Vytvořit a odeslat kód zařízení

1. Použití `Ctrl+P` (systému macOS: `Cmd+P`) ke spuštění `task config-device-connection`.

2. Terminálu se zeptá, jestli chcete použít připojovací řetězec, který načte z `task cloud-provision` krok. Můžete také zadat vlastní řetězec připojení zařízení tak, že vyberete **"Vytvořit novou..."**

3. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a verzí na tlačítko Obnovit. Na obrazovce zobrazí DevKit ID a "Konfigurace".
  ![Ověření a odeslání nákresu Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Po `task config-device-connection` dokončení, klikněte na tlačítko `F1` se načíst příkazy VS Code a vyberte `Arduino: Upload`, pak spustí VS Code ověření a odesílání Arduino načrtnout: ![ověření a odeslání nákresu Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

DevKit restartuje a spuštění kódu.

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace postupujte podle pokynů na obrazovce DevKit. Výchozí jazyk zdroj je čínština.

Vyberte jiný jazyk pro překlad:

1. Stisknutím tlačítka A režim instalace.
2. Stisknutím tlačítka B přesuňte všechny podporované jazyky zdroje.
3. Stisknutím tlačítka A potvrďte svou volbu jazyka zdroje.
4. Stiskněte a podržte tlačítko B při mluvení a poté uvolněte tlačítko B zahájíte překlad.
5. Přeložený text v anglické zobrazí na obrazovce.

![Posuňte se k výběru jazyka](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Výsledek překlad](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na obrazovce výsledek překlad můžete:

- Stiskněte tlačítko A a B, posuňte a vyberte jazyk zdroje.
- Stisknutím tlačítka B komunikovat, verze odesílat hlasu a získat překlad text

## <a name="how-it-works"></a>Jak to funguje

![Mini-Solution-Voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Zaznamenává nákresu Arduino váš hlas pak odešle požadavek HTTP k aktivační události Azure Functions. Azure Functions volá překladač řeči kognitivní služby API udělat překlad. Po Azure Functions načte text překlad, odešle zprávu C2D do zařízení. Překlad se pak zobrazí na obrazovce.

## <a name="change-device-id"></a>ID zařízení změn

Výchozí ID zařízení zaregistrované v Azure IoT Hub je **AZ3166**. Pokud chcete upravit, postupujte podle [pokynů tady](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

Pokud narazíte na problémy, podívejte se na [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se obraťte na nás z následující kanály:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Teď provedete IoT DevKit jako převaděč pomocí funkce Azure a kognitivní služeb. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Pomocí Visual Studio Code úlohy můžete automatizovat zřizuje cloudu
> * Konfigurace připojovacího řetězce zařízení Azure IoT
> * Nasazení Azure – funkce
> * Test překladu hlasové zprávy

Zálohy na další kurzy, další informace:

> [!div class="nextstepaction"]
> [Připojení k Azure IoT vzdálené monitorování akcelerátoru řešení IoT DevKit AZ3166](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

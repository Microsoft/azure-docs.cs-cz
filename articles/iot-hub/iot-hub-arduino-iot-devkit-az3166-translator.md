---
title: Vytvoření IoT DevKit translator, pomocí Azure Functions a Cognitive Services | Dokumentace Microsoftu
description: Používat mikrofon na IoT DevKit přijímat hlasovou zprávu a pak pomocí služeb Azure Cognitive Services pro zpracování na přeložený text v angličtině.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: cd67e612dd020ba600e33ac8baf77bc094d8afd3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42060117"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Používejte IoT DevKit AZ3166 se službou Azure Functions a Cognitive Services, aby převaděč jazyka

V tomto článku se dozvíte, jak vytvořit IoT DevKit jako převaděč jazyka pomocí [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Zaznamenává vašeho hlasu, jak a převede jej na obrazovce DevKit anglický text.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [rozšíření Visual Studio Code pro Arduino](https://aka.ms/arduino). A stále se rozšiřující obsahuje [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a provede vás prostřednictvím řešení Internetu věcí (IoT) prototypu, které budou využívat služeb Microsoft Azure.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mají vaše DevKit připojení k Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud nemáte, můžete zaregistrovat pomocí jedné z těchto dvou metod:

* Aktivovat [bezplatné 30denní zkušební verzi účtu Microsoft Azure](https://azure.microsoft.com/free/)
* Deklarace identity vaší [kredit Azure ve výši](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Pokud máte předplatné MSDN nebo Visual Studio

## <a name="open-the-project-folder"></a>Otevřít složku projektu

Nejprve otevřete složku projektu. 

### <a name="start-vs-code"></a>Spusťte VS Code

- Ujistěte se, že vaše DevKit se připojení k vašemu počítači.

- Spusťte VS Code.

- Připojení DevKit k vašemu počítači.

### <a name="open-the-arduino-examples-folder"></a>Otevřete složku příklady Arduino

Rozbalte na levé straně **ARDUINO příklady > Příklady MXCHIP AZ3166 > AzureIoT**a vyberte **DevKitTranslator**. Otevře se nové okno VS Code, zobrazení složky projektu. Pokud nevidíte příklady pro oddíl MXCHIP AZ3166, ujistěte se, že vaše zařízení správně připojené a znovu spusťte VS Code.  

![IoT DevKit ukázky](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

V příkladu můžete také otevřít z palety příkazů. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

## <a name="provision-azure-services"></a>Zřízení služby Azure

Zadejte v okně řešení `Ctrl+P` (macOS: `Cmd+P`) a zadejte `task cloud-provision`.

V terminálu VS Code vás provedou interaktivního příkazového řádku můžete zřídit všechny nezbytné služby Azure:

![Úloha zřízení cloudu](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="deploy-the-azure-function"></a>Nasazení funkce Azure functions

Použití `Ctrl+P` (macOS: `Cmd+P`) ke spuštění `task cloud-deploy` nasazení kódu funkce Azure. Tento proces trvá obvykle 2 až 5 minut.

![Úloha nasazení cloudu](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Po úspěšném nasazení funkce Azure Functions vyplňte azure_config.h soubor s názvem aplikace function app. Můžete přejít na [webu Azure portal](https://portal.azure.com/) a vyhledejte jej:

![Najít název aplikace funkcí Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Pokud funkce Azure Functions nefunguje správně, zkontrolujte [stránky "funkce Azure Functions v podrobnostech o chybě komplikace" v části Nejčastější dotazy IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) k jeho vyřešení.

## <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

1. Použití `Ctrl+P` (macOS: `Cmd+P`) ke spuštění `task config-device-connection`.

2. Terminálu zeptá, jestli chcete použít připojovací řetězec, který načte z `task cloud-provision` kroku. Můžete taky zadat vlastní připojovací řetězec zařízení tak, že vyberete **"Vytvořit novou..."**

3. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí DevKit ID a "Configuration".

   ![Ověřování a odesílání Arduino sketch](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Po `task config-device-connection` hotovi, klikněte na tlačítko `F1` načtení příkazy VS Code a vyberte `Arduino: Upload`, VS Code pak spustí, ověření a nahrávání Arduino náčrt.

   ![Ověřování a odesílání Arduino sketch](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

DevKit restartuje a spustí kód.

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace postupujte podle pokynů na obrazovce DevKit. Výchozí jazyk zdroje je čínštině.

Chcete-li vybrat jiný jazyk pro překlad:

1. Stisknutím tlačítka A režim instalace.

2. Kliknutím na tlačítko B posouvat všechny podporované jazyky zdroje.

3. Stisknutím tlačítka A potvrďte svou volbu Zdrojový jazyk.

4. Stiskněte a podržte tlačítko B úst a pak uvolněte tlačítko B zahájíte překlad.

5. Přeložený text v angličtině zobrazí na obrazovce.

![Posunout výběr jazyka](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Výsledek překladu](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na obrazovce s výsledky překladu je možné:

- Stisknutím tlačítka A a B a posuňte vyberte zdrojový jazyk.

- Stiskněte tlačítko B mluvit. Chcete-li odeslat hlasu a získat překlad textu, uvolněte tlačítko B.

## <a name="how-it-works"></a>Jak to funguje

![Mini-Solution-Voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Záznamy sketch Arduino vašeho hlasu, jak potom odešle HTTP žádost o aktivaci funkce Azure. Funkce Azure Functions volá překlady řeči služby cognitive Services API provedete překlad. Po funkce Azure Functions získá překlad textu, odešle zprávu (cloud zařízení) C2D do zařízení. Překlad se pak zobrazí na obrazovce.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na potíže, přečtěte si [nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás prostřednictvím následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Jste se naučili, jak použít IoT DevKit jako převaděče s využitím Azure Functions a Cognitive Services. V tomto návodu jste zjistili, jak:

> [!div class="checklist"]
> * Pomocí Visual Studio Code úlohy automatizace ustanovení cloudu
> * Nakonfigurovat připojovací řetězec zařízení Azure IoT
> * Nasazení funkce Azure functions
> * Testování překlad hlasové zprávy

Přejděte k další kurzy:

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166 se připojit k akcelerátoru řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

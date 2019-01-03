---
title: Vytvoření IoT DevKit translator, pomocí Azure Functions a Cognitive Services | Dokumentace Microsoftu
description: Používat mikrofon na IoT DevKit přijímat hlasovou zprávu a pak pomocí služeb Azure Cognitive Services pro zpracování na přeložený text v angličtině.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 87091cf3d128eecdbbf06a41d516f13e590338b9
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788408"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Používejte IoT DevKit AZ3166 se službou Azure Functions a Cognitive Services, aby převaděč jazyka

V tomto článku se dozvíte, jak vytvořit IoT DevKit jako převaděč jazyka pomocí [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Zaznamenává vašeho hlasu, jak a převede jej na obrazovce DevKit anglický text.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v jednom Arduino kompatibilní deska bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [Workbench zařízení Azure IoT](https://aka.ms/iot-workbench) nebo [nástroje Azure IoT](https://aka.ms/azure-iot-tools) balíček rozšíření pro Visual Studio Code. [Katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) obsahuje ukázkové aplikace můžete řešení IoT prototypu.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto kurzu, proveďte následující úlohy:

* Příprava vašich DevKit podle postupu v [připojení IoT DevKit AZ3166 se do služby Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Vytvoření služby Azure Cognitive Services

1. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek** a vyhledejte **řeči**. Vyplňte formulář pro vytvoření Speech Service.
  ![Speech service](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Přejít na Speech service jste právě vytvořili, klikněte na tlačítko **klíče** části a zkopírujte a poznamenejte si **Key1** pro DevKit přístup k němu.
  ![Zkopírování klíče](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Otevřete ukázkový projekt

1. Ujistěte se, že je vaše IoT DevKit **Nepřipojeno** k vašemu počítači. Nejprve spusťte VS Code a DevKit připojte se k počítači.

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Otevřít příklady...** . Potom vyberte **IoT DevKit** jako panel.

1. Na stránce IoT aplikace Workbench příklady najít **DevKit Translator** a klikněte na tlačítko **otevřít ukázkové**. Potom vybere výchozí cestu pro stažení ukázkového kódu.
  ![Otevřete ukázkový](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Speech Service pomocí Azure Functions

1. V nástroji VS Code, klikněte na tlačítko `F1`zadejte a vyberte **Workbench zařízení Azure IoT: Zřízení služby Azure...** . ![Zřízení služby Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Postupujte podle kroků k dokončení zřizování Azure IoT Hub a Azure Functions.
  ![Postup zřizování](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

  Poznamenejte si název zařízení Azure IoT Hub, kterou jste vytvořili.

1. Otevřít `Functions\DevKitTranslatorFunction.cs` a aktualizujte následující řádky kódu pomocí devce název a klíč Speech Service jste si poznamenali.
  ```csharp
  // Subscription Key of Speech Service
  const string speechSubscriptionKey = "";

  // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
  const string speechServiceRegion = "";

  // Device ID
  const string deviceName = "";
  ```

1. Klikněte na tlačítko `F1`zadejte a vyberte **Workbench zařízení Azure IoT: Nasazení do Azure...** . Pokud VS Code požádá o potvrzení pro opětovné nasazení, klikněte na tlačítko **Ano**.
  ![Nasazení upozornění](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Ujistěte se, že je nasazení úspěšné.
  ![Úspěšné nasazení](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Na webu Azure portal, přejděte na **aplikace Function App** části, najít aplikace funkcí Azure právě vytvořili. Klikněte na tlačítko `devkit_translator`, pak klikněte na tlačítko **<> / získat adresu URL funkce** zkopírujte adresu URL.
  ![Zkopírujte adresu URL funkce](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Vložte adresu URL do `azure_config.h` souboru.
  ![Konfigurace Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

  > [!NOTE]
  > Pokud aplikace Function app nebude správně fungovat, zaškrtněte toto políčko [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) části k jeho vyřešení.

## <a name="build-and-upload-device-code"></a>Vytvoření a nahrání kód zařízení

1. Přepnout DevKit k **režim konfigurace** podle:
  * Podržte tlačítko **A**.
  * Stisknutím a uvolněním **resetování** tlačítko.

  Se zobrazí na obrazovce se zobrazí DevKit ID a **konfigurace**.

  ![Režim konfigurace DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klikněte na tlačítko `F1`zadejte a vyberte **Workbench zařízení Azure IoT: Konfigurace nastavení zařízení... > připojovací řetězec zařízení Config**. Vyberte **vyberte připojovací řetězec zařízení IoT Hub** k jeho konfiguraci DevKit.
  ![Konfigurace připojovacího řetězce](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Po úspěšném dokončení se zobrazí oznámení.
  ![Nakonfigurovat připojovací řetězec úspěch](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klikněte na tlačítko `F1` znovu, zadejte a vyberte **Workbench zařízení Azure IoT: Nahrát kód zařízení**. Spustí se kompilace a nahrajte kód do DevKit.
  ![Nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

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

IoT DevKit zaznamenává vašeho hlasu, jak potom odešle požadavek HTTP na aktivační událost Azure Functions. Služba Azure Functions volá překlady řeči služby cognitive Services API provedete překlad. Jakmile Azure Functions získá překlad textu, odešle zprávu C2D do zařízení. Překlad se pak zobrazí na obrazovce.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na potíže, přečtěte si [nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo kontaktujte nás prostřednictvím následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Jste se naučili, jak použít IoT DevKit jako převaděče s využitím Azure Functions a Cognitive Services. V tomto návodu jste zjistili, jak:

> [!div class="checklist"]
> * Pomocí Visual Studio Code úlohy automatizace ustanovení cloudu
> * Nakonfigurovat připojovací řetězec zařízení Azure IoT
> * Nasazení funkce Azure functions
> * Testování překlad hlasové zprávy

Přejděte k další kurzy:

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166 se připojit k akcelerátoru řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

---
title: Vytvořte překladač w/Azure Functions, Cognitive Services, IoT DevKit
description: Použití mikrofonu na IoT DevKit přijímat hlasovou zprávu a pak použít Azure Cognitive Services pro zpracování do přeloženého textu v angličtině
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953354"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Použití IoT DevKit AZ3166 s Funkcemi Azure a kognitivními službami k vytvoření překladače jazyků

V tomto článku se dozvíte, jak vytvořit IoT DevKit jako překladač jazyků pomocí [Služby Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Zaznamenává váš hlas a překládá jej do anglického textu zobrazeného na obrazovce DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je all-in-one Arduino kompatibilní deska s bohatými periferiemi a senzory. Můžete vyvíjet pro něj pomocí [Azure IoT Device Workbench](https://aka.ms/iot-workbench) nebo [Azure IoT Tools](https://aka.ms/azure-iot-tools) rozšíření pack v Kódu Visual Studia. [Katalog projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) obsahuje ukázkové aplikace, které vám pomohou prototypovat řešení IoT.

## <a name="before-you-begin"></a>Než začnete

Postup v tomto kurzu můžete provést nejprve následujícím úkolem:

* Připravte si DevKit podle kroků v [části Connect IoT DevKit AZ3166 k Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Vytvoření azure kognitivní služby

1. Na webu Azure Portal klikněte na **Vytvořit prostředek** a vyhledejte **řeč**. Vyplňte formulář pro vytvoření služby řeči.
  ![Služba Speech](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Přejděte na službu Řeči, kterou jste právě vytvořili, kliknutím na **oddíl Klíče** zkopírujte a poznamenejte si **klávesu Key1** pro DevKit, která k ní přistupuje.
  ![Kopírování klíčů](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Otevřít ukázkový projekt

1. Ujistěte se, že váš IoT DevKit **není připojený** k počítači. Nejprve spusťte kód VS a potom připojte devkit k počítači.

1. Kliknutím `F1` otevřete paletu příkazů, zadejte a vyberte **Azure IoT Device Workbench: Open Examples...**. Pak vyberte **IoT DevKit** jako nástěnku.

1. Na stránce Příklady pracovní plochy IoT najděte **Překladač DevKit** a klepněte na **otevřít ukázku**. Potom vybere výchozí cestu ke stažení ukázkového kódu.
  ![Otevřít vzorek](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Použití služby řeči s funkcemi Azure

1. V kódu VS `F1`klikněte na , zadejte a vyberte **Pracovní plocha zařízení Azure IoT Workbench: Provision Azure Services...**. ![Poskytování služeb Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Postupujte podle pokynů k dokončení zřizování Azure IoT Hub a Azure Functions.
   ![Kroky poskytování](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Poznamenejte si název zařízení Azure IoT Hub, který jste vytvořili.

1. Otevřete `Functions\DevKitTranslatorFunction.cs` a aktualizujte následující řádky kódu s názvem zařízení a klíčem služby rozpoznávání řeči, který jste si poznamenali.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Klikněte na `F1`, zadejte a vyberte **Azure IoT Device Workbench: Deploy to Azure...**. Pokud kód VS požádá o potvrzení o opětovném nasazení, klepněte na tlačítko **Ano**.
   ![Upozornění na nasazení](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Ujistěte se, že nasazení je úspěšné.
   ![Úspěšnost nasazení](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Na webu Azure Portal přejděte do části **Functions Apps** a najděte právě vytvořenou aplikaci Azure Function. Klepněte na `devkit_translator`tlačítko a potom klepněte na **adresu URL funkce</> a** zkopírujte ji.
   ![Kopírovat adresu URL funkce](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Vložte adresu `azure_config.h` URL do souboru.
   ![Konfigurace Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Pokud aplikace Funkce nefunguje správně, v této části [s nejčastějšími dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) ji vyřešte.

## <a name="build-and-upload-device-code"></a>Vytvoření a nahrání kódu zařízení

1. Přepněte DevKit do **konfiguračního režimu** takto:
   * Podržte tlačítko **A**.
   * Stiskněte a uvolněte tlačítko **Reset.**

   Zobrazí se na obrazovce se zobrazí DevKit ID a **konfigurace**.

   ![Režim konfigurace DevKitu](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klikněte na `F1`, zadejte a vyberte **pracovní plocha zařízení Azure IoT: Konfigurace nastavení zařízení... > připojovací řetězec konfiguračního zařízení**. Chcete-li nakonfigurovat na DevKit, vyberte **možnost Vybrat připojovací řetězec zařízení služby IoT Hub.**
   ![Konfigurace připojovacího řetězce](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Oznámení se zobrazí, jakmile bude úspěšně provedeno.
   ![Konfigurace úspěchu připojovacího řetězce](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klikněte `F1` znovu, zadejte a vyberte **Azure IoT Device Workbench: Upload Device Code**. Spustí kompilaci a nahrát kód devKit.
   ![Nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace postupujte podle pokynů na obrazovce DevKit. Výchozím zdrojovým jazykem je čínština.

Výběr jiného jazyka pro překlad:

1. Stisknutím tlačítka A přejdete do režimu nastavení.

2. Stisknutím tlačítka B můžete posouvat všechny podporované zdrojové jazyky.

3. Stisknutím tlačítka A potvrďte svůj výběr zdrojového jazyka.

4. Stiskněte a podržte tlačítko B během mluvení a poté uvolněte tlačítko B a spusťte překlad.

5. Přeložený text v angličtině se zobrazuje na obrazovce.

![Posunutím vyberte jazyk.](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Výsledek překladu](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na obrazovce s výsledky překladu můžete:

- Stisknutím tlačítek A a B se posunete a vyberete zdrojový jazyk.

- Stisknutím tlačítka B pro mluvte. Chcete-li odeslat hlas a získat text překladu, uvolněte tlačítko B.

## <a name="how-it-works"></a>Jak to funguje

![mini-řešení-hlas-k-pípání-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit zaznamenává váš hlas a pak publikuje požadavek HTTP pro aktivaci funkcí Azure. Funkce Azure volá rozhraní API překladu překladu kognitivní služby. Poté, co Funkce Azure získá text překladu, odešle zprávu C2D do zařízení. Poté se překlad zobrazí na obrazovce.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [Nejčastější dotazy KIOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo nás oslovte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Naučili jste se používat IoT DevKit jako překladač pomocí Funkcí Azure a kognitivních služeb. V tomto návodu jste se naučili:

> [!div class="checklist"]
> * Použití úlohy Kódu visual studia k automatizaci ustanovení cloudu
> * Konfigurace připojovacího řetězce zařízení Azure IoT
> * Nasazení funkce Azure
> * Testování překladu hlasové zprávy

Převést na další výukové programy se učit:

> [!div class="nextstepaction"]
> [Připojení akcelerátoru řešení vzdáleného monitorování IoT DevKit AZ3166 k Azure IoT Remote Monitoring](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

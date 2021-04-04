---
title: Vytvoření překladatele s Azure Functions Cognitive Services, IoT DevKit
description: Použijte mikrofon na IoT DevKit k přijetí hlasové zprávy a pak použijte Azure Cognitive Services ke zpracování přeloženého textu v angličtině.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96024159"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Použití IoT DevKit AZ3166 s Azure Functions a Cognitive Services k vytvoření překladatele jazyka

V tomto článku se dozvíte, jak pomocí [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)vytvořit službu IoT DevKit jako překladatel jazyka. Záznam hlasu a převede ho na anglický text zobrazený na obrazovce DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je vše v Arduino kompatibilní vývěsce s bohatými periferními zařízeními a snímači. Můžete ji vyvíjet pro IT pomocí [Azure IoT Device Workbench](https://aka.ms/iot-workbench) nebo rozšíření sady [nástrojů Azure iot Tools](https://aka.ms/azure-iot-tools) v Visual Studio Code. [Katalog projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) obsahuje ukázkové aplikace, které vám pomůžou prototypovat řešení IoT.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto kurzu nejdříve proveďte následující úlohy:

* Připravte své DevKit podle kroků v části [připojení IoT DEVKIT AZ3166 do Azure IoT Hub v cloudu](./iot-hub-arduino-iot-devkit-az3166-get-started.md).

## <a name="create-azure-cognitive-service"></a>Vytvořit službu pro rozpoznávání Azure

1. V Azure Portal klikněte na **vytvořit prostředek** a vyhledejte **řeč**. Vyplňte formulář pro vytvoření služby Speech.
  ![Služba řeči](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Přejděte ke službě rozpoznávání řeči, kterou jste právě vytvořili, klikněte na tlačítko **klíče** a zkopírujte a poznamenejte si **klíč1** pro DevKit přístupu k němu.
  ![Kopírovat klíče](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Otevřít ukázkový projekt

1. Ujistěte se, že vaše aplikace IoT DevKit není **připojená** k vašemu počítači. Nejprve začněte VS Code a pak připojte DevKit k počítači.

1. Kliknutím `F1` otevřete paletu příkazů, zadáte a vyberete **Azure IoT Device Workbench: otevřít příklady...**. Pak jako panel vyberte **IoT DevKit** .

1. Na stránce příklady IoT Workbench Najděte **DevKit Translator** a klikněte na **otevřít ukázku**. Pak vybere výchozí cestu pro stažení ukázkového kódu.
  ![Otevřít ukázku](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Používání služby Speech s Azure Functions

1. V VS Code klikněte na `F1` , zadejte a vyberte **Azure IoT Device Workbench: zřízení služeb Azure...**. ![ Zřizování služeb Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Podle pokynů dokončete zřizování Azure IoT Hub a Azure Functions.
   ![Zřídit kroky](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Poznamenejte si název zařízení Azure IoT Hub, které jste vytvořili.

1. Otevřete `Functions\DevKitTranslatorFunction.cs` a aktualizujte následující řádky kódu s názvem zařízení a klíčem služby Speech, který jste si poznamenali.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Klikněte na `F1` , zadejte a vyberte **Azure IoT Device Workbench: nasadit do Azure...**. Pokud VS Code požádá o potvrzení pro opětovné nasazení, klikněte na **Ano**.
   ![Upozornění nasazení](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Ujistěte se, že nasazení proběhlo úspěšně.
   ![Úspěch nasazení](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. V části Azure Portal, přejít na **aplikace** Functions, najděte právě vytvořenou aplikaci Azure Functions. Klikněte na `devkit_translator` a potom klikněte na **</> získat adresu URL funkce** a zkopírujte adresu URL.
   ![Kopírovat adresu URL funkce](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Vložte adresu URL do `azure_config.h` souboru.
   ![Konfigurace Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Pokud aplikace Function App nefunguje správně, přečtěte si část [Nejčastější dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) a vyřešte ji.

## <a name="build-and-upload-device-code"></a>Sestavení a nahrání kódu zařízení

1. Přepněte DevKit do **režimu konfigurace** podle:
   * Podržte tlačítko **A**.
   * Tlačítko pro **resetování** kláves a uvolnění

   Zobrazí se obrazovka s ID a **konfigurací** DevKit.

   ![Režim konfigurace DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klikněte na `F1` , zadejte a vyberte **Azure IoT Device Workbench: Konfigurace nastavení zařízení... > připojovací řetězec zařízení**. Vyberte **vybrat IoT Hub připojovací řetězec zařízení** , abyste ho mohli nakonfigurovat na DevKit.
   ![Konfigurace připojovacího řetězce](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Po úspěšném dokončení se zobrazí oznámení.
   ![Konfigurace úspěšného připojovacího řetězce](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klikněte `F1` znovu na znovu, zadejte a vyberte **Azure IoT Device Workbench: nahrání kódu zařízení**. Spustí zkompilování a nahrání kódu do DevKit.
   ![Nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testování projektu

Po inicializaci aplikace postupujte podle pokynů na obrazovce DevKit. Výchozí zdrojový jazyk je čínština.

Pokud chcete vybrat pro překlad jiný jazyk:

1. Stiskněte tlačítko A a vstupte do režimu nastavení.

2. Stiskněte tlačítko B a projděte všechny podporované zdrojové jazyky.

3. Stisknutím tlačítka A potvrďte zvolený zdrojový jazyk.

4. Stiskněte a podržte tlačítko B a mluvte. Potom tlačítko B uvolněte a spusťte překlad.

5. Přeložený text v angličtině se zobrazí na obrazovce.

![Posunout na vybraný jazyk](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Výsledek překladu](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na obrazovce s výsledkem překladu:

- Stisknutím tlačítek A a B se můžete posouvat a vybrat zdrojový jazyk.

- Můžete stisknout tlačítko B a mluvit. Pokud chcete odeslat řeč a získat přeložený text, uvolněte tlačítko B.

## <a name="how-it-works"></a>Jak to funguje

![Mini-Solution – Voice-to-propouštění – diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

DevKit IoT záznam hlasu a potom odešle požadavek HTTP na Trigger Azure Functions. Azure Functions volá rozhraní API překladatele pro rozpoznávání řeči ke službě rozpoznávání. Jakmile Azure Functions získá text překladu, pošle zařízení zprávu C2D. Pak se překlad zobrazí na obrazovce.

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, přečtěte si [Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se můžete obrátit na nás pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak používat službu IoT DevKit jako překladatel pomocí Azure Functions a Cognitive Services. V tomto postupu jste se dozvěděli, jak:

> [!div class="checklist"]
> * Použití úlohy Visual Studio Code k automatizaci cloudových předpisů
> * Konfigurace připojovacího řetězce zařízení Azure IoT
> * Nasazení funkce Azure Functions
> * Testování překladu hlasových zpráv

Přejděte k ostatním kurzům a Naučte se:

> [!div class="nextstepaction"]
> [Připojení IoT DevKit AZ3166 k akcelerátoru řešení vzdáleného monitorování Azure IoT](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)
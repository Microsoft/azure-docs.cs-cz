---
title: Konfigurace aplikace hlasového asistenta pomocí Azure IoT Hub
description: Konfigurace aplikace hlasového asistenta pomocí Azure IoT Hub
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: b22ef4ee0a8b5978bb2ec1c02fadf368815f3014
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095778"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Konfigurace aplikace hlasového asistenta pomocí Azure IoT Hub

Tento článek popisuje, jak nakonfigurovat aplikaci hlasového asistenta pomocí IoT Hub. Podrobný kurz, který vás provede procesem vytvoření hlasového asistenta pomocí ukázkové šablony, najdete v tématu [Vytvoření hlasového asistenta bez kódu s využitím Azure Percept studia a zvuku Azure Percept](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>Aktualizace konfigurace hlasového asistenta

1. Otevřete [Azure Portal](https://portal.azure.com) a do panelu hledání zadejte **IoT Hub** . Kliknutím na ikonu otevřete stránku IoT Hub.

1. Na stránce IoT Hub vyberte IoT Hub, na které se zařízení zřídilo.

1. Pokud chcete zobrazit všechna zařízení připojená k IoT Hub, vyberte v části **Automatická správa zařízení** v levé navigační nabídce **IoT Edge** .

1. Vyberte zařízení, na které se nasadila vaše aplikace hlasového asistenta.

1. Klikněte na **nastavit moduly**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Obrazovka stránky zařízení se zvýrazněnou možností nastavit moduly":::

1. Ověřte, že je v části **Container Registry přihlašovací údaje** k dispozici následující položka. V případě potřeby přidejte přihlašovací údaje.

    |Název|Adresa|Uživatelské jméno|Heslo|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. V části **IoT Edge moduly** vyberte **azureearspeechclientmodule**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Snímek obrazovky zobrazující seznam všech modulů IoT Edge v zařízení.":::

1. Klikněte na kartu **nastavení modulu** . Ověřte následující konfiguraci:

    Identifikátor URI image|Restartovat zásadu|Požadovaný stav
    ---------|--------------|--------------
    mcr.microsoft.com/azureedgedevices/azureearspeechclientmodule:preload-devkit|stál|instalovanou

    Pokud se nastavení neshodují, upravte je a klikněte na **aktualizovat**.

1. Klikněte na kartu **proměnné prostředí** . Ověřte, zda nejsou definovány žádné proměnné prostředí.

1. Klikněte na kartu **nastavení s dvojitou** platností modulu. Aktualizujte část **speechConfigs** následujícím způsobem:

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedsamples.blob.core.windows.net/speech/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > Výše použité klíčové slovo je výchozím klíčovým slovem, které je veřejně dostupné. Pokud chcete použít vlastní, můžete přidat vlastní klíčové slovo nahráním vytvořeného souboru tabulky do úložiště objektů BLOB. Úložiště objektů BLOB je potřeba nakonfigurovat buď pomocí anonymního přístupu ke kontejneru, nebo anonymního přístupu k objektu BLOB.

## <a name="how-to-find-out-appid-key-and-region"></a>Jak zjistit appId, klíč a region

Pokud chcete najít své **AppID**, **klíč** a **oblast**, přejděte na [řeč Studio](https://speech.microsoft.com/):

1. Přihlaste se a vyberte příslušný prostředek řeči.
1. Na domovské stránce sady **Speech Studio** klikněte na **vlastní příkazy** v části **hlas asistenti**.
1. Vyberte cílový projekt.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Snímek stránky projektu v studiu řeči":::

1. V levém panelu nabídek klikněte na **Nastavení** .
1. **AppID** a **klíč** se nacházejí na kartě **Obecné** nastavení.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Snímek obrazovky s obecným nastavením pro projekt řeči":::

1. Pokud chcete najít vaši **oblast**, otevřete v nastavení kartu **prostředky Luis** . Výběr **prostředků pro vytváření obsahu** bude obsahovat informace o oblasti.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Snímek obrazovky s LUIS prostředky projektu řeči":::

1. Po zadání informací o **speechConfigs** klikněte na **aktualizovat**.

1. Klikněte na kartu **trasy** v horní části stránky **nastavit moduly** . Ujistěte se, že máte trasu s následující hodnotou:

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Přidejte trasu, pokud neexistuje.

1. Klikněte na **Zkontrolovat a vytvořit**.

1. Klikněte na **Vytvořit**.


## <a name="next-steps"></a>Další kroky

Po aktualizaci konfigurace hlasového asistenta se vraťte do ukázky v Azure Percept studiu, abyste mohli pracovat s aplikací.


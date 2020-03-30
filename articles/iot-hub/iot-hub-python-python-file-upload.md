---
title: Nahrávání souborů ze zařízení do Azure IoT Hub s Pythonem | Dokumenty společnosti Microsoft
description: Jak nahrát soubory ze zařízení do cloudu pomocí Azure IoT zařízení SDK pro Python. Nahrané soubory se ukládají v kontejneru objektů blob úložiště Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110742"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Nahrávání souborů ze zařízení do cloudu pomocí služby IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Tento článek ukazuje, jak pomocí [možností nahrávání souborů služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do úložiště objektů [blob Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně zajistěte kontejner úložiště pro nahrávání souboru.

* Pomocí klienta Pythonu nahrajte soubor přes ioT hub.

[Odeslat telemetrii ze zařízení do rychlého startu služby IoT hub](quickstart-send-telemetry-python.md) demonstruje základní funkce zasílání zpráv mezi zařízeními a cloudy služby IoT Hub. V některých případech však nelze snadno mapovat data, která vaše zařízení odesílají do relativně malých zpráv zařízení cloud, které služba IoT Hub přijímá. Když potřebujete upland soubory ze zařízení, můžete stále používat zabezpečení a spolehlivost IoT Hub.

> [!NOTE]
> Sada IoT Hub Python SDK v současné době podporuje pouze nahrávání souborů založených na znaku, jako jsou soubory **TXT.**

Na konci tohoto kurzu spustíte konzolovou aplikaci Pythonu:

* **FileUpload.py**, který nahraje soubor do úložiště pomocí sady Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Tato příručka používá zastaralou sadku V1 Python SDK, protože funkce Nahrávání souborů ještě nebyla implementována v nové sdk v nové sdk v2.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace pro zařízení

V této části vytvoříte aplikaci pro zařízení pro nahrání souboru do centra IoT hub.

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **azure-iothub-device-client:**

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Pomocí textového editoru vytvořte testovací soubor, který nahrajete do úložiště objektů blob.

    > [!NOTE]
    > Sada IoT Hub Python SDK v současné době podporuje pouze nahrávání souborů založených na znaku, jako jsou soubory **TXT.**

3. Pomocí textového editoru vytvořte **FileUpload.py** soubor v pracovní složce.

4. Na začátek `import` **souboru FileUpload.py** přidejte následující příkazy a proměnné. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. V souboru `[Device Connection String]` nahraďte připojovacířetězec zařízení ioT hub. Nahraďte `[Full path to file]` cestou k testovacímu souboru, který jste vytvořili, nebo libovolným souborem v zařízení, který chcete nahrát. Nahraďte `[File name for storage]` název, který chcete dát do souboru po jeho nahrání do úložiště objektů blob. 

6. Vytvořte zpětné volání pro **funkci upload_blob:**

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Přidejte následující kód pro připojení klienta a nahrání souboru. Také zahrnují `main` rutinní:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Uložte a zavřete **soubor UploadFile.py.**

## <a name="run-the-application"></a>Spuštění aplikace

Nyní jste připraveni spustit aplikaci.

1. Na příkazovém řádku v pracovní složce spusťte následující příkaz:

    ```cmd/sh
    python FileUpload.py
    ```

2. Následující snímek obrazovky ukazuje výstup z aplikace **FileUpload:**

    ![Výstup z aplikace simulované zařízení](./media/iot-hub-python-python-file-upload/1.png)

3. Pomocí portálu můžete zobrazit nahraný soubor v kontejneru úložiště, který jste nakonfigurovali:

    ![Nahraný soubor](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat možnosti nahrávání souborů v centru IoT Hub ke zjednodušení odesílání souborů ze zařízení. Můžete pokračovat v prozkoumání funkcí a scénářů centra IoT v následujících článcích:

* [Programové vytvoření centra IoT hub](iot-hub-rm-template-powershell.md)

* [Úvod do sady C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

---
title: Nahrání souborů ze zařízení do Azure IoT Hub pomocí Pythonu | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí sady SDK pro zařízení Azure IoT pro Python Nahrané soubory se ukládají v kontejneru objektů BLOB služby Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 37ea126952451eae81c548a11f70b9a258808d34
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147359"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto článku se dozvíte, jak pomocí [možností nahrávání souborů IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [úložiště objektů BLOB v Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně poskytněte kontejner úložiště pro nahrání souboru.

* Pomocí klienta Pythonu nahrajte soubor prostřednictvím služby IoT Hub.

[Možnost Odeslat telemetrii ze zařízení do centra IoT](quickstart-send-telemetry-python.md) se rychlým startem předvádí základní funkce zasílání zpráv typu zařízení-Cloud IoT Hub. V některých scénářích ale nemůžete snadno namapovat data, která zařízení odesílají do relativně malých zpráv ze zařízení do cloudu, které IoT Hub přijmout. Pokud potřebujete nakládat soubory ze zařízení, můžete i nadále používat zabezpečení a spolehlivost IoT Hub.

> [!NOTE]
> Sada IoT Hub Python SDK aktuálně podporuje pouze nahrávání znakových souborů, jako jsou soubory **. txt** .

Na konci tohoto kurzu spustíte konzolovou aplikaci v Pythonu:

* **FileUpload.py**, který nahraje soubor do úložiště pomocí sady SDK pro zařízení Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace zařízení

V této části vytvoříte aplikaci pro zařízení pro nahrání souboru do centra IoT.

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **Azure-iothub-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Pomocí textového editoru vytvořte testovací soubor, který odešlete do úložiště objektů BLOB.

    > [!NOTE]
    > Sada IoT Hub Python SDK aktuálně podporuje pouze nahrávání znakových souborů, jako jsou soubory **. txt** .

3. Pomocí textového editoru vytvořte v pracovní složce soubor **FileUpload.py** .

4. Na začátek souboru `import` **FileUpload.py** přidejte následující příkazy a proměnné. 

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

5. V souboru nahraďte `[Device Connection String]` připojovacím řetězcem zařízení služby IoT Hub. Nahraďte `[Full path to file]` cestou k testovacímu souboru, který jste vytvořili, nebo jakýmkoli souborem v zařízení, které chcete nahrát. Nahraďte `[File name for storage]` názvem, který chcete souboru poskytnout po nahrání do úložiště objektů BLOB. 

6. Vytvořte zpětné volání pro funkci **upload_blob** :

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Přidejte následující kód pro připojení klienta a nahrání souboru. Zahrňte `main` také rutinu:

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

8. Uložte a zavřete soubor **UploadFile.py** .

## <a name="run-the-application"></a>Spuštění aplikace

Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku v pracovní složce spusťte následující příkaz:

    ```cmd/sh
    python FileUpload.py
    ```

2. Na následujícím snímku obrazovky vidíte výstup z aplikace pro **nahrání souborů** :

    ![Výstup z aplikace simulovaného zařízení](./media/iot-hub-python-python-file-upload/1.png)

3. K zobrazení nahraného souboru v kontejneru úložiště, který jste nakonfigurovali, můžete použít portál:

    ![Nahraný soubor](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat možnosti nahrávání souborů IoT Hub ke zjednodušení nahrávání souborů ze zařízení. Pomocí následujících článků můžete dál prozkoumat funkce a scénáře IoT Hub:

* [Programové vytvoření centra IoT Hub](iot-hub-rm-template-powershell.md)

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

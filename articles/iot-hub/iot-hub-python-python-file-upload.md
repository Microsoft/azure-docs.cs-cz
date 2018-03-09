---
title: "Odeslání souborů ze zařízení do služby Azure IoT Hub s Pythonem | Microsoft Docs"
description: "Postup nahrání souborů ze zařízení do cloudu pomocí zařízení Azure IoT SDK pro jazyk Python. Odeslané soubory jsou uloženy v kontejneru objektů blob úložiště Azure."
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 5939f87684e92e1f95d39ea5bd52b424ca683acc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Odeslání souborů ze zařízení do cloudu s centrem IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu následuje postup použití [souboru nahrávání služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [úložiště objektů blob Azure](../storage/index.yml). Tento kurz ukazuje, jak na:

- Bezpečně zadejte kontejner úložiště pro nahrání souboru.
- Pomocí klienta Python pro nahrání souboru prostřednictvím služby IoT hub.

[Začínáme se službou IoT Hub](iot-hub-node-node-getstarted.md) a [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-node-node-c2d.md) kurzy zobrazit základních funkcí zařízení cloud a z cloudu do zařízení zasílání zpráv služby IoT Hub. Ale v některých scénářích nelze mapovat snadno data, která vaše zařízení odesílají do poměrně malý zprávy typu zařízení cloud, které IoT Hub přijímá. Když potřebujete hornatých soubory ze zařízení, když můžete nadále používat zabezpečení a spolehlivost služby IoT Hub.

> [!NOTE]
> IoT Hub Python SDK aktuálně podporuje jenom jako nahrávání souborů znakový **.txt** soubory.

Na konci tohoto kurzu Spusťte konzolovou aplikaci Python:

* **FileUpload.py**, které se uloží soubor do úložiště pomocí sady SDK zařízení Python.

> [!NOTE]
> IoT Hub podporuje mnoho zařízení platformy a jazyky (včetně C, .NET, Javascript, Python a Java) prostřednictvím SDK pro zařízení Azure IoT. Odkazovat [Azure střediska pro vývojáře IoT] podrobné pokyny o tom, jak připojit zařízení ke službě Azure IoT Hub.

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* Aktivní účet Azure. (Pokud nemáte účet, můžete vytvořit [bezplatný účet](http://azure.microsoft.com/pricing/free-trial/) si během několika minut.)


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Nahrát soubor z aplikace na zařízení

V této části můžete vytvořit aplikaci zařízení pro nahrání souboru do služby IoT hub.

1. Na příkazovém řádku, spusťte následující příkaz k instalaci **azure-iothub zařízení client** balíčku:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Pomocí textového editoru, vytvořte **FileUpload.py** souboru v pracovní složky.

1. Přidejte následující `import` příkazy a proměnné na začátku **FileUpload.py** souboru. Nahraďte `deviceConnectionString` připojovacím řetězcem zařízení IoT hub:

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.HTTP

    FILENAME = 'sample.txt'
    ```

1. Vytvořte zpětné volání pro **upload_blob** funkce:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Přidejte následující kód k připojení klienta a nahrání souboru. Zahrnují taky `main` rutiny:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )
        
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            client.upload_blob_async(FILENAME, FILENAME, os.path.getsize(FILENAME), blob_upload_conf_callback, 0)
        
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

1. Uložte a zavřete **UploadFile.py** souboru.

1. Ukázkový textový soubor zkopírujte do pracovní složku a přejmenujte ji `sample.txt`.

    > [!NOTE]
    > IoT Hub Python SDK aktuálně podporuje jenom jako nahrávání souborů znakový **.txt** soubory.


## <a name="run-the-application"></a>Spuštění aplikace

Nyní jste připraveni ke spuštění aplikace.

1. Na příkazovém řádku ve složce pracovní, spusťte následující příkaz:

    ```cmd/sh
    python FileUpload.py
    ```

1. Následující snímek obrazovky ukazuje výstup **odesílání souborů při odpovědích** aplikace:

    ![Výstup z aplikace simulated-device](./media/iot-hub-python-python-file-upload/1.png)

1. Na portálu můžete použít k zobrazení nahrávaný soubor v kontejneru úložiště, které jste nakonfigurovali:

    ![Nahrávaný soubor](./media/iot-hub-python-python-file-upload/2.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat funkce nahrávání souboru služby IoT Hub pro zjednodušení nahrávání souborů ze zařízení. Můžete dál prozkoumat funkcí služby IoT hub a scénáře v následujících článcích:

* [Vytvoření služby IoT hub prostřednictvím kódu programu][lnk-create-hub]
* [Úvod do jazyka C SDK][lnk-c-sdk]
* [Sady SDK služby Azure IoT][lnk-sdks]

<!-- Links -->
[Azure střediska pro vývojáře IoT]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
---
title: Nahrávání souborů ze zařízení do Azure IoT Hub s Pythonem | Dokumenty společnosti Microsoft
description: Jak nahrát soubory ze zařízení do cloudu pomocí Azure IoT zařízení SDK pro Python. Nahrané soubory se ukládají v kontejneru objektů blob úložiště Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.openlocfilehash: 706e1920c6c4fe39e885fd3f5a631070545509ee
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529288"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Nahrávání souborů ze zařízení do cloudu pomocí služby IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Tento článek ukazuje, jak pomocí [možností nahrávání souborů služby IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do úložiště objektů [blob Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně zajistěte kontejner úložiště pro nahrávání souboru.

* Pomocí klienta Pythonu nahrajte soubor přes ioT hub.

[Odeslat telemetrii ze zařízení do rychlého startu služby IoT hub](quickstart-send-telemetry-python.md) demonstruje základní funkce zasílání zpráv mezi zařízeními a cloudy služby IoT Hub. V některých případech však nelze snadno mapovat data, která vaše zařízení odesílají do relativně malých zpráv zařízení cloud, které služba IoT Hub přijímá. Když potřebujete upland soubory ze zařízení, můžete stále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte konzolovou aplikaci Pythonu:

* **FileUpload.py**, který nahraje soubor do úložiště pomocí sady Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace pro zařízení

V této části vytvoříte aplikaci pro zařízení pro nahrání souboru do centra IoT hub.

1. Na příkazovém řádku spusťte následující příkaz a nainstalujte balíček **azure-iot-device.** Tento balíček slouží ke koordinaci nahrávání souborů s centrem IoT.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Na příkazovém řádku spusťte následující příkaz a nainstalujte balíček [**azure.storage.blob.**](https://pypi.org/project/azure-storage-blob/) Tento balíček slouží k provedení nahrávání souboru.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Vytvořte testovací soubor, který nahrajete do úložiště objektů blob.

1. Pomocí textového editoru vytvořte **FileUpload.py** soubor v pracovní složce.

1. Na začátek `import` **souboru FileUpload.py** přidejte následující příkazy a proměnné.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. V souboru `[Device Connection String]` nahraďte připojovacířetězec zařízení ioT hub. Nahraďte `[Full path to local file]` cestou k testovacímu souboru, který jste vytvořili, nebo libovolným souborem v zařízení, který chcete nahrát.

1. Vytvořte funkci pro nahrání souboru do úložiště objektů blob:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Tato funkce analyzuje *blob_info* struktury do něj vytvořit adresu URL, která používá k inicializaci [azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Pak nahraje soubor do úložiště objektů blob Azure pomocí tohoto klienta.

1. Přidejte následující kód pro připojení klienta a nahrání souboru:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Tento kód vytvoří asynchronní **IoTHubDeviceClient** a používá následující rozhraní API pro správu nahrávání souborů s centrem IoT:

    * **get_storage_info_for_blob** získává informace z vašeho centra IoT hub o propojeném účtu úložiště, který jste vytvořili dříve. Tyto informace zahrnují název hostitele, název kontejneru, název objektu blob a token SAS. Informace o úložišti se předávají **funkci store_blob** (vytvořené v předchozím kroku), takže **objekt BlobClient** v této funkci se může ověřit pomocí úložiště Azure. Metoda **get_storage_info_for_blob** také vrátí correlation_id, který se používá v **notify_blob_upload_status** metodě. correlation_id je způsob, jak ioT Hub označuje, na kterém objektu blob pracujete.

    * **notify_blob_upload_status** upozorní službu IoT Hub na stav operace úložiště objektů blob. Předáte ji correlation_id získané **metodou get_storage_info_for_blob.** Služba IoT Hub ho používá k upozornění jakékoli služby, která může naslouchat oznámení o stavu úlohy nahrávání souborů.

1. Uložte a zavřete **soubor UploadFile.py.**

## <a name="run-the-application"></a>Spuštění aplikace

Nyní jste připraveni spustit aplikaci.

1. Na příkazovém řádku v pracovní složce spusťte následující příkaz:

    ```cmd/sh
    python FileUpload.py
    ```

2. Následující snímek obrazovky ukazuje výstup z aplikace **FileUpload:**

    ![Výstup z aplikace simulované zařízení](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Pomocí portálu můžete zobrazit nahraný soubor v kontejneru úložiště, který jste nakonfigurovali:

    ![Nahraný soubor](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat možnosti nahrávání souborů v centru IoT Hub ke zjednodušení odesílání souborů ze zařízení. Můžete pokračovat v prozkoumání funkcí a scénářů centra IoT v následujících článcích:

* [Programové vytvoření centra IoT hub](iot-hub-rm-template-powershell.md)

* [Úvod do sady C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Další informace o Azure Blob Storage s následujícími odkazy:

* [Dokumentace k úložišti objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/)

* [Azure Blob Storage pro dokumentaci rozhraní API pythonu](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)

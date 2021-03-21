---
title: Nahrání souborů ze zařízení do Azure IoT Hub pomocí Pythonu | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí sady SDK pro zařízení Azure IoT pro Python Nahrané soubory se ukládají v kontejneru objektů BLOB služby Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 77d51b2c839a64567838fa4d6308d203a6bb8b82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501137"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto článku se dozvíte, jak pomocí [možností nahrávání souborů IoT Hub](iot-hub-devguide-file-upload.md) nahrát soubor do [úložiště objektů BLOB v Azure](../storage/index.yml). V tomto kurzu získáte informace o následujících postupech:

* Bezpečně poskytněte kontejner úložiště pro nahrání souboru.

* Pomocí klienta Pythonu nahrajte soubor prostřednictvím služby IoT Hub.

[Možnost Odeslat telemetrii ze zařízení do centra IoT](quickstart-send-telemetry-python.md) se rychlým startem předvádí základní funkce zasílání zpráv typu zařízení-Cloud IoT Hub. V některých scénářích ale nemůžete snadno namapovat data, která zařízení odesílají do relativně malých zpráv ze zařízení do cloudu, které IoT Hub přijmout. Pokud potřebujete nakládat soubory ze zařízení, můžete i nadále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte konzolovou aplikaci v Pythonu:

* **FileUpload.py**, který nahraje soubor do úložiště pomocí sady SDK pro zařízení Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace zařízení

V této části vytvoříte aplikaci pro zařízení pro nahrání souboru do centra IoT.

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **Azure-IoT-Device** . Tento balíček můžete použít ke koordinaci nahrávání souborů ve službě IoT Hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku [**Azure. Storage. blob**](https://pypi.org/project/azure-storage-blob/) . Pomocí tohoto balíčku provedete odeslání souboru.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Vytvořte testovací soubor, který nahrajete do úložiště objektů BLOB.

1. Pomocí textového editoru vytvořte v pracovní složce soubor **FileUpload.py** .

1. Na `import` začátek souboru **FileUpload.py** přidejte následující příkazy a proměnné.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. V souboru nahraďte `[Device Connection String]` připojovacím řetězcem zařízení služby IoT Hub. Nahraďte `[Full path to local file]` cestou k testovacímu souboru, který jste vytvořili, nebo jakýmkoli souborem v zařízení, který chcete nahrát.

1. Vytvořte funkci pro nahrání souboru do úložiště objektů BLOB:

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

    Tato funkce analyzuje předanou strukturu *blob_info* , aby VYTVOŘILA adresu URL, kterou používá k inicializaci [Azure. Storage. blob. BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient). Pak tento soubor nahraje do úložiště objektů BLOB v Azure pomocí tohoto klienta.

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

    Tento kód vytvoří asynchronní **IoTHubDeviceClient** a pomocí následujících rozhraní API spravuje nahrávání souboru ve službě IoT Hub:

    * **get_storage_info_for_blob** získá informace ze služby IoT Hub o propojeném účtu úložiště, který jste vytvořili dříve. Tyto informace zahrnují název hostitele, název kontejneru, název objektu BLOB a token SAS. Informace o úložišti jsou předány funkci **store_blob** (vytvořené v předchozím kroku), aby se **BlobClient** v této funkci mohla ověřit pomocí Azure Storage. Metoda **get_storage_info_for_blob** také vrací correlation_id, která se používá v metodě **notify_blob_upload_status** . Correlation_id je IoT Hub způsob označení objektu blob, na kterém pracujete.

    * **notify_blob_upload_status** upozorní IoT Hub stavu operace úložiště objektů BLOB. Předáte je correlation_id získanou metodou **get_storage_info_for_blob** . Používá se IoT Hub k oznámení jakékoli služby, která může naslouchat oznámení o stavu úlohy nahrávání souboru.

1. Uložte a zavřete soubor **UploadFile.py** .

## <a name="run-the-application"></a>Spuštění aplikace

Teď jste připraveni aplikaci spustit.

1. Na příkazovém řádku v pracovní složce spusťte následující příkaz:

    ```cmd/sh
    python FileUpload.py
    ```

2. Na následujícím snímku obrazovky vidíte výstup z aplikace pro **nahrání souborů** :

    ![Výstup z aplikace simulovaného zařízení](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. K zobrazení nahraného souboru v kontejneru úložiště, který jste nakonfigurovali, můžete použít portál:

    ![Nahraný soubor](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat možnosti nahrávání souborů IoT Hub ke zjednodušení nahrávání souborů ze zařízení. Pomocí následujících článků můžete dál prozkoumat funkce a scénáře IoT Hub:

* [Programové vytvoření centra IoT Hub](iot-hub-rm-template-powershell.md)

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Další informace o Azure Blob Storage s následujícími odkazy:

* [Dokumentace k Azure Blob Storage](../storage/blobs/index.yml)

* [Dokumentace k rozhraní Python API pro Azure Blob Storage](/python/api/overview/azure/storage-blob-readme)
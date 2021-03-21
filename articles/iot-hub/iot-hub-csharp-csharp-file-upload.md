---
title: Nahrání souborů ze zařízení do Azure IoT Hub pomocí .NET | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí sady SDK pro zařízení Azure IoT pro .NET Nahrané soubory se ukládají v kontejneru objektů BLOB služby Azure Storage.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 43cafb8c5efe0581fe7c4136aa41980b3d817be2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981404"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu se seznámíte s kódem v tématu [posílání zpráv z cloudu do zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu, ve kterém se dozvíte, jak používat možnosti nahrávání souborů IoT Hub. Ukazuje, jak:

* Bezpečně poskytněte zařízení s identifikátorem URI objektu blob Azure pro nahrání souboru.

* Pomocí IoT Hub oznámení o nahrávání souborů můžete aktivovat zpracování souboru v back-endu vaší aplikace.

[Odeslání telemetrie ze zařízení do rychlého startu centra IoT](quickstart-send-telemetry-dotnet.md) a [posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu zobrazuje základní funkce zasílání zpráv typu zařízení-Cloud a Cloud-zařízení IoT Hub. Kurz [Konfigurace směrování zpráv pomocí IoT Hub](tutorial-routing.md) popisuje způsob, jak spolehlivě ukládat zprávy typu zařízení-cloud do Microsoft Azure úložiště objektů BLOB. V některých scénářích ale nemůžete snadno namapovat data, která zařízení odesílají do relativně malých zpráv ze zařízení do cloudu, které IoT Hub přijmout. Například:

* Velké soubory, které obsahují obrázky

* Videa

* Ukázka dat vibrací s vysokou frekvencí

* Některá forma předzpracovaných dat

Tyto soubory jsou obvykle dávkově zpracovávány v cloudu pomocí nástrojů, jako je [Azure Data Factory](../data-factory/introduction.md) nebo zásobník [Hadoop](../hdinsight/index.yml) . Pokud potřebujete nahrávat soubory ze zařízení, můžete i nadále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte dvě konzolové aplikace .NET:

* **SimulatedDevice**. Tato aplikace nahraje soubor do úložiště pomocí identifikátoru URI SAS, který poskytuje vaše služba IoT Hub. Jedná se o upravenou verzi aplikace vytvořenou v kurzu [posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu.

* **ReadFileUploadNotification**. Tato aplikace přijímá oznámení o nahrání souborů ze služby IoT Hub.

> [!NOTE]
> IoT Hub podporuje mnoho platforem a jazyků zařízení, včetně jazyků C, Java, Python a JavaScriptu, prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny k připojení zařízení k Azure IoT Hub najdete v [centru pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot) .

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Předpoklady

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace zařízení

V této části upravíte aplikaci zařízení, kterou jste vytvořili v části [posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) pro příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. V aplikaci Visual Studio Průzkumník řešení klikněte pravým tlačítkem na projekt **SimulatedDevice** a vyberte **Přidat**  >  **existující položku**. Najděte soubor obrázku a zahrňte ho do projektu. V tomto kurzu se předpokládá, že se image jmenuje `image.jpg` .

1. Klikněte pravým tlačítkem na obrázek a pak vyberte **vlastnosti**. Ujistěte se, že je **Složka kopírovat do výstupního adresáře** nastavená na hodnotu **vždy kopírovat**.

    ![Zobrazit, kde aktualizovat vlastnost image pro kopírování do výstupního adresáře](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. V souboru **program. cs** přidejte do horní části souboru následující příkazy:

    ```csharp
    using System.IO;
    ```

1. Do třídy **program** přidejte následující metodu:

    ```csharp
    private static async Task SendToBlobAsync(string fileName)
    {
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        await deviceClient.GetFileUploadSasUriAsync(new FileUploadSasUriRequest { BlobName = fileName });
        var blob = new CloudBlockBlob(sas.GetBlobUri());
        await blob.UploadFromFileAsync(fileName);
        await deviceClient.CompleteFileUploadAsync(new FileUploadCompletionNotification { CorrelationId = sas.CorrelationId, IsSuccess = true });

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync`Metoda přebírá název souboru a zdroj streamu souboru, který se má nahrát, a zpracuje odeslání do úložiště. Aplikace konzoly zobrazuje dobu potřebnou k nahrání souboru.

1. Do metody **Main** přidejte následující řádek, a to hned před `Console.ReadLine()` :

    ```csharp
    await SendToBlobAsync("image.jpg");
    ```

> [!NOTE]
> V zájmu zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly být implementovány zásady opakování, například exponenciální omezení rychlosti, navržené při [zpracování přechodné chyby](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro příjem zpráv s oznámením o nahrávání souborů ze služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md). Aby bylo možné přijímat zprávy s oznámením o nahrávání souborů, vaše služba potřebuje oprávnění **k připojení služby** . Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Doručení oznámení o nahrání souboru

V této části napíšete konzolovou aplikaci .NET, která přijímá zprávy s oznámením o nahrávání souborů z IoT Hub.

1. V aktuálním řešení sady Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**. V poli **vytvořit nový projekt** vyberte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.

1. Pojmenujte projekt *ReadFileUploadNotification*. V části **řešení** vyberte **Přidat do řešení**. Vyberte **Vytvořit** a vytvořte projekt.

    ![Konfigurace projektu ReadFileUploadNotification v aplikaci Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt **ReadFileUploadNotification** a vyberte možnost **Spravovat balíčky NuGet**.

1. V **nástroji Správce balíčků NuGet** vyberte **Procházet**. Vyhledejte a vyberte **Microsoft. Azure. Devices** a pak vyberte **nainstalovat**.

    Tento krok stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) v projektu **ReadFileUploadNotification** .

1. V souboru **program. cs** pro tento projekt přidejte do horní části souboru následující příkaz:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{iot hub connection string}` hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce centra IoT](#get-the-iot-hub-connection-string):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Do třídy **program** přidejte následující metodu:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Poznámka: Tento model příjmu je stejný jako použitý k přijímání zpráv z cloudu na zařízení z aplikace zařízení.

1. Nakonec přidejte do metody **Main** následující řádky:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. V Průzkumníku řešení klikněte pravým tlačítkem na své řešení a vyberte **nastavit projekty po spuštění**.

1. V **okně běžné vlastnosti**  >  **spouštěný projekt** vyberte **více projektů po spuštění** a pak vyberte akci **spuštění** pro **ReadFileUploadNotification** a **SimulatedDevice**. Výběrem **OK** uložte změny.

1. Stiskněte klávesu **F5**. Obě aplikace by měly být spuštěny. Měli byste vidět, že nahrávání bylo dokončeno v jedné aplikaci konzoly a zpráva oznámení o odeslání obdržená jinou konzolovou aplikací. K vyhledání přítomnosti nahraného souboru ve vašem účtu Azure Storage můžete použít [Azure Portal](https://portal.azure.com/) nebo Průzkumník serveru sady Visual Studio.

    ![Snímek obrazovky zobrazující obrazovku výstup](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat možnosti nahrávání souborů IoT Hub ke zjednodušení nahrávání souborů ze zařízení. Můžete dál prozkoumat IoT Hub funkcí a scénářů pomocí následujících článků:

* [Programové vytvoření centra IoT Hub](iot-hub-rm-template-powershell.md)

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)
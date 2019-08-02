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
ms.openlocfilehash: 6e41b1999033c00b277cd35173b3247a727e9a8a
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668140"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu se seznámíte s kódem v tématu [posílání zpráv z cloudu do zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu, ve kterém se dozvíte, jak používat možnosti nahrávání souborů IoT Hub. Ukazuje, jak:

* Bezpečně poskytněte zařízení s identifikátorem URI objektu blob Azure pro nahrání souboru.

* Pomocí IoT Hub oznámení o nahrávání souborů můžete aktivovat zpracování souboru v back-endu vaší aplikace.

[Odeslání telemetrie ze zařízení do rychlého startu centra IoT](quickstart-send-telemetry-dotnet.md) a posílání [zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu zobrazuje základní funkce zasílání zpráv typu zařízení-Cloud a Cloud-zařízení IoT Hub. Kurz [Konfigurace směrování zpráv pomocí IoT Hub](tutorial-routing.md) popisuje způsob, jak spolehlivě ukládat zprávy typu zařízení-Cloud do úložiště objektů BLOB v Azure. V některých scénářích ale nemůžete snadno namapovat data, která zařízení odesílají do relativně malých zpráv ze zařízení do cloudu, které IoT Hub přijmout. Příklad:

* Velké soubory, které obsahují obrázky
* Videa
* Ukázka dat vibrací s vysokou frekvencí
* Některá forma předzpracovaných dat

Tyto soubory jsou obvykle dávkově zpracovávány v cloudu pomocí nástrojů, jako je [Azure Data Factory](../data-factory/introduction.md) nebo zásobník [Hadoop](../hdinsight/index.yml) . Pokud potřebujete nahrávat soubory ze zařízení, můžete i nadále používat zabezpečení a spolehlivost IoT Hub.

Na konci tohoto kurzu spustíte dvě konzolové aplikace .NET:

* **SimulatedDevice**je upravená verze aplikace vytvořená v kurzu [posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu. Tato aplikace nahraje soubor do úložiště pomocí identifikátoru URI SAS, který poskytuje vaše služba IoT Hub.

* **ReadFileUploadNotification**, který přijímá oznámení o nahrávání souborů ze služby IoT Hub.

> [!NOTE]
> IoT Hub podporuje spoustu platforem a jazyků zařízení (včetně C, Java, Pythonu a JavaScriptu) prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny k připojení zařízení k Azure IoT Hub najdete v [centru pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot) .

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace zařízení

V této části upravíte aplikaci zařízení, kterou jste vytvořili v části [posílání zpráv z cloudu na zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) pro příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. V aplikaci Visual Studio klikněte pravým tlačítkem na projekt **SimulatedDevice** , klikněte na **Přidat**a pak klikněte na **existující položka**. Přejděte do souboru obrázku a vložte ho do projektu. V tomto kurzu se předpokládá, že `image.jpg`se image jmenuje.

1. Klikněte pravým tlačítkem na obrázek a pak klikněte na **vlastnosti**. Ujistěte se, že je **Složka kopírovat do výstupního adresáře** nastavená na hodnotu **vždy kopírovat**.

    ![Zobrazit, kde aktualizovat vlastnost image pro kopírování do výstupního adresáře](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. V souboru **program.cs** přidejte do horní části souboru následující příkazy:

    ```csharp
    using System.IO;
    ```

1. Přidejte následující metodu do třídy **Program**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` Metoda přebírá název souboru a zdroj streamu souboru, který se má nahrát, a zpracuje odeslání do úložiště. Aplikace konzoly zobrazuje dobu potřebnou k nahrání souboru.

1. Do metody **Main** přidejte následující metodu, která je přímo před `Console.ReadLine()` řádkem:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> V zájmu zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro příjem zpráv s oznámením o nahrávání souborů ze služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-dotnet.md). Aby bylo možné přijímat zprávy s oznámením o nahrávání souborů, vaše služba potřebuje oprávnění **k připojení služby** . Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Doručení oznámení o nahrání souboru

V této části napíšete konzolovou aplikaci .NET, která přijímá zprávy s oznámením o nahrávání souborů z IoT Hub.

1. V aktuálním řešení sady Visual Studio vytvořte projekt Visual C# Windows pomocí šablony projektu konzolová **aplikace** . Pojmenujte projekt **ReadFileUploadNotification**.

    ![Nový projekt v aplikaci Visual Studio](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. V Průzkumník řešení klikněte pravým tlačítkem na projekt **ReadFileUploadNotification** a potom klikněte na **Spravovat balíčky NuGet...** .

3. V okně **Správce balíčků NuGet** vyhledejte **Microsoft. Azure. Devices**, klikněte na **nainstalovat**a přijměte podmínky použití.

    Tato akce stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) v projektu **ReadFileUploadNotification** .

4. V souboru **program.cs** přidejte do horní části souboru následující příkazy:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu [](#get-the-iot-hub-connection-string) zástupnéhosymbolupřipojovacímřetězcemIoTHub,kterýjstezkopírovalidřívevčástizískánípřipojovacíhořetězcecentra`{iot hub connection string}` IoT:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

6. Přidejte následující metodu do třídy **Program**:

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

7. Nakonec do metody **Main** přidejte následující řádky:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. V sadě Visual Studio klikněte pravým tlačítkem na své řešení a vyberte **nastavit projekty po spuštění**. Vyberte **více projektů po spuštění**a pak vyberte akci **spuštění** pro **ReadFileUploadNotification** a **SimulatedDevice**.

2. Stiskněte klávesu **F5**. Obě aplikace by měly být spuštěny. Měli byste vidět, že nahrávání bylo dokončeno v jedné aplikaci konzoly a zpráva oznámení o odeslání obdržená jinou konzolovou aplikací. K vyhledání přítomnosti nahraného souboru ve vašem účtu Azure Storage můžete použít [Azure Portal](https://portal.azure.com/) nebo Průzkumník serveru sady Visual Studio.

    ![Snímek obrazovky zobrazující obrazovku výstup](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat možnosti nahrávání souborů IoT Hub ke zjednodušení nahrávání souborů ze zařízení. Pomocí následujících článků můžete dál prozkoumat funkce a scénáře IoT Hub:

* [Programové vytvoření centra IoT Hub](iot-hub-rm-template-powershell.md)

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení pomocí Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

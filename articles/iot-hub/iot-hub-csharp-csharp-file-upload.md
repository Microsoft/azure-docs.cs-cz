---
title: Odeslání souborů ze zařízení do služby Azure IoT Hub s .NET | Microsoft Docs
description: Postup nahrání souborů ze zařízení do cloudu pomocí zařízení Azure IoT SDK pro .NET. Odeslané soubory jsou uloženy v kontejneru objektů blob úložiště Azure.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 901b4b6c631d47a6c37eb232f66d8350faa9be76
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Odeslání souborů ze zařízení do cloudu službou IoT Hub pomocí rozhraní .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu vychází kód [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-csharp-csharp-c2d.md) kurzu ukazují, jak využít možnosti nahrávání souboru služby IoT Hub. Jak ukazuje na:

- Bezpečně zadejte zařízení s Azure blob identifikátor URI pro nahrání souboru.
- Oznámení o odeslání souboru IoT Hub použijte k aktivaci zpracování souboru ve vaší aplikaci back-end.

[Začínáme se službou IoT Hub](iot-hub-csharp-csharp-getstarted.md) a [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-csharp-csharp-c2d.md) kurzy zobrazit základních funkcí zařízení cloud a z cloudu do zařízení zasílání zpráv služby IoT Hub. [Zprávy procesu zařízení-Cloud](iot-hub-csharp-csharp-process-d2c.md) kurz popisuje způsob, jak spolehlivě ukládat zprávy typu zařízení cloud do úložiště objektů blob v Azure. Ale v některých scénářích nelze mapovat snadno data, která vaše zařízení odesílají do poměrně malý zprávy typu zařízení cloud, které IoT Hub přijímá. Příklad:

* Velkých souborů, které obsahují Image
* Videa
* Data vibrace odebírána data v vysoká frekvence
* Určitou formu předběžně zpracované dat

Tyto soubory jsou obvykle dávkové zpracování v cloudu pomocí nástrojů, jako [Azure Data Factory](../data-factory/introduction.md) nebo [Hadoop](../hdinsight/index.yml) zásobníku. Pokud budete potřebovat k nahrání souborů ze zařízení, když můžete nadále používat zabezpečení a spolehlivost služby IoT Hub.

Na konci tohoto kurzu můžete spustit dvě aplikace konzoly .NET:

* **SimulatedDevice**, upravenou verzi aplikace vytvořená v [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-csharp-csharp-c2d.md) kurzu. Tato aplikace se uloží soubor do úložiště pomocí identifikátoru URI SAS poskytované služby IoT hub.
* **ReadFileUploadNotification**, který obdrží oznámení o odeslání souboru ze služby IoT hub.

> [!NOTE]
> IoT Hub podporuje mnoho zařízení platformy a jazyky (včetně C, Javy a JavaScriptu) prostřednictvím sady SDK pro zařízení Azure IoT. Odkazovat [Azure střediska pro vývojáře IoT] podrobné pokyny o tom, jak připojit zařízení ke službě Azure IoT Hub.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2015 nebo Visual Studio 2017
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrát soubor z aplikace na zařízení

V této části upravíte zařízení aplikaci, kterou jste vytvořili v [odesílání zpráv typu Cloud-zařízení s centrem IoT](iot-hub-csharp-csharp-c2d.md) pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. V sadě Visual Studio, klikněte pravým tlačítkem myši **SimulatedDevice** projektu, klikněte na tlačítko **přidat**a potom klikněte na **existující položka**. Přejděte na soubor obrázku a její zahrnutí do projektu. Tento kurz předpokládá bitovou kopii jmenuje `image.jpg`.

1. Klikněte pravým tlačítkem na bitovou kopii a pak klikněte na **vlastnosti**. Ujistěte se, že **kopírovat do výstupního adresáře** je nastaven na **vždy Kopírovat**.

    ![][1]

1. V **Program.cs** souboru, v horní části souboru přidejte následující příkazy:

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

    `UploadToBlobAsync` Metoda přebírá ve zdrojovém souboru název a datový proud souboru k odeslání a zpracovává nahrávání do úložiště. Konzolové aplikace zobrazí dobu potřebnou k odeslání souboru.

1. Přidejte následující metodu v **hlavní** metoda, těsně před `Console.ReadLine()` řádku:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Pro saké na jednoduchost tento kurz neimplementuje žádné zásady opakování. V produkčním kódu, měli byste implementovat zásady opakování (například exponenciálního omezení rychlosti), dle pokynů v článku na webu MSDN [přechodných chyb].

## <a name="receive-a-file-upload-notification"></a>Nechte si zaslat oznámení nahrávání souborů

V této části napíšete konzolovou aplikaci .NET, která přijímá zprávy oznámení nahrávání souborů ze služby IoT Hub.

1. V aktuálním řešení sady Visual Studio vytvořte projekt Visual C# Windows pomocí **konzolové aplikace** šablona projektu. Název projektu **ReadFileUploadNotification**.

    ![Nový projekt v sadě Visual Studio][2]

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **ReadFileUploadNotification** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .

1. V **Správce balíčků NuGet** vyhledejte **Microsoft.Azure.Devices**, klikněte na tlačítko **nainstalovat**a přijměte podmínky použití.

    Tato akce stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT] v **ReadFileUploadNotification** projektu.

1. V **Program.cs** souboru, v horní části souboru přidejte následující příkazy:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem IoT hub z [Začínáme s centrem IoT]:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Přidejte následující metodu do třídy **Program**:

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
            Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Všimněte si, že tento vzor receive se shoduje s klíčem pro příjem zpráv typu cloud zařízení z aplikace zařízení.

1. Nakonec do metody **Main** přidejte následující řádky:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. V sadě Visual Studio, klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění**. Vyberte **více projektů po spuštění**, vyberte **spustit** akce pro **ReadFileUploadNotification** a **SimulatedDevice**.

1. Stiskněte klávesu **F5**. Obě aplikace by se měl spustit. Měli byste vidět nahrávání dokončit v jedné aplikaci konzoly a odesílání oznámení přijatých konzolovou aplikaci. Můžete použít [portál Azure] nebo Průzkumníka serveru Visual Studia chcete zkontrolovat přítomnost nahrávaný soubor ve vašem účtu úložiště Azure.

    ![][50]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat funkce nahrávání souboru služby IoT Hub pro zjednodušení nahrávání souborů ze zařízení. Můžete dál prozkoumat funkcí služby IoT hub a scénáře v následujících článcích:

* [Vytvoření služby IoT hub prostřednictvím kódu programu][lnk-create-hub]
* [Úvod do jazyka C SDK][lnk-c-sdk]
* [Sady SDK služby Azure IoT][lnk-sdks]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[portál Azure]: https://portal.azure.com/

[Azure střediska pro vývojáře IoT]: http://azure.microsoft.com/develop/iot

[přechodných chyb]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[balíček NuGet sady SDK služby Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

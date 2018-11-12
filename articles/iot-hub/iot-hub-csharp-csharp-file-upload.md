---
title: Nahrání souborů ze zařízení do služby Azure IoT Hub pomocí .NET | Dokumentace Microsoftu
description: Postup nahrání souborů ze zařízení do cloudu pomocí zařízení Azure IoT SDK pro .NET. Nahrané soubory se ukládají v kontejneru objektů blob v Azure storage.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 25ec3a158d1eca77a7ca622af9b249789ef3b5e2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259322"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Nahrání souborů ze zařízení do cloudu pomocí služby IoT Hub pomocí .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

V tomto kurzu vychází z kódu v [odesílat zprávy typu Cloud-zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu se dozvíte, jak pomocí funkce nahrání souboru služby IoT Hub. To se dozvíte, jak do:

- Zabezpečeně dodávají zařízení s Azure blob identifikátorů URI pro nahrání souboru.

- Oznámení o nahrávání souborů služby IoT Hub použijte k aktivaci zpracování souboru v back-endu aplikace.

[Odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md) a [odesílat zprávy typu Cloud-zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md) články popisují základní funkce typu zařízení cloud a cloud zařízení zasílání zpráv služby IoT Hub. [Konfigurace směrování zpráv pomocí služby IoT Hub](tutorial-routing.md) kurz popisuje způsob, jak spolehlivě ukládat zprávy typu zařízení cloud ve službě Azure blob storage. Nicméně v některých scénářích nelze mapovat snadno data, která vaše zařízení odesílají do poměrně málo početnému zpráv typu zařízení cloud, které služby IoT Hub přijímá. Příklad:

* Velké soubory, které obsahují obrázky
* Videa
* Data pronikavost odebírána data v vysoká frekvence
* Určitou formu předzpracovaná dat

Tyto soubory jsou obvykle dávkově zpracovány v cloudu pomocí nástrojů, jako [Azure Data Factory](../data-factory/introduction.md) nebo [Hadoop](../hdinsight/index.yml) zásobníku. Když budete potřebovat k nahrání souborů ze zařízení, můžete stále použít zabezpečení a spolehlivost služby IoT Hub.

Na konci tohoto kurzu spustíte dvě konzolové aplikace .NET:

* **SimulatedDevice**, upravenou verzi aplikaci vytvořenou v [odesílat zprávy typu Cloud-zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md) kurzu. Tato aplikace nahraje soubor do služby storage pomocí SAS URI poskytované služby IoT hub.

* **ReadFileUploadNotification**, který obdrží oznámení o nahrávání souborů ze služby IoT hub.

> [!NOTE]
> IoT Hub podporuje mnoho platforem zařízení a jazyků (včetně C, Javy a JavaScriptu) prostřednictvím sady SDK pro zařízení Azure IoT. Odkazovat [centrum pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot) podrobné pokyny o tom, jak připojit zařízení ke službě Azure IoT Hub.

Pro absolvování tohoto kurzu potřebujete:

* Visual Studio 2017
* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrajte soubor z aplikace pro zařízení

V této části upravíte zařízení aplikaci, kterou jste vytvořili v [odesílat zprávy typu Cloud-zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md) pro příjem zpráv typu cloud zařízení ze služby IoT hub.

1. V sadě Visual Studio, klikněte pravým tlačítkem myši **SimulatedDevice** projektu, klikněte na tlačítko **přidat**a potom klikněte na **existující položku**. Přejděte na soubor obrázku a zahrnout do projektu. Tento kurz předpokládá, že image má název `image.jpg`.

1. Klikněte pravým tlačítkem na obrázku a potom klikněte na tlačítko **vlastnosti**. Ujistěte se, že **kopírovat do výstupního adresáře** je nastavena na **vždy Kopírovat**.

    ![Zobrazit, kde se aktualizovat vlastnosti image pro kopírovat do výstupního adresáře](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. V **Program.cs** na začátek souboru přidejte následující příkazy:

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

    `UploadToBlobAsync` Metoda přijímá ve zdrojovém souboru název a datový proud souboru k odeslání a zpracovává nahrávání do úložiště. Aplikace konzoly se zobrazí čas potřebný k odeslání souboru.

1. Přidejte následující metodu v **hlavní** metody, těsně před `Console.ReadLine()` řádku:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Pro saké pro zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="receive-a-file-upload-notification"></a>Přijímat oznámení o nahrání souborů

V této části napíšete konzolovou aplikaci .NET, která bude přijímat zprávy oznámení nahrávání souborů ze služby IoT Hub.

1. V aktuálním řešení sady Visual Studio vytvořte projekt Visual C# Windows s použitím **konzolovou aplikaci** šablony projektu. Pojmenujte projekt **ReadFileUploadNotification**.

    ![Nový projekt v sadě Visual Studio](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **ReadFileUploadNotification** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** .

3. V **Správce balíčků NuGet** okna, vyhledejte **Microsoft.Azure.Devices**, klikněte na tlačítko **nainstalovat**a přijměte podmínky použití.

    Tato akce stáhne, nainstaluje a přidá odkaz na [balíček NuGet sady SDK služby Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) v **ReadFileUploadNotification** projektu.

4. V **Program.cs** na začátek souboru přidejte následující příkazy:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. Do třídy **Program** přidejte následující pole. Nahraďte hodnotu zástupného symbolu připojovacím řetězcem IoT hub z [odesílání telemetrických dat ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md):

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

    Všimněte si, že tento model receive je stejný, slouží k přijímání zpráv z cloudu do zařízení z aplikace pro zařízení.

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

1. V sadě Visual Studio, klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění**. Vyberte **více projektů po spuštění**a pak **Start** akce pro **ReadFileUploadNotification** a **SimulatedDevice**.

2. Stisknutím klávesy **F5**. Obě aplikace by měla začít. Nahrávání dokončeno v jedné aplikaci konzoly a odesílání zprávy oznámení přijatých konzolovou aplikaci, byste měli vidět. Můžete použít [webu Azure portal](https://portal.azure.com/) nebo Průzkumníka serveru Visual Studia ke kontrole přítomnosti nahraný soubor ve vašem účtu úložiště Azure.

    ![Snímek obrazovky výstupu](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak zjednodušit nahrávání souborů ze zařízení pomocí možnosti nahrávání souborů služby IoT Hub. Můžete pokračovat k prozkoumání funkcí služby IoT hub a scénáře najdete v následujících článcích:

* [Vytvoření centra IoT prostřednictvím kódu programu](iot-hub-rm-template-powershell.md)
* [Seznámení s C SDK](iot-hub-device-sdk-c-intro.md)
* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)


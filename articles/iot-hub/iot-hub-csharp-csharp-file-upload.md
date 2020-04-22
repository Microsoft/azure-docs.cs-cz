---
title: Nahrávání souborů ze zařízení do služby Azure IoT Hub s rozhraním .NET | Dokumenty společnosti Microsoft
description: Jak nahrát soubory ze zařízení do cloudu pomocí sady Azure IoT device SDK pro rozhraní .NET. Nahrané soubory se ukládají v kontejneru objektů blob úložiště Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 14472e84d425bf03a3c6a0c2dc558d4b8225caec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733397"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Nahrávání souborů ze zařízení do cloudu pomocí služby IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Tento kurz vychází z kódu v kurzu Odesílat zprávy cloud zařízení pomocí služby [IoT Hub,](iot-hub-csharp-csharp-c2d.md) který vám ukáže, jak používat možnosti nahrávání souborů služby IoT Hub. To vám ukáže, jak:

* Bezpečně poskytněte zařízení s identifikátorem URI objektu BLOB Azure pro nahrávání souboru.

* Pomocí oznámení o nahrání souboru centra IoT Hub aktivujte zpracování souboru v back-endu aplikace.

[Odeslání telemetrie ze zařízení do rychlého startu služby IoT hub](quickstart-send-telemetry-dotnet.md) a odesílání zpráv z cloudu na zařízení pomocí kurzu [služby IoT Hub](iot-hub-csharp-csharp-c2d.md) zobrazují základní funkce zasílání zpráv mezi zařízeními a cloud-to-zařízení služby IoT Hub. Kurz [Konfigurace směrování zpráv pomocí služby IoT Hub](tutorial-routing.md) popisuje způsob spolehlivého ukládání zpráv mezi zařízeními a cloudy v úložišti objektů Blob Microsoft Azure. V některých případech však nelze snadno mapovat data, která vaše zařízení odesílají do relativně malých zpráv zařízení ke cloudu, které služba IoT Hub přijímá. Příklad:

* Velké soubory, které obsahují obrázky

* Videa

* Údaje o vibracích odebrané při vysoké frekvenci

* Určitá forma předem zpracovaných dat

Tyto soubory se obvykle zpracovávají v cloudu pomocí nástrojů, jako je [Azure Data Factory](../data-factory/introduction.md) nebo zásobník [Hadoop.](../hdinsight/index.yml) Když potřebujete nahrát soubory ze zařízení, můžete dál používat zabezpečení a spolehlivost IoT Hubu.

Na konci tohoto kurzu spustíte dvě konzolové aplikace .NET:

* **SimulatedDevice**. Tato aplikace nahraje soubor do úložiště pomocí identifikátoru URI SAS poskytovaného službou IoT hub. Jedná se o upravenou verzi aplikace vytvořenou v kurzu Odeslat zprávy z cloudu na zařízení pomocí služby [IoT Hub.](iot-hub-csharp-csharp-c2d.md)

* **ReadFileUploadNotification**. Tato aplikace přijímá oznámení o nahrání souborů z vašeho centra IoT hub.

> [!NOTE]
> IoT Hub podporuje mnoho platforem a jazyků zařízení, včetně C, Java, Pythonu a Javascriptu, prostřednictvím sad SDK zařízení Azure IoT. Podrobné pokyny k připojení zařízení k Azure IoT Hub vám najdete v [Centru pro vývojáře Azure IoT.](https://azure.microsoft.com/develop/iot)

## <a name="prerequisites"></a>Požadavky

* Visual Studio

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Nahrání souboru z aplikace pro zařízení

V této části upravíte aplikaci zařízení, kterou jste vytvořili v [aplikaci Odesílání zpráv z cloudu na zařízení pomocí služby IoT Hub,](iot-hub-csharp-csharp-c2d.md) abyste přijímali zprávy z cloudu na zařízení z centra IoT.

1. V Průzkumníku řešení Visual Studio klepněte pravým tlačítkem myši na projekt **SimulatedDevice** a vyberte **přidat** > **existující položku**. Najděte soubor obrázku a zahrňte ho do projektu. Tento kurz předpokládá, že `image.jpg`bitová kopie je pojmenována .

1. Klepněte pravým tlačítkem myši na obraz a pak vyberte **příkaz Vlastnosti**. Ujistěte se, že **je možnost Kopírovat do výstupního adresáře** nastavena na kopírovat **vždy**.

    ![Zobrazit, kde se má aktualizovat vlastnost obrázku pro kopírovat do výstupního adresáře](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Do **souboru Program.cs** přidejte do horní části souboru následující příkazy:

    ```csharp
    using System.IO;
    ```

1. Do třídy **Program** přidejte následující metodu:

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

    Metoda `UploadToBlobAsync` přebírá název souboru a zdroj datového proudu souboru, který má být odeslán, a zpracovává nahrávání do úložiště. Aplikace konzoly zobrazuje čas potřebný k nahrání souboru.

1. Přidejte následující řádek v **Main** `Console.ReadLine()`metoda, těsně před :

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Z důvodu jednoduchosti tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování, jako je exponenciální backoff, jak je navrženo v [přechodové zpracování chyb](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro příjem oznámení o nahrání souborů z centra IoT, které jste vytvořili v [aplikaci Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-dotnet.md). Chcete-li přijímat oznámení o nahrání souboru, vaše služba potřebuje oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Přijetí oznámení o nahrání souboru

V této části napíšete aplikaci konzoly .NET, která přijímá oznámení o nahrání souborů z centra IoT Hub.

1. V aktuálním řešení Sady Visual Vyberte **soubor** > **nový** > **projekt**. V **okně Vytvořit nový projekt**vyberte Console App **(.NET Framework)** a pak vyberte **Další**.

1. Pojmenujte projekt *ReadFileUploadNotification*. V části **Řešení**vyberte **Přidat do řešení**. Vyberte **Vytvořit** a vytvořte projekt.

    ![Konfigurace projektu ReadFileUploadNotification v sadě Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **ReadFileUploadNotification** a vyberte **spravovat balíčky NuGet**.

1. Ve **Správci balíčků NuGet**vyberte **procházet**. Vyhledejte a vyberte **Microsoft.Azure.Devices**a pak vyberte **Instalovat**.

    Tento krok stáhne, nainstaluje a přidá odkaz na [balíček Služby Azure IoT SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/) v projektu **ReadFileUploadNotification.**

1. Do **souboru Program.cs** pro tento projekt přidejte v horní části souboru následující příkaz:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte `{iot hub connection string}` zástupnou hodnotu připojovacím řetězcem služby IoT hub, který jste dříve zkopírovali v [připojovacím řetězci Služby IoT hub](#get-the-iot-hub-connection-string):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Do třídy **Program** přidejte následující metodu:

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

    Všimněte si, že tento vzor příjmu je stejný jako ten, který se používá k přijímání zpráv typu cloud zařízení z aplikace zařízení.

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

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na vaše řešení a vyberte **Nastavit počáteční projekty**.

1. V **projektu spuštění běžných vlastností** > **Startup Project**vyberte možnost Více projektů **po spuštění**a vyberte akci **Spustit** pro **readfileuploadnotification** a **simulované zařízení**. Výběrem **OK** uložte změny.

1. Stiskněte **klávesu F5**. Obě aplikace by se měly spustit. Odeslání by se mělo zobrazit v jedné konzolové aplikaci a v oznámení o nahrání přijaté druhou konzolovou aplikací. Pomocí [portálu Azure nebo](https://portal.azure.com/) Průzkumníka serveru Visual Studio můžete zkontrolovat přítomnost nahraného souboru v účtu Azure Storage.

    ![Snímek obrazovky s výstupní obrazovkou](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat možnosti nahrávání souborů v centru IoT Hub ke zjednodušení odesílání souborů ze zařízení. Můžete pokračovat v prozkoumání funkcí a scénářů ioT hubu v následujících článcích:

* [Programové vytvoření centra IoT hub](iot-hub-rm-template-powershell.md)

* [Úvod do sady C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

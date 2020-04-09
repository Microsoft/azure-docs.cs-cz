---
title: Kurz zabezpečení X.509 v Azure IoT Hub | Dokumenty společnosti Microsoft
description: Začínáme se zabezpečením založeným na X.509 ve vašem azure iot hubu v simulovaném prostředí.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: a22808b1d7ab2b2451f50470e8da3770d07407a5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985656"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Nastavení zabezpečení X.509 ve službě Azure IoT Hub

Tento kurz ukazuje kroky, které potřebujete k zabezpečení služby Azure IoT hub pomocí *ověřování certifikátu X.509*. Pro ilustraci používáme open-source nástroj OpenSSL k lokálnímu vytváření certifikátů na vašem počítači se systémem Windows. Doporučujeme použít tento kurz pouze pro testovací účely. V produkčním prostředí byste měli certifikáty zakoupit od *kořenové certifikační autority .*

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje, abyste měli připraveni následující prostředky:

* Vytvořili jste službu IoT hub s předplatným Azure. Podrobné kroky najdete [v tématu Vytvoření centra IoT prostřednictvím portálu.](iot-hub-create-through-portal.md)

* Máte [nainstalovanou Visual Studio 2017 nebo Visual Studio 2019.](https://www.visualstudio.com/vs/)

## <a name="get-x509-ca-certificates"></a>Získání certifikátů certifikační autority X.509

Zabezpečení založené na certifikátech X.509 v centru IoT Hub vyžaduje, abyste začali s [řetězem certifikátů X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), který obsahuje kořenový certifikát a všechny zprostředkující certifikáty až do listu.

Certifikáty můžete získat libovolným z následujících způsobů:

* Nákup certifikátů X.509 od *kořenové certifikační autority (CA)*. Tato metoda se doporučuje pro produkční prostředí.

* Vytvořte si vlastní certifikáty X.509 pomocí nástroje jiného výrobce, jako je [OpenSSL](https://www.openssl.org/). Tato technika je v pořádku pro testovací a vývojové účely. Informace o generování testovacích certifikátů certifikační autority pomocí prostředí PowerShell nebo Bash naleznete [v tématu Správa testovacích certifikátů certifikační autority.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) Zbytek tohoto kurzu používá testovací certifikáty certifikační autority generované podle pokynů v [části Správa testovacích certifikátů certifikační autority pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Vygenerujte [zprostředkující certifikát certifikační autority X.509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) podepsaný existujícím kořenovým certifikátem certifikační autority a nahrajte jej do centra. Jakmile je zprostředkující certifikát nahrán a ověřen podle pokynů níže, může být použit na místo níže uvedeného kořenového certifikátu certifikační autority. Nástroje jako OpenSSL[(openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) a [openssl ca)](https://www.openssl.org/docs/man1.1.0/man1/ca.html)lze použít ke generování a podepsání zprostředkujícího certifikátu CA.

> [!NOTE]
> Nenahrávejte kořen třetí strany, pokud není jedinečný pro vás, protože to by umožnilo ostatním zákazníkům třetí strany připojit svá zařízení k vašemu centru IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrace certifikátů Certifikační autority X.509 do centra IoT hub

Tyto kroky ukazují, jak přidat novou certifikační autoritu do centra IoT prostřednictvím portálu.

1. Na webu Azure Portal přejděte do centra IoT hub a vyberte **Nastavení** > **certifikátů** pro centrum.

1. Výběrem **možnosti Přidat** přidáte nový certifikát.

1. Do **pole Název certifikátu**zadejte popisný zobrazovaný název a vyberte soubor certifikátu, který jste vytvořili v předchozí části z počítače.

1. Jakmile dostanete oznámení, že je certifikát úspěšně odeslán, vyberte **Uložit**.

    ![Nahrání certifikátu](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Certifikát se zobrazí v seznamu certifikátů se stavem **Neověřeno**.

1. Vyberte certifikát, který jste právě přidali, a **zobrazte podrobnosti o certifikátu**a pak vyberte **generovat ověřovací kód**.

   ![Ověření certifikátu](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Zkopírujte **ověřovací kód** do schránky. Slouží k ověření vlastnictví certifikátu.

1. Postupujte podle kroku 3 při [správě testovacích certifikátů certifikační autority pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Tento proces podepíše ověřovací kód pomocí soukromého klíče přidruženého k certifikátu certifikační autority X.509, který vygeneruje podpis. K dispozici jsou nástroje pro provedení tohoto procesu podepisování, například OpenSSL. Tento proces je známý jako [důkaz vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1).

1. V **části Podrobnosti o certifikátu**v části **Ověřovací certifikát .pem nebo soubor CER**vyhledejte a otevřete soubor podpisu. Pak vyberte **Ověřit**.

   Stav certifikátu se změní na **Ověřeno**. Pokud se certifikát neaktualizuje automaticky, vyberte **aktualizovat.**

## <a name="create-an-x509-device-for-your-iot-hub"></a>Vytvoření zařízení X.509 pro centrum IoT hub

1. Na webu Azure Portal přejděte do svého centra IoT hub a vyberte **Průzkumníky** > **zařízení IoT**.

1. Chcete-li přidat nové zařízení, vyberte **nový.**

1. Do **ID zařízení**zadejte popisný zobrazovaný název. V **popřípadě Typ ověřování**zvolte Příkaz podpis **x.509 podepsaný**certifikační autoritou a pak vyberte **Uložit**.

   ![Vytvoření zařízení X.509 na portálu](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Ověření zařízení X.509 pomocí certifikátů X.509

Chcete-li ověřit zařízení X.509, musíte zařízení nejprve podepsat certifikátem certifikační autority. Podepisování listových zařízení se obvykle provádí ve výrobním závodě, kde byly odpovídajícím způsobem povoleny výrobní nástroje. Vzhledem k tomu, že zařízení přejde od jednoho výrobce k druhému, podpisová akce každého výrobce je zachycena jako zprostředkující certifikát v rámci řetězce. Výsledkem je řetěz certifikátů z certifikátu certifikační autority na listový certifikát zařízení. Krok 4 v [části Správa testovacích certifikátů certifikační autority pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generuje certifikát zařízení.

Dále vám ukážeme, jak vytvořit aplikaci C#, která simuluje zařízení X.509 registrované pro váš iot hub. Do vašeho rozbočovače vám zašleme hodnoty teploty a vlhkosti ze simulovaného zařízení. V tomto kurzu vytvoříme pouze aplikaci zařízení. Je ponecháno jako cvičení pro čtenáře k vytvoření aplikace služby IoT Hub, která odešle odpověď na události odeslané tímto simulovaným zařízením. Aplikace C# předpokládá, že jste postupovali podle kroků v [části Správa testovacích certifikátů certifikační autority pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Otevřete Visual Studio, vyberte **Vytvořit nový projekt**a pak zvolte šablonu projektu **Konzola aplikace (.NET Framework).** Vyberte **další**.

1. V **části Konfigurace nového projektu**pojmenujte projekt *SimulateX509Device*a pak vyberte **Vytvořit**.

   ![Vytvoření projektu zařízení X.509 v sadě Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **SimulateX509Device** a pak vyberte **spravovat balíčky NuGet**.

1. Ve **Správci balíčků NuGet**vyberte **Procházet** a vyhledejte a zvolte **Microsoft.Azure.Devices.Client**. Vyberte **Install** (Nainstalovat).

   ![Přidání balíčku SDK NuGet sady Zařízení v sadě Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Tento krok stáhne, nainstaluje a přidá odkaz na balíček SDK NuGet zařízení Azure IoT a jeho závislosti.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Do třídy **Program** přidejte následující pole:

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Použijte popisný název zařízení, který jste použili v předchozí části místo _<your_device_id>_.

1. Přidejte následující funkci pro vytvoření náhodných čísel pro teplotu a vlhkost a odešlete tyto hodnoty do rozbočovače:

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Nakonec přidejte následující řádky kódu do **funkce Main** a nahraďte zástupné symboly _id zařízení_, název vašeho _centra_a absolutní cestu _k vašemu zařízení pfx-file_ podle požadavků nastavení.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Tento kód se připojí k centru IoT vytvořením připojovacího řetězce pro vaše zařízení X.509. Po úspěšném připojení pak odešle události teploty a vlhkosti do rozbočovače a čeká na svou odpověď.

1. Spusťte aplikaci. Vzhledem k tomu, že tato aplikace přistupuje k souboru *.pfx,* bude pravděpodobně nutné spustit tuto aplikaci jako správce.

   1. Vytvořte řešení sady Visual Studio.

   1. Otevřete nové okno příkazového řádku pomocí **příkazu Spustit jako správce**.  

   1. Přejděte do složky, která obsahuje vaše řešení, a potom přejděte na cestu *bin/ladění* ve složce řešení.

   1. Spusťte aplikaci **SimulateX509Device.exe** z příkazového řádku.

   Zařízení by se mělo úspěšně připojovat k rozbočovači a odesílat události.

   ![Spuštění aplikace zařízení](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení řešení IoT najdete v tématu:

* [Doporučené postupy zabezpečení IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Architektura zabezpečení IoT](../iot-fundamentals/iot-security-architecture.md)

* [Zabezpečení nasazení IoT](../iot-fundamentals/iot-security-deployment.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

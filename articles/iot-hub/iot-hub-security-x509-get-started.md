---
title: Kurz pro zabezpečení X. 509 v Azure IoT Hub | Microsoft Docs
description: Začněte využívat zabezpečení na základě X. 509 ve službě Azure IoT Hub v simulovaném prostředí.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: 8c9555bc208766d5275e392ee22889f5b618e216
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90018301"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Nastavení zabezpečení X.509 v Azure IoT Hubu

V tomto kurzu se dozvíte o krocích, které potřebujete k zabezpečení služby Azure IoT Hub pomocí *ověřování certifikátu X. 509*. Pro účely ilustrace používáme open source nástroj OpenSSL k vytváření certifikátů místně na vašem počítači s Windows. Doporučujeme použít tento kurz pouze pro účely testování. V produkčním prostředí byste měli koupit certifikáty od kořenové certifikační *autority (CA)*.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje, abyste měli připraveny tyto prostředky:

* Vytvořili jste službu IoT Hub s vaším předplatným Azure. Podrobné pokyny najdete v tématu [vytvoření centra IoT prostřednictvím portálu](iot-hub-create-through-portal.md) .

* Máte nainstalovanou [aplikaci Visual studio 2017 nebo Visual studio 2019](https://www.visualstudio.com/vs/) .

## <a name="get-x509-ca-certificates"></a>Získání certifikátů certifikační autority X. 509

Zabezpečení založené na certifikátech X. 509 v IoT Hub vyžaduje, abyste začali s [řetězem certifikátů x. 509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), který zahrnuje kořenový certifikát i všechny zprostředkující certifikáty až po listový certifikát.

Pro získání certifikátů můžete zvolit některý z následujících způsobů:

* Zakupte certifikáty X. 509 od *kořenové certifikační autority (CA)*. Tato metoda se doporučuje pro produkční prostředí.

* Vytvářejte vlastní certifikáty X. 509 pomocí nástroje třetí strany, jako je například [OpenSSL](https://www.openssl.org/). Tato technika je pro účely testování a vývoje vhodná. V tématu [Správa certifikátů testovací CA najdete ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) pro informace o generování certifikátů TESTOVACÍch certifikačních autorit pomocí PowerShellu nebo bash. Zbytek tohoto kurzu používá certifikáty testovací CA vygenerované podle pokynů v tématu [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Vygenerujte [certifikát zprostředkující certifikační autority X. 509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) podepsaný stávajícím certifikátem kořenové certifikační autority a nahrajte ho do centra. Po nahrání a ověření zprostředkujícího certifikátu, jak je uvedeno níže, se dá použít místo certifikátu kořenové certifikační autority uvedeného níže. K vygenerování a podepsání zprostředkujícího certifikátu certifikační autority se dají použít nástroje jako OpenSSL ([OpenSSL REQ](https://www.openssl.org/docs/man1.1.0/man1/req.html) a [OpenSSL CA](https://www.openssl.org/docs/man1.1.0/man1/ca.html)).

> [!NOTE]
> Nenahrávat kořen třetí strany, pokud pro vás není jedinečný, protože by umožnil jiným zákazníkům třetí strany připojit svá zařízení k vašemu IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrace certifikátů certifikační autority X. 509 do služby IoT Hub

Tyto kroky ukazují, jak přidat novou certifikační autoritu do služby IoT Hub prostřednictvím portálu.

> [!NOTE]
> Maximální počet certifikátů certifikační autority X. 509, které mohou být zaregistrovány ve službě IoT Hub, je 25. Další informace najdete v tématu [kvóty a omezení pro Azure IoT Hub](iot-hub-devguide-quotas-throttling.md).

1. V Azure Portal přejděte do svého centra IoT a vyberte **Nastavení**  >  **certifikáty** pro centrum.

1. Vyberte **Přidat** a přidejte nový certifikát.

1. Do pole **název certifikátu**zadejte popisný zobrazovaný název a vyberte soubor certifikátu, který jste vytvořili v předchozí části počítače.

1. Jakmile dostanete oznámení, že se váš certifikát úspěšně nahrál, vyberte **Uložit**.

    ![Nahrání certifikátu](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Váš certifikát se zobrazí v seznamu certifikáty se stavem **Neověřeno**.

1. Vyberte certifikát, který jste právě přidali k zobrazení **podrobností o certifikátu**, a pak vyberte **Generovat ověřovací kód**.

   ![Ověřit certifikát](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Zkopírujte **ověřovací kód** do schránky. Použijete ji k ověření vlastnictví certifikátu.

1. Postupujte podle kroků 3 v částech [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Tento proces podepíše ověřovací kód s privátním klíčem přidruženým k certifikátu CA X. 509, který generuje podpis. K dispozici jsou nástroje pro provádění tohoto procesu podepisování, například OpenSSL. Tento proces se označuje jako [důkaz o vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1).

1. V části **Podrobnosti o certifikátu**v části **soubor ověřovacího certifikátu. pem nebo. cer**Najděte a otevřete soubor signatury. Pak vyberte **ověřit**.

   Stav certifikátu se změní na **ověřený**. Pokud se certifikát automaticky neaktualizuje, vyberte **aktualizovat** .

## <a name="create-an-x509-device-for-your-iot-hub"></a>Vytvoření zařízení X. 509 pro Centrum IoT

1. V Azure Portal přejděte do svého centra IoT a pak vyberte **Průzkumník**  >  **zařízení IoT**.

1. Pokud chcete přidat nové zařízení, vyberte **Nový** .

1. Do **ID zařízení**zadejte popisný název zobrazení. Jako **typ ověřování**zvolte **podepsaná certifikační autorita X. 509**a pak vyberte **Uložit**.

   ![Vytvoření zařízení X. 509 na portálu](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Ověření zařízení X. 509 pomocí certifikátů X. 509

Abyste mohli ověřit zařízení X. 509, musíte nejdřív podepsat zařízení certifikátem certifikační autority. Přihlášení na listových zařízeních se obvykle provádí v výrobním závodě, kde jsou odpovídajícím způsobem povoleny výrobní nástroje. Jak zařízení přechází z jednoho výrobce do jiného, je akce podepisování každého výrobce zachycena jako zprostředkující certifikát v rámci řetězce. Výsledkem je řetěz certifikátů z certifikátu certifikační autority k certifikátu na listech zařízení. Krok 4 ve [správě certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generují certifikát zařízení.

V dalším kroku vám ukážeme, jak vytvořit aplikaci v jazyce C#, která simuluje zařízení X. 509 zaregistrované ve službě IoT Hub. Hodnoty teploty a vlhkosti se z simulovaného zařízení pošle do vašeho středu. V tomto kurzu vytvoříme jenom aplikaci zařízení. K vytvoření aplikace IoT Hub služby, která odešle odpověď na události odesílané simulovaným zařízením, je ponecháno cvičení pro čtenáře. Aplikace jazyka C# předpokládá, že jste postupovali podle kroků v části [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Otevřete Visual Studio, vyberte **vytvořit nový projekt**a pak zvolte šablonu projektu **Konzolová aplikace (.NET Framework)** . Vyberte **Další**.

1. V části **Konfigurovat nový projekt**pojmenujte projekt *SimulateX509Device*a pak vyberte **vytvořit**.

   ![Vytvoření projektu zařízení X. 509 v aplikaci Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **SimulateX509Device** a pak vyberte **Spravovat balíčky NuGet**.

1. V okně **Správce balíčků NuGet**vyberte **Procházet** a vyhledejte a vyberte **Microsoft. Azure. Devices. Client**. Vyberte **Nainstalovat**.

   ![Přidat balíček NuGet sady SDK pro zařízení v sadě Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Tento krok stáhne a nainstaluje balíček NuGet sady SDK pro zařízení Azure IoT a jeho závislosti a přidá se na něj odkaz.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Do třídy **program** přidejte následující pole:

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Místo _<your_device_id>_ použijte popisný název zařízení, které jste použili v předchozí části.

1. Přidejte následující funkci pro vytvoření náhodných čísel pro teplotu a vlhkost a odeslání těchto hodnot do centra:

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

1. Nakonec do funkce **Main** přidejte následující řádky kódu, kterým nahradíte zástupné symboly _ID zařízení_, _název-IoT-Hub-Name_a _absolutní cesta k souboru_ .

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

   Tento kód se připojí ke službě IoT Hub vytvořením připojovacího řetězce pro vaše zařízení X. 509. Po úspěšném připojení odešle do centra události teploty a vlhkosti a počká na jeho odpověď.

1. Spusťte aplikaci. Vzhledem k tomu, že tato aplikace přistupuje k souboru *. pfx* , možná budete muset tuto aplikaci spustit jako správce.

   1. Sestavte řešení sady Visual Studio.

   1. Otevřete nové okno příkazového řádku pomocí příkazu **Spustit jako správce**.  

   1. Přejděte do složky, která obsahuje vaše řešení, a pak přejděte na *přihrádku/cestu ladění* ve složce řešení.

   1. Spusťte aplikaci **SimulateX509Device.exe** z příkazového řádku.

   Měli byste vidět, že vaše zařízení se úspěšně připojilo k centru a odesílá události.

   ![Spustit aplikaci pro zařízení](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení řešení IoT najdete v těchto tématech:

* [Osvědčené postupy zabezpečení IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Architektura zabezpečení IoT](../iot-fundamentals/iot-security-architecture.md)

* [Zabezpečení nasazení IoT](../iot-fundamentals/iot-security-deployment.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

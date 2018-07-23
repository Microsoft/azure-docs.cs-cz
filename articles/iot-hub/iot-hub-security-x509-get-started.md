---
title: Kurz pro X.509 zabezpečení ve službě Azure IoT Hub | Dokumentace Microsoftu
description: Začít používat založená na X.509 zabezpečení ve službě Azure IoT hub v simulovaném prostředí.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 61aefe70b7091f666dbb40bec6a4f25437f489d3
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186930"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Nastavit X.509 zabezpečení ve službě Azure IoT hub

Tento kurz simuluje kroky nutné k zabezpečení pomocí Azure IoT hub *ověřování pomocí certifikátu X.509*. Pro účely ilustrace vám ukážeme, jak vytvářet certifikáty místně na svém počítači Windows pomocí open source nástroj OpenSSL. Doporučujeme použít tento kurz pouze pro účely testování. Pro produkční prostředí, byste si zakoupit certifikáty vydané *kořenové certifikační autority (CA)*. 

## <a name="prerequisites"></a>Požadavky
Tento kurz vyžaduje, že máte připraveno následující prostředky:

- Vytvořili jste službu IoT hub ve vašem předplatném Azure. Zobrazit [vytvoření služby IoT hub přes portál](iot-hub-create-through-portal.md) podrobné pokyny. 
- Máte [Visual Studio 2015 nebo Visual Studio 2017](https://www.visualstudio.com/vs/) na vašem počítači nainstalovaný. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>Získání certifikátů webu X.509
Zabezpečení pomocí certifikátů X.509 ve službě IoT Hub je nutné začít s [řetěz certifikátů X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), který obsahuje kořenový certifikát, stejně jako všechny zprostředkující certifikáty až listový certifikát. 

Můžete zvolit jednu z následujících způsobů, jak získat certifikáty:
- Zakoupení certifikátů X.509 z *kořenové certifikační autority (CA)*. To se doporučuje pro produkční prostředí.
NEBO,
- Vytvořit vlastní certifikáty X.509 pomocí nástroje třetích stran, jako [OpenSSL](https://www.openssl.org/). To bude bez problémů pro vývoj a testování účely. Zobrazit [Správa testu certifikační Autority certifikáty pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) informace o generování testovací certifikáty certifikační Autority pomocí prostředí PowerShell nebo Bash. Zbývající části tohoto kurzu používá testovací certifikační Autority certifikáty generované infrastrukturou podle pokynů v [Správa testu certifikační Autority certifikáty pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Zaregistrovat certifikáty webu X.509 do služby IoT hub

Tyto kroky ukazují, jak přidat nové certifikační autority do služby IoT hub pomocí portálu.

1. Na webu Azure Portal, přejděte do služby IoT hub a otevřete **nastavení** > **certifikáty** nabídky. 
2. Klikněte na tlačítko **přidat** přidat nový certifikát.
3. Zadejte popisný Zobrazovaný název vašeho certifikátu. Vyberte soubor kořenového certifikátu s názvem *RootCA.cer* vytvořili v předchozí části, z vašeho počítače. Klikněte na **Odeslat**.
4. Jakmile obdržíte oznámení, že váš certifikát se úspěšně nahraje, klikněte na tlačítko **Uložit**.

    ![Nahrání certifikátu](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Tím se zobrazí váš certifikát v **Průzkumník certifikátů** seznamu. Poznámka: **stav** tento certifikát je *neověřené*.

5. Klikněte na certifikát, který jste přidali v předchozím kroku.

6. V **podrobnosti o certifikátu** okna, klikněte na tlačítko **vygenerovat ověřovací kód**.

7. Vytvoří **ověřovací kód** k ověření vlastnictví certifikátu. Zkopírujte kód do schránky. 

   ![Ověření certifikátu](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Teď, budete muset přihlásit to *ověřovací kód* s privátní klíč spojený s vaší certifikátu webu X.509, který generuje podpis. Nejsou k dispozici pro tento proces podepisování, například OpenSSL provést nástroje. To se označuje jako [doklad o vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1). Krok 3 v [Správa testu certifikační Autority certifikáty pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generuje kód pro ověření.
 
9. Nahrajte výsledný podpis z kroku 8 do služby IoT hub v portálu. V **podrobnosti o certifikátu** okna na webu Azure portal, přejděte **soubor .pem nebo .cer ověřovacího certifikátu**a vyberte požadovaný podpis, například *VerifyCert4.cer*vytvořené pomocí příkazu Powershellu ukázkový _Průzkumníka souborů_ ikonu kromě ho.

10. Po úspěšném nahrání certifikátu klikněte na tlačítko **ověřte**. **Stav** změn certifikát **_ověřeno_** v **certifikáty** okno. Klikněte na tlačítko **aktualizovat** Pokud neaktualizuje automaticky.

   ![Nahrajte certifikát ověření](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Vytvoření zařízení X.509 pro službu IoT hub

1. Na webu Azure Portal, přejděte do služby IoT hub **Device Explorer**.

2. Klikněte na tlačítko **přidat** přidat nové zařízení. 

3. Zadejte popisný Zobrazovaný název pro **ID zařízení**a vyberte **_X.509 podepsaný certifikační Autority_** jako **typ ověřování**. Klikněte na **Uložit**.

   ![Vytvoření zařízení X.509 na portálu](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Ověření zařízení X.509 pomocí certifikátů X.509

Pokud chcete ověřit vaše zařízení X.509, budete muset nejprve podepište zařízení pomocí certifikátu certifikační Autority. Podepisování zařízení typu list se obvykle provádí ve výrobních závodů, kde výrobní nástroje byly povoleny odpovídajícím způsobem. Jak zařízení přejde od jednoho výrobce do druhého, zaznamená v podobě zprostředkující certifikát v řetězu podpisový akce jednotlivých výrobců. Konečný výsledek je řetěz certifikátů z certifikační Autority certifikátu listový certifikát zařízení. Krok 4 v [Správa testu certifikační Autority certifikáty pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) vytvoří i certifikát zařízení.

V dalším kroku vám ukážeme, jak vytvořit aplikaci v C# simulovat zařízení X.509 pro službu IoT hub. Pošleme hodnoty teploty a vlhkosti ze simulovaného zařízení k centru. Všimněte si, že v tomto kurzu vytvoříme pouze aplikace, zařízení. Jako cvičení je ponecháno na čtenáři k vytvoření aplikace služby IoT Hub, který odešle odpověď na události odeslané toto simulované zařízení. Aplikace v C# se předpokládá, že jste udělali kroky v [Správa testu certifikační Autority certifikáty pro ukázky a kurzy](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. V sadě Visual Studio vytvořte nový Visual C# Windows klasický desktopový projekt pomocí šablony projektu konzolové aplikace. Pojmenujte projekt **SimulateX509Device**.
   ![Vytvoření projektu zařízení X.509 v sadě Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulateX509Device** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** . V okně Správce balíčků NuGet vyberte **Procházet** a vyhledejte **microsoft.azure.devices.client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na balíček NuGet sady SDK pro zařízení Azure IoT a jeho závislosti.
   ![Přidejte balíček NuGet sady SDK pro zařízení v sadě Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Přidejte následující řádky kódu v horní části *Program.cs* souboru:
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Přidejte následující řádky kódu uvnitř **Program** třídy:
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   Použití zařízení popisný název jste použili v předchozí části místo _< your_device_id >_ zástupný symbol.

5. Přidejte následující funkci, která vytvořit náhodných čísel pro teploty a vlhkosti a tyto hodnoty odešle do centra:
    ```CSharp
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

6. Nakonec přidejte následující řádky kódu **hlavní** funkce zástupné texty _id zařízení_, _your-iot-hub-name_ a  _Absolute-path-to-your-Device-PFX-File_ podle požadavků vašeho nastavení.
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "123");
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
   Tento kód se připojí ke službě IoT hub vytvořením připojovacím řetězcem pro vaše zařízení X.509. Po úspěšném připojení, potom odešle do centra události teploty a vlhkosti a čeká na odpověď. 
7. Protože má přístup k této aplikaci *.pfx* souboru, je nutné spustit v *správce* režimu. Sestavte řešení sady Visual Studio. Otevřete nové okno příkazového řádku jako **správce**a přejděte do složky obsahující tato řešení. Přejděte *bin/Debug* cestu ve složce řešení. Spusťte aplikaci **SimulateX509Device.exe** z _správce_ příkazové okno. Měli byste vidět vaše zařízení úspěšně připojení k centru a odesílá události. 
   ![Spuštění aplikace pro zařízení](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o zabezpečení řešení IoT najdete v tématu:

* [Osvědčené postupy zabezpečení IoT][lnk-security-best-practices]
* [Architektura zabezpečení IoT][lnk-security-architecture]
* [Zabezpečení nasazení IoT][lnk-security-deployment]

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

[lnk-security-best-practices]: ../iot-fundamentals/iot-security-best-practices.md
[lnk-security-architecture]: ../iot-fundamentals/iot-security-architecture.md
[lnk-security-deployment]: ../iot-fundamentals/iot-security-deployment.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

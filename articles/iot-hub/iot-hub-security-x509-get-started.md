---
title: Kurz pro X.509 zabezpečení ve službě Azure IoT Hub | Microsoft Docs
description: Začněte na základě X.509 zabezpečení ve službě Azure IoT hub v simulovaném prostředí.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: dd5b9f196f911011d9dd606d46f515b88d338531
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635572"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Nastavit X.509 zabezpečení ve službě Azure IoT hub

V tomto kurzu simuluje kroky nutné k zabezpečení pomocí Azure IoT hub *ověřování pomocí certifikátu X.509*. Pro účely obrázku ukážeme, jak používat nástroj open source OpenSSL k vytvoření certifikátů, místně na počítači se systémem Windows. Doporučujeme používat pouze pro účely tohoto kurzu. Pro produkční prostředí by měl koupíte certifikáty z *kořenová certifikační autorita (CA)*. 

## <a name="prerequisites"></a>Požadavky
Tento kurz vyžaduje, aby byl připraven následující prostředky:

- Vytvoření služby IoT hub s vašeho předplatného Azure. V tématu [vytvoření služby IoT hub prostřednictvím portálu](iot-hub-create-through-portal.md) podrobné pokyny. 
- Máte [Visual Studio 2015 nebo Visual Studio 2017](https://www.visualstudio.com/vs/) nainstalovaný na počítači. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>Získat certifikáty X.509 certifikační Autority
Zabezpečení na základě certifikátu X.509 ve službě IoT Hub, musíte spustit s [řetěz certifikátů X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), což zahrnuje kořenový certifikát a také všechny zprostředkující certifikáty, až se certifikát typu list. 

Můžete zvolit jednu z následujících způsobů získat certifikáty:
- Nákup certifikátů X.509 z *kořenová certifikační autorita (CA)*. Toto nastavení se doporučuje pro provozní prostředí.
NEBO,
- Vytvářet vlastní certifikáty X.509 pomocí nástroje třetích stran, jako třeba [OpenSSL](https://www.openssl.org/). To bude vhodná pro testovací a vývojové účely. Části s názvem *vytvořit certifikáty X.509* a *řetěz certifikátů X.509 vytvořit* v článku [jak pomocí prostředí PowerShell k vytvoření certifikátů X.509](iot-hub-security-x509-create-certificates.md) vás prostřednictvím ukázkový skript PowerShell k vytvoření certifikátů pomocí OpenSSL a prostředí PowerShell. Pokud byste radši chtěli použít **Bash** prostředí shell místo prostředí PowerShell najdete v částech související [Správa ukázka certifikáty certifikační Autority](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). Zbytek tohoto kurzu použije nastavení v tomto prostředí OpenSSL *postup* Průvodce vás provede procesem začátku do konce X.509 zabezpečení v Azure IoT Hub.


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Zaregistrovat certifikáty X.509 certifikační Autority do služby IoT hub

Tyto kroky ukazují, jak přidat nové certifikační autority do služby IoT hub pomocí portálu.

1. Na portálu Azure přejděte do služby IoT hub a otevřete **nastavení** > **certifikáty** nabídky. 
2. Klikněte na tlačítko **přidat** přidat nový certifikát.
3. Zadejte popisný Zobrazovaný název pro svůj certifikát. Vyberte soubor kořenového certifikátu s názvem *RootCA.cer* vytvořili v předchozí části, z počítače. Klikněte na **Odeslat**.
4. Jakmile se zobrazí oznámení, že je váš certifikát úspěšně nahrána, klikněte na možnost **Uložit**.

    ![Nahrání certifikátu](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Zobrazí certifikát v **certifikát Explorer** seznamu. Poznámka: **stav** tento certifikát je *Unverified*.

5. Klikněte na certifikát, který jste přidali v předchozím kroku.

6. V **podrobnosti certifikátu** okně klikněte na tlačítko **generovat ověřovací kód**.

7. Vytvoří **ověřovací kód** certifikát vlastnictví můžete ověřit. Zkopírujte kód do schránky. 

   ![Ověřit certifikát](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Nyní, budete muset přihlásit to *ověřovací kód* s privátní klíč spojený s váš certifikát certifikační Autority X.509, který generuje podpis. Nejsou k dispozici pro tento proces podepisování, například OpenSSL provést nástroje. To se označuje jako [důkaz vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1). Pokud jste použili naše ukázkové skripty prostředí PowerShell v předchozí části, spusťte skript uvedený v části s názvem [ověření u sebe váš certifikát certifikační Autority X.509](iot-hub-security-x509-create-certificates.md#signverificationcode).
 
9. Nahrajte výsledný podpis z kroku 8 do služby IoT hub v portálu. V **podrobnosti certifikátu** okno na portálu Azure přejděte do **soubor .pem nebo .cer ověření certifikátu**a vyberte podpis, například *VerifyCert4.cer*vytvořené pomocí příkazu prostředí PowerShell ukázkový _Průzkumníka souborů_ ikonu kromě ho.

10. Jakmile je úspěšně nahrán certifikát, klikněte na možnost **ověřte**. **Stav** vaše změny certifikátu **_ověřeno_** v **certifikáty** okno. Klikněte na tlačítko **aktualizovat** Pokud neaktualizuje automaticky.

   ![Nahrajte certifikát ověření](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Vytvoření služby IoT hub zařízení s X.509

1. Na portálu Azure přejděte do služby IoT hub **Explorer zařízení**.

2. Klikněte na tlačítko **přidat** pro přidání nových zařízení. 

3. Zadejte popisný Zobrazovaný název pro **ID zařízení**a vyberte **_X.509 certifikační Autority podepsané_** jako **typ ověřování**. Klikněte na **Uložit**.

   ![Vytvoření X.509 zařízení na portálu](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Ověření X.509 zařízení s certifikáty X.509

K ověření zařízení X.509, musíte nejdřív přihlásit zařízení v certifikátu certifikační Autority. Podepisování listu zařízení se obvykle provádí na výrobních závodů, kde výrobní nástroje byly povoleny odpovídajícím způsobem. Jako zařízení přejde do jiného od jednoho výrobce, každého výrobce podpisový akce se zaznamená jako zprostředkující certifikát v řetězu. Konečný výsledek je řetěz certifikátů z certifikátu certifikační Autority do listu certifikátu zařízení. Pokud používáte naše skriptů prostředí PowerShell v předchozích částech a potom můžete spustit skript uvedený v části s názvem *vytvořit certifikát X.509 listu pro vaše zařízení* v článku [skriptů prostředí PowerShell pro spravovat certifikáty certifikační Autority podepsané X.509](iot-hub-security-x509-create-certificates.md) k simulaci tohoto procesu.

V dalším kroku jsme vám ukáže, jak vytvořit aplikaci C# k simulaci X.509 zařízení zaregistrovat pro službu IoT hub. Zašleme teploty a vlhkosti hodnoty do vašeho centra ze simulovaného zařízení. Všimněte si, že v tomto kurzu vytvoříme pouze aplikaci zařízení. Zůstává jako cvičení k uživatelům vytvořit aplikaci služby IoT Hub, který bude odesílat reakci na události poslal toto simulované zařízení. Aplikace C# se předpokládá, že jste provedli skriptů prostředí PowerShell uvedených v článku [skriptů prostředí PowerShell ke správě certifikační Autority podepsané X.509 – certifikáty](iot-hub-security-x509-create-certificates.md)

1. V sadě Visual Studio vytvořte nový projekt Visual C# Windows klasický desktopový pomocí šablony projektu konzolové aplikace. Název projektu **SimulateX509Device**.
   ![Vytvoření projektu X.509 zařízení v sadě Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. V Průzkumníku řešení klikněte pravým tlačítkem myši **SimulateX509Device** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet...** . V okně Správce balíčků NuGet vyberte **Procházet** a vyhledejte **microsoft.azure.devices.client**. Vyberte **nainstalovat** k instalaci **Microsoft.Azure.Devices.Client** balíček a přijměte podmínky použití. Tento postup stáhne, nainstaluje a přidá odkaz na balíček NuGet sady SDK pro zařízení Azure IoT a jeho závislosti.
   ![Přidejte balíček NuGet sady SDK pro zařízení v sadě Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Přidejte následující řádky kódu v horní části *Program.cs* souboru:
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Přidejte následující řádky kódu do **Program** třídy:
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   Použijte název popisný zařízení jste použili v předchozí části místě _< your_device_id >_ zástupný symbol.

5. Přidejte následující funkci k vytvoření náhodná čísla pro teploty a vlhkosti a tyto hodnoty odešle do centra:
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

6. Nakonec přidejte následující řádky kódu **hlavní** funkce, nahraďte zástupné symboly _id zařízení_, _vaše iot-hub-name_ a  _Absolute-path-to-your-Device-PFX-File_ podle požadavku vašeho nastavení.
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
   Tento kód se připojí ke službě IoT hub vytvořením připojovací řetězec pro vaše zařízení X.509. Po úspěšném připojení, potom odešle do centra událostí teploty a vlhkosti a čeká na odpověď. 
7. Vzhledem k tomu, že má přístup k této aplikaci *.pfx* souboru, je nutné provést v *správce* režimu. Sestavte řešení sady Visual Studio. Otevřete nové příkazové okno jako **správce**a přejděte do složky obsahující toto řešení. Přejděte na *bin/Debug* cestu ve složce řešení. Spusťte aplikaci **SimulateX509Device.exe** z _správce_ příkazové okno. Měli byste vidět zařízení úspěšně připojení k rozbočovači a odeslání události. 
   ![Spuštění aplikace pro zařízení](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o zabezpečení řešení IoT najdete v tématu:

* [Osvědčené postupy zabezpečení IoT][lnk-security-best-practices]
* [Architektura IoT zabezpečení][lnk-security-architecture]
* [Zabezpečit vaše nasazení IoT][lnk-security-deployment]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

[lnk-security-best-practices]: iot-hub-security-best-practices.md
[lnk-security-architecture]: iot-hub-security-architecture.md
[lnk-security-deployment]: iot-hub-security-deployment.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

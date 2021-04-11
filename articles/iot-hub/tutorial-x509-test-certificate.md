---
title: Kurz – testování možností certifikátů X. 509 k ověřování zařízení na Azure IoT Hub | Microsoft Docs
description: Kurz – testování vašich certifikátů X. 509 pro ověřování v Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 91eea344914120a396ba9465ec504a37f5844d4e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630657"
---
# <a name="tutorial-testing-certificate-authentication"></a>Kurz: testování ověřování certifikátů

Můžete použít následující příklad kódu C# k otestování, jestli váš certifikát může ověřit vaše zařízení s vaším IoT Hub. Všimněte si, že před spuštěním testovacího kódu je třeba provést následující:

* Vytvořte kořenovou certifikační autoritu nebo certifikát podřízené certifikační autority.
* Nahrajte do svého IoT Hub certifikát certifikační autority.
* Prokázání, že máte certifikát certifikační autority.
* Přidejte zařízení do svého IoT Hub.
* Vytvořte certifikát zařízení se stejným ID zařízení jako vaše zařízení.

## <a name="code-example"></a>Příklad kódu

Následující příklad kódu ukazuje, jak vytvořit aplikaci v jazyce C# pro simulaci zařízení X. 509 registrovaného pro Centrum IoT. Příklad odesílá hodnoty teploty a vlhkosti ze simulovaného zařízení do vašeho rozbočovače. V tomto kurzu vytvoříme jenom aplikaci zařízení. K vytvoření aplikace IoT Hub služby, která odešle odpovědi na události odesílané simulovaným zařízením, je ponecháno cvičení pro čtenáře.

1. Otevřete Visual Studio, vyberte **vytvořit nový projekt** a pak zvolte šablonu projektu **Konzolová aplikace (.NET Framework)** . Vyberte **Další**.

1. V části **Konfigurovat nový projekt** pojmenujte projekt *SimulateX509Device* a pak vyberte **vytvořit**.

   ![Vytvoření projektu zařízení X. 509 v aplikaci Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **SimulateX509Device** a pak vyberte **Spravovat balíčky NuGet**.

1. V okně **Správce balíčků NuGet** vyberte **Procházet** a vyhledejte a vyberte **Microsoft. Azure. Devices. Client**. Vyberte **Nainstalovat**.

   ![Přidat balíček NuGet sady SDK pro zařízení v sadě Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Tento krok stáhne a nainstaluje balíček NuGet sady SDK pro zařízení Azure IoT a jeho závislosti a přidá se na něj odkaz.

    Zadejte a spusťte následující kód:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

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
         }
    }
}
```
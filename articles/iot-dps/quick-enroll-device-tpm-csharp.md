---
title: 'Registrace zařízení TPM do služby Azure Device Provisioning Service pomocí C #'
description: Úvodní příručka – registrace zařízení TPM do služby Azure IoT Hub Device Provisioning Service (DPS) pomocí sady SDK služby C#. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: ee1b803459e0c81b86021b617a29e0b29ee19909
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976838"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Úvodní příručka: Registrace zařízení TPM do služby Zřizování zařízení služby IoT Hub pomocí sady C# service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Tento článek ukazuje, jak programově vytvořit individuální registraci pro zařízení TPM ve službě Azure IoT Hub Device Provisioning Service pomocí sady [C# Service SDK](https://github.com/Azure/azure-iot-sdk-csharp) a ukázkové aplikace C# .NET Core. Volitelně můžete zaregistrovat simulované zařízení TPM do zřizovací služby pomocí této jednotlivé položky registrace. Přestože tyto kroky fungují na počítačích se systémem Windows i Linux, tento článek používá vývojový počítač se systémem Windows.

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

1. Ověřte, zda máte v počítači nainstalovanou [Visual Studio 2019.](https://www.visualstudio.com/vs/)

1. Ověřte, zda máte v počítači nainstalovanou sadu [.NET Core SDK.](https://www.microsoft.com/net/download/windows)

1. Než budete pokračovat, proveďte kroky v [části Nastavení služby zřizování zařízení služby IoT Hub pomocí portálu Azure.](./quick-setup-auto-provision.md)

1. (Nepovinné) Pokud chcete zaregistrovat simulované zařízení na konci tohoto rychlého startu, postupujte podle postupu v [vytvořit a zřídit simulované zařízení TPM pomocí C# zařízení SDK](quick-create-simulated-device-tpm-csharp.md) až do kroku, kde získáte ověřovací klíč pro zařízení. Uložte ověřovací klíč, ID registrace a volitelně ID zařízení, protože je třeba použít později v tomto rychlém startu.

   > [!NOTE]
   > Nepostupujte podle pokynů k vytvoření individuální registrace pomocí portálu Azure.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Získání připojovacího řetězce pro službu zřizování

Pro ukázku v tomto rychlém startu potřebujete připojovací řetězec pro vaši službu zřizování.

1. Přihlaste se k portálu Azure, vyberte **Všechny prostředky**a pak službu zřizování zařízení.

1. Zvolte **Zásady sdíleného přístupu**a vyberte zásady přístupu, které chcete použít k otevření jeho vlastností. V **zásadách aplikace Access**zkopírujte a uložte připojovací řetězec primárního klíče.

    ![Získání připojovacího řetězce služby zřizování z portálu](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Vytvoření ukázky jednotlivé registrace

Tato část ukazuje, jak vytvořit konzolovou aplikaci .NET Core, která přidá individuální registraci pro zařízení TPM do vaší zřizovací služby. S určitými úpravami můžete pomocí tohoto postupu vytvořit pro přidání jednotlivé registrace také konzolovou aplikaci [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot). Další informace o vývoji pomocí ioT jádra najdete v [tématu Windows IoT Core developer dokumentace](https://docs.microsoft.com/windows/iot-core/).

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**. V **části Vytvoření nového projektu**zvolte šablonu projektu **Konzola Aplikace (.NET Core)** pro C# a vyberte **Další**.

1. Pojmenujte projekt *CreateTpmEnrollment*a stiskněte **klávesu Create**.

    ![Konfigurace projektu Visual C# Klasické plochy systému Windows](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Když se řešení otevře v sadě Visual Studio, klikněte v podokně **Průzkumník řešení** pravým tlačítkem myši na projekt **CreateTpmEnrollment.** Vyberte **spravovat nugetové balíčky**.

1. Ve **Správci balíčků NuGet**vyberte **Procházet**, vyhledejte a zvolte **Microsoft.Azure.Devices.Provisioning.Service**a stiskněte **tlačítko Instalovat**.

   ![Okno Správce balíčků NuGet](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Tento krok stáhne, nainstaluje a přidá odkaz na balíček [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet a jeho závislosti.

1. Za ostatní `using` `using` příkazy v horní části `Program.cs`přidejte následující příkazy :
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Přidejte do třídy `Program` následující pole a provázte změny uvedené níže.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * `ProvisioningServiceConnectionString` Nahraďte zástupnou hodnotu připojovacím řetězcem služby zřizování, pro kterou chcete vytvořit registraci.

   * Volitelně můžete změnit ID registrace, ověřovací klíč, ID zařízení i stav zřizování.

   * Pokud používáte tento rychlý start společně s [vytvořením a zřízením simulovaného zařízení TPM pomocí rychlého startu sady SDK zařízení C#](quick-create-simulated-device-tpm-csharp.md) pro zřízení simulovaného zařízení, nahraďte ověřovací klíč a ID registrace hodnotami, které jste si poznamenali v tomto rychlém startu. ID zařízení můžete nahradit hodnotou navrženou v tomto rychlém startu, použít vlastní hodnotu nebo použít výchozí hodnotu v této ukázce.

1. Přidejte do třídy následující metodu. `Program`  Tento kód vytvoří jednotlivé položky `CreateOrUpdateIndividualEnrollmentAsync` zápisu `ProvisioningServiceClient` a potom volá metodu na přidat jednotlivé registrace do zřizovací služby.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Nakonec nahraďte tělo `Main` metody následujícími řádky:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Sestavte řešení.

## <a name="run-the-individual-enrollment-sample"></a>Spuštění ukázky jednotlivé registrace
  
Spusťte ukázku v sadě Visual Studio, aby se vytvořila jednotlivá registrace pro vaše zařízení TPM.

Zobrazí se okno příkazového řádku, které se začne zobrazovat s potvrzovacími zprávami. Při úspěšném vytvoření se v okně příkazového řádku zobrazí vlastnosti nové individuální registrace.

Můžete ověřit, zda byla vytvořena jednotlivá registrace. Přejděte do souhrnu **služby**zřizování zařízení a vyberte Spravovat registrace a vyberte **Jednotlivé registrace**. Měla by se zobrazit nová položka registrace odpovídající ID registrace, které jste použili v ukázce.

![Vlastnosti registrace na portálu](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Vyberte položku, chcete-li ověřit klíč potvrzení a další vlastnosti položky.

Pokud jste byli podle kroků v [vytvoření a zřízení simulované zařízení TPM pomocí c# zařízení SDK](quick-create-simulated-device-tpm-csharp.md) rychlý start, můžete pokračovat se zbývající kroky v tomto rychlém startu zaregistrovat simulované zařízení. Nezapomeňte přeskočit kroky k vytvoření jednotlivé registrace pomocí webu Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu prozkoumat ukázku služby C#, nečistěte prostředky vytvořené v tomto rychlém startu. V opačném případě odstraňte všechny prostředky vytvořené tímto rychlým startem pomocí následujících kroků.

1. Zavřete ukázkové výstupní okno jazyka C# v počítači.

1. Přejděte na azure portálu do **služby**Device Provisioning, vyberte Spravovat registrace a pak vyberte kartu **Individuální registrace.** Zaškrtněte políčko vedle *ID registrace* pro položku registrace, kterou jste vytvořili pomocí tohoto rychlého startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

1. Pokud jste postupovali podle kroků v [části Vytvoření a zřízení simulovaného zařízení TPM pomocí sady SDK zařízení C#](quick-create-simulated-device-tpm-csharp.md) k vytvoření simulovaného zařízení TPM, proveďte následující kroky:

    1. Zavřete okno simulátoru TPM a okno výstupu ukázky pro simulované zařízení.

    1. Na webu Azure Portal přejděte do služby IoT Hub, ve které se zřídilo vaše zařízení. V nabídce **Explorers**zaškrtněte **políčko IoT ,** zaškrtněte políčko vedle *ID zařízení* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste programově vytvořili individuální položku registrace pro zařízení TPM. Volitelně jste v počítači vytvořili simulované zařízení TPM a zřídíte ho do vašeho centra IoT pomocí služby Azure IoT Hub Device Provisioning Service. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal.

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)

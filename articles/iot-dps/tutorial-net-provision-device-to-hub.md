---
title: Zřízení zařízení pomocí služby Azure IoT Hub Device Provisioning (.NET) | Microsoft Docs
description: Zřízení zařízení pro jedno centrum IoT pomocí služby Azure IoT Hub Device Provisioning (.NET)
author: wesmc7777
ms.author: wesmc
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 8602f020481249282756a952a46b32bd9e768372
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241315"
---
# <a name="enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Registrace zařízení do centra IoT pomocí klienta služby Azure IoT Hub Device Provisioning (.NET)

V předchozím kurzu jste se naučili nastavit zařízení pro připojení ke službě Device Provisioning. V tomto kurzu zjistíte, jak pomocí této služby zřídit zařízení pro jedno centrum IoT s využitím **_jednotlivé registrace_** i **_skupin registrací_**. V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, nezapomeňte nakonfigurovat své zařízení a *modul hardwarového zabezpečení*, jak je popsáno v kurzu [Nastavení zařízení pro zřízení pomocí služby Azure IoT Hub Device Provisioning](./tutorial-set-up-device.md).

* Visual Studio 2015 nebo Visual Studio 2017

> [!NOTE]
> Sada Visual Studio se nevyžaduje. Stačí nainstalovat [.NET](https://www.microsoft.com/net) a vývojáři můžou používat svůj upřednostňovaný editor ve Windows nebo v Linuxu.  

Tento kurz simuluje období během výrobního procesu hardwaru nebo těsně po něm, kdy se do zřizovací služby přidají informace o zařízení. Tento kód se obvykle spouští na počítači nebo továrním zařízení, které umožňuje spouštění kódu .NET, a neměl by se přidávat do samotných zařízení.


## <a name="enroll-the-device"></a>Registrace zařízení

Tento krok zahrnuje přidání jedinečných artefaktů zabezpečení zařízení do služby Device Provisioning. Tyto artefakty zabezpečení jsou následující:

- Zařízení založená na TPM:
    - *Ověřovací klíč*, který je jedinečný pro každý čip TPM nebo jeho simulaci. Další informace najdete v tématu [Vysvětlení ověřovacího klíče TPM](https://technet.microsoft.com/library/cc770443.aspx).
    - *ID registrace*, které slouží k jednoznačné identifikaci zařízení v oboru názvů nebo oboru. Toto ID může, ale nemusí být stejné jako ID zařízení. ID je povinné pro každé zařízení. U zařízení založených na TPM můžete ID registrace získat přímo z TMP, například jako hodnotu hash SHA-256 ověřovacího klíče TPM.

- Zařízení založená na X.509:
    - [Certifikát X.509 vydaný pro zařízení](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) ve formě souboru *.pem* nebo *.cer*. K jednotlivé registraci musíte použít *listový certifikát* pro váš systém X.509, zatímco pro skupiny registrací musíte použít *kořenový certifikát* nebo ekvivalentní *certifikát podpisovatele*.
    - *ID registrace*, které slouží k jednoznačné identifikaci zařízení v oboru názvů nebo oboru. Toto ID může, ale nemusí být stejné jako ID zařízení. ID je povinné pro každé zařízení. Pro zařízení založená na X.509 se ID registrace odvodí z běžného názvu certifikátu. Další informace o těchto požadavcích najdete v tématu [Koncepty zařízení](https://docs.microsoft.com/azure/iot-dps/concepts-device).

Zařízení můžete do služby Device Provisioning zaregistrovat dvěma způsoby:

- **Jednotlivé registrace** představují záznamy jednotlivých zařízení, která se můžou zaregistrovat do služby Device Provisioning. Jednotlivé registrace můžou jako mechanismus ověřování využívat certifikáty X.509 nebo tokeny SAS (na skutečném nebo virtuálním zařízení TPM). Jednotlivé registrace doporučujeme použít pro zařízení, která vyžadují jedinečnou počáteční konfiguraci, nebo zařízení, která jako mechanismus ověřování můžou využívat pouze tokeny SAS prostřednictvím zařízení TPM. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

- **Skupiny registrací** představují skupinu zařízení, která sdílí konkrétní mechanismus ověřování. Skupinu registrací doporučujeme použít pro velké množství zařízení, která sdílí požadovanou počáteční konfiguraci, nebo pro zařízení, která budou patřit do stejného tenanta. Skupiny registrací jsou určené pouze pro zařízení X.509 a všechna zařízení ve svém řetězu certifikátů X.509 sdílejí podpisový certifikát.

### <a name="enroll-the-device-using-individual-enrollments"></a>Registrace zařízení s využitím jednotlivých registrací

1. Pomocí šablony projektu **Konzolová aplikace** vytvořte v sadě Visual Studio projekt konzolové aplikace Visual C#. Pojmenujte projekt **DeviceProvisioning**.
    
1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **DeviceProvisioning** a pak klikněte na **Spravovat balíčky NuGet...**.

1. V okně **Správce balíčků NuGet** vyberte **Procházet** a vyhledejte **microsoft.azure.devices.provisioning.service**. Vyberte příslušnou položku, kliknutím na **Nainstalovat** nainstalujte balíček **Microsoft.Azure.Devices.Provisioning.Service** a přijměte podmínky použití. Tímto postupem se stáhne a nainstaluje balíček NuGet [sady SDK služby Azure IoT Device Provisioning](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) a jeho závislosti a přidá se na něj odkaz.

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu připojovacím řetězcem služby Device Provisioning, který jste si poznamenali v předchozí části.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Přidejte následující kód, kterým pro zařízení implementujete registraci:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Nakonec do metody **Main** přidejte následující kód, který otevře připojení k vašemu centru IoT a zahájí registraci:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem na své řešení a pak klikněte na **Nastavit projekty po spuštění...**. Vyberte **Jeden spouštěný projekt** a pak v rozevírací nabídce vyberte projekt **DeviceProvisioning**.  

1. Spusťte aplikaci .NET pro zařízení **DeviceProvisiong**. Pro zařízení by se mělo nastavit zřizování: 

    ![Spuštění jednotlivé registrace](./media/tutorial-net-provision-device-to-hub/individual.png)

Po úspěšné registraci by se zařízení mělo zobrazit na portálu, jak je znázorněno níže:

   ![Úspěšná registrace na portálu](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Registrace zařízení s využitím skupin registrací

> [!NOTE]
> Ukázka skupiny registrací vyžaduje certifikát X.509.

1. V Průzkumníku řešení sady Visual Studio otevřete projekt **DeviceProvisioning** vytvořený výše. 

1. Do horní části souboru **Program.cs** přidejte následující příkazy `using`:
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Do třídy **Program** přidejte následující pole. Nahraďte zástupnou hodnotu umístěním certifikátu X.509.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Do souboru **Program.cs** přidejte následující kód, kterým pro skupinu implementujete registraci:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Nakonec do metody **Main** přidejte následující kód, který otevře připojení k vašemu centru IoT a zahájí registraci skupiny:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Spusťte aplikaci .NET pro zařízení **DeviceProvisiong**. Pro zařízení by se mělo nastavit skupinové zřizování: 

    ![Spuštění skupinové registrace](./media/tutorial-net-provision-device-to-hub/group.png)

    Po úspěšné registraci by se skupina zařízení měla zobrazit na portálu, jak je znázorněno níže:

   ![Úspěšná registrace skupiny na portálu](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Spuštění zařízení

V tuto chvíli je následující nastavení připravené k registraci zařízení:

1. Vaše zařízení nebo skupina zařízení jsou zaregistrované do služby Device Provisioning. 
2. Vaše zařízení je připravené s nakonfigurovaným zabezpečením a přístupné přes aplikaci pomocí klientské sady SDK služby Device Provisioning.

Spusťte aplikaci, aby klientská aplikace mohla zahájit registraci do vaší služby Device Provisioning.  


## <a name="verify-the-device-is-registered"></a>Ověření registrace zařízení

Po spuštění zařízení by se měly provést následující akce. Další podrobnosti najdete v [ukázce zřizování klienta zařízení](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device). 

1. Zařízení odešle žádost o registraci do vaší služby Device Provisioning.
2. Pro zařízení TPM služba Device Provisioning odešle zpět výzvu k registraci, na kterou zařízení zareaguje. 
3. Po úspěšné registraci služba Device Provisioning odešle zpět do zařízení identifikátor URI centra IoT, ID zařízení a zašifrovaný klíč. 
4. Klientská aplikace IoT Hub na zařízení se pak připojí k vašemu centru. 
5. Po úspěšném připojení k centru by se zařízení mělo zobrazit v nástroji **Device Explorer** centra IoT. 

    ![Úspěšné připojení k centru na portálu](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

V dalším kurzu se dozvíte, jak zřídit několik zařízení napříč centry s vyrovnáváním zatížení. 

> [!div class="nextstepaction"]
> [Zřízení zařízení v několika centrech IoT s vyrovnáváním zatížení](./tutorial-provision-multiple-hubs.md)

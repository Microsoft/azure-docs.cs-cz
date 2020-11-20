---
title: 'Rychlý Start – registrace zařízení X. 509 do služby Azure Device Provisioning pomocí jazyka C #'
description: V tomto rychlém startu se používají skupinové registrace. V tomto rychlém startu zaregistrujete zařízení X. 509 do Azure IoT Hub Device Provisioning Service (DPS) pomocí jazyka C#.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 9fc34532818a742ef67e4b2532966874d083199d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959845"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Rychlý start: Registrace zařízení X.509 do služby Device Provisioning Service pomocí C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Tento rychlý start ukazuje, jak prostřednictvím kódu programu v C# vytvořit [skupinu registrací](concepts-service.md#enrollment-group), která používá zprostředkující nebo kořenové certifikáty X.509 certifikační autority. Skupina registrací se vytvoří pomocí [sady Microsoft Azure IoT SDK pro .NET](https://github.com/Azure/azure-iot-sdk-csharp) a ukázkové aplikace .NET Core v C#. Skupina registrací řídí přístup ke službě zřizování pro zařízení, která ve svém řetězu certifikátů sdílejí společný podpisový certifikát. Další informace najdete v tématu [Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Další informace o používání infrastruktury veřejných klíčů (PKI) založené na certifikátech X.509 se službami Azure IoT Hub a Device Provisioning najdete v tématu [Přehled zabezpečení pomocí certifikátu webu X.509](../iot-hub/iot-hub-x509ca-overview.md). 

V tomto rychlém startu se očekává, že už máte vytvořenou instanci služby IoT Hub a Device Provisioning. Pokud jste tyto prostředky ještě nevytvořili, dokončete [nastavení IoT Hub Device Provisioning Service s Azure Portal](./quick-setup-auto-provision.md) rychlý Start, než budete pokračovat v tomto článku.

I když postup v tomto článku funguje na počítačích se systémem Windows i Linux, Tento článek používá vývojový počítač s Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalujte sadu [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Nainstalujte [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Nainstalujte [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Příprava testovacích certifikátů

Pro účely tohoto rychlého startu potřebujete soubor .pem nebo .cer, který obsahuje veřejnou část zprostředkujícího nebo kořenového certifikátu X.509 certifikační autority. Certifikát musí být nahraný do vaší služby zřizování a ověřený touto službou.

[Sada Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) obsahuje testovací nástroje, které vám pomůžou vytvořit řetěz certifikátů X. 509, nahrát kořenový nebo zprostředkující certifikát z tohoto řetězu a ověřit certifikát tak, že ho dokončí služba.

> [!CAUTION]
> Používejte certifikáty vytvořené pomocí nástrojů sady SDK pouze pro vývojové testování.
> Nepoužívejte tyto certifikáty v produkčním prostředí.
> Obsahují pevně kódovaná hesla, například *1234*, jejichž platnost vyprší po 30 dnech.
> Informace o získání certifikátů vhodných pro použití v produkčním prostředí najdete v tématu věnovaném [získání certifikátu webu X.509](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) v dokumentaci ke službě Azure IoT Hub.
>

Chcete-li použít toto testovací nástroje pro generování certifikátů, proveďte následující kroky:

1. Vyhledejte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady Azure IoT C SDK.

2. Otevřete příkazový řádek nebo prostředí Git Bash a přejděte do pracovní složky na svém počítači. Spuštěním následujících příkazů naklonujte nejnovější verzi úložiště GitHub pro [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) . Použijte značku, kterou jste našli v předchozím kroku, jako hodnotu `-b` parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

   Testovací nástroje se nacházejí ve složce *azure-iot-sdk-c/tools/CACertificates* úložiště, které jste naklonovali.

3. Ukázky a kurzy najdete v článku [Správa testovacích certifikátů certifikační autority](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Kromě nástrojů v sadě C SDK obsahuje [Ukázka ověření certifikátu skupiny](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) v sadě *Microsoft Azure IoT SDK pro .NET* , jak provést kontrolu v jazyce C# s existujícím certifikátem X. 509 Intermediate nebo rootem CA.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Získání připojovacího řetězce pro službu zřizování

Pro ukázku v tomto rychlém startu potřebujete připojovací řetězec pro vaši službu zřizování.

1. Přihlaste se k Azure Portal, vyberte **všechny prostředky** a službu Device Provisioning.

1. Vyberte **zásady sdíleného přístupu** a pak zvolte zásadu přístupu, kterou chcete použít k otevření jejích vlastností. V části **zásady přístupu** zkopírujte a uložte připojovací řetězec primárního klíče.

    ![Získání připojovacího řetězce služby zřizování z portálu](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Vytvoření ukázky skupiny registrací 

V této části se dozvíte, jak vytvořit konzolovou aplikaci .NET Core, která do vaší služby zřizování přidá skupinu pro registraci. S určitými úpravami můžete pomocí tohoto postupu vytvořit pro přidání skupiny registrací také konzolovou aplikaci [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot). Další informace o vývoji s využitím IoT Core najdete v [dokumentaci pro vývojáře pro Windows IoT Core](/windows/iot-core/).

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**. V části **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Core)** pro šablonu projektu C# a vyberte **Další**.

1. Pojmenujte projekt *CreateEnrollmentGroup* a potom stiskněte **vytvořit**.

    ![Konfigurovat klasický desktopový projekt Visual C# pro Windows](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Po otevření řešení v aplikaci Visual Studio klikněte v podokně **Průzkumník řešení** pravým tlačítkem myši na projekt **CreateEnrollmentGroup** a vyberte možnost **Spravovat balíčky NuGet**.

1. V **nástroji Správce balíčků NuGet** vyberte **Procházet**, vyhledejte a zvolte **Microsoft. Azure. Devices. Provisioning. Service** a potom stiskněte **nainstalovat**.

    ![Okno Správce balíčků NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Tento krok stáhne a nainstaluje balíček NuGet [klientské sady SDK služby zřizování pro Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) a jeho závislosti a přidá se na něj odkaz.

1. Přidejte následující `using` příkazy za další `using` příkazy v horní části `Program.cs` :

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Přidejte do třídy následující pole `Program` a proveďte uvedené změny.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Nahraďte `ProvisioningServiceConnectionString` hodnotu zástupného symbolu připojovacím řetězcem služby zřizování, pro kterou chcete vytvořit registraci.

   * Nahraďte `X509RootCertPath` hodnotu zástupného symbolu cestou k souboru. pem nebo. cer. Tento soubor představuje veřejnou část certifikátu X. 509 zprostředkujícího nebo kořenového certifikačního úřadu, který se dřív nahrál a ověřil pomocí vaší služby zřizování.

   * Volitelně můžete změnit `EnrollmentGroupId` hodnotu. Řetězec může obsahovat pouze malá písmena a pomlčky.

   > [!IMPORTANT]
   > V případě produkčního kódu mějte na paměti následující aspekty zabezpečení:
   >
   > * Uložení připojovacího řetězce pro správce služby zřizování v kódu je v rozporu s osvědčenými postupy zabezpečení. Místo toho by se měl připojovací řetězec uchovávat zabezpečeným způsobem, například v zabezpečeném konfiguračním souboru nebo v registru.
   > * Nezapomeňte nahrát pouze veřejnou část podpisového certifikátu. Nikdy nenahrávejte soubory .pfx (PKCS12) ani .pem obsahující privátní klíče ke službě zřizování.

1. Do třídy přidejte následující metodu `Program` . Tento kód vytvoří položku skupiny registrací a pak zavolá `CreateOrUpdateEnrollmentGroupAsync` metodu pro `ProvisioningServiceClient` Přidání skupiny registrací do služby zřizování.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. Nakonec nahraďte `Main` metodu následujícími řádky:

   ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
   ```

1. Sestavte řešení.

## <a name="run-the-enrollment-group-sample"></a>Spuštění ukázky skupiny registrací
  
Spusťte ukázku v sadě Visual Studio, aby se vytvořila skupina registrací. Zobrazí se okno příkazového řádku a začne zobrazovat potvrzovací zprávy. Po úspěšném vytvoření se v okně příkazového řádku zobrazí vlastnosti nové skupiny pro registraci.

Můžete ověřit, že se vytvořila skupina pro registraci. Přejít do přehledu služby Device Provisioning a vybrat **spravovat registrace** a pak vybrat **skupiny** registrací. Měla by se zobrazit nová položka registrace odpovídající ID registrace, které jste použili v ukázce.

![Vlastnosti registrace na portálu](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Vyberte položku pro ověření kryptografického otisku certifikátu a dalších vlastností položky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete prozkoumat ukázku služby C#, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. V opačném případě pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete okno výstup ukázky C# ve vašem počítači.

1. V Azure Portal přejděte do vaší služby Device Provisioning, vyberte **spravovat registrace** a pak vyberte **skupiny** registrací. Vyberte *ID registrace* pro položku registrace, kterou jste vytvořili v tomto rychlém startu, a stiskněte klávesu **Delete**.

1. Ze služby Device Provisioning v Azure Portal vyberte **certifikáty**, zvolte certifikát, který jste nahráli pro tento rychlý Start, a v horní části **podrobností certifikátu** stiskněte **Odstranit** .  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu registrací pro certifikát X. 509 zprostředkující nebo kořenové certifikační autority pomocí Azure IoT Hub Device Provisioning Service. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal.

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)
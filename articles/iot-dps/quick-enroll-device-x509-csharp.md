---
title: 'Registrace zařízení X.509 do služby Azure Device Provisioning Service pomocí C #'
description: V tomto rychlém startu se používají skupinové registrace. V tomto rychlém startu zaregistrujte zařízení X.509 do služby Azure IoT Hub Device Provisioning Service (DPS) pomocí jazyka C#.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 64bc3921a606ab3211173b46b268ded53952c8bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75434664"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Rychlý start: Registrace zařízení X.509 do služby Device Provisioning Service pomocí C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Tento rychlý start ukazuje, jak prostřednictvím kódu programu v C# vytvořit [skupinu registrací](concepts-service.md#enrollment-group), která používá zprostředkující nebo kořenové certifikáty X.509 certifikační autority. Skupina zápisu se vytvoří pomocí [sady Microsoft Azure IoT SDK pro rozhraní .NET](https://github.com/Azure/azure-iot-sdk-csharp) a ukázkové aplikace C# .NET Core. Skupina registrací řídí přístup ke službě zřizování pro zařízení, která ve svém řetězu certifikátů sdílejí společný podpisový certifikát. Další informace najdete v tématu [Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Další informace o používání infrastruktury veřejných klíčů (PKI) založené na certifikátech X.509 se službami Azure IoT Hub a Device Provisioning najdete v tématu [Přehled zabezpečení pomocí certifikátu webu X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Tento rychlý start očekává, že jste již vytvořili službu IoT hub a službu zřizování zařízení. Pokud jste ještě nevytvořili tyto prostředky, dokončete [nastavení služby zřizování zařízení služby IoT Hub pomocí rychlého](./quick-setup-auto-provision.md) startu na webu Azure Portal, než budete pokračovat v tomto článku.

Přestože kroky v tomto článku fungují na počítačích se systémem Windows i Linux, tento článek používá vývojový počítač se systémem Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalujte sadu [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Nainstalujte [sadu .NET Core SDK](https://www.microsoft.com/net/download/windows).
* Nainstalujte [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Příprava testovacích certifikátů

Pro účely tohoto rychlého startu potřebujete soubor .pem nebo .cer, který obsahuje veřejnou část zprostředkujícího nebo kořenového certifikátu X.509 certifikační autority. Certifikát musí být nahraný do vaší služby zřizování a ověřený touto službou.

Sada [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) obsahuje testovací nástroje, které vám pomůžou vytvořit řetěz certifikátů X.509, nahrát kořenový nebo zprostředkující certifikát z tohoto řetězce a provést ověření vlastnictví se službou k ověření certifikátu.

> [!CAUTION]
> Certifikáty vytvořené pomocí nástrojů sady SDK používejte pouze pro testování vývoje.
> Nepoužívejte tyto certifikáty ve výrobě.
> Obsahují pevně zakódovaná hesla, například *1234*, jejichž platnost vyprší po 30 dnech.
> Informace o získání certifikátů vhodných pro použití v produkčním prostředí najdete v tématu věnovaném [získání certifikátu webu X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) v dokumentaci ke službě Azure IoT Hub.
>

Chcete-li pomocí tohoto testovacího nástroje generovat certifikáty, postupujte takto:

1. Najděte název značky pro [nejnovější verzi](https://github.com/Azure/azure-iot-sdk-c/releases/latest) sady Azure IoT C SDK.

2. Otevřete příkazový řádek nebo prostředí Git Bash a přejděte do pracovní složky na svém počítači. Spusťte následující příkazy a naklonovat nejnovější verzi úložiště [GitHub Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) Jako hodnotu `-b` parametru použijte značku, kterou jste našli v předchozím kroku:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

   Testovací nástroje se nacházejí ve složce *azure-iot-sdk-c/tools/CACertificates* úložiště, které jste naklonovali.

3. Ukázky a kurzy najdete v článku [Správa testovacích certifikátů certifikační autority](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Kromě nástrojů v sadě C SDK [ukázka ověření certifikátu skupiny](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) v *sadě Microsoft Azure IoT SDK pro rozhraní .NET* ukazuje, jak provést ověření vlastnictví v c# s existujícím certifikátem X.509 zprostředkující nebo kořenová certifikační autorita.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Získání připojovacího řetězce pro službu zřizování

Pro ukázku v tomto rychlém startu potřebujete připojovací řetězec pro vaši službu zřizování.

1. Přihlaste se k portálu Azure, vyberte **Všechny prostředky**a pak službu zřizování zařízení.

1. Vyberte **Zásady sdíleného přístupu**a vyberte zásady přístupu, které chcete použít k otevření jeho vlastností. V **zásadách aplikace Access**zkopírujte a uložte připojovací řetězec primárního klíče.

    ![Získání připojovacího řetězce služby zřizování z portálu](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Vytvoření ukázky skupiny registrací 

Tato část ukazuje, jak vytvořit konzolovou aplikaci .NET Core, která přidá skupinu registrace do služby zřizování. S určitými úpravami můžete pomocí tohoto postupu vytvořit pro přidání skupiny registrací také konzolovou aplikaci [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot). Další informace o vývoji s využitím IoT Core najdete v [dokumentaci pro vývojáře pro Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**. V **části Vytvoření nového projektu**zvolte **konzolovou aplikaci (.NET Core)** pro šablonu projektu C# a vyberte **Další**.

1. Pojmenujte projekt *CreateEnrollmentGroup*a stiskněte **klávesu Create**.

    ![Konfigurace projektu Visual C# Klasické plochy systému Windows](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Když se řešení otevře v sadě Visual Studio, klikněte v podokně **Průzkumník řešení** pravým tlačítkem myši na projekt **CreateEnrollmentGroup** a pak vyberte **Příkaz Spravovat balíčky NuGet**.

1. Ve **Správci balíčků NuGet**vyberte **Procházet**, vyhledejte a zvolte **Microsoft.Azure.Devices.Provisioning.Service**a stiskněte **tlačítko Instalovat**.

    ![Okno Správce balíčků NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Tento krok stáhne, nainstaluje a přidá odkaz na balíček [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet a jeho závislosti.

1. Za ostatní `using` `using` příkazy v horní části `Program.cs`přidejte následující příkazy :

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Přidejte do třídy `Program` následující pole a proveďte uvedené změny.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * `ProvisioningServiceConnectionString` Nahraďte zástupnou hodnotu připojovacím řetězcem služby zřizování, pro kterou chcete vytvořit registraci.

   * Nahraďte `X509RootCertPath` zástupnou hodnotu cestou k souboru Pem nebo CER. Tento soubor představuje veřejnou část zprostředkujícího nebo kořenového certifikátu CA X.509, který byl dříve odeslán a ověřen pomocí služby zřizování.

   * Volitelně můžete změnit `EnrollmentGroupId` hodnotu. Řetězec může obsahovat pouze malá písmena a pomlčky.

   > [!IMPORTANT]
   > V případě produkčního kódu mějte na paměti následující aspekty zabezpečení:
   >
   > * Uložení připojovacího řetězce pro správce služby zřizování v kódu je v rozporu s osvědčenými postupy zabezpečení. Místo toho by se měl připojovací řetězec uchovávat zabezpečeným způsobem, například v zabezpečeném konfiguračním souboru nebo v registru.
   > * Nezapomeňte nahrát pouze veřejnou část podpisového certifikátu. Nikdy nenahrávejte soubory .pfx (PKCS12) ani .pem obsahující privátní klíče ke službě zřizování.

1. Přidejte do třídy následující metodu. `Program` Tento kód vytvoří položku skupiny `CreateOrUpdateEnrollmentGroupAsync` zápisu a pak volá metodu k `ProvisioningServiceClient` přidání skupiny zápisu do zřizovací služby.

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

1. Nakonec nahraďte tělo `Main` metody následujícími řádky:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Sestavte řešení.

## <a name="run-the-enrollment-group-sample"></a>Spuštění ukázky skupiny registrací
  
Spusťte ukázku v sadě Visual Studio, aby se vytvořila skupina registrací. Zobrazí se okno příkazového řádku, které se začne zobrazovat s potvrzovacími zprávami. Při úspěšném vytvoření se v okně příkazového řádku zobrazí vlastnosti nové skupiny zápisů.

Můžete ověřit, zda byla vytvořena skupina zápisu. Přejděte do souhrnu **služby**zřizování zařízení a vyberte Spravovat registrace a vyberte **položku Skupiny registrace**. Měla by se zobrazit nová položka registrace odpovídající ID registrace, které jste použili v ukázce.

![Vlastnosti registrace na portálu](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Vyberte položku, chcete-li ověřit kryptografický otisk certifikátu a další vlastnosti položky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu prozkoumat ukázku služby C#, nečistěte prostředky vytvořené v tomto rychlém startu. V opačném případě odstraňte všechny prostředky vytvořené tímto rychlým startem pomocí následujících kroků.

1. Zavřete ukázkové výstupní okno jazyka C# v počítači.

1. Přejděte na portál Azure Portal ke službě Zřizování zařízení, vyberte **Spravovat registrace**a pak vyberte **Skupiny registrace**. Vyberte *ID registrace* pro položku registrace, kterou jste vytvořili pomocí tohoto rychlého startu, a stiskněte **klávesu Delete**.

1. Ve službě Zřizování zařízení na webu Azure Portal vyberte **Certifikáty**, vyberte certifikát, který jste nahráli pro tento rychlý start, a stiskněte **Delete** v horní části **Podrobnosti o certifikátu**.  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu zápisů pro zprostředkující nebo kořenový certifikát certifikační autority X.509 pomocí služby Azure IoT Hub Device Provisioning Service. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal.

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)

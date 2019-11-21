---
title: Quickstart - Provision a simulated TPM device to Azure IoT Hub using C#
description: Quickstart - Create and provision a simulated TPM device using C# device SDK for Azure IoT Hub Device Provisioning Service. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 65e914700749fca2011d189dc1fbce701a05f16d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228699"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Create and provision a simulated TPM device using C# device SDK for IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

These steps show you how to use the [Azure IoT Samples for C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) to simulate a TPM device on a development machine running the Windows OS. The sample also connects the simulated device to an IoT Hub using the Device Provisioning Service. 

Vzorový kód používá simulátor Windows TPM jako [modul hardwarového zabezpečení (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) zařízení. 

Pokud neznáte proces automatického zřizování, měli byste se seznámit také s [koncepty automatického zřizování](concepts-auto-provisioning.md). Než budete pokračovat, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). 

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace:](concepts-service.md#individual-enrollment) Slouží k registraci jednoho zařízení.

V tomto článku si předvedeme jednotlivé registrace.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí 

1. Make sure you have the [.NET Core 2.1 SDK or later](https://www.microsoft.com/net/download/windows) installed on your machine. 

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

1. Otevřete příkazový řádek nebo Git Bash. Clone the Azure IoT Samples for C# GitHub repo:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Zřízení simulovaného zařízení

1. Přihlaste se k portálu Azure. V nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu Device Provisioning. V okně **Přehled** si poznamenejte hodnotu **_Rozsah ID_** .

    ![Zkopírování hodnoty Rozsah ID služby zřizování z okna portálu](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

2. V příkazovém řádku přejděte do adresáře projektu s ukázkou zřizování zařízení TPM.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

2. Zadejte následující příkaz, který sestaví a spustí ukázku zřizování zařízení TPM. Hodnotu `<IDScope>` nahraďte hodnotou Rozsah ID pro vaši službu zřizování. 

    ```cmd
    dotnet run <IDScope>
    ```

    This command will launch the TPM chip simulator in a separate command prompt. On Windows, you may encounter a Windows Security Alert that asks whether you want to allow Simulator.exe to communicate on public networks. For the purposes of this sample, you may cancel the request.

1. V příkazovém okně se zobrazí **_Ověřovací klíč_** , **_ID registrace_** a navrhované **_ID zařízení_** potřebné pro registraci zařízení. Take note of these values. You will use these value to create an individual enrollment in your Device Provisioning Service instance. 
   > [!NOTE]
   > Nespleťte si okno obsahující výstup příkazu s oknem obsahujícím výstup ze simulátoru TPM. Možná budete na příkazové okno muset kliknout a přenést ho do popředí.

    ![Výstup v příkazovém okně](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

4. Na webu Azure Portal v okně s přehledem služby Device Provisioning vyberte **Správa registrací**. Vyberte kartu **Jednotlivé registrace** a v horní části klikněte na tlačítko **Přidat jednotlivou registraci**. 

5. V části **Přidat registraci** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **TPM**.
   - Enter the *Registration ID* and *Endorsement key* for your TPM device that you noted earlier.
   - Volitelně vyberte centrum IoT propojené s vaší službou zřizování.
   - Zadejte jedinečné ID zařízení. Můžete zadat ID zařízení navrhované v ukázkovém výstupu nebo vlastní ID zařízení. Pokud použijete vlastní, při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. 
   - Optionally update the **Initial device twin state** with the desired initial configuration for the device.
   - Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 

     ![Zadání informací o registraci zařízení v okně portálu](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Po úspěšné registraci se *ID registrace* vašeho zařízení zobrazí v seznamu na kartě *Jednotlivé registrace*. 

6. Stisknutím klávesy Enter v příkazovém okně (ve kterém se zobrazil **_Ověřovací klíč_** , **_ID registrace_** a navrhované **_ID zařízení_** ) zaregistrujte simulované zařízení. Všimněte si zpráv, které simulují spouštění zařízení a jeho připojování ke službě Device Provisioning pro získání informací o vašem centru IoT. 

1. Ověřte zřízení zařízení. Po úspěšném zřízení simulovaného zařízení pro centrum IoT propojené se službou zřizování se ID zařízení zobrazí v okně **Zařízení IoT** centra. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s touto ukázkou klienta zařízení a jejím prozkoumáváním, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. Zavřete na svém počítači okno simulátoru TPM.
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou službu Device Provisioning. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste na svém počítači vytvořili simulované zařízení TPM a pomocí služby IoT Hub Device Provisioning jste ho zřídili pro své centrum IoT. Pokud chcete zjistit, jak zaregistrovat zařízení TPM prostřednictvím kódu programu, pokračujte k rychlému startu pro registraci zařízení TPM prostřednictvím kódu programu. 

> [!div class="nextstepaction"]
> [Rychlý start Azure – Registrace zařízení TPM do služby Azure IoT Hub Device Provisioning](quick-enroll-device-tpm-csharp.md)

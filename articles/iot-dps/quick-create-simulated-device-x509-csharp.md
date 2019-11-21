---
title: Provision simulated X.509 device to Azure IoT Hub using C#
description: Quickstart - Create and provision a simulated X.509 device using C# device SDK for Azure IoT Hub Device Provisioning Service. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 94ac67ec75a46dcf13bdc1024ec06be687554999
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228725"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Create and provision a simulated X.509 device using C# device SDK for IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

These steps show you how to use the [Azure IoT Samples for C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) to simulate an X.509 device on a development machine running the Windows OS. The sample also connects the simulated device to an IoT Hub using the Device Provisioning Service.

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

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Vytvoření certifikátu zařízení X.509 podepsaného svým držitelem a položky jednotlivé registrace

V této části budete používat certifikát X.509 podepsaný svým držitelem. Je důležité vzít v úvahu následující body:

* Certifikáty podepsané svým držitelem jsou určené jenom pro testování a neměly by se používat v produkčním prostředí.
* Výchozí datum vypršení platnosti certifikátu podepsaného svým držitelem je jeden rok.

You will use sample code from the [Provisioning Device Client Sample - X.509 Attestation](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) to create the certificate to be used with the individual enrollment entry for the simulated device.


1. V příkazovém řádku přejděte do adresáře projektu s ukázkou zřizování zařízení X.509.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Vzorový kód je nastavený tak, aby používal certifikáty X.509 uložené v souboru ve formátu PKCS12 chráněném heslem (certificate.pfx). Kromě toho potřebujete soubor certifikátu veřejného klíče (certificate.cer) pro vytvoření jednotlivé registrace v pozdější části tohoto rychlého startu. Pokud chcete vygenerovat certifikát podepsaný svým držitelem a přidružené soubory .cer a .pfx, spusťte následující příkaz:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. Skript váz vyzve k zadání hesla PFX. Toto heslo si zapamatujte, protože ho musíte použít při spuštění ukázky.

    ![ Zadání hesla PFX](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu zřizování.

5. V okně s přehledem služby Device Provisioning vyberte **Správa registrací**. Vyberte kartu **Jednotlivé registrace** a nahoře klikněte na tlačítko **Přidat jednotlivou registraci**. 

6. Na panelu **Přidat registraci** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **X.509**.
   - V části *Soubor .pem nebo .cer primárního certifikátu* klikněte na *Vyberte soubor* a vyberte soubor certifikátu **certificate.cer** vytvořený v předchozích krocích.
   - **ID zařízení** nechte prázdné. Vaše zařízení se zřídí s ID zařízení nastaveným na běžný název v certifikátu X.509, tedy **iothubx509device1**. Tento název se použije také jako ID registrace pro položku jednotlivé registrace. 
   - Volitelně můžete zadat následující informace:
       - Vyberte centrum IoT propojené s vaší zřizovací službou.
       - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 

     [![Přidání jednotlivé registrace pro ověření X.509 na portálu](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Po úspěšné registraci se záznam vaší registrace X.509 zobrazí jako **iothubx509device1** ve sloupci *ID registrace* na kartě *Jednotlivé registrace*. 

## <a name="provision-the-simulated-device"></a>Zřízení simulovaného zařízení

1. Z okna **Přehled** pro vaši službu zřizování si poznamenejte hodnotu **_Rozsah ID_** .

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Zadejte následující příkaz, který sestaví a spustí ukázku zřizování zařízení X.509. Hodnotu `<IDScope>` nahraďte hodnotou Rozsah ID pro vaši službu zřizování. 

    ```cmd
    dotnet run <IDScope>
    ```

3. Po zobrazení výzvy zadejte heslo k souboru PFX, který jste vytvořili dříve. Všimněte si zpráv, které simulují spouštění zařízení a jeho připojování ke službě Device Provisioning pro získání informací o vašem centru IoT. 

    ![Ukázkový výstup zařízení](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. Ověřte zřízení zařízení. Po úspěšném zřízení simulovaného zařízení pro centrum IoT propojené se službou zřizování se ID zařízení zobrazí v okně **Zařízení IoT** centra. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s touto ukázkou klienta zařízení a jejím prozkoumáváním, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. Zavřete na svém počítači okno simulátoru TPM.
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou službu Device Provisioning. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste na svém počítači s Windows vytvořili simulované zařízení X.509 a pomocí služby Azure IoT Hub Device Provisioning na portálu jste ho zřídili pro své centrum IoT. Pokud chcete zjistit, jak zaregistrovat zařízení X.509 prostřednictvím kódu programu, pokračujte k rychlému startu pro registraci zařízení X.509 prostřednictvím kódu programu. 

> [!div class="nextstepaction"]
> [Rychlý start Azure – Registrace zařízení X.509 do služby Azure IoT Hub Device Provisioning](quick-enroll-device-x509-csharp.md)

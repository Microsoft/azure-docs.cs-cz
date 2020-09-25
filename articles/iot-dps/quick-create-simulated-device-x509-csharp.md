---
title: 'Rychlé zprovoznění – zřizování simulovaného zařízení X. 509 do Azure IoT Hub pomocí jazyka C #'
description: Rychlý Start – vytvoření a zřízení simulovaného zařízení X. 509 pomocí sady SDK pro zařízení v jazyce C# pro Azure IoT Hub Device Provisioning Service (DPS). V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 27bb1c97fa082f15642ab9eff6b0bdba357068a2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323970"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Rychlý Start: vytvoření a zřízení simulovaného zařízení X. 509 pomocí sady SDK pro zařízení v jazyce C# pro IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Tyto kroky ukazují, jak pomocí [ukázek Azure IoT pro jazyk C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) simulovat zařízení X. 509 na vývojovém počítači s operačním systémem Windows. Ukázka také připojí simulované zařízení k IoT Hub pomocí služby Device Provisioning.

Pokud nejste obeznámeni s procesem autozřizování, přečtěte si přehled [zřizování](about-iot-dps.md#provisioning-process) . Než budete pokračovat, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). 

Služba Azure IoT Device Provisioning podporuje dva typy registrací:
- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace:](concepts-service.md#individual-enrollment) Slouží k registraci jednoho zařízení.

V tomto článku si předvedeme jednotlivé registrace.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí 

1. Ujistěte se, že máte na svém počítači nainstalovanou [sadu .NET Core 2,1 SDK nebo novější](https://www.microsoft.com/net/download/windows) . 

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

1. Otevřete příkazový řádek nebo Git Bash. Naklonujte ukázky Azure IoT pro úložiště GitHub v jazyce C#:
    
    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Vytvoření certifikátu zařízení X.509 podepsaného svým držitelem a položky jednotlivé registrace

V této části budete používat certifikát X.509 podepsaný svým držitelem. Je důležité vzít v úvahu následující body:

* Certifikáty podepsané svým držitelem jsou určené jenom pro testování a neměly by se používat v produkčním prostředí.
* Výchozí datum vypršení platnosti certifikátu podepsaného svým držitelem je jeden rok.

K vytvoření certifikátu, který se má použít s položkou jednotlivé registrace pro simulované zařízení, použijete vzorový kód [Ukázka-X. 509 ověření identity klienta](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) .


1. V příkazovém řádku přejděte do adresáře projektu s ukázkou zřizování zařízení X.509.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Vzorový kód je nastavený tak, aby používal certifikáty X.509 uložené v souboru ve formátu PKCS12 chráněném heslem (certificate.pfx). Kromě toho budete potřebovat soubor certifikátu veřejného klíče (Certificate. cer) k vytvoření jednotlivé registrace později v tomto rychlém startu. Pokud chcete vygenerovat certifikát podepsaný svým držitelem a přidružené soubory .cer a .pfx, spusťte následující příkaz:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. Skript váz vyzve k zadání hesla PFX. Toto heslo si zapamatujte, protože ho musíte použít při spuštění ukázky.

    ![ Zadání hesla PFX](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Přihlaste se k Azure Portal, v nabídce na levé straně vyberte tlačítko **všechny prostředky** a otevřete svou službu zřizování.

5. V nabídce služba Device Provisioning vyberte **spravovat registrace**. Vyberte kartu **jednotlivé registrace** a v horní části vyberte tlačítko **přidat jednotlivou registraci** . 

6. Na panelu **Přidat registraci** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **X.509**.
   - V části *soubor. pem nebo. cer primárního certifikátu*zvolte *Vybrat soubor* a vyberte soubor certifikátu **Certificate. cer** vytvořený v předchozích krocích.
   - **ID zařízení** nechte prázdné. Vaše zařízení se zřídí s ID zařízení nastaveným na běžný název v certifikátu X.509, tedy **iothubx509device1**. Tento název se použije také jako ID registrace pro položku jednotlivé registrace. 
   - Volitelně můžete zadat následující informace:
       - Vyberte centrum IoT propojené s vaší zřizovací službou.
       - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Po dokončení klikněte na tlačítko **Uložit** . 

     [![Přidání jednotlivé registrace pro ověření X. 509 na portálu](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Po úspěšné registraci se záznam vaší registrace X.509 zobrazí jako **iothubx509device1** ve sloupci *ID registrace* na kartě *Jednotlivé registrace*. 

## <a name="provision-the-simulated-device"></a>Zřízení simulovaného zařízení

1. V okně **Přehled** pro vaši službu zřizování si poznamenejte hodnotu **_Rozsah ID_** .

    ![Extrahování informací o koncovém bodu služby Device Provisioning z okna portálu](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Zadejte následující příkaz, který sestaví a spustí ukázku zřizování zařízení X.509. Hodnotu `<IDScope>` nahraďte hodnotou Rozsah ID pro vaši službu zřizování. 

    ```cmd
    dotnet run <IDScope>
    ```

3. Po zobrazení výzvy zadejte heslo k souboru PFX, který jste vytvořili dříve. Všimněte si zpráv, které simulují spouštění zařízení a jeho připojování ke službě Device Provisioning pro získání informací o vašem centru IoT. 

    ![Ukázkový výstup zařízení](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. Ověřte zřízení zařízení. Po úspěšném zřízení simulovaného zařízení pro Centrum IoT propojené se službou zřizování se ID zařízení zobrazí v okně **zařízení IoT** centra. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [pochopení a používání nevláken zařízení v IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci a prozkoumat si ukázku klienta zařízení, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. Zavřete na svém počítači okno simulátoru TPM.
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. V horní části okna **Přehled** klikněte v horní části podokna na **Odstranit** .  
1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. V horní části okna **Přehled** klikněte v horní části podokna na **Odstranit** .  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste na svém počítači s Windows vytvořili simulované zařízení X. 509 a pomocí Azure IoT Hub Device Provisioning Service na portálu jste ho zřídili ve službě IoT Hub. Informace o tom, jak zaregistrovat zařízení X. 509 prostřednictvím kódu programu, najdete v rychlém startu pro programovou registraci zařízení X. 509. 

> [!div class="nextstepaction"]
> [Rychlý Start Azure – registrace zařízení X. 509 do Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-csharp.md)

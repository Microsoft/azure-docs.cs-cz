---
title: 'Úvodní příručka – zřízení simulovaného zařízení TPM do služby Azure IoT Hub pomocí c #'
description: Úvodní příručka – vytvoření a zřízení simulované zařízení TPM pomocí sady SDK zařízení C# pro službu Azure IoT Hub Device Provisioning Service (DPS). V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 82bd284ede23e8880f79c614f4a6e2f588a4293c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976991"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Úvodní příručka: Vytvoření a zřízení simulovaného zařízení TPM pomocí sady SDK zařízení C# pro službu zřizování zařízení služby IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Tyto kroky ukazují, jak použít [ukázky Azure IoT pro C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) k simulaci zařízení TPM ve vývojovém počítači se systémem Windows OS. Ukázka také připojí simulované zařízení k centru IoT Hub pomocí služby zřizování zařízení. 

Vzorový kód používá simulátor Windows TPM jako [modul hardwarového zabezpečení (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) zařízení. 

Pokud nejste obeznámeni s procesem automatického zřizování, nezapomeňte také zkontrolovat [koncepty automatického zřizování](concepts-auto-provisioning.md). Než budete pokračovat, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). 

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace:](concepts-service.md#individual-enrollment) Slouží k registraci jednoho zařízení.

V tomto článku si předvedeme jednotlivé registrace.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí 

1. Ujistěte se, že máte v počítači nainstalovanou sadu [.NET Core 2.1 SDK nebo novější.](https://www.microsoft.com/net/download/windows) 

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

1. Otevřete příkazový řádek nebo Git Bash. Klonování ukázek Azure IoT pro úložiště C# GitHub:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Zřízení simulovaného zařízení

1. Přihlaste se k portálu Azure. V levé nabídce vyberte tlačítko **Všechny prostředky** a otevřete službu Zřizování zařízení. V okně **Přehled** si poznamenejte hodnotu **_oboru ID._**

    ![Zkopírování hodnoty Rozsah ID služby zřizování z okna portálu](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. V příkazovém řádku přejděte do adresáře projektu s ukázkou zřizování zařízení TPM.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Zadejte následující příkaz, který sestaví a spustí ukázku zřizování zařízení TPM. Hodnotu `<IDScope>` nahraďte hodnotou Rozsah ID pro vaši službu zřizování. 

    ```cmd
    dotnet run <IDScope>
    ```

    Tento příkaz spustí simulátor čipů Čip tpm v samostatném příkazovém řádku. V systému Windows se může setkat s výstrahou zabezpečení systému Windows, která se zeptá, zda chcete povolit simulator.exe komunikovat ve veřejných sítích. Pro účely této ukázky můžete požadavek zrušit.

1. Původní příkazové okno zobrazuje **_klíč potvrzení_**, **_ID registrace_** a navrhované **_ID zařízení_** potřebné pro registraci zařízení. Vezměte na vědomí tyto hodnoty. Tuto hodnotu použijete k vytvoření individuální registrace v instanci služby Zřizování zařízení. 
   > [!NOTE]
   > Nespleťte si okno obsahující výstup příkazu s oknem obsahujícím výstup ze simulátoru TPM. Možná budete muset vybrat původní příkazové okno, aby se do popředí.

    ![Výstup v příkazovém okně](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. Na webu Azure Portal vyberte v nabídce Služba zřizování zařízení **možnost Spravovat registrace**. Vyberte kartu **Individuální zápisy** a nahoře vyberte tlačítko **Přidat jednotlivé zápisy.** 

1. V panelu **Přidat zápis** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **TPM**.
   - Zadejte *ID registrace* a *ověřovací klíč* pro zařízení TPM z hodnot, které jste si dříve poznamenali.
   - Vyberte centrum IoT propojené s vaší zřizovací službou.
   - Volitelně můžete zadat následující informace:
       - Zadejte jedinečné *ID zařízení* (můžete použít navrhované nebo zadat vlastní). Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. Pokud se rozhodnete neposkytnout jeden, id registrace se použije k identifikaci zařízení místo.
       - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Po dokončení stiskněte tlačítko **Uložit.** 

     ![Zadání informací o registraci zařízení v okně portálu](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Po úspěšné registraci se *ID registrace* vašeho zařízení zobrazí v seznamu na kartě *Jednotlivé registrace*. 

1. Stisknutím *klávesy Enter* v příkazovém okně (ten, který zobrazil **_inkadovní klíč_**, **_ID registrace_** a navrhované **_ID zařízení)_** zaregistrujte simulované zařízení. Všimněte si zpráv, které simulují spouštění zařízení a jeho připojování ke službě Device Provisioning pro získání informací o vašem centru IoT. 

1. Ověřte zřízení zařízení. Při úspěšném zřizování simulovaného zařízení do centra IoT propojeného s vaší zřizovací službou se ID zařízení zobrazí na okně **zařízení IoT** centra. 

    ![Zařízení je zaregistrované u centra IoT](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci a zkoumání ukázky klienta zařízení, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. Zavřete na svém počítači okno simulátoru TPM.
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu Zřizování zařízení. V horní části okna **Přehled** stiskněte **klávesu Delete** v horní části podokna.  
1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. V horní části okna **Přehled** stiskněte **klávesu Delete** v horní části podokna.  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v počítači vytvořili simulované zařízení TPM a zřídíte ho do služby IoT hub pomocí služby Zřizování zařízení služby IoT Hub. Chcete-li se dozvědět, jak zaregistrovat zařízení Čipové čipové čipy programově, pokračujte na úvodním startu pro programovou registraci zařízení TPM. 

> [!div class="nextstepaction"]
> [Azure quickstart – registrace zařízení TPM do služby Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-csharp.md)

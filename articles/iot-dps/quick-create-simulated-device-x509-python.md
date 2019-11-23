---
title: Provision simulated X.509 device to Azure IoT Hub using Python
description: Quickstart - Create and provision a simulated X.509 device using Python device SDK for IoT Hub Device Provisioning Service. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: f26bde0aca1cc1af9c4533724394f6e5e9c7249f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423295"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Create and provision a simulated X.509 device using Python device SDK for IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Tyto kroky ukazují, jak na vývojovém počítači s operačním systémem Windows simulovat zařízení X.509 a pomocí vzorového kódu Pythonu propojit toto simulované zařízení se službou Device Provisioning a centrem IoT. 

Pokud neznáte proces automatického zřizování, měli byste se seznámit také s [koncepty automatického zřizování](concepts-auto-provisioning.md). Než budete pokračovat, ujistěte se také, že jste provedli kroky uvedené v tématu [Nastavení služby IoT Hub Device Provisioning Service pomocí webu Azure Portal](./quick-setup-auto-provision.md). 

Služba Azure IoT Device Provisioning podporuje dva typy registrací:
- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace:](concepts-service.md#individual-enrollment) Slouží k registraci jednoho zařízení.

V tomto článku si předvedeme jednotlivé registrace.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

> [!NOTE]
> This guide only applies to the now-deprecated V1 Python SDK. Simulated X.509 devices are not yet been supported in V2. The team is currently hard at work bringing V2 to feature parity.

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Make sure you have installed either [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later, with the 'Desktop development with C++' workload enabled for your Visual Studio installation.

2. Stáhněte a nainstalujte [sestavovací systém CMake](https://cmake.org/download/).

3. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

4. Otevřete příkazový řádek nebo Git Bash. Naklonujte úložiště GitHub se vzorovým kódem pro simulaci zařízení.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

5. V lokální kopii tohoto úložiště GitHub vytvořte složku pro proces sestavení CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

6. Spuštěním následujícího příkazu vytvořte v sadě Visual Studio řešení pro klienta zřizování.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Vytvoření certifikátu zařízení X.509 podepsaného svým držitelem a položky jednotlivé registrace

V této části použijete certifikát zařízení X.509 podepsaný svým držitelem. Mějte na paměti následující důležité skutečnosti:

* Certifikáty podepsané svým držitelem jsou určené jenom pro testování a neměly by se používat v produkčním prostředí.
* Výchozí datum vypršení platnosti certifikátu podepsaného svým držitelem je jeden rok.

Pomocí vzorového kódu ze sady Azure IoT C SDK vytvoříte certifikát, který použije položka registrace pro simulované zařízení.

1. Otevřete řešení `azure_iot_sdks.sln` vygenerované ve složce *cmake* a sestavte ho v sadě Visual Studio.

2. Klikněte pravým tlačítkem na projekt **dice\_device\_enrollment** ve složce **Provision\_Tools** a vyberte **Nastavit jako spouštěný projekt**. Spusťte řešení. 

3. Po zobrazení výzvy zadejte v okně Výstup `i` pro jednotlivou registraci. V okně Výstup se zobrazí místně vygenerovaný certifikát X.509 pro vaše simulované zařízení. 
    
    Nejprve zkopírujte certifikát do schránky. Začněte prvním výskytem tohoto řetězce:
    
        -----BEGIN CERTIFICATE----- 
        
    Kopírování ukončete po prvním výskytu řetězce:
    
        -----END CERTIFICATE-----
        
    Nezapomeňte zahrnout i obě tyto čáry. 

    ![Aplikace pro registraci zařízení Dice](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
4. Na svém počítači s Windows vytvořte soubor **_X509testcertificate.pem_** , otevřete ho v libovolném editoru a zkopírujte do něj obsah schránky. Uložte soubor. 

5. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your provisioning service.

6. From the Device Provisioning Service menu, select **Manage enrollments**. Select **Individual Enrollments** tab and select the **Add individual enrollment** button at the top. 

7. In the **Add Enrollment** panel, enter the following information:
   - Jako *Mechanismus* ověření identity vyberte **X.509**.
   - Under the *Primary certificate .pem or .cer file*, choose *Select a file* to select the certificate file **X509testcertificate.pem** created in the previous steps.
   - Volitelně můžete zadat následující informace:
     - Vyberte centrum IoT propojené s vaší zřizovací službou.
     - Zadejte jedinečné ID zařízení. Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. 
     - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Once complete, press the **Save** button. 

     [![Přidání jednotlivé registrace pro ověření X.509 na portálu](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Po úspěšné registraci se vaše zařízení X.509 zobrazí jako **riot-device-cert** ve sloupci *ID registrace* na kartě *Jednotlivé registrace*. 

## <a name="simulate-the-device"></a>Simulace zařízení

1. From the Device Provisioning Service menu, select **Overview**. Poznamenejte si _Rozsah ID_ a _Globální koncový bod služby_.

    ![Informace o službě](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Stáhněte a nainstalujte [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnných prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Pokud používáte Windows, nainstalujte také [Distribuovatelné součásti Visual C++ pro Visual Studio 2015](https://www.microsoft.com/download/confirmation.aspx?id=48145). Balíčky pip vyžadují tyto distribuovatelné součásti k načítání nebo spouštění knihoven DLL jazyka C.

3. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) sestavte balíčky Pythonu.

   > [!NOTE]
   > Pokud používáte `pip`, nezapomeňte nainstalovat také balíček `azure-iot-provisioning-device-client`.

4. Přejděte do složky s ukázkami.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

5. Pomocí svého integrovaného vývojového prostředí (IDE) pro Python upravte skript Pythonu **provisioning\_device\_client\_sample.py**. Proměnné _GLOBAL\_PROV\_URI_ a _ID\_SCOPE_ změňte na hodnoty, které jste si poznamenali dříve.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.X509
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

6. Spusťte ukázku. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

7. Aplikace se připojí, zaregistruje zařízení a zobrazí zprávu o úspěšné registraci.

    ![úspěšná registrace](./media/python-quick-create-simulated-device-x509/enrollment-success.png)

8. Na portálu přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **Device Explorer**. Po úspěšném zřízení simulovaného zařízení X.509 pro toto centrum se ID tohoto zařízení zobrazí v okně **Device Explorer** a jeho *STAV* bude **povoleno**. You might need to press the **Refresh** button at the top if you already opened the blade prior to running the sample device application. 

    ![Zařízení je zaregistrované u centra IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Vyčištění prostředků

If you plan to continue working on and exploring the device client sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
2. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning service. Open the **Manage Enrollments** blade for your service, and then select the **Individual Enrollments** tab. Select the check box next to the *REGISTRATION ID* of the device you enrolled in this quickstart, and press the **Delete** button at the top of the pane. 
3. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open the **IoT devices** blade for your hub, select the check box next to the *DEVICE ID* of the device you registered in this quickstart, and then press the **Delete** button at the top of the pane.

## <a name="next-steps"></a>Další kroky

In this quickstart, you’ve created a simulated X.509 device on your Windows machine and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service on the portal. To learn how to enroll your X.509 device programmatically, continue to the quickstart for programmatic enrollment of X.509 devices. 

> [!div class="nextstepaction"]
> [Azure quickstart - Enroll X.509 devices to Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)

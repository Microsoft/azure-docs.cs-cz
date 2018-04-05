---
title: Zřízení simulovaného zařízení TPM pro službu Azure IoT Hub pomocí Pythonu | Dokumentace Microsoftu
description: Rychlý start Azure – Vytvoření a zřízení simulovaného zařízení TPM pomocí sady Java SDK pro zařízení pro službu IoT Hub Device Provisioning
services: iot-dps
keywords: ''
author: msebolt
ms.author: v-masebo
ms.date: 12/12/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 603ca504d95b7999137a4638aeed3ac58ce6c72d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Vytvoření a zřízení simulovaného zařízení TPM pomocí sady Python SDK pro zařízení pro službu IoT Hub Device Provisioning

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Tyto kroky ukazují, jak na vývojovém počítači s operačním systémem Windows vytvořit simulované zařízení, spustit simulátor Windows TPM jako [modul hardwarového zabezpečení (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) zařízení a jak pomocí vzorového kódu Pythonu propojit toto simulované zařízení se službou Device Provisioning a centrem IoT. 

Než budete pokračovat, nezapomeňte dokončit kroky v tématu [Nastavení služby IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md).

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Ujistěte se, že na svém počítači máte nainstalovanou sadu [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) nebo [Visual Studio 2017](https://www.visualstudio.com/vs/). Pro vaši instalaci sady Visual Studio musíte mít povolenou sadu funkcí 	Vývoj desktopových aplikací pomocí C++.

1. Stáhněte a nainstalujte [sestavovací systém CMake](https://cmake.org/download/).

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

1. Otevřete příkazový řádek nebo Git Bash. Naklonujte úložiště GitHub se vzorovým kódem pro simulaci zařízení:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. V lokální kopii tohoto úložiště GitHub vytvořte složku pro proces sestavení CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. Vzorový kód používá simulátor Windows TPM. Spusťte následující příkaz, kterým povolíte ověřování pomocí tokenu SAS. Zároveň se vygeneruje řešení v sadě Visual Studio pro simulované zařízení.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. V samostatném příkazovém řádku přejděte do složky simulátoru TPM a spusťte simulátor [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). Klikněte na **Povolit přístup**. Ten naslouchá přes soket na portech 2321 a 2322. Toto příkazové okno nezavírejte, simulátor je potřeba nechat spuštěný až do konce této úvodní příručky. 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![Simulátor TPM](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>Vytvoření položky registrace zařízení

1. Otevřete řešení `azure_iot_sdks.sln` vygenerované ve složce *cmake* a sestavte ho v sadě Visual Studio.

1. Klikněte pravým tlačítkem na projekt **tpm_device_provision** a vyberte **Nastavit jako spouštěný projekt**. Spusťte řešení. V okně výstupu se zobrazí **_Ověřovací klíč_** a **_ID registrace_** potřebné pro registraci zařízení. Poznamenejte si tyto hodnoty. 

    ![Nastavení TPM](./media/python-quick-create-simulated-device/tpm-setup.png)

1. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu Device Provisioning.

1. V okně s přehledem služby Device Provisioning vyberte **Správa registrací**. Vyberte kartu **Jednotlivé registrace** a klikněte na tlačítko **Přidat** v horní části. 

1. V části **Přidat položku seznamu registrací** zadejte následující informace:
    - Jako *Mechanismus* ověření identity vyberte **TPM**.
    - Zadejte *ID registrace* a *Ověřovací klíč* pro zařízení TPM. 
    - Vyberte centrum IoT propojené s vaší zřizovací službou.
    - Zadejte jedinečné ID zařízení. Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data.
    - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
    - Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 

    ![Zadání informací o registraci zařízení v okně portálu](./media/python-quick-create-simulated-device/enter-device-enrollment.png)  

   Po úspěšné registraci se *ID registrace* vašeho zařízení zobrazí v seznamu na kartě *Jednotlivé registrace*. 


## <a name="simulate-the-device"></a>Simulace zařízení

1. Stáhněte a nainstalujte [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnných prostředí pro konkrétní platformu.
    - Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145).

1. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) sestavte balíčky Pythonu.

    > [!NOTE]
        > Pokud používáte `build_client.cmd`, nezapomeňte použít příznak `--use-tpm-simulator`.

    > [!NOTE]
        > Pokud používáte `pip`, nezapomeňte nainstalovat také balíček `azure-iot-provisioning-device-client`. Poznámka: vydané balíčky PIP skutečně využívají TPM, a ne simulátor. Pokud chcete použít simulátor, musíte je zkompilovat ze zdroje s použitím příznaku `--use-tpm-simulator`.

1. Přejděte do složky s ukázkami.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. Pomocí svého integrovaného vývojového prostředí (IDE) pro Python upravte skript Pythonu **provisioning\_device\_client\_sample.py**. Proměnné _GLOBAL\_PROV\_URI_ a _ID\_SCOPE_ změňte na hodnoty, které jste si poznamenali dříve.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![Informace o službě](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. Spusťte ukázku. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. Všimněte si zpráv, které simulují spouštění zařízení a jeho připojování ke službě Device Provisioning pro získání informací o vašem centru IoT. 

    ![Úspěšná registrace](./media/python-quick-create-simulated-device/registration-success.png)

1. Po úspěšném zřízení simulovaného zařízení pro centrum IoT propojené se zřizovací službou se ID zařízení zobrazí v okně **Device Explorer** centra.

    ![Zařízení je zaregistrované u centra IoT](./media/python-quick-create-simulated-device/hub-registration.png) 

    Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s touto ukázkou klienta zařízení a jejím prozkoumáváním, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
1. Zavřete na svém počítači okno simulátoru TPM.
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou službu Device Provisioning. Otevřete okno **Správa registrací** pro vaši službu a pak klikněte na kartu **Jednotlivé registrace**. Vyberte *ID REGISTRACE* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části. 
1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. Otevřete okno **Zařízení IoT** pro vaše centrum, vyberte *ID ZAŘÍZENÍ*, které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části.

## <a name="next-steps"></a>Další kroky

V rámci tohoto rychlého startu jste na svém počítači vytvořili simulované zařízení TPM a pomocí služby IoT Hub Device Provisioning jste ho zřídili pro své centrum IoT. Pokud chcete zjistit, jak zaregistrovat zařízení TPM prostřednictvím kódu programu, pokračujte k rychlému startu pro registraci zařízení TPM prostřednictvím kódu programu. 

> [!div class="nextstepaction"]
> [Rychlý start Azure – Registrace zařízení TPM do služby Azure IoT Hub Device Provisioning](quick-enroll-device-tpm-java.md)

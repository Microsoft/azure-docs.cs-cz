---
title: Zřízení simulovaného zařízení X.509 do azure iot hubu pomocí Pythonu
description: Úvodní příručka – vytvoření a zřízení simulovaného zařízení X.509 pomocí sady SDK zařízení Python pro službu DPS služby DPS hubu (IoT Hub Provisioning Service). V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 44f1a2cd3336eeae87878c333fb05d2e6b1f88e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605387"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Úvodní příručka: Vytvoření a zřízení simulovaného zařízení X.509 pomocí sady SDK zařízení Python pro službu zřizování zařízení služby IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

V tomto rychlém startu vytvoříte simulované zařízení X.509 v počítači se systémem Windows. Ukázkový kód Pythonu zařízení se používá k připojení tohoto simulovaného zařízení k centru IoT pomocí individuální registrace pomocí služby Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Požadavky

- Přehled [konceptů automatického zřizování](concepts-auto-provisioning.md).
- Dokončení [nastavení služby zřizování zařízení služby IoT Hub na webu Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2015+](https://visualstudio.microsoft.com/vs/) s vývojem plochy s C++.
- [CMake sestavení systému](https://cmake.org/download/).
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Tento článek se vztahuje pouze na zastaralé V1 Python SDK. Klienti zařízení a služeb pro službu Zřizování zařízení služby Iot Hub ještě nejsou k dispozici ve V2. Tým je v současné době tvrdě pracuje, aby V2 funkce parity.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Ujistěte se, že jste nainstalovali [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 nebo novější, s "Vývoj plochy s C++' úlohy povolené pro instalaci sady Visual Studio.

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

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace](concepts-service.md#individual-enrollment): Používá se k registraci jednoho zařízení.

Tento článek ukazuje jednotlivé zápisy.

1. Otevřete řešení `azure_iot_sdks.sln` vygenerované ve složce *cmake* a sestavte ho v sadě Visual Studio.

2. Klikněte pravým tlačítkem na projekt **dice\_device\_enrollment** ve složce **Provision\_Tools** a vyberte **Nastavit jako spouštěný projekt**. Spusťte řešení. 

3. Po zobrazení výzvy zadejte v okně Výstup `i` pro jednotlivou registraci. V okně Výstup se zobrazí místně vygenerovaný certifikát X.509 pro vaše simulované zařízení. 
    
    Nejprve zkopírujte certifikát do schránky. Začněte prvním výskytem tohoto řetězce:
    
        -----BEGIN CERTIFICATE----- 
        
    Kopírování ukončete po prvním výskytu řetězce:
    
        -----END CERTIFICATE-----
        
    Nezapomeňte zahrnout i obě tyto čáry. 

    ![Aplikace pro registraci zařízení Dice](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
4. Na svém počítači s Windows vytvořte soubor **_X509testcertificate.pem_**, otevřete ho v libovolném editoru a zkopírujte do něj obsah schránky. Uložte soubor. 

5. Přihlaste se k portálu Azure, vyberte tlačítko **Všechny prostředky** v levé nabídce a otevřete zřizovací službu.

6. V nabídce Služba zřizování zařízení vyberte **Spravovat registrace**. Vyberte **kartu Jednotlivé registrace** a nahoře vyberte tlačítko Přidat jednotlivé **zápisy.** 

7. V panelu **Přidat zápis** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **X.509**.
   - V části *Primární certifikát .pem nebo soubor CER*zvolte Vybrat *soubor* a vyberte soubor certifikátu **X509testcertificate.pem** vytvořený v předchozích krocích.
   - Volitelně můžete zadat následující informace:
     - Vyberte centrum IoT propojené s vaší zřizovací službou.
     - Zadejte jedinečné ID zařízení. Při pojmenování zařízení se ujistěte, že nepoužíváte citlivá data. 
     - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Po dokončení stiskněte tlačítko **Uložit.** 

     [![Přidání individuální registrace pro atestaci X.509 na portálu](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Po úspěšné registraci se vaše zařízení X.509 zobrazí jako **riot-device-cert** ve sloupci *ID registrace* na kartě *Jednotlivé registrace*. 

## <a name="simulate-the-device"></a>Simulace zařízení

1. V nabídce Služba zřizování zařízení vyberte **Přehled**. Poznamenejte si _Rozsah ID_ a _Globální koncový bod služby_.

    ![Informace o službě](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Stáhněte a nainstalujte [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnných prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Pokud používáte Windows, nainstalujte také [Distribuovatelné součásti Visual C++ pro Visual Studio 2015](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). Balíčky pip vyžadují tyto distribuovatelné součásti k načítání nebo spouštění knihoven DLL jazyka C.

3. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) sestavte balíčky Pythonu.

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

8. Na portálu přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **Device Explorer**. Po úspěšném zřízení simulovaného zařízení X.509 pro toto centrum se ID tohoto zařízení zobrazí v okně **Device Explorer** a jeho *STAV* bude **povoleno**. Možná budete muset stisknout tlačítko **Aktualizovat** v horní části, pokud jste již otevřeli okno před spuštěním ukázkové aplikace zařízení. 

    ![Zařízení je zaregistrované u centra IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat v práci a zkoumání ukázky klienta zařízení, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte pomocí následujících kroků všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
2. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu Zřizování zařízení. Otevřete okno **Spravovat registrace** pro vaši službu a pak zaškrtněte kartu **Jednotlivé registrace.** Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna. 
3. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. Otevřete okno **zařízení IoT** pro váš rozbočovač, zaškrtněte políčko vedle *ID zařízení* zařízení, které jste zaregistrovali v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste na počítači s Windows vytvořili simulované zařízení X.509 a zřídíte ho do vašeho centra IoT hub pomocí služby Azure IoT Hub Device Provisioning Service na portálu. Chcete-li se dozvědět, jak zaregistrovat zařízení X.509 programově, pokračujte na rychlém startu pro programovou registraci zařízení X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart – registrace zařízení X.509 do služby Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)

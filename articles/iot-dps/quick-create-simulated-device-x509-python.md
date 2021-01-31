---
title: Rychlé zprovoznění – zřizování simulovaného zařízení X. 509 do Azure IoT Hub pomocí Pythonu
description: Rychlý Start – vytvoření a zřízení simulovaného zařízení X. 509 pomocí sady SDK pro zařízení Python pro IoT Hub Device Provisioning Service (DPS). V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 01/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: c151f78c6164cc62aac618a141a26eb1da574e3c
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218353"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Rychlý Start: vytvoření a zřízení simulovaného zařízení X. 509 pomocí sady SDK pro zařízení Python pro IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

V tomto rychlém startu zřídíte vývojový počítač jako zařízení Python X. 509. Pomocí ukázkového kódu zařízení ze [sady Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) můžete zařízení připojit ke službě IoT Hub. V tomto příkladu se používá v rámci služby Device Provisioning (DPS) individuální registrace.

## <a name="prerequisites"></a>Požadavky

- Seznamte se s koncepty [zřizování](about-iot-dps.md#provisioning-process) .
- Dokončení [nastavení IoT Hub Device Provisioning Service s Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.5.3 nebo novější](https://www.python.org/downloads/)
- [Git](https://git-scm.com/download/).


[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Ujistěte se, že je na vašem počítači nainstalovaný `git` a že je přidaný do proměnných prostředí, ke kterým má příkazové okno přístup. Na stránce [klientských nástrojů Git organizace Software Freedom Conservancy](https://git-scm.com/download/) najdete nejnovější verzi nástrojů `git` k instalaci. Jejich součástí je i **Git Bash**, aplikace příkazového řádku, pomocí které můžete pracovat se svým místním úložištěm Git. 

2. Otevřete příkazový řádek Git bash. Naklonujte úložiště GitHub pro [sadu Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python).
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```


## <a name="create-a-self-signed-x509-device-certificate"></a>Vytvoření certifikátu zařízení X.509 podepsaného svým držitelem 

V této části vytvoříte certifikát X. 509 podepsaný svým držitelem. Mějte na paměti následující důležité skutečnosti:

* Certifikáty podepsané svým držitelem jsou určené jenom pro testování a neměly by se používat v produkčním prostředí.
* Výchozí datum vypršení platnosti certifikátu podepsaného svým držitelem je jeden rok.

Pokud ještě nemáte certifikáty zařízení k ověřování zařízení, můžete vytvořit certifikát podepsaný svým držitelem s OpenSSL pro testování v tomto článku.  OpenSSL je součástí instalace Gitu. 

1. Na příkazovém řádku Git bash spusťte následující příkaz.

    # <a name="windows"></a>[Windows](#tab/windows)
    
    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > Nadbytečné lomítko uvedené pro název subjektu ( `//CN=Python-device-01` ) je vyžadováno pouze k řídicímu řetězci na platformách systému Windows v Gitu. 

    # <a name="linux"></a>[Linux](#tab/linux)
    
    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```
    
    ---
    
2. Až se zobrazí výzva k **zadání fráze PEM Pass:**, použijte `1234` k testování pomocí fráze Pass v tomto článku.    

3. Po opětovném zobrazení výzvy **Ověřte zadání fráze PEM Pass:**, použijte znovu heslo `1234` .    

Soubor testovacího certifikátu (*Python-Device. pem*) a soubor privátního klíče (*Python-Device. Key. pem*) jsou generovány v adresáři, ve kterém jste spustili `openssl` příkaz.


## <a name="create-an-individual-enrollment-entry-in-dps"></a>Vytvoření položky individuální registrace v DPS


Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace](concepts-service.md#individual-enrollment): používá se k registraci jednoho zařízení.

Tento článek popisuje jednotlivou registraci jednoho zařízení, které se má zřídit ve službě IoT Hub.

1. Přihlaste se k Azure Portal, v nabídce na levé straně vyberte tlačítko **všechny prostředky** a otevřete svou službu zřizování.

2. V nabídce služba Device Provisioning vyberte **spravovat registrace**. Vyberte kartu **jednotlivé registrace** a v horní části vyberte tlačítko **přidat jednotlivou registraci** . 

3. Na panelu **Přidat registraci** zadejte následující informace:
   - Jako *Mechanismus* ověření identity vyberte **X.509**.
   - V části *soubor. pem nebo. cer primárního certifikátu* zvolte *možnost vybrat soubor* a vyberte soubor certifikátu **Python-Device. pem** , pokud používáte testovací certifikát, který jste vytvořili dříve.
   - Volitelně můžete zadat následující informace:
     - Vyberte centrum IoT propojené s vaší zřizovací službou.
     - Aktualizujte **Počáteční stav dvojčete zařízení** s použitím požadované počáteční konfigurace zařízení.
   - Po dokončení klikněte na tlačítko **Uložit** . 

     [![Přidání jednotlivé registrace pro ověření X. 509 na portálu](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Po úspěšné registraci se vaše zařízení X. 509 zobrazí jako **Python-Device-01** pod sloupcem *ID registrace* na kartě *jednotlivé registrace* . Tato registrační hodnota pochází z názvu subjektu v certifikátu zařízení. 

## <a name="simulate-the-device"></a>Simulace zařízení

Ukázka zřizování Pythonu, [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) , se nachází v `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` adresáři. Tato ukázka používá šest proměnných prostředí k ověření a zřízení zařízení IoT pomocí DPS. Tyto proměnné prostředí jsou:

| Název proměnné              | Description                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Tato hodnota je globální koncový bod, který se používá pro připojení k vašemu prostředku DPS. |    
| `PROVISIONING_IDSCOPE`     |  Tato hodnota je rozsah ID pro prostředek DPS. |    
| `DPS_X509_REGISTRATION_ID` |  Tato hodnota je ID vašeho zařízení. Musí taky odpovídat názvu subjektu v certifikátu zařízení. |    
| `X509_CERT_FILE`           |  Název souboru certifikátu zařízení |    
| `X509_KEY_FILE`            |  Název souboru privátního klíče pro certifikát zařízení |
| `PASS_PHRASE`              |  Předávací fráze, kterou jste použili k zašifrování certifikátu a souboru privátního klíče ( `1234` ). |    

1. V nabídce služba Device Provisioning vyberte **Přehled**. Poznamenejte si _Rozsah ID_ a _globální koncový bod zařízení_.

    ![Informace o službě](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Do příkazového řádku Git bash použijte následující příkazy a přidejte proměnné prostředí pro globální koncový bod zařízení a rozsah ID.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

3. ID registrace pro zařízení IoT se musí shodovat s názvem subjektu v certifikátu zařízení. Pokud jste vygenerovali testovací certifikát podepsaný svým držitelem, `Python-device-01` je to název předmětu a ID registrace pro dané zařízení. 

    Pokud již máte certifikát zařízení, můžete použít `certutil` k ověření běžného názvu subjektu používaného pro vaše zařízení, jak je znázorněno níže pro testovací certifikát podepsaný svým držitelem:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

    V příkazovém řádku Git bash nastavte proměnnou prostředí pro ID registrace následujícím způsobem:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

4. V příkazovém řádku Git bash nastavte proměnné prostředí pro soubor certifikátu, soubor privátního klíče a heslo.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

5. Přečtěte si kód pro [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) , pokud nepoužíváte **Python verze 3,7** nebo novější, podle [zde uvedené změny kódu](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) nahraďte `asyncio.run(main())` a uložte vaši změnu. 

6. Spusťte ukázku. Ukázka se připojí, zřídí zařízení do centra a pošle některé zkušební zprávy do centra.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

7. Na portálu přejděte k centru IoT propojenému s vaší službou zřizování a otevřete okno **zařízení IoT** , které najdete v části **Průzkumník** v nabídce vlevo. Po úspěšném zřízení simulovaného zařízení X.509 pro toto centrum se ID tohoto zařízení zobrazí v okně **Device Explorer** a jeho *STAV* bude **povoleno**. Pokud jste okno už otevřeli před spuštěním ukázkové aplikace zařízení, možná budete muset stisknout tlačítko **aktualizovat** v horní části. 

    ![Zařízení je zaregistrované u centra IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Pokud jste v položce registrace pro vaše zařízení změnili *počáteční stav dvojčete zařízení* z výchozí hodnoty, může si zařízení požadovaný stav dvojčete vyžádat z centra a příslušně na něj reagovat. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci a prozkoumat si ukázku klienta zařízení, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky klienta zařízení.
2. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. Otevřete okno **Správa** registrací pro vaši službu a pak vyberte kartu **jednotlivé registrace** . Zaškrtněte políčko vedle *ID registrace* zařízení, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části podokna. 
3. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. Otevřete okno **zařízení IoT** pro vaše centrum, zaškrtněte políčko vedle *ID zařízení* , které jste zaregistrovali v rámci tohoto rychlého startu, a pak klikněte na tlačítko **Odstranit** v horní části podokna.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili simulované zařízení X. 509 na svém vývojovém počítači a zřídili ho do služby IoT Hub pomocí IoT Hub Device Provisioning Service Azure na portálu. Informace o tom, jak zaregistrovat zařízení X. 509 prostřednictvím kódu programu, najdete v rychlém startu pro programovou registraci zařízení X. 509. 

> [!div class="nextstepaction"]
> [Rychlý Start Azure – registrace zařízení X. 509 do Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)

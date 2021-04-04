---
title: Rychlý Start – registrace zařízení X. 509 do služby Azure Device Provisioning pomocí Pythonu
description: V tomto rychlém startu se používají skupinové registrace. V tomto rychlém startu budete registrovat zařízení X. 509 do Azure IoT Hub Device Provisioning Service (DPS) pomocí Pythonu.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 6997c8af3034483c6278023627dd79f8108b135c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94959743"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-python"></a>Rychlý start: Registrace zařízení X.509 do služby Device Provisioning Service pomocí Pythonu

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

V tomto rychlém startu použijete Python k programovému vytvoření skupiny registrací, která využívá certifikáty X. 509 zprostředkující nebo kořenové certifikační autority. Skupina registrací řídí přístup ke službě zřizování pro zařízení, která ve svém řetězu certifikátů sdílejí společný podpisový certifikát. Skupina registrací se vytvoří pomocí sady SDK služby zřizování pro Python a ukázkové aplikace v Pythonu.

## <a name="prerequisites"></a>Požadavky

- Dokončení [nastavení IoT Hub Device Provisioning Service pomocí Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 2.x nebo 3.x](https://www.python.org/downloads/). Přidejte Python k proměnným prostředí pro konkrétní platformu. V tomto rychlém startu se nainstaluje [sada SDK služby zřizování pro Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) .
- [PIP](https://pip.pypa.io/en/stable/installing/), pokud není součástí vaší distribuce Pythonu.
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Tento článek platí jenom pro zastaralé sady SDK verze V1 Pythonu. Klienti zařízení a služeb pro IoT Hub Device Provisioning Service ještě nejsou ve verzi v2 k dispozici. V současné době je tým v práci, aby se do parity funkcí přineslo v2.

## <a name="prepare-test-certificates"></a>Příprava testovacích certifikátů

Pro účely tohoto rychlého startu potřebujete soubor .pem nebo .cer, který obsahuje veřejnou část zprostředkujícího nebo kořenového certifikátu X.509 certifikační autority. Certifikát musí být nahraný do vaší služby zřizování a ověřený touto službou.

Další informace o používání infrastruktury veřejných klíčů (PKI) založené na certifikátech X.509 se službami Azure IoT Hub a Device Provisioning najdete v tématu [Přehled zabezpečení pomocí certifikátu webu X.509](../iot-hub/iot-hub-x509ca-overview.md).

Sada [SDK služby Azure IoT pro jazyk C](https://github.com/Azure/azure-iot-sdk-c) obsahuje testovací nástroje, které vám můžou pomoct vytvořit řetěz certifikátů X.509, nahrát kořenový nebo zprostředkující certifikát z tohoto řetězu a ověřit certifikát testováním vlastnictví pomocí této služby. Certifikáty vytvořené pomocí nástrojů sady SDK jsou určené **jenom k testování během vývoje**. Tyto certifikáty **se nesmí používat v produkčním prostředí**. Obsahují pevně zakódovaná hesla („1234“), jejichž platnost vyprší po 30 dnech. Informace o získání certifikátů vhodných pro použití v produkčním prostředí najdete v tématu věnovaném [získání certifikátu webu X.509](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) v dokumentaci ke službě Azure IoT Hub.

Pokud chcete vygenerovat certifikáty pomocí těchto testovacích nástrojů, proveďte následující kroky:

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

## <a name="modify-the-python-sample-code"></a>Úprava vzorového kódu Pythonu

Tato část ukazuje, jak do vzorového kódu přidat podrobnosti o zřizování vašeho zařízení X.509. 

1. Pomocí textového editoru vytvořte nový soubor **EnrollmentGroup.py**.

1. Na začátek souboru **EnrollmentGroup.py** přidejte následující příkazy `import` a proměnné. Pak nahraďte `dpsConnectionString` vaším připojovacím řetězcem, který najdete v části **Zásady sdíleného přístupu** ve vaší službě **Device Provisioning** na webu **Azure Portal**. Zástupný text certifikátu nahraďte certifikátem, který jste vytvořili v části [Příprava testovacích certifikátů](quick-enroll-device-x509-python.md#prepare-test-certificates). Nakonec vytvořte jedinečné ID registrace `registrationid` a ujistěte se, že se skládá pouze z malých alfanumerických znaků a pomlček.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. Přidejte následující funkci a volání funkce implementující vytvoření skupinové registrace:
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. Uložte a zavřete soubor **EnrollmentGroup.py**.
 

## <a name="run-the-sample-group-enrollment"></a>Spuštění ukázkové skupinové registrace

Služba Azure IoT Device Provisioning podporuje dva typy registrací:

- [Skupiny registrací:](concepts-service.md#enrollment-group) Slouží k registraci několika souvisejících zařízení.
- [Jednotlivé registrace](concepts-service.md#individual-enrollment): používá se k registraci jednoho zařízení.

Na vytváření jednotlivých registrací prostřednictvím [sady SDK služby zřizování pro Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) se ještě pracuje. Další informace najdete v tématu [Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

1. Otevřete příkazový řádek a spuštěním následujícího příkazu nainstalujte klienta [azure-iot-provisioning-device-client](https://pypi.org/project/azure-iot-provisioning-device-client).

    ```cmd/sh
    pip install azure-iothub-provisioningserviceclient    
    ```

2. Na příkazovém řádku spusťte skript.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

3. Sledujte ve výstupu úspěšnou registraci.

4. Přejděte k vaší službě zřizování na webu Azure Portal. Klikněte na **Správa registrací**. Všimněte si, že se na kartě **Skupiny registrací** zobrazí vaše skupina zařízení X.509 s dříve vytvořeným názvem `registrationid`. 

    ![Ověření úspěšné registrace X.509 na portálu](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud plánujete prozkoumat ukázku služby Java, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Javě.
1. Zavřete na svém počítači okno _Generátoru certifikátů X.509_.
1. V Azure Portal přejděte do vaší služby Device Provisioning, vyberte **spravovat registrace** a pak vyberte kartu **skupiny** registrací. Zaškrtněte políčko vedle *názvu skupiny* pro zařízení X. 509, které jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části podokna.    


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste do své služby Device Provisioning zaregistrovali simulovanou skupinu zařízení X. 509. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)
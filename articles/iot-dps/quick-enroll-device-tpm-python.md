---
title: Rychlý Start – registrace zařízení TPM do služby Azure Device Provisioning pomocí Pythonu
description: Rychlý Start – registrace zařízení TPM do Azure IoT Hub Device Provisioning Service (DPS) pomocí sady SDK služby zřizování pro Python V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 96bd1e85de45ac36515580025dfc392e931643f3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323759"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Rychlý Start: registrace zařízení TPM pro IoT Hub Device Provisioning Service pomocí sady SDK služby zřizování pro Python

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

V tomto rychlém startu jste programově vytvořili jednotlivou registraci pro zařízení TPM ve službě Azure IoT Hub Device Provisioning Service pomocí sady SDK služby zřizování pro Python a pomohli vám ukázkovou aplikaci v Pythonu.

## <a name="prerequisites"></a>Požadavky

- Dokončení [nastavení IoT Hub Device Provisioning Service pomocí Azure Portal](./quick-setup-auto-provision.md).
- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 2.x nebo 3.x](https://www.python.org/downloads/). V tomto rychlém startu se nainstaluje [sada SDK služby zřizování pro Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) .
- [PIP](https://pip.pypa.io/en/stable/installing/), pokud není součástí vaší distribuce Pythonu.
- Ověřovací klíč Použijte postup v tématu [Vytvoření a zřízení simulovaného zařízení](quick-create-simulated-device.md) nebo použijte ověřovací klíč dodaný se sadou SDK, popsanou níže.

> [!IMPORTANT]
> Tento článek platí jenom pro zastaralé sady SDK verze V1 Pythonu. Klienti zařízení a služeb pro IoT Hub Device Provisioning Service ještě nejsou ve verzi v2 k dispozici. V současné době je tým v práci, aby se do parity funkcí přineslo v2.

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Stáhněte a nainstalujte [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnných prostředí pro konkrétní platformu. 

1. V [sadě SDK služby zřizování pro Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)vyberte jednu z následujících možností:

    - Sestavení a kompilace sady **SDK služby Azure IoT pro Python**. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) sestavte balíčky Pythonu. Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba nainstalovat také [balíček distribuovatelných součástí Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

    - [Instalace nebo upgrade nástroje *pip*, což je systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/), a instalace balíčku pomocí následujícího příkazu:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Potřebujete ověřovací klíč pro vaše zařízení. Pokud jste vytvořili simulované zařízení TPM pomocí postupu v rychlém startu [Vytvoření a zřízení simulovaného zařízení](quick-create-simulated-device.md), použijte klíč vytvořený pro toto zařízení. Jinak můžete použít následující ověřovací klíč, který je součástí sady SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Úprava vzorového kódu Pythonu

Tato část ukazuje, jak do vzorového kódu přidat podrobnosti o zřizování vašeho zařízení TPM. 

1. Pomocí textového editoru vytvořte nový soubor **TpmEnrollment.py**.

1. Na začátek souboru **TpmEnrollment.py** přidejte následující příkazy `import` a proměnné. Pak nahraďte `dpsConnectionString` vaším připojovacím řetězcem, který najdete v části **Zásady sdíleného přístupu** ve vaší službě **Device Provisioning** na webu **Azure Portal**. Nahraďte `endorsementKey` hodnotou uvedenou dříve v části [Příprava prostředí](quick-enroll-device-tpm-python.md#prepareenvironment). Nakonec vytvořte jedinečné ID registrace `registrationid` a ujistěte se, že se skládá pouze z malých alfanumerických znaků a pomlček.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Přidejte následující funkci a volání funkce implementující vytvoření skupinové registrace:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Uložte a zavřete soubor **TpmEnrollment.py**.
 

## <a name="run-the-sample-tpm-enrollment"></a>Spuštění ukázkové registrace TPM

1. Otevřete příkazový řádek a spusťte skript.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Sledujte ve výstupu úspěšnou registraci.

1. Přejděte k vaší službě zřizování na webu Azure Portal. Vyberte **Správa registrací**. Všimněte si, že se na kartě **Jednotlivé registrace** zobrazí vaše zařízení TPM s dříve vytvořeným názvem `registrationid`. 

    ![Ověření úspěšné registrace TPM na portálu](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud plánujete prozkoumat ukázku služby Java, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Pythonu.
1. Pokud jste vytvořili simulované zařízení TPM, zavřete okno simulátoru TPM.
1. V Azure Portal přejděte do vaší služby Device Provisioning, vyberte **spravovat registrace**a pak vyberte kartu **jednotlivé registrace** . Zaškrtněte políčko vedle *ID registrace* pro položku registrace, kterou jste vytvořili v tomto rychlém startu, a stiskněte tlačítko **Odstranit** v horní části podokna.


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste programově vytvořili jednotlivou položku registrace pro zařízení TPM a volitelně jste na svém počítači vytvořili simulované zařízení TPM a pomocí Azure IoT Hub Device Provisioning Service ho zřídili ve službě IoT Hub. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal.

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)

---
title: Registrace zařízení TPM do služby Azure Device Provisioning pomocí Pythonu | Microsoft Docs
description: Rychlý start Azure – Registrace zařízení TPM do služby Azure IoT Hub Device Provisioning pomocí sady SDK služby zřizování pro Python
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2018
ms.topic: quickstarts
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: f83586a07612287b8ada625bf0c8a45d0f22b392
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521104"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Registrace zařízení TPM do služby IoT Hub Device Provisioning pomocí sady SDK služby zřizování pro Python
[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Tyto kroky ukazují, jak prostřednictvím kódu programu vytvořit jednotlivou registraci pro zařízení TPM ve službě Azure IoT Hub Device Provisioning pomocí sady [SDK služby zřizování pro Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) a ukázkové aplikace v Pythonu. Přestože sada SDK služby pro Python funguje na počítačích s Windows i Linuxem, tento článek provádí procesem registrace s použitím vývojového počítače s Windows.

Než budete pokračovat, nezapomeňte [nastavit službu IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md).


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Stáhněte a nainstalujte [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnných prostředí pro konkrétní platformu. 

1. Zvolte jednu z následujících možností:

    - Sestavení a kompilace sady **SDK služby Azure IoT pro Python**. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) sestavte balíčky Pythonu. Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba nainstalovat také [balíček distribuovatelných součástí Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145).

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

1. Přejděte k vaší službě zřizování na webu Azure Portal. Klikněte na **Správa registrací**. Všimněte si, že se na kartě **Jednotlivé registrace** zobrazí vaše zařízení TPM s dříve vytvořeným názvem `registrationid`. 

    ![Ověření úspěšné registrace TPM na portálu](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete prozkoumat ukázku služby v Javě, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Pythonu.
1. Pokud jste vytvořili simulované zařízení TPM, zavřete okno simulátoru TPM.
1. Přejděte k vaší službě Device Provisioning na webu Azure Portal, klikněte na **Správa registrací** a pak vyberte kartu **Jednotlivé registrace**. Vyberte *ID registrace* pro položku registrace, kterou jste vytvořili v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části okna.  


## <a name="next-steps"></a>Další postup
V rámci tohoto rychlého startu jste na svém počítači prostřednictvím kódu programu vytvořili položku jednotlivé registrace pro zařízení TPN a volitelně vytvořili simulované zařízení TPM a pomocí služby Azure IoT Hub Device Provisioning jste ho zřídili pro své centrum IoT. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal.

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)

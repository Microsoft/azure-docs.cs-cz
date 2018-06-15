---
title: Registrace zařízení X.509 do služby Azure Device Provisioning pomocí Pythonu | Microsoft Docs
description: Rychlý start Azure – Registrace zařízení X.509 do služby Azure IoT Hub Device Provisioning pomocí sady SDK služby zřizování pro Python
author: bryanla
ms.author: bryanla
ms.date: 01/25/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: c677bece27d011c5618845d950dd87e5b0e6bd06
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629333"
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Registrace zařízení X.509 do služby IoT Hub Device Provisioning pomocí sady SDK služby zřizování pro Python
[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Tyto kroky ukazují, jak prostřednictvím kódu programu zaregistrovat skupinu simulovaných zařízení X.509 do služby Azure IoT Hub Device Provisioning pomocí sady [SDK služby zřizování pro Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) a ukázkové aplikace v Pythonu. Přestože sada SDK služby pro Javu funguje na počítačích s Windows i Linuxem, tento článek provádí procesem registrace s použitím vývojového počítače s Windows.

Než budete pokračovat, nezapomeňte [nastavit službu IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md).

> [!NOTE]
> Tento rychlý start podporuje pouze **skupinu registrací**. Na **jednotlivé registraci** prostřednictvím sady _SDK služby zřizování pro Python_ se pracuje.
> 

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Příprava prostředí 

1. Stáhněte a nainstalujte [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnných prostředí pro konkrétní platformu. 

1. Zvolte jednu z následujících možností:

    - Sestavení a kompilace sady **SDK služby Azure IoT pro Python**. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) sestavte balíčky Pythonu. Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba nainstalovat také [balíček distribuovatelných součástí Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145).

    - [Instalace nebo upgrade nástroje *pip*, což je systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/), a instalace balíčku pomocí následujícího příkazu:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Potřebujete soubor .pem obsahující certifikát X.509 zprostředkující nebo kořenové certifikační autority, který je už uložený a ověřený ve vaší službě zřizování. Sada **SDK služby Azure IoT pro jazyk C** obsahuje nástroje, které vám můžou pomoci vytvořit řetěz certifikátů X.509, nahrát kořenový nebo zprostředkující certifikát z tohoto řetězu a ověřit certifikát provedením testování vlastnictví pomocí této služby. Pokud chcete využít tyto nástroje, naklonujte na svůj počítač sadu [SDK služby Azure IoT pro jazyk C](https://github.com/Azure/azure-iot-sdk-c) a postupujte podle kroků v souboru [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).


## <a name="modify-the-python-sample-code"></a>Úprava vzorového kódu Pythonu

Tato část ukazuje, jak do vzorového kódu přidat podrobnosti o zřizování vašeho zařízení X.509. 

1. Pomocí textového editoru vytvořte nový soubor **EnrollmentGroup.py**.

1. Na začátek souboru **EnrollmentGroup.py** přidejte následující příkazy `import` a proměnné. Pak nahraďte `dpsConnectionString` vaším připojovacím řetězcem, který najdete v části **Zásady sdíleného přístupu** ve vaší službě **Device Provisioning** na webu **Azure Portal**. Zástupný certifikát nahraďte certifikátem, který jste vytvořili dříve v části [Příprava prostředí](quick-enroll-device-x509-python.md#prepareenvironment). Nakonec vytvořte jedinečné ID registrace `registrationid` a ujistěte se, že se skládá pouze z malých alfanumerických znaků a pomlček.  
   
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

1. Otevřete příkazový řádek a spusťte skript.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

1. Sledujte ve výstupu úspěšnou registraci.

1. Přejděte k vaší službě zřizování na webu Azure Portal. Klikněte na **Správa registrací**. Všimněte si, že se na kartě **Skupiny registrací** zobrazí vaše skupina zařízení X.509 s dříve vytvořeným názvem `registrationid`. 

    ![Ověření úspěšné registrace X.509 na portálu](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete prozkoumat ukázku služby v Javě, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Javě.
1. Zavřete na svém počítači okno _Generátoru certifikátů X.509_.
1. Přejděte k vaší službě Device Provisioning na webu Azure Portal, klikněte na **Správa registrací** a pak vyberte kartu **Skupiny registrací**. Vyberte *NÁZEV SKUPINY* pro zařízení X.509, která jste zaregistrovali v rámci tohoto rychlého startu, a klikněte na tlačítko **Odstranit** v horní části okna.  


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste do své služby Device Provisioning zaregistrovali skupinu simulovaných zařízení X.509. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)

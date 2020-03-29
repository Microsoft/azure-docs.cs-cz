---
title: Jak používat automatické zřizování služby Azure IoT Hub Deviceprovisioning Service k registraci MXChip IoT DevKit s IoT Hub | Dokumenty společnosti Microsoft
description: Jak používat automatické zřizování zařízení Služby Azure IoT Hub (DPS) k registraci MXChip IoT DevKit s IoT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975648"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Automatické zřizování služby Azure IoT Hub Deviceprovisioning Service k registraci MXChip IoT DevKit s IoT Hubem

Tento článek popisuje, jak používat [automatické zřizování](concepts-auto-provisioning.md)služby Azure IoT Hub DeviceProvisioning , k registraci MXChip IoT DevKit pomocí služby Azure IoT Hub. V tomto kurzu se naučíte:

* Nakonfigurujte globální koncový bod služby Device Provisioning na zařízení.
* Ke generování certifikátu X.509 použijte jedinečný tajný klíč zařízení (UDS).
* Zaregistrujte jednotlivé zařízení.
* Ověřte, zda je zařízení zaregistrované.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je all-in-one Arduino-kompatibilní deska s bohatými periferiemi a senzory. Můžete vyvíjet pro něj pomocí [Azure IoT Device Workbench](https://aka.ms/iot-workbench) nebo [Azure IoT Tools](https://aka.ms/azure-iot-tools) rozšíření pack v Kódu Visual Studia. DevKit je dodáván s rostoucím [katalogem projektů,](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) který vás bude řídit pomocí prototypu řešení Internetu věcí (IoT), která využívají služeb Azure.

## <a name="before-you-begin"></a>Než začnete

Postup v tomto kurzu můžete provést nejprve následujícím úkolem:

* Nakonfigurujte Wi-Fi devKitu a připravte si vývojové prostředí podle kroků části "Příprava vývojového prostředí" v [části Connect IoT DevKit AZ3166 do Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Upgradujte na nejnovější firmware (1.3.0 nebo novější) pomocí výukového programu [pro firmware Update DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)
* Vytvořte a propojte službu IoT Hub s instancí služby Zřizování zařízení podle kroků v [části Nastavení služby zřizování zařízení služby IoT Hub na webu Azure Portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Otevřít ukázkový projekt

1. Ujistěte se, že váš IoT DevKit **není připojený** k počítači. Nejprve spusťte kód VS a potom připojte devkit k počítači.

1. Kliknutím `F1` otevřete paletu příkazů, zadejte a vyberte **Azure IoT Device Workbench: Open Examples...**. Pak vyberte **IoT DevKit** jako nástěnku.

1. Na stránce Příklady pracovní plochy IoT najděte **registraci zařízení pomocí DPS** a klikněte na **Otevřít ukázku**. Potom vybere výchozí cestu ke stažení ukázkového kódu.
    ![Otevřít vzorek](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Uložení jedinečného tajného klíče zařízení do úložiště zabezpečení zařízení

Automatické zřizování lze nakonfigurovat na zařízení na základě [mechanismu ověřování](concepts-security.md#attestation-mechanism)zařízení . MXChip IoT DevKit používá [modul pro kompozici identity zařízení](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) ze [skupiny Trusted Computing Group](https://trustedcomputinggroup.org). **Unikátní tajný klíč zařízení** (UDS) uložený v bezpečnostním čipu STSAFE[(STSAFE-A100)](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)na DevKitu se používá ke generování jedinečného [certifikátu X.509](concepts-security.md#x509-certificates)zařízení . Certifikát se později používá pro proces registrace ve službě Device Provisioning a při registraci za běhu.

Typický UDS je řetězec 64 znaků, jak je vidět v následující ukázce:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Uložení uds na DevKit:

1. V kódu VS klikněte na stavový řádek a vyberte port COM pro DevKit.
  ![Vybrat port COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. Na devKitu podržte **tlačítko A**, stiskněte a uvolněte tlačítko **reset** a poté uvolněte **tlačítko A**. DevKit přejde do konfiguračního režimu.

1. Klepnutím `F1` otevřete paletu příkazů, zadejte a vyberte **pracovní plocha zařízení Azure IoT: Konfigurace nastavení zařízení > řetězce jedinečného zařízení (UDS)** konfigurace.
  ![Konfigurace uds](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Poznamenejte si generovaný řetězec UDS. Budete ji potřebovat ke generování certifikátu X.509. Poté `Enter`stiskněte klávesu .
  ![Kopírování uds](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Z oznámení ověřte, že ust s. byl úspěšně nakonfigurován na stsafe.
  ![Konfigurace úspěchu uds](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Alternativně můžete nakonfigurovat UDS přes sériový port pomocí nástrojů, jako je Putty. Postupujte [takto: Použijte konfigurační režim.](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aktualizace globálního koncového bodu zařízení a oboru ID

V kódu zařízení je třeba zadat [koncový bod zřizování zařízení](/azure/iot-dps/concepts-service#device-provisioning-endpoint) a obor ID, abyste zajistili izolaci klienta.

1. Na webu Azure Portal vyberte podokno **Přehled** služby Zřizování zařízení a poznamenejte si hodnoty **globálního koncového bodu zařízení** a oboru **ID.**
  ![Globální koncový bod a obor ID služby zřizování zařízení](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Otevřete **soubor DevKitDPS.ino**. Najděte `[Global Device Endpoint]` a `[ID Scope]` nahraďte a s hodnotami, které jste právě poznamenali.
  ![Koncový bod služby zřizování zařízení](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Vyplňte `registrationId` proměnnou v kódu. Je povolena pouze alfanumerická, malá a pomlčka s maximálně 128 znaky. Také poznamenal hodnotu.
  ![ID registrace](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klikněte na `F1`, zadejte a vyberte **Pracovní plocha zařízení Azure IoT: Nahrát kód zařízení**. Začne sestavovat a nahrávat kód do DevKitu.
  ![Nahrávání zařízení](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generovat certifikát X.509

[Atestační mechanismus](/azure/iot-dps/concepts-device#attestation-mechanism) používaný tímto vzorkem je certifikát X.509. Ke generování je třeba použít nástroj.

1. V kódu VS `F1`klikněte na , zadejte a vyberte **Otevřít nový terminál,** chcete-li otevřít okno terminálu.

1. Spustit `dps_cert_gen.exe` `tool` ve složce.

1. Zadejte zkompilované `..\.build\DevKitDPS`umístění binárního souboru jako . Pak vložte **UDS** a **registrationId** jste právě poznamenal. 
  ![Generovat X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Certifikát `.pem` X.509 generuje ve stejné složce.
  ![Soubor X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Vytvoření položky registrace zařízení

1. Na webu Azure Portal otevřete službu Device Provision Service, přejděte do části Spravovat registrace a klikněte na **Přidat individuální registraci**.
  ![Přidání individuální registrace](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Chcete-li nahrát vygenerovaný soubor, klepněte na ikonu souboru `.pem` vedle **souboru Pem nebo CER.**
  ![Nahrát .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Ověření registrace DevKitu v Azure IoT Hubu

Stiskněte tlačítko **Obnovit** na devKitu. Měli byste vidět **DPS Connected!** na obrazovce DevKit. Po restartování zařízení proběhne následující akce:

1. Zařízení odešle žádost o registraci do vaší služby Device Provisioning.
1. Služba Zřizování zařízení odešle zpět výzvu k registraci, na kterou vaše zařízení reaguje.
1. Při úspěšné registraci služba Zřizování zařízení odešle identifikátor URI služby IoT Hub, ID zařízení a šifrovaný klíč zpět do zařízení.
1. Klientská aplikace služby IoT Hub na zařízení se připojí k vašemu rozbočovači.
1. Při úspěšném připojení k rozbočovači se zařízení zobrazí v Průzkumníku zařízení centra IoT.
  ![Zařízení registrováno](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [časté dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)k Iot DevKit nebo se obraťte na následující kanály pro podporu:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili bezpečně zaregistrovat zařízení do služby zřizování zařízení pomocí modulu pro kompozici identity zařízení, aby se zařízení mohlo automaticky zaregistrovat pomocí služby Azure IoT Hub. 

Stručně řečeno, jste se naučili, jak:

> [!div class="checklist"]
> * Nakonfigurujte globální koncový bod služby Device Provisioning na zařízení.
> * Ke generování certifikátu X.509 použijte jedinečný tajný klíč zařízení.
> * Zaregistrujte jednotlivé zařízení.
> * Ověřte, zda je zařízení zaregistrované.

Přečtěte si, jak [vytvořit a zřídit simulované zařízení](./quick-create-simulated-device.md).


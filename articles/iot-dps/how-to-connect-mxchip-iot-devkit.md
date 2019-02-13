---
title: Jak používat Azure IoT Hub Device Provisioning Service automatického zřizování k registraci ve službě IoT Hub MXChip IoT DevKit | Dokumentace Microsoftu
description: Jak používat Azure IoT Hub Device Provisioning Service automatického zřizování k MXChip IoT DevKit registraci ve službě IoT Hub.
author: liydu
ms.author: liydu
ms.date: 12/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 07614147189732223f972c1f66bb6562280d3f39
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118356"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Použití Azure IoT Hub Device Provisioning Service automatického zřizování k registraci ve službě IoT Hub MXChip IoT DevKit

Tento článek popisuje, jak používat Azure IoT Hub Device Provisioning Service [automatického zřizování](concepts-auto-provisioning.md), k registraci MXChip IoT DevKit ve službě Azure IoT Hub. V tomto kurzu se naučíte:

* Nakonfigurujte globální koncový bod služby Device Provisioning na zařízení.
* Jedinečná zařízení tajný klíč (UD) slouží k vytvoření certifikátu X.509.
* Registrace k jednotlivým zařízením.
* Ověřte, že je zařízení zaregistrované.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je kompatibilní s Arduino deska All-in-one bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj pomocí [Workbench zařízení Azure IoT](https://aka.ms/iot-workbench) nebo [nástroje Azure IoT](https://aka.ms/azure-iot-tools) balíček rozšíření pro Visual Studio Code. DevKit se dodává s stále se rozšiřující [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) vodítko řešení Internetu věcí (IoT) prototypu, které budou využívat služeb Azure.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto kurzu, proveďte následující úlohy:

* Příprava vašich DevKit podle postupu v [připojení IoT DevKit AZ3166 se do služby Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Upgrade na nejnovější firmware (1.3.0 nebo novější) se [DevKit aktualizace firmwaru](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) kurzu.
* Vytvořit a propojit službu IoT Hub s instancí služby Device Provisioning pomocí následujících kroků v [nastavit IoT Hub Device Provisioning pomocí webu Azure portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Otevřete ukázkový projekt

1. Ujistěte se, že je vaše IoT DevKit **Nepřipojeno** k vašemu počítači. Nejprve spusťte VS Code a DevKit připojte se k počítači.

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Otevřít příklady...** . Potom vyberte **IoT DevKit** jako panel.

1. Na stránce IoT aplikace Workbench příklady najít **registrace zařízení ve službě Device Provisioning** a klikněte na tlačítko **otevřít ukázkové**. Potom vybere výchozí cestu pro stažení ukázkového kódu.
    ![Otevřete ukázkový](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Uložit jedinečný tajný klíč zařízení na zařízení zabezpečení úložiště

Automatické zřizování lze konfigurovat pro zařízení na zařízení na základě [mechanismus ověřování](concepts-security.md#attestation-mechanism). Používá MXChip IoT DevKit [zařízení Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [Trusted Computing Group](https://trustedcomputinggroup.org). A **jedinečný tajný klíč zařízení** (UD) uloží do čipu zabezpečení STSAFE ([STSAFE A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) na DevKit se používá ke generování zařízení uživatele jedinečné [certifikát X.509](concepts-security.md#x509-certificates). Certifikát se později používá pro registraci ve službě Device Provisioning a během registrace v době běhu.

Typické aktualizačními doménami není řetězcový 64 znaků, jak je znázorněno v následujícím příkladu:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Uložit aktualizačními doménami DevKit:

1. V nástroji VS Code klikněte na stavový řádek vyberte COM port DevKit.
  ![Vybrat Port COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. U DevKit, podržte klávesu **tlačítko A**, push a verzi **resetování** tlačítko a pak uvolněte **tlačítko A**. Vaše DevKit přejde do režimu konfigurace.

1. Klikněte na tlačítko `F1` otevřete paletu příkazů, zadejte a vyberte **Workbench zařízení Azure IoT: Konfigurace nastavení zařízení... > Konfigurace jedinečný řetězec (UD)**.
  ![Konfigurace aktualizačními doménami](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Poznamenejte si vygenerovaný řetězec aktualizačními doménami. Potřebujete ji k vygenerování certifikátu X.509. Stiskněte klávesu `Enter`.
  ![Zkopírujte aktualizačními doménami](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Potvrzení z oznámení, že aktualizačními doménami se už nakonfigurovala v STSAFE úspěšně.
  ![Konfigurace aktualizačními doménami úspěch](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Alternativně můžete nakonfigurovat aktualizačními doménami přes sériový port pomocí nástrojů, jako je například Putty. Postupujte podle [konfigurace režimu použít](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) Uděláte to tak.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aktualizovat globální koncový bod zařízení a ID oboru

V zařízení kód, je třeba zadat [Device provisioning koncový bod](/azure/iot-dps/concepts-service#device-provisioning-endpoint) a rozsah ID zajistit izolaci klientů.

1. Na webu Azure Portal, vyberte **přehled** podokně vaší služby Device Provisioning a poznamenejte **globální koncový bod zařízení** a **rozsah ID** hodnoty.
  ![Zřizování globální koncový bod služby a rozsah ID zařízení](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Otevřít **DeKitDPS.ino**. Najít a nahradit `[Global Device Endpoint]` a `[ID Scope]` hodnotami, které právě poznamenali.
  ![Koncový bod služby zřizování zařízení](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Zadejte `registrationId` proměnné v kódu. Pouze alfanumerické znaky, malá písmena, a je povolen kombinaci spojovník nesmí být delší než 128 znaků. Také jste si poznamenali si hodnotu.
  ![ID registrace](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klikněte na tlačítko `F1`zadejte a vyberte **Workbench zařízení Azure IoT: Nahrát kód zařízení**. Spustí sestavení a publikování kódu DevKit.
  ![Nahrávání zařízení](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Vygenerování certifikátu X.509

[Mechanismus ověřování](/azure/iot-dps/concepts-device#attestation-mechanism) používané v tomto příkladu je certifikát X.509. Budete muset použít nástroj, který k jeho vygenerování.

> [!NOTE]
> Generátor certifikátů X.509 pouze teď podporuje Windows.

1. V nástroji VS Code, klikněte na tlačítko `F1`zadejte a vyberte **otevřete nový terminálu** otevřete okno terminálu.

1. Spustit `dps_cert_gen.exe` v `tool` složky.

1. Zadejte umístění kompilované binární soubor jako `..\.build\DevKitDPS`. Vložte **aktualizačními doménami** a **registrationId** právě poznamenali. 
  ![Generovat X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. A `.pem` certifikát X.509 generuje ve stejné složce.
  ![Soubor X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Vytvoření položky registrace zařízení

1. Na webu Azure Portal, otevřete svou službu zřizování zařízení, přejděte do části registrace spravovat a klikněte na **přidat jednotlivou registraci**.
  ![Přidání jednotlivé registrace](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Klikněte na ikonu souboru vedle **soubor .pem nebo .cer primárního certifikátu** k nahrání `.pem` soubor generovaný.
  ![Nahrát .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Ověřte, zda že devkit zaregistrován u služby Azure IoT Hub

Stisknutím klávesy **resetování** tlačítko na vaše DevKit. Měli byste vidět **distribučních bodů připojení!** na obrazovce DevKit. Po restartování zařízení, provedou se následující akce:

1. Zařízení odešle žádost o registraci do vaší služby Device Provisioning.
1. Služby Device Provisioning odešle zpět výzvu registrace, ke kterému se zařízení zareaguje.
1. Na úspěšnou registraci služby Device Provisioning odešle URI centra IoT, ID zařízení a šifrovaný klíč zpět do zařízení.
1. Klientská aplikace IoT Hub na zařízení se připojí k vašemu centru.
1. Na úspěšné připojení k rozbočovači zobrazí se zařízení v IoT Hub Device Explorer.
  ![Zaregistrované zařízení](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na Iot DevKit [nejčastějších dotazech týkajících se](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), nebo se obraťte na podporu následujících kanálů:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, při registraci zařízení do služby Device Provisioning pomocí Kompoziční modul Identity zařízení tak, aby automaticky zaregistrovat zařízení s Azure IoT Hub. 

Stručně řečeno, jste zjistili, jak:

> [!div class="checklist"]
> * Nakonfigurujte globální koncový bod služby Device Provisioning na zařízení.
> * Tajný kód jedinečná zařízení slouží k vytvoření certifikátu X.509.
> * Registrace k jednotlivým zařízením.
> * Ověřte, že je zařízení zaregistrované.

Zjistěte, jak [vytvoření a zřízení simulovaného zařízení](./quick-create-simulated-device.md).


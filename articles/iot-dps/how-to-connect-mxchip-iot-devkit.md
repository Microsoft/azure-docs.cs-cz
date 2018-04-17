---
title: Jak používat Azure IoT Hub zařízení zřizování služby Automatické zřizování k registraci MXChip IoT DevKit službou IoT Hub | Microsoft Docs
description: Jak používat Azure IoT Hub zařízení zřizování služby Automatické zřizování k registraci MXChip IoT DevKit službou IoT Hub.
services: iot-dps
keywords: ''
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d60c5766b22e31c33d0dd4a743fa297470109ac6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Použít automatické zřizování Azure IoT Hub zařízení zřizování služby k registraci MXChip IoT DevKit službou IoT Hub

Tento článek popisuje, jak pomocí Azure IoT Hub zařízení zřizování služby [automatické zřizování](concepts-auto-provisioning.md), zaregistrujte MXChip IoT DevKit službou Azure IoT Hub. V tomto kurzu se naučíte:

* Nakonfigurujte globální koncový bod zařízení zřizování služby v zařízení.
* Pomocí tajného klíče jedinečné zařízení (UDS) vygenerujte certifikát X.509.
* Zaregistrovat k jednotlivým zařízením.
* Ověřte, že je zařízení zaregistrované.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je panelu vše v jednom Arduino kompatibilní s bohatou periferní zařízení a senzory. Můžete vyvíjet pro něj pomocí [Visual Studio Code rozšíření pro Arduino](https://aka.ms/arduino). DevKit se dodává s rozšiřujících se [projekty katalogu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) do Průvodce řešení Internetu věcí (IoT) prototypu, které využívat výhod služeb Azure.

## <a name="before-you-begin"></a>Než začnete

Pokud chcete provést kroky v tomto kurzu, proveďte následující úkoly:

* Příprava vašeho DevKit podle kroků v [připojit IoT DevKit AZ3166 do služby Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Upgrade na nejnovější firmware (1.3.0 nebo novější) s [DevKit aktualizace firmwaru](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) kurzu.
* Vytvoření a propojení zařízení zřizování podle kroků v instanci služby IoT Hub [nastavení IoT Hub zařízení zřizování služby pomocí portálu Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Sestavení a nasazení automatické zřizování softwaru registrace zařízení

Pro připojení k zařízení zřizování instance služby, kterou jste vytvořili DevKit:

1. Na portálu Azure vyberte **přehled** podokně zařízení zřizování služby a Poznámka dolů **koncový bod globální zařízení** a **ID oboru** hodnoty.
  ![Distribučních bodů globální koncový bod a ID oboru](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Zajistěte, aby byla `git` v počítači nainstalován a zda je přidána do proměnné prostředí, který je přístupný pro příkazové okno. V tématu [softwaru volnost ochranou životního prostředí na nástrojích klienta Git](https://git-scm.com/download/) mít nainstalovanou nejnovější verzi.

3. Otevřete příkazový řádek. Naklonujte úložiště GitHub pro zařízení zřizování služby ukázkový kód:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Otevřete Visual Studio Code, připojte DevKit k počítači a pak otevřete složku, která obsahuje kód, který jste naklonovali.

5. Otevřete **DevKitDPS.ino**. Najít a nahradit `[Global Device Endpoint]` a `[ID Scope]` s hodnotami právě uvedených dolů.
  ![Koncový bod distribučních bodů](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) můžete nechat **registrationId** prázdné. Aplikace generuje za vás na základě verze MAC adres a firmware. Pokud chcete přizpůsobit ID registrace, musíte používat jenom alfanumerické znaky a malých písmen a pomlčku kombinací s maximálně 128 znaků. Další informace najdete v tématu [spravovat registrace zařízení pomocí portálu Azure](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Použít rychlé otevřete v produktu VS Code (Windows: `Ctrl+P`, systému macOS: `Cmd+P`) a typ *úkolů zařízení – nahrání* sestavit a odeslat do DevKit kód.

7. Ve výstupním okně zobrazuje, zda byla úloha úspěšná.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Uložení tajný klíč jedinečných zařízení na čip TPM STSAFE zabezpečení

Automatické zřizování můžete nakonfigurovat na zařízení na zařízení na základě [ověření mechanismu](concepts-security.md#attestation-mechanism). Používá MXChip IoT DevKit [modul složení Identity zařízení](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [Trusted Computing Group](https://trustedcomputinggroup.org). A *tajný klíč jedinečný zařízení* (UDS) uložené v zabezpečení služby STSAFE čip TPM na DevKit slouží ke generování zařízení je jedinečný [certifikát X.509](concepts-security.md#x509-certificates). Certifikát se později používá pro proces registrace v zařízení zřizování služby a během registrace za běhu.

Tajný klíč typické jedinečný zařízení je 64 znaků řetězce, jak je vidět v následující ukázce:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Každé dva znaky se používá jako hodnota šestnáctkově výpočtu zabezpečení. V předchozím příkladu je přeložen na UDS: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Na DevKit uložení tajný klíč jedinečný zařízení:

1. Otevřete sériové monitorování pomocí nástroje, jako je například Putty. V tématu [konfigurace režimu použít](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) podrobnosti.

2. S DevKit připojené k počítači, podržte klávesu **A** tlačítko a potom stiskněte klávesu a verzí **resetovat** tlačítko Přejít do režimu konfigurace. Na obrazovce zobrazuje DevKit ID a konfigurace.

3. Odebere vzorek UDS řetězec a změnit jeden nebo více znaků jiné hodnoty mezi `0` a `f` pro vlastní UDS.

4. V okně Sledování sériového portu zadejte *set_dps_uds [your_own_uds_value]* a klávesy Enter.
  > [!NOTE]
  > Například pokud nastavíte tak, že změníte poslední dva znaky, které mají vlastní UDS `f`, je třeba zadat příkaz takto: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Bez zavření okna sériové sledování, stiskněte **resetovat** na DevKit tlačítko.

6. Poznamenejte si **adresa MAC DevKit** a **verzi firmwaru DevKit** hodnoty.
  ![Verze firmwaru](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Vygenerujte certifikát X.509

### <a name="windows"></a>Windows

1. Otevřete Průzkumníka souborů a přejděte do složky, která obsahuje zařízení zřizování služby ukázkový kód, který jste dříve klonovat. V **.build** složky, najít a zkopírujte **DPS.ino.bin** a **DPS.ino.map** do složky, která obsahuje kód.
  ![Generované soubory](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Pokud jste změnili `built.path` konfigurace pro Arduino do jiné složky, budete muset najít tyto soubory ve složce, která jste nakonfigurovali.

2. Vložte tyto dva soubory do **nástroje** složky na stejné úrovni s **.build** složky.

3. Run **dps_cert_gen.exe**. Postupujte podle pokynů zadejte vaše **UDS**, **adresa MAC** pro DevKit a **verzi firmwaru** k vygenerování certifikátu X.509.
  ![Spustit distribučních bodů. cert gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Po vygenerování certifikátu X.509 **.pem** certifikát je uložit do stejné složky.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Vytvořit záznam registrace zařízení v zařízení zřizování služby

1. Na portálu Azure přejděte na instanci služby zřizování zařízení. Vyberte **spravovat registrace**a pak vyberte **jednotlivých registrace** kartě. ![Jednotlivé registrace](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Vyberte **Přidat**.

3. Na panelu "Přidat registrace":
   - Vyberte **X.509** pod **mechanismus**
   - Klikněte na tlačítko "Vyberte soubor" v části **soubor .pem nebo .cer primární certifikátu**
   - v dialogovém okně Otevřít přejděte do a nahrajte **.pem** certifikátů, které jste právě vygenerovali
   - Ponechejte zbývající jako výchozí nastavení a klikněte na tlačítko **uložit**

   ![Nahrání certifikátu](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

## <a name="start-the-devkit"></a>Spuštění DevKit

1. Otevřete VS Code a sériové monitorování.

2. Stiskněte **resetovat** na vaše DevKit tlačítko.

Zobrazí DevKit počáteční registraci službou zařízení zřizování.

![Výstupní kód VS](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Ověří, zda DevKit registrován službou Azure IoT Hub

Po spuštění zařízení, proveďte následující akce místní:

1. Zařízení odesílá žádost o registraci zařízení zřizováním služby.
2. Zřizování služby zařízení odesílá zpět registraci výzvy, ke kterému odpovídá zařízení.
3. Na úspěšnou registraci zařízení zřizování služby odesílá identifikátor URI centra IoT, ID zařízení a zašifrovaný klíč zpět do zařízení.
4. Připojí se do vašeho centra IoT Hub klientská aplikace na zařízení.
5. Úspěšné připojení k centru najdete v části zařízení zobrazí v Průzkumníku zařízení IoT Hub.
  ![Zařízení registrovaná](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Změňte ID zařízení

ID zařízení výchozí registrované službou Azure IoT Hub je *AZ3166*. Pokud chcete změnit ID, postupujte podle pokynů v [přizpůsobit ID zařízení](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

Pokud narazíte na problémy, podívejte se na Iot DevKit [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), nebo se obraťte na podporu následující kanály:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli registrace zařízení do zařízení zřizování služby pomocí modulu složení Identity zařízení tak, aby automaticky zaregistrovat zařízení s Azure IoT Hub. 

V souhrn, jste zjistili, jak:

> [!div class="checklist"]
> * Nakonfigurujte globální koncový bod zařízení zřizování služby v zařízení.
> * Pomocí jedinečné zařízení tajný klíč vygenerujte certifikát X.509.
> * Zaregistrovat k jednotlivým zařízením.
> * Ověřte, že je zařízení zaregistrované.

Zjistěte, jak [vytvořit a zřídit simulované zařízení](./quick-create-simulated-device.md).


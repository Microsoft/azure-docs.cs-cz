---
title: Jak používat Azure IoT Hub Device Provisioning Service automatického zřizování k registraci ve službě IoT Hub MXChip IoT DevKit | Dokumentace Microsoftu
description: Jak používat Azure IoT Hub Device Provisioning Service automatického zřizování k MXChip IoT DevKit registraci ve službě IoT Hub.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 300bde27f956b449d1e0e73f7efb54a13df27b0c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145661"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Použití Azure IoT Hub Device Provisioning Service automatického zřizování k registraci ve službě IoT Hub MXChip IoT DevKit

Tento článek popisuje, jak používat Azure IoT Hub Device Provisioning Service [automatického zřizování](concepts-auto-provisioning.md), k registraci MXChip IoT DevKit ve službě Azure IoT Hub. V tomto kurzu se naučíte:

* Nakonfigurujte globální koncový bod službou device provisioning na zařízení.
* Jedinečná zařízení tajný klíč (UD) slouží k vytvoření certifikátu X.509.
* Registrace k jednotlivým zařízením.
* Ověřte, že je zařízení zaregistrované.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) je kompatibilní s Arduino deska All-in-one bohaté periferní zařízení a senzorů. Můžete vyvíjet pro něj s použitím [rozšíření Visual Studio Code pro Arduino](https://aka.ms/arduino). DevKit se dodává s stále se rozšiřující [katalogu projektů](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) vodítko řešení Internetu věcí (IoT) prototypu, které budou využívat služeb Azure.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto kurzu, proveďte následující úlohy:

* Příprava vašich DevKit podle postupu v [připojení IoT DevKit AZ3166 se do služby Azure IoT Hub v cloudu](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Upgrade na nejnovější firmware (1.3.0 nebo novější) se [DevKit aktualizace firmwaru](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) kurzu.
* Vytvoření a propojení služby IoT Hub device provisioning pomocí následujících kroků v instanci služby [nastavit IoT Hub Device Provisioning pomocí webu Azure portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Sestavování a nasazování automatického zřizování software pro registraci zařízení

Připojení DevKit ke zřizování instance služby, kterou jste vytvořili zařízení:

1. Na webu Azure Portal, vyberte **přehled** podokno zařízení zřízení služby a Všimněte si dolů **globální koncový bod zařízení** a **rozsah ID** hodnoty.
  ![DPS globální koncový bod a rozsah ID](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Ujistěte se, že máte `git` na vašem počítači nainstalovaný a zda je přidána do proměnné prostředí přístupné pro příkazové okno. Zobrazit [nástrojů klienta Git Software Freedom Conservancy](https://git-scm.com/download/) mít nainstalovanou nejnovější verzi.

3. Otevřete příkazový řádek. Naklonujte úložiště GitHub pro zřizování ukázkového kódu služby zařízení:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Otevřít Visual Studio Code, připojení DevKit do vašeho počítače a poté otevřete složku, která obsahuje kód, který jste naklonovali.

5. Otevřít **DevKitDPS.ino**. Najít a nahradit `[Global Device Endpoint]` a `[ID Scope]` hodnotami, které právě poznamenali.
  ![Koncový bod DPS](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) můžete nechat **registrationId** prázdné. Aplikace vytvoří za vás na základě verze MAC adres a firmware. Pokud chcete přizpůsobit ID registrace, musíte použít jenom alfanumerické znaky, malá písmena a spojovníky kombinace s délkou maximálně 128 znaků. Další informace najdete v tématu [Správa registrací zařízení pomocí webu Azure portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Použijte okno rychlého otevření v nástroji VS Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) a typ *úlohy zařízení – nahrání* k sestavení a odešlete kód DevKit.

7. V okně výstupu se zobrazí, zda byla úloha úspěšná.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Uložit tajného kódu jedinečných zařízení čipu STSAFE zabezpečení

Automatické zřizování lze konfigurovat pro zařízení na zařízení na základě [mechanismus ověřování](concepts-security.md#attestation-mechanism). Používá MXChip IoT DevKit [zařízení Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [Trusted Computing Group](https://trustedcomputinggroup.org). A *tajný klíč jedinečná zařízení* (UD) uloží do security STSAFE čip TPM na DevKit se používá ke generování zařízení uživatele jedinečné [certifikát X.509](concepts-security.md#x509-certificates). Certifikát se později používá pro proces registrace do služby zřizování zařízení a během registrace v době běhu.

Tajný kód typické jedinečná zařízení je 64 znaků řetězec, jak je znázorněno v následujícím příkladu:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Řetězec je rozdělený na páry znaků, které se používají při výpočtu zabezpečení. V předchozím příkladu je přeložen na aktualizačními doménami: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Uložte tajný klíč jedinečných zařízení na DevKit:

1. Otevřete monitorování sériového portu, pomocí nástroje, jako je například Putty. Zobrazit [konfigurace režimu použít](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) podrobnosti.

2. S DevKit připojíte k počítači, podržte stisknutou klávesu **A** tlačítko a potom stiskněte klávesu a verzi **resetování** tlačítko vstoupit do režimu konfigurace. Na obrazovce se zobrazí DevKit ID a konfigurace.

3. Je vzorek aktualizačními doménami řetězce a změnit jeden nebo více znaků do jiné hodnoty mezi `0` a `f` pro vlastní aktualizačními doménami.

4. V okně monitor sériového portu, zadejte *set_dps_uds [your_own_uds_value]* a stiskněte Enter.
  > [!NOTE]
  > Například pokud nastavíte tak, že změníte poslední dva znaky, které mají vlastní aktualizačními doménami `f`, budete muset zadat příkaz takto: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Bez zavření okna sériového portu monitorování, stiskněte **resetování** tlačítko na DevKit.

6. Poznamenejte si **adresa MAC DevKit** a **verze firmwaru DevKit** hodnoty.
  ![Verze firmwaru](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Generovat certifikát X.509

### <a name="windows"></a>Windows

1. Otevřete Průzkumníka souborů a přejděte do složky, který obsahuje vzorový kód služby, které jste naklonovali dříve zřizování zařízení. V **.build** složky, vyhledejte a zkopírujte **DPS.ino.bin** a **DPS.ino.map**.
  ![Generované soubory](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Pokud jste změnili `built.path` konfigurace pro Arduino do jiné složky, musíte najít tyto soubory ve složce, která jste nakonfigurovali.

2. Vložte tyto dva soubory do **nástroje** složky na stejné úrovni s **.build** složky.

3. Run **dps_cert_gen.exe**. Postupujte podle zobrazených výzev a zadejte vaše **aktualizačními doménami**, **adresa MAC** pro DevKit a **verze firmwaru** k vygenerování certifikátu X.509.
  ![Dps-cert-gen.exe spuštění](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Po vygenerování certifikátu X.509 **.pem** certifikát je uložen do stejné složky.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Vytvoření položky registrace zařízení do služby zřizování zařízení

1. Na webu Azure Portal přejděte k vaší instanci služby Device Provisioning. Vyberte **Správa registrací**a pak vyberte **jednotlivé registrace** kartu. ![Jednotlivé registrace](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Vyberte **Přidat**.

3. Na panelu "Přidat registraci":
   - Vyberte **X.509** pod **mechanismus**
   - Klikněte na možnost "Vybrat soubor" v části **soubor .pem nebo .cer primárního certifikátu**
   - v dialogovém okně Otevřít soubor, přejděte na a nahrát **.pem** certifikátů, které jste právě vygenerovali
   - Ponechejte zbývající jako výchozí a klikněte na tlačítko **uložit**

   ![Nahrání certifikátu](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > Pokud máte k chybě s touto zprávou:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Otevřít soubor certifikátu **.pem** jako text (otevřít v programu Poznámkový blok nebo libovolného textového editoru) a odstraňte řádky:
  >
  > `"-----BEGIN CERTIFICATE-----"` a `"-----END CERTIFICATE-----"`.
  >


## <a name="start-the-devkit"></a>Spustit DevKit

1. Spusťte VS Code a sériového portu monitorování.

2. Stisknutím klávesy **resetování** tlačítko na vaše DevKit.

DevKit počáteční registraci se zobrazí ve vaší službě zřizování zařízení.

![Výstup kódu VS](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Ověřte, že DevKit zaregistrován u služby Azure IoT Hub

Po spuštění zařízení, provedou se následující akce:

1. Zařízení odešle žádost o registraci do služby zřizování zařízení.
2. Službou device provisioning odešle zpět výzvu registrace, ke kterému se zařízení zareaguje.
3. Na úspěšnou registraci službou device provisioning odešle URI centra IoT, ID zařízení a šifrovaný klíč zpět do zařízení.
4. Klientská aplikace IoT Hub na zařízení se připojí k vašemu centru.
5. Na úspěšné připojení k rozbočovači zobrazí se zařízení v IoT Hub Device Explorer.
  ![Zaregistrované zařízení](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Změnit ID zařízení

Výchozí ID zařízení zaregistrované pomocí služby Azure IoT Hub je *AZ3166*. Pokud chcete změnit ID, postupujte podle pokynů v [vlastní ID zařízení](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na Iot DevKit [nejčastějších dotazech týkajících se](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), nebo se obraťte na podporu následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, registraci zařízení do služby zřizování pomocí Kompoziční modul Identity zařízení tak, aby automaticky zaregistrovat zařízení s Azure IoT Hub zařízení. 

Stručně řečeno, jste zjistili, jak:

> [!div class="checklist"]
> * Nakonfigurujte globální koncový bod službou device provisioning na zařízení.
> * Tajný kód jedinečná zařízení slouží k vytvoření certifikátu X.509.
> * Registrace k jednotlivým zařízením.
> * Ověřte, že je zařízení zaregistrované.

Zjistěte, jak [vytvoření a zřízení simulovaného zařízení](./quick-create-simulated-device.md).


---
title: Postup ověření vlastnictví pro certifikáty webu X.509 s Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Ověření certifikátů webu X.509 s vaší služby Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: afa4b3861e9fb7f91fd9f5d540353c5fad23efe0
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913610"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Postup ověření vlastnictví pro certifikáty webu X.509 s vaší služby Device Provisioning Service

Ověřené X.509 certifikační autoritu (CA) certifikát je certifikát certifikační Autority, který je nahraný a zaregistrované u zřizování služeb a náramků RFID prostřednictvím důkaz vlastnictví se službou. 

Důkaz vlastnictví zahrnuje následující kroky:
1. Získáte jedinečný ověřovací kód vygenerovaný služby zřizování pro váš certifikát X.509 certifikační Autority. Můžete to provést z portálu Azure portal.
2. Vytvořte certifikát pro ověření X.509 s ověřovací kód jako jeho předmět a podepsání certifikátu s privátním klíčem přidružené k vaší certifikátu webu X.509.
3. Nahrajte certifikát podepsaný držitelem ověření ke službě. Služba ověří ověřovací certifikát, který se má ověřit, s využitím veřejné části certifikátu certifikační Autority tedy prokázání, že máte k dispozici privátnímu klíči certifikátu certifikační Autority.

Ověřené certifikáty hrají důležitou roli při použití skupin pro registraci. Ověření vlastnictví certifikátu poskytuje další vrstvu zabezpečení tím, že zajišťuje, který uživatele nahrávajícího certifikátu je ve vlastnictví privátní klíč certifikátu. Ověření brání škodlivým actor pro analýzu sítě provoz z extrahování zprostředkující certifikát a vytvořit skupinu registrací ve své vlastní službě zřizování pomocí tohoto certifikátu, efektivně zneužitím vašich zařízení. Tím, že prokáže vlastnictví kořenové nebo zprostředkující certifikát v řetězu certifikátů, už prokázání, že máte oprávnění k generovat listové certifikáty pro zařízení, která se registruje jako součást této skupiny pro registraci. Z tohoto důvodu, kořenový nebo zprostředkující certifikát nakonfigurované ve skupině pro registraci musí být ověřený certifikát nebo musí shrnutí ověřený certifikát v řetězu certifikátů zařízení uvede během ověřování se službou. Další informace o skupinách registrace najdete v tématu [certifikáty X.509](concepts-security.md#x509-certificates) a [řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Zaregistrujte veřejnou část certifikátu X.509 a získat ověřovací kód

Zaregistrovat certifikát certifikační Autority ve vaší službě zřizování a získat ověřovací kód, který vám pomůže během důkaz vlastnictví, postupujte podle těchto kroků. 

1. Na webu Azure Portal, přejděte k vaší službě zřizování a otevřete **certifikáty** z nabídky na levé straně. 
2. Klikněte na tlačítko **přidat** přidat nový certifikát.
3. Zadejte popisný Zobrazovaný název pro váš certifikát. Vyhledejte soubor .cer nebo .pem, který představuje veřejnou část certifikátu X.509. Klikněte na **Odeslat**.
4. Jakmile obdržíte oznámení, že váš certifikát se úspěšně nahraje, klikněte na tlačítko **Uložit**.

    ![Nahrání certifikátu](./media/how-to-verify-certificates/add-new-cert.png)  

   Váš certifikát se zobrazí v **Průzkumník certifikátů** seznamu. Všimněte si, **stav** tento certifikát je *neověřené*.

5. Klikněte na certifikát, který jste přidali v předchozím kroku.

6. V **podrobnosti o certifikátu**, klikněte na tlačítko **vygenerovat ověřovací kód**.

7. Vytvoří službu zřizování **ověřovací kód** , můžete použít k ověření vlastnictví certifikátu. Zkopírujte kód do schránky. 

   ![Ověření certifikátu](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Digitálně podepisovat ověřovací kód k vytvoření ověřovacího certifikátu

Teď, budete muset přihlásit *ověřovací kód* s privátním klíčem přidružené k vaší certifikátu webu X.509, který generuje podpis. To se označuje jako [doklad o vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1) a má za následek podepsané ověřovací certifikát.

Společnost Microsoft poskytuje nástroje a ukázky, které vám pomůžou vytvořit certifikát podepsaný držitelem ověření: 

- **Azure IoT Hub C SDK** poskytuje Powershellu (Windows) a skripty Bash (Linux), vám pomůže vytvořit certifikační Autority a listové certifikáty pro vývoj a provádět testování vlastnictví pomocí ověřovacího kódu. Si můžete stáhnout [soubory](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) relevantní pro váš systém pracovní složky a postupujte podle pokynů [readme certifikáty certifikační Autority Správa](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) k provedení ověření vlastnictví certifikátu certifikační Autority. 
- **SDK Azure IoT Hub C#** obsahuje [Ukázka ověření certifikátu skupiny](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), které můžete provést ověření vlastnictví.
 
> [!IMPORTANT]
> Kromě provádění důkaz vlastnictví, skripty prostředí PowerShell a Bash uvedeném dříve také umožňují vytvářet kořenových certifikátů zprostředkující certifikáty a listové certifikáty, které slouží k ověřování a zřizování zařízení. Tyto certifikáty by měla sloužit pouze pro vývoj. Jejich byste nikdy neměli používat v produkčním prostředí. 

Využívají Bash a Powershellu skripty k dispozici v dokumentaci a sady SDK [OpenSSL](https://www.openssl.org/). Můžete také použít OpenSSL nebo jiných nástrojů třetích stran pomoct vám důkaz vlastnictví. Další informace o nástrojů, které jsou součástí sady SDK najdete v tématu [tom, jak použít nástroje poskytované v sadách SDK](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Nahrát podepsaný ověřovací certifikát

1. Výsledný podpis nahrajte jako ověřovací certifikát k vaší službě zřizování na portálu. V **podrobnosti o certifikátu** na webu Azure portal, použijte _Průzkumníka souborů_ ikonu vedle **soubor .pem nebo .cer ověřovacího certifikátu** pole, které chcete nahrát podepsané ověřovací certifikát z vašeho systému.

2. Po úspěšném nahrání certifikátu klikněte na tlačítko **ověřte**. **Stav** změn certifikát **_ověřeno_** v **Průzkumník certifikátů** seznamu. Klikněte na tlačítko **aktualizovat** Pokud neaktualizuje automaticky.

   ![Nahrajte certifikát ověření](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak vytvořit skupinu registrací pomocí portálu, najdete v článku [Správa registrací zařízení pomocí webu Azure portal](how-to-manage-enrollments.md).
- Další informace o tom, jak pomocí sady SDK pro služby můžete vytvořit skupinu registrací najdete v tématu [Správa registrací zařízení pomocí sady SDK pro služby](how-to-manage-enrollments-sdks.md).











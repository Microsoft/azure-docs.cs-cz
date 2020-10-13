---
title: Ověření certifikátů certifikační autority X. 509 pomocí Azure IoT Hub Device Provisioning Service
description: Postup kontroly vlastnictví certifikátů CA X. 509 pomocí Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 50e8dfd2998b50bfff7341e49ac4d0770c115166
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530858"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Jak v rámci služby Device Provisioning udělat důkaz o vlastnictví certifikátů certifikační autority X. 509

Ověřený certifikát certifikační autority (CA) X. 509 je certifikát certifikační autority, který se nahrál a zaregistroval ve vaší službě zřizování a který se dokončí pomocí ověření pomocí služby. 

Důkaz o vlastnictví zahrnuje následující kroky:
1. Získejte jedinečný ověřovací kód vygenerovaný službou zřizování pro certifikát vaší certifikační autority X. 509. Můžete to udělat na webu Azure Portal.
2. Vytvořte ověřovací certifikát X. 509 s ověřovacím kódem jako jeho subjektem a podepište certifikát s privátním klíčem přidruženým k vašemu certifikátu certifikační autority X. 509.
3. Nahrajte do služby certifikát pro ověření podepsaného ověřování. Služba ověřuje ověřovací certifikát pomocí veřejné části certifikátu certifikační autority, která má být ověřena, čímž prokáže, že jste držiteli privátního klíče certifikátu certifikační autority.

Ověřené certifikáty při používání skupin registrací hrají důležitou roli. Ověřování vlastnictví certifikátu poskytuje další vrstvu zabezpečení tím, že zajišťuje, že k odeslání certifikátu má k dispozici privátní klíč certifikátu. Ověřování brání škodlivému objektu actor sledovat provoz z extrakce zprostředkujícího certifikátu a pomocí tohoto certifikátu vytvořit skupinu registrací ve své vlastní službě zřizování a efektivně tak znestarost vaše zařízení. Prokázáním vlastnictví kořenového nebo zprostředkujícího certifikátu v řetězu certifikátů zjistíte, že máte oprávnění generovat listové certifikáty pro zařízení, která se budou registrovat jako součást této skupiny zápisu. Z tohoto důvodu musí být kořenový nebo zprostředkující certifikát nakonfigurovaný ve skupině pro registraci buď ověřený certifikát, nebo musí v řetězu certifikátů vyhledat ověřený certifikát, který zařízení prezentuje při ověřování ve službě. Další informace o ověřování certifikátů X. 509 najdete v tématu [certifikáty x. 509](concepts-x509-attestation.md) a [řízení přístupu zařízení ke službě zřizování pomocí certifikátů x. 509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrace veřejné části certifikátu X. 509 a získání ověřovacího kódu

Pokud chcete zaregistrovat certifikát certifikační autority se službou zřizování a získat ověřovací kód, který můžete použít při kontrole, postupujte podle těchto kroků. 

1. V Azure Portal přejděte do svojí služby zřizování a v nabídce na levé straně otevřete **certifikáty** . 
2. Kliknutím na **Přidat** přidejte nový certifikát.
3. Zadejte popisný zobrazovaný název certifikátu. Vyhledejte soubor. cer nebo. pem, který představuje veřejnou část certifikátu X. 509. Klikněte na **Odeslat**.
4. Jakmile dostanete oznámení, že se váš certifikát úspěšně nahrál, klikněte na **Uložit**.

    ![Nahrání certifikátu](./media/how-to-verify-certificates/add-new-cert.png)  

   Váš certifikát se zobrazí v seznamu **Průzkumník certifikátů** . Všimněte si, že **stav** tohoto certifikátu není *ověřen*.

5. Klikněte na certifikát, který jste přidali v předchozím kroku.

6. V **podrobnostech o certifikátu**klikněte na **Generovat ověřovací kód**.

7. Služba zřizování vytvoří **ověřovací kód** , který můžete použít k ověření vlastnictví certifikátu. Zkopírujte kód do schránky. 

   ![Ověřit certifikát](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Digitálně podepsat ověřovací kód pro vytvoření ověřovacího certifikátu

Nyní je třeba podepsat *ověřovací kód* s privátním klíčem přidruženým k certifikátu CA X. 509, který vygeneruje signaturu. To se označuje jako [důkaz o vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1) a výsledkem je podepsaný ověřovací certifikát.

Microsoft poskytuje nástroje a ukázky, které vám pomůžou vytvořit certifikát podepsaného ověřování: 

- **Sada Azure IoT Hub C SDK** poskytuje skripty PowerShellu (Windows) a bash (Linux), které vám pomůžou vytvořit certifikační autority a listové certifikáty pro účely vývoje a provádět kontrolu vlastnictví pomocí ověřovacího kódu. [Soubory](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) relevantní pro systém můžete stáhnout do pracovní složky a podle pokynů v [souboru Readme Certificates CA Správa certifikátů](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) certifikační autority provést kontrolu vlastnictví certifikátu certifikační autority. 
- **Sada Azure IoT Hub C# SDK** obsahuje [ukázku ověření certifikátu skupiny](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), kterou můžete použít k tomu, abyste provedli kontrolu vlastnictví.
 
> [!IMPORTANT]
> Kromě provádění ověření přístupnosti vám skripty PowerShellu a bash citované dříve také umožňují vytvářet kořenové certifikáty, zprostředkující certifikáty a listové certifikáty, které se dají použít k ověřování a zřizování zařízení. Tyto certifikáty by se měly používat jenom pro vývoj. Nikdy by se neměly používat v produkčním prostředí. 

Skripty PowerShellu a Bash, které jsou uvedené v dokumentaci a sady SDK, spoléhají na [OpenSSL](https://www.openssl.org/). Můžete také použít OpenSSL nebo jiné nástroje třetích stran, které vám pomůžou s prováděním důkazů o vlastnictví. Další informace o nástrojích dodaných se sadami SDK najdete v tématu [Jak používat nástroje](how-to-use-sdk-tools.md), které jsou k dispozici v sadách SDK. 


## <a name="upload-the-signed-verification-certificate"></a>Nahrát podepsaný ověřovací certifikát

1. Nahrajte výsledný podpis jako ověřovací certifikát do vaší služby zřizování na portálu. V části **Podrobnosti o certifikátu** na Azure Portal použijte ikonu  _Průzkumníka souborů_ vedle pole **soubor ověřovacího certifikátu. pem nebo. cer** k nahrání podepsaného ověřovacího certifikátu z vašeho systému.

2. Po úspěšném nahrání certifikátu klikněte na **ověřit**. **Stav** certifikátu se změní na **_ověřený_** v seznamu **Průzkumník certifikátů** . Klikněte na tlačítko **aktualizovat** , pokud se neaktualizuje automaticky.

   ![Odeslat ověření certifikátu](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak pomocí portálu vytvořit skupinu registrací, najdete v tématu [Správa registrace zařízení pomocí Azure Portal](how-to-manage-enrollments.md).
- Další informace o tom, jak používat sady SDK služby k vytvoření skupiny registrací, najdete v tématu Správa registrací [zařízení pomocí sad SDK pro služby](how-to-manage-enrollments-sdks.md).











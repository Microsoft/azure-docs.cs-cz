---
title: Ověření certifikátů Certifikační autority X.509 pomocí služby Azure IoT Hub Device Provisioning Service
description: Jak provést kontrolu vlastnictví certifikátů Certifikační autority X.509 pomocí služby Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b008c4ebc83200043d51fc8ef367f1983c549949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973437"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Jak provést kontrolu vlastnictví certifikátů Certifikační autority X.509 pomocí služby Device Provisioning Service

Ověřený certifikát Certifikační autority (CA) je certifikát certifikační autority, který byl nahrán a zaregistrován ve vaší zřizovací službě a prošel ověřením vlastnictví služby. 

Doklad o vlastnictví zahrnuje následující kroky:
1. Získejte jedinečný ověřovací kód generovaný zřizovací službou pro váš certifikát Certifikační autority X.509. Můžete to udělat na webu Azure Portal.
2. Vytvořte ověřovací certifikát X.509 s ověřovacím kódem jako subjektem a podepište jej soukromým klíčem přidruženým k certifikátu Certifikační autority X.509.
3. Nahrajte podepsaný ověřovací certifikát do služby. Služba ověří ověřovací certifikát pomocí veřejné části certifikátu certifikační autority, která má být ověřena, čímž prokáže, že vlastníte soukromý klíč certifikátu certifikační autority.

Ověřené certifikáty hrají důležitou roli při používání skupin pro zápis. Ověření vlastnictví certifikátu poskytuje další vrstvu zabezpečení tím, že zajišťuje, že uživatel, který nahrál certifikát, má k dispozici soukromý klíč certifikátu. Ověření zabrání objektu actor škodlivého objektu, který přičichá k vašemu provozu, v extrahování zprostředkujícího certifikátu a použití tohoto certifikátu k vytvoření skupiny zápisu ve vlastní zřizovací službě, čímž efektivně zneujde vaše zařízení. Prokázáním vlastnictví kořenového adresáře nebo zprostředkujícího certifikátu v řetězu certifikátů dokazujete, že máte oprávnění ke generování listových certifikátů pro zařízení, která se budou registrovat jako součást této skupiny zápisů. Z tohoto důvodu musí být kořenový nebo zprostředkující certifikát nakonfigurovaný ve skupině zápisů ověřeným certifikátem nebo se musí vrátit k ověřenému certifikátu v řetězu certifikátů, který zařízení zobrazí při ověřování se službou. Další informace o skupinách pro zápis najdete v [tématu Certifikáty X.509](concepts-security.md#x509-certificates) a [přístup zařízení k zřizovací službě s certifikáty X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Zaregistrujte veřejnou část certifikátu X.509 a získejte ověřovací kód

Pokud chcete zaregistrovat certifikát certifikační autority u zřizovací služby a získat ověřovací kód, který můžete použít během kontroly vlastnictví, postupujte takto. 

1. Na webu Azure Portal přejděte na zřizovací službu a otevřete **certifikáty** z nabídky na levé straně. 
2. Kliknutím na **Přidat** přidejte nový certifikát.
3. Zadejte popisný zobrazovaný název certifikátu. Přejděte do souboru CER nebo .pem, který představuje veřejnou část certifikátu X.509. Klikněte na **Odeslat**.
4. Jakmile dostanete oznámení, že je certifikát úspěšně odeslán, klikněte na **Uložit**.

    ![Nahrání certifikátu](./media/how-to-verify-certificates/add-new-cert.png)  

   Certifikát se zobrazí v seznamu **Průzkumník certifikátů.** Všimněte si, že **stav** tohoto certifikátu je *neověřeno*.

5. Klikněte na certifikát, který jste přidali v předchozím kroku.

6. V **části Podrobnosti o certifikátu**klepněte na **tlačítko Generovat ověřovací kód**.

7. Zřizovací služba vytvoří **ověřovací kód,** který můžete použít k ověření vlastnictví certifikátu. Zkopírujte kód do schránky. 

   ![Ověření certifikátu](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Digitální podepsání ověřovacího kódu k vytvoření ověřovacího certifikátu

Nyní musíte podepsat *ověřovací kód* pomocí soukromého klíče přidruženého k certifikátu certifikační autority X.509, který generuje podpis. To to je známé jako [doklad o vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1) a má za následek podepsané ověřovací certifikát.

Společnost Microsoft poskytuje nástroje a ukázky, které vám mohou pomoci vytvořit podepsaný ověřovací certifikát: 

- **Azure IoT Hub C SDK** poskytuje PowerShell (Windows) a Bash (Linux) skripty, které vám pomohou vytvořit certifikáty CA a list pro vývoj a provádět ověření vlastnictví pomocí ověřovacího kódu. [Soubory](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) relevantní pro váš systém můžete stáhnout do pracovní složky a podle pokynů v [readme Prostou certifikátů certifikační autority](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) provést kontrolu vlastnictví certifikátu certifikační autority. 
- Sada **Azure IoT Hub C# SDK** obsahuje [ukázku ověření certifikátu skupiny](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), kterou můžete použít k ověření vlastnictví.
 
> [!IMPORTANT]
> Kromě provádění ověření vlastnictví, PowerShell a Bash skripty citované dříve také umožňují vytvářet kořenové certifikáty, zprostředkující certifikáty a listové certifikáty, které lze použít k ověření a zřízení zařízení. Tyto certifikáty by měly být používány pouze pro vývoj. Nikdy by neměly být používány v produkčním prostředí. 

Skripty Prostředí PowerShell a Bash uvedené v dokumentaci a sady SDK spoléhají na [soubor OpenSSL](https://www.openssl.org/). Můžete také použít OpenSSL nebo jiné nástroje třetích stran, které vám pomohou provést kontrolu vlastnictví. Další informace o nástrojích dodávaných se sadami SDK naleznete v tématu [Jak používat nástroje poskytované v sadách SDK](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Nahrání podepsaného ověřovacího certifikátu

1. Nahrajte výsledný podpis jako ověřovací certifikát do zřizovací služby na portálu. V **části Podrobnosti o certifikátu** na portálu Azure použijte ikonu _Průzkumníka souborů_ vedle pole Ověřovací certifikát **.pem nebo .cer k** nahrání podepsaného ověřovacího certifikátu z vašeho systému.

2. Po úspěšném nahrání certifikátu klepněte na tlačítko **Ověřit**. **Stav** certifikátu se v seznamu **Průzkumník certifikátů** změní na **_Ověřeno._** Pokud se aktualizace neaktualizuje automaticky, klepněte na tlačítko **Aktualizovat.**

   ![Nahrát ověření certifikátu](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak pomocí portálu vytvořit skupinu registrací, najdete v [tématu Správa registrací zařízení pomocí portálu Azure .](how-to-manage-enrollments.md)
- Informace o tom, jak pomocí sad SDK služby vytvořit skupinu pro registraci, naleznete [v tématu Správa registrací zařízení pomocí sad SDK služby](how-to-manage-enrollments-sdks.md).











---
title: Postup ověření vlastnictví pro certifikáty certifikační Autority X.509 službou Azure IoT Hub zařízení zřizování | Microsoft Docs
description: Postup ověření certifikátů X.509 certifikační Autority s službou distribučních bodů
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: dd54ca5a172bdb65b7781f2fb764b00f50e5df28
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Postup ověření vlastnictví pro certifikáty certifikační Autority X.509 s služby zřizování zařízení

Ověřené X.509 certifikační autoritou (CA) certifikát je certifikát Certifikační autority, který byl nahrán a zaregistrovat vaše zřízení služby a přešel prostřednictvím důkaz vlastnictví službou. 

Ověření vlastnictví zahrnuje následující kroky:
1. Získáte jedinečný ověřovací kód vygenerované službou zřizování pro váš certifikát certifikační Autority X.509. Můžete provést z portálu Azure.
2. Vytvoření certifikátu X.509. certifikát ověření pomocí ověřovací kód jako jeho subjektu a podpisu certifikátu s privátním klíčem přidružené k vaší certifikát X.509 certifikační Autority.
3. Nahrajte certifikát podepsaný držitelem ověření ke službě. Služba ověří ověřovací certifikát na server veřejnou část certifikátu certifikační Autority pomocí ověřovaného, proto prokázání, že jste v soukromý klíč certifikátu certifikační Autority.

Ověřené certifikáty hraje důležitou roli při používání skupin registrace. Ověření vlastnictví certifikát poskytuje další vrstvu zabezpečení zajištěním, že proces pro načtení certifikátu k dispozici soukromý klíč certifikátu. Ověření zabraňuje škodlivý actor sledování toku dat provozu z extrahování zprostředkující certifikát a vytvořit skupinu registrace vlastní zřizování služby pomocí certifikátu, efektivně weby zařízení. Prokázání vlastnictví kořenové nebo zprostředkující certifikát v řetězu certifikátů, jste prokázání, že máte oprávnění pro generování certifikátů listu pro zařízení, která bude registraci v rámci této skupiny pro zápis. Z toho důvodu, kořenovou nebo zprostředkující certifikát nakonfigurované ve skupině registrace musí být buď certifikát ověřené nebo musí shrnutí ověřené certifikát v řetězu certifikátů zařízení uvede během ověřování se službou. Další informace o skupinách registrace najdete v tématu [certifikáty X.509](concepts-security.md#x509-certificates) a [řízení přístupu k zařízení ke službě zřizování pomocí certifikátů X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Část veřejný certifikát X.509 zaregistrovat a získat ověřovací kód

Pokud chcete zaregistrovat certifikát Certifikační autority s zřizování služby a získat ověřovací kód, který můžete použít během ověření vlastnictví, postupujte podle těchto kroků. 

1. Na portálu Azure přejděte do služby zřizování a otevřete **certifikáty** z nabídky na levé straně. 
2. Klikněte na tlačítko **přidat** přidat nový certifikát.
3. Zadejte popisný Zobrazovaný název pro svůj certifikát. Vyhledejte soubor .cer nebo .pem, který reprezentuje část veřejného certifikátu X.509. Klikněte na **Odeslat**.
4. Jakmile se zobrazí oznámení, že je váš certifikát úspěšně nahrána, klikněte na možnost **Uložit**.

    ![Nahrání certifikátu](./media/how-to-verify-certificates/add-new-cert.png)  

   Certifikát se zobrazí v **certifikát Explorer** seznamu. Všimněte si, že **stav** tento certifikát je *Unverified*.

5. Klikněte na certifikát, který jste přidali v předchozím kroku.

6. V **podrobnosti certifikátu**, klikněte na tlačítko **generovat ověřovací kód**.

7. Zřizování služba vytvoří **ověřovací kód** , můžete použít k ověření vlastnictví certifikátu. Zkopírujte kód do schránky. 

   ![Ověřit certifikát](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Digitálně podepsat ověřovací kód k vytvoření ověřovacího certifikátu

Nyní je třeba se přihlásit *ověřovací kód* s privátním klíčem přidružené váš certifikát certifikační Autority X.509, který generuje podpis. To se označuje jako [důkaz vlastnictví](https://tools.ietf.org/html/rfc5280#section-3.1) a výsledky v certifikát podepsaný držitelem ověření.

Společnost Microsoft poskytuje nástroje a ukázky, které vám mohou pomoci vytvořit certifikát podepsaný držitelem ověření: 

- **Sady SDK Azure IoT Hub C** poskytuje prostředí PowerShell (Windows) a skripty Bash (Linux), vám pomůže vytvořit certifikáty certifikační Autority a listu pro vývoj a k provádění ověření z – u sebe pomocí ověřovací kód. Si můžete stáhnout [soubory](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) relevantní pro váš systém pracovní složky a postupujte podle pokynů [readme certifikáty certifikační Autority pro správu](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) k provedení ověření vlastnictví na certifikát Certifikační autority. 
- **Azure IoT Hub C# SDK** obsahuje [skupiny certifikát ověření ukázka](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample), které můžete provést ověření u sebe.
- Můžete podle kroků v [skriptů prostředí PowerShell ke správě certifikátů certifikační Autority podepsané X.509](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates) článku dokumentace IoT Hub, konkrétně skript uvedený v části s názvem [ověření u sebe vaší Certifikát certifikační Autority X.509](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-create-certificates#signverificationcode).
 
> [!IMPORTANT]
> Kromě provádí ověření u sebe, skripty prostředí PowerShell a Bash citovalo dříve také umožňují vytvořit kořenových certifikátů, zprostředkující certifikáty a listu certifikáty, které lze použít k ověřování a zřízení zařízení. Tyto certifikáty by měl použít pro vývoj jenom. Musí být nikdy použity v produkčním prostředí. 

Skripty prostředí PowerShell a Bash, které jsou uvedeny v dokumentaci a sady SDK spoléhají na [OpenSSL](https://www.openssl.org/). Můžete provést ověření u sebe může také použít OpenSSL nebo jiné nástroje třetích stran. Další informace o nástroji, které jsou součástí sady SDK najdete v tématu [použití nástrojů, které jsou součástí sady SDK](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>Nahrát na server certifikát podepsaný držitelem ověření

1. Nahrajte výsledný podpis jako ověřovací certifikát k zřizování službě na portálu. V **podrobnosti certifikátu** na portálu Azure, použijte _Průzkumníka souborů_ ikonu vedle **soubor .pem nebo .cer ověření certifikátu** pole, které chcete nahrát podepsané ověření certifikátu z vašeho systému.

2. Jakmile je úspěšně nahrán certifikát, klikněte na možnost **ověřte**. **Stav** vaše změny certifikátu **_ověřeno_** v **certifikát Explorer** seznamu. Klikněte na tlačítko **aktualizovat** Pokud neaktualizuje automaticky.

   ![Nahrajte certifikát ověření](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Další postup

- Další informace o použití portálu k vytvoření skupiny registrace najdete v tématu [Správa registrace zařízení pomocí portálu Azure](how-to-manage-enrollments.md).
- Další informace o tom, jak vytvořit skupinu registrace pomocí sady SDK služby najdete v tématu [Správa registrace zařízení pomocí služby SDK](how-to-manage-enrollments-sdks.md).











---
title: Použití nástrojů Azure IoT Hub Device Provisioning Service SDK
description: Tento dokument přesuzuje nástroje poskytované v Azure IoT Hub Device Provisioning Service (DPS) SDK pro vývoj.
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 28d682a9ca698afb72d08b4d111562c725d46996
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530841"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak používat nástroje, které nabízí sady SDK ke zjednodušení vývoje pro zřizování
IoT Hub Device Provisioning Service zjednodušuje proces zřizování s nulovým [přízřízením](about-iot-dps.md#provisioning-process) za běhu zabezpečeným a škálovatelným způsobem.  Vyžaduje se ověření zabezpečení ve formě certifikátu X. 509 nebo čipu TPM (Trusted Platform Module).  Společnost Microsoft také spolupracuje s [dalšími bezpečnostními partnery](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) , aby vylepšila jistotu při zabezpečení nasazení IoT. Princip hardwarového požadavku na zabezpečení může být pro vývojáře poměrně náročný. K dispozici je sada sad SDK služby zřizování Azure IoT, aby vývojáři mohli používat pohodlí při psaní klientů, kteří komunikují se službou zřizování. Sady SDK také poskytují ukázky pro běžné scénáře a také sadu nástrojů pro zjednodušení ověření zabezpečení při vývoji.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulátor čipu TPM (Trusted Platform Module)
[Čip TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) může odkazovat na standard pro bezpečné ukládání klíčů pro ověřování platformy nebo může odkazovat na vstupně-výstupní rozhraní, které slouží k interakci s moduly, které implementují Standard. Čipy TPM může existovat jako diskrétní hardware, integrovaný hardware, firmware nebo software založený na firmwaru nebo softwaru.  V produkčním prostředí je čip TPM umístěný na zařízení, a to buď jako diskrétní hardware, integrovaný hardware, nebo na základě firmwaru. Ve fázi testování se vývojářům poskytuje softwarový simulátor čipu TPM.  Tento simulátor je k dispozici pouze pro vývoj na platformě Windows pro nyní.

Postup pro použití simulátoru TPM:
1. [Připravte vývojové prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) a naklonujte úložiště GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Přejděte do složky simulátoru TPM v části ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` .
3. Spusťte Simulator.exe před spuštěním jakékoli klientské aplikace pro zřízení zařízení.
4. Nechte simulátor běžet na pozadí během procesu zřizování, aby získal ID registrace a ověřovací klíč.  Obě hodnoty jsou platné pouze pro jednu instanci běhu.

## <a name="x509-certificate-generator"></a>Generátor certifikátů X. 509
[Certifikáty X. 509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) se dají použít jako mechanismus ověřování, který umožňuje škálovat produkční prostředí a zjednodušit zřizování zařízení.  Existuje [několik způsobů](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) , jak získat certifikát X. 509:
* V produkčním prostředí doporučujeme koupit certifikát CA X. 509 od veřejné kořenové certifikační autority.
* Pro testovací prostředí můžete vygenerovat kořenový certifikát X. 509 nebo řetěz certifikátů X. 509 pomocí:
    * OpenSSL: můžete použít skripty pro generování certifikátů:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell nebo bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulátor modulu složení identity zařízení (kost): index se dá použít pro identitu kryptografického zařízení a ověření identity na základě protokolu TLS a klientských certifikátů X. 509.  [Přečtěte si další informace](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) o identitě zařízení pomocí kostky.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Použití generátoru certifikátů X. 509 s emulátorem kostky
Sady SDK poskytují generátor certifikátů X. 509 s emulátorem kostky, který je umístěný v sadě [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Tento generátor funguje pro různé platformy.  Vygenerovaný certifikát lze použít pro vývoj v jiných jazycích.

Zatímco emulátor kostky v současné době výstupuje kořenový certifikát, zprostředkující certifikát, listový certifikát a přidružený privátní klíč.  Kořenový certifikát nebo zprostředkující certifikát však nelze použít k podepsání samostatného listového certifikátu.  Pokud máte v úmyslu otestovat scénář registrace skupiny, který používá jeden podpisový certifikát k podepsání listových certifikátů více zařízení, můžete použít OpenSSL k vytvoření řetězce certifikátů.

Generování certifikátu X. 509 pomocí tohoto generátoru:
1. [Připravte vývojové prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) a naklonujte úložiště GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Změňte kořen na Azure-IoT-SDK-Java.
3. Spusťte ```mvn install -DskipTests=true``` , aby se stáhly všechny požadované balíčky a zkompiluje se sada SDK.
4. Přejděte do kořenového adresáře generátoru certifikátů X. 509 v ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` .
5. Sestavit s ```mvn clean install```
6. Spusťte nástroj pomocí následujících příkazů:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Po zobrazení výzvy můžete volitelně zadat _běžný název_ pro vaše certifikáty.
8. Nástroj místně vygeneruje **klientský certifikát**, **privátní klíč certifikátu klienta**, **zprostředkující certifikát**a **kořenový certifikát**.

Certifikát **klienta** je listový certifikát na zařízení.  V klientovi zařízení je nutný **certifikát klienta** a přidružený **privátní klíč certifikátu klienta** . V závislosti na zvoleném jazyce se mechanismus, který se má umístit do klientské aplikace, může lišit.  Další informace najdete v tématu [rychlé starty](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) v tématu Vytvoření simulovaného zařízení pomocí X. 509 pro další informace.

Kořenový certifikát nebo zprostředkující se dají použít k vytvoření skupiny registrací nebo jednotlivé registrace prostřednictvím [kódu programu](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) nebo pomocí [portálu](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Další kroky
* Vývoj s využitím [sady Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) pro Azure IoT Hub a Azure IoT Hub Device Provisioning Service

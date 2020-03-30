---
title: Použití nástrojů sad SDK služby Azure IoT Hub Pro zřizování zařízení
description: Tento dokument zkontroluje nástroje poskytované ve sadách SDK služby Azure IoT Hub Device Provisioning Service (DPS) pro vývoj.
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b817b3cfe47ed08cae9e7e0b1c2c24363f2ccfed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271522"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak používat nástroje uvedené v sadách SDK ke zjednodušení vývoje pro zajišťování
Služba zřizování zařízení služby IoT Hub zjednodušuje proces zřizování pomocí nulového dotykového ovládání [a automatického zřizování](concepts-auto-provisioning.md) just-in-time bezpečným a škálovatelným způsobem.  Je vyžadováno bezpečnostní osvědčení ve formě certifikátu X.509 nebo modulu TPM (Trusted Platform Module).  Společnost Microsoft také spolupracuje s [dalšími hardwarovými partnery zabezpečení](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) s cílem zvýšit důvěru v zabezpečení nasazení IoT. Pochopení požadavku na zabezpečení hardwaru může být pro vývojáře velmi náročné. Sada sad SDK služby zřizování Azure IoT jsou k dispozici tak, aby vývojáři mohli použít vrstvu pohodlí pro psaní klientů, kteří mluví ke službě zřizování. Sady SDK také poskytují ukázky pro běžné scénáře, stejně jako sadu nástrojů pro zjednodušení osvědčení zabezpečení ve vývoji.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulátor modulu důvěryhodné platformy (TPM)
[Tpm](https://docs.microsoft.com/azure/iot-dps/concepts-security) může odkazovat na standard pro bezpečné ukládání klíčů k ověření platformy nebo může odkazovat na rozhraní vstupně-in ů používaných k interakci s moduly implementujícími standard. TPM mohou existovat jako samostatný hardware, integrovaný hardware, firmware nebo software.  V produkčním prostředí je čip TPM umístěn v zařízení jako samostatný hardware, integrovaný hardware nebo firmware. Ve fázi testování je vývojářům k dispozici simulátor čipu TPM založený na softwaru.  Tento simulátor je k dispozici pouze pro vývoj na platformě Windows pro tuto chvíli.

Postup použití simulátoru čipu TPM jsou:
1. [Připravte vývojové prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) a klonovat úložiště GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Přejděte do složky ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```simulátoru čipu TPM v části .
3. Spusťte Simulator.exe před spuštěním libovolné klientské aplikace pro zřizování zařízení.
4. Nechte simulátor spustit na pozadí v celém procesu zřizování získat ID registrace a ověřovací klíč.  Obě hodnoty jsou platné pouze pro jednu instanci spuštění.

## <a name="x509-certificate-generator"></a>Generátor certifikátů X.509
[Certifikáty X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) lze použít jako mechanismus ověřování pro škálování výroby a zjednodušení zřizování zařízení.  Certifikát X.509 lze získat [několika způsoby:](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate)
* V produkčním prostředí doporučujeme zakoupit certifikát certifikační autority X.509 od veřejné kořenové certifikační autority.
* Pro testovací prostředí můžete vygenerovat kořenový certifikát X.509 nebo řetěz certifikátů X.509 pomocí:
    * OpenSSL: Skripty pro generování certifikátů můžete použít:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [Prostředí PowerShell nebo Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulátor Dice (Device Identity Composition Engine) (DICE): DICE lze použít pro identitu kryptografických zařízení a atestaci založenou na protokolu TLS a klientských certifikátech X.509.  [Přečtěte si další informace](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) o identitě zařízení pomocí aplikace DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Použití generátoru certifikátů X.509 s emulátorem DICE
Sady SDK poskytují generátor certifikátů X.509 s emulátorem DICE, který se nachází v [java sdk .](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)  Tento generátor funguje napříč platformami.  Vygenerovaný certifikát lze použít pro vývoj v jiných jazycích.

V současné době, zatímco emulátor DICE vytiskne kořenový certifikát, zprostředkující certifikát, listový certifikát a přidružený soukromý klíč.  Kořenový certifikát nebo zprostředkující certifikát však nelze použít k podepsání samostatného listu certifikátu.  Pokud máte v úmyslu otestovat scénář registrace skupiny, kde jeden podpisový certifikát se používá k podpisu listové certifikáty více zařízení, můžete použít OpenSSL k vytvoření řetězce certifikátů.

Chcete-li generovat certifikát X.509 pomocí tohoto generátoru:
1. [Připravte vývojové prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) a klonovat úložiště GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Změňte kořen na azure-iot-sdk-java.
3. Spuštění ```mvn install -DskipTests=true``` maže težcí ke stažení všech požadovaných balíčků a kompilaci sady SDK.
4. Přejděte ke kořenovému adresáři generátoru ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```certifikátů X.509 v .
5. Sestavujte pomocí```mvn clean install```
6. Spusťte nástroj pomocí následujících příkazů:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Po zobrazení výzvy můžete volitelně zadat _běžný název_ pro vaše certifikáty.
8. Nástroj místně generuje **certifikát klienta**, **soukromý klíč certifikátu klienta**, **zprostředkující certifikát**a **kořenový certifikát**.

**Certifikát klienta** je listový certifikát v zařízení.  **Certifikát klienta** a přidružený **privátní klíč certifikátu klienta** jsou potřeba v klientovi zařízení. V závislosti na tom, jaký jazyk zvolíte, mechanismus, který to vloží do klientské aplikace, se může lišit.  Další informace naleznete [v tématu Rychlé starty](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) při vytváření simulovaných zařízení pomocí X.509 další informace.

Kořenový certifikát nebo zprostředkující lze vytvořit skupinu pro zápis nebo individuální zápis [programově](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) nebo pomocí [portálu](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Další kroky
* Vývoj pomocí [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) pro Azure IoT Hub a Službu zřizování zařízení Azure IoT Hub

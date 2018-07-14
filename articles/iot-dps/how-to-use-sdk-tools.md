---
title: Použít nástroje poskytované v Azure IoT Hub zřizování služby sady SDK pro zařízení pro zjednodušení vývoje
description: Tento dokument revize nástrojů sady Azure IoT Hub zřizování služby sady SDK pro zařízení pro vývoj
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: 647f54d8252c594a280f81d661a3de6270bf692b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001343"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak použít nástroje poskytované v sadách SDK pro zjednodušení vývoje pro zřizování
IoT Hub Device Provisioning Service zjednodušuje proces s plně automatizované zřizování za běhu [automatického zřizování](concepts-auto-provisioning.md) bezpečným a škálovatelným způsobem.  Vyžaduje se ověření zabezpečení ve formě certifikátu X.509 nebo Trusted Platform Module (TPM).  Microsoft je také partnerství s [jiných hardwarových partnerů zabezpečení](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) k vylepšení spolehlivosti při zabezpečení nasazení IoT. Principy hardwarový požadavek zabezpečení může být poměrně obtížné pro vývojáře. Sada SDK služby zřizování prostředí Azure IoT jsou k dispozici tak, aby vývojáři můžou pomocí vrstvy usnadnění psaní klienty, kteří mluví ke službě zřizování. Sady SDK také poskytnout ukázky pro běžné scénáře, jakož i sadu nástrojů pro zjednodušení ověření zabezpečení při vývoji.

## <a name="trusted-platform-module-tpm-simulator"></a>Důvěryhodné simulátor Platform Module (TPM)
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) mohou odkazovat na úroveň standard týkající se bezpečného ukládání klíče k ověření na platformu, nebo mohou odkazovat na vstupně-výstupních operací rozhraní používaných pro interakci s moduly implementace standardu. Čipy TPM můžete existuje jako diskrétní hardware, integrované hardwaru, na základě firmwaru, nebo na software.  V produkčním prostředí, TPM se nachází na zařízení, buď jako diskrétní hardware, integrované hardwaru, nebo na základě firmwaru. Ve fázi testování simulátor TPM na základě softwaru poskytuje vývojářům.  Simulátor dostupná jenom pro vývoj na platformě Windows teď.

Tady jsou kroky pro použití simulátor TPM:
1. [Příprava vývojového prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) a naklonujte úložiště GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Přejděte do složky simulátoru TPM v rámci ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Spusťte Simulator.exe před spuštěním jakékoli klientské aplikace pro zřizování zařízení.
4. Nechte běžet na pozadí během procesu zřizování získat ID registrace a ověřovacím klíčem simulátoru.  Obě hodnoty jsou platné pouze pro jednu instanci spuštění.

## <a name="x509-certificate-generator"></a>Generátor certifikátů X.509
[Certifikáty X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) slouží jako mechanismus ověřování škálovat produkčního prostředí a zjednodušuje zřizování zařízení.  Existují [několik způsobů, jak](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) získat certifikát X.509:
* Pro produkční prostředí doporučujeme zakoupit od certifikační autority veřejného kořenového certifikátu webu X.509.
* Pro testovací prostředí, můžete vygenerovat kořenový certifikát X.509 nebo pomocí řetězce certifikátu X.509:
    * OpenSSL: Můžete použít skripty pro vytvoření certifikátu:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [Prostředí PowerShell nebo prostředí Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulátor Identity Composition Engine (DICE) zařízení: DICE lze použít pro šifrování zařízení identit a ověření identity na základě protokolu TLS a X.509 klientské certifikáty.  [Přečtěte si](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) Další informace o identitu zařízení s emulátorem DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Generátor certifikátů X.509 pomocí emulátor DICE
Sady SDK poskytují generátor certifikátů X.509 s emulátorem DICE, umístěný ve [sady Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Tento generátor funguje napříč platformami.  Vygenerovaný certifikát můžete použít pro vývoj v jiných jazycích.

V současné době while emulátorem DICE výstupy kořenový certifikát, zprostředkující certifikát, listového certifikátu a přidružený privátní klíč.  Však certifikát kořenové nebo zprostředkující certifikát nelze použít se samostatnou listový certifikát.  Pokud chcete testovat scénáře registrace skupiny kde jeden podpisový certifikát se používá k podepisování certifikátů listu více zařízení, můžete vytvořit řetěz certifikátů OpenSSL.

Chcete-li generovat certifikát X.509 pomocí tohoto generátoru:
1. [Příprava vývojového prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) a naklonujte úložiště GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Změňte kořenový adresář azure-iot-sdk-java.
3. Spustit ```mvn install -DskipTests=true``` stáhnout všechny požadované balíčky a zkompilujte sadu SDK
4. Přejděte do kořenového adresáře pro generátor certifikátů X.509 v ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Sestavení s ```mvn clean install```
6. Spusťte nástroj pomocí následujících příkazů:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Po zobrazení výzvy můžete volitelně zadat _běžný název_ pro vaše certifikáty.
8. Tento nástroj místně vygeneruje **klientský certifikát**, **privátní klíč klientského certifikátu**, **zprostředkující certifikát**a **Root Cert**.

**Klientský certifikát** je listový certifikát na zařízení.  **Klientský certifikát** a přidružené **privátní klíč klientského certifikátu** jsou potřeba v klientovi zařízení. V závislosti na tom, jaký jazyk zvolíte může být jiný mechanismus pro umístění v klientské aplikaci.  Další informace najdete v tématu [rychlých startů](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) při vytvoření simulovaného zařízení X.509 pro další informace.

Certifikát kořenové nebo zprostředkující slouží k vytvoření skupiny pro registraci nebo jednotlivou registraci [programově](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) nebo pomocí [portál](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Další postup
* Vývoj pomocí [sady SDK Azure IoT]( https://github.com/Azure/azure-iot-sdks) pro Azure IoT Hub a Azure IoT Hub Device Provisioning Service
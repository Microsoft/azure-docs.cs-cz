---
title: Pomocí nástrojů sady Azure IoT Hub zařízení zřizování služby SDK pro zjednodušení vývoje
description: Tento dokument zkontroluje nástrojů sady Azure IoT Hub zařízení zřizování služby sady SDK pro vývoj
services: iot-dps
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a7e1a55bcfc8c7a8f76416fb12edf712340fadd2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak používat nástroje, které jsou součástí sady SDK pro zjednodušení vývoje pro zřizování
Zřizování služby zařízení IoT Hub zjednodušuje proces zřizování se nula touch, za běhu [automatické zřizování](concepts-auto-provisioning.md) zabezpečené a škálovatelné způsobem.  Ověření zabezpečení ve formě certifikátu X.509 nebo Trusted Platform Module (TPM) je povinný.  Microsoft je také partnerství se [dalšími partnery hardwaru zabezpečení](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) ke zlepšení důvěru zabezpečení IoT nasazení. Seznámení s požadavky na hardware zabezpečení může být velmi náročné pro vývojáře. Sadu SDK služby Azure IoT zřizování služby jsou k dispozici, aby vývojáři mohli používat vrstvu pohodlí pro zápis klienty, kteří komunikují s zřizování služby. Sady SDK poskytují ukázky pro běžné scénáře, jakož i sadu nástrojů pro zjednodušení ověření zabezpečení v vývoj.

## <a name="trusted-platform-module-tpm-simulator"></a>Důvěryhodné simulátoru (Trusted Platform Module)
[Čip TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) mohou odkazovat na standard týkající se bezpečného ukládání klíče k ověření platformy, nebo mohou odkazovat na rozhraní vstupně-výstupních operací používají k interakci s moduly implementace standardní. Čipy TPM může existovat jako diskrétní hardware, integrované hardware, založené na firmwaru, nebo na základě softwaru.  V produkčním prostředí, čip TPM je umístěn na zařízení, buď jako diskrétní hardware, integrované hardwaru, nebo na základě firmwaru. Ve fázi testování TPM jsme na základě softwaru poskytuje vývojářům.  Tato simulátoru je pouze pro vývoj na platformě Windows zatím k dispozici.

Postup použití simulátoru TPM jsou:
1. [Příprava vývojového prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) a naklonujte úložiště GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Přejděte do složky simulátoru TPM v části ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Simulator.exe spusťte před spuštěním jakékoli klientskou aplikaci pro zřizování zařízení.
4. Umožní simulátoru spuštěný na pozadí během procesu zřizování k získání ID registrace a ověřovací klíč.  Obě hodnoty jsou platné pouze pro jednu instanci spustit.

## <a name="x509-certificate-generator"></a>Generátor certifikát X.509
[Certifikáty X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) slouží jako mechanismus ověření škálování produkční a zjednodušit zřizování zařízení.  Existují [několik způsobů](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) k získání certifikátu X.509:
* Pro produkční prostředí doporučujeme nákupu certifikát X.509 certifikační Autority od veřejné kořenové certifikační autority.
* Pro testovací prostředí, můžete vygenerovat X.509 kořenový certifikát nebo řetěz certifikát X.509 pomocí:
    * OpenSSL: to [postup průvodce](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) provede ukázkové skripty prostředí PowerShell, které používají [OpenSSL](https://www.openssl.org/) k vytvoření a podepsání certifikátů X.509.  Kromě toho můžete také použít skript v jiných jazycích pro generování certifikátů:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Zařízení emulátoru Identity složení modulu (ROZČLENĚNÍ): ROZČLENĚNÍ lze použít pro identitu kryptografických zařízení a ověření na základě protokolu TLS a X.509 klientské certifikáty.  [Další informace](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) více informací o identitu zařízení v ROZČLENĚNÍ.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Pomocí emulátoru ROZČLENĚNÍ generátor certifikát X.509
Sady SDK poskytují generátor certifikát X.509 s ROZČLENĚNÍ emulátoru, umístěný ve [sady Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Tento generátor funguje napříč platformami.  Vygenerovaný certifikát můžete použít pro vývoj v dalších jazycích.

V současné době při emulátoru ROZČLENĚNÍ výstupy kořenový certifikát, zprostředkující certifikát, listu certifikát a přidružený privátní klíč.  Však certifikát kořenové nebo zprostředkující certifikát nelze použít k podepsání certifikátu samostatné listu.  Pokud chcete otestovat scénáře registrace skupiny kde jeden podpisový certifikát se používá k podepisování certifikátů listu více zařízení, můžete vytvořit řetěz certifikátů OpenSSL.

Pro vytvoření certifikátu X.509 pomocí tohoto generátoru:
1. [Příprava vývojového prostředí](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) a naklonujte úložiště GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Změňte kořenu azure-iot-sdk-Java.
3. Spustit ```mvn install -DskipTests=true``` ke stažení všechny požadované balíčky a zkompilujte sadu SDK
4. Přejděte do kořenového adresáře pro generátor certifikát X.509 v ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. K sestavení ```mvn clean install```
6. Spusťte nástroj pomocí následujících příkazů:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Po zobrazení výzvy můžete volitelně zadat _běžný název_ pro vaše certifikáty.
8. Místně Nástroj generuje **klientského certifikátu**, **klientského certifikátu. privátní klíč**, **zprostředkující certifikát**a **kořenový certifikát**.

**Klientského certifikátu** je listu certifikát v zařízení.  **Klientského certifikátu** a přidružené **klientského certifikátu. privátní klíč** jsou potřeba v klientovi zařízení. V závislosti na tom, jaké jazyka zvolíte může být jiný mechanismus to uvedli do klientské aplikace.  Další informace najdete v tématu [– elementy QuickStart](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) na vytvoření simulovaného zařízení pomocí X.509 Další informace.

Certifikát kořenové nebo zprostředkující lze použít pro vytvoření aplikace registrace skupinu nebo jednotlivé registrace [prostřednictvím kódu programu](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) nebo pomocí [portál](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Další postup
* Vývoj pomocí [sady SDK Azure IoT]( https://github.com/Azure/azure-iot-sdks) pro Azure IoT Hub a Azure IoT Hub zařízení zřizování služby
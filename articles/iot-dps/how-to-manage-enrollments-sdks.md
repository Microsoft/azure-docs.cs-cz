---
title: Správa registrací zařízení pomocí sad Azure DPS SDK
description: Jak spravovat registrace zařízení ve službě DPS (IoT Hub Device Provisioning Service) pomocí sad SDK služby
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975070"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Jak spravovat registrace zařízení pomocí sad SDK služby Azure Device Provisioning Service
*Registrace zařízení* vytvoří záznam jednoho zařízení nebo skupiny zařízení, které se mohou v určitém okamžiku zaregistrovat u služby Device Provisioning Service. Záznam registrace obsahuje počáteční požadovanou konfiguraci pro zařízení jako součást této registrace, včetně požadovaného centra IoT Hub. Tento článek ukazuje, jak spravovat registrace zařízení pro službu zřizování programově pomocí sad Azure IoT Provisioning Service SDKs.  Sady SDK jsou k dispozici na GitHubu ve stejném úložišti jako sady Azure IoT SDK.

## <a name="prerequisites"></a>Požadavky
* Získejte připojovací řetězec z instance služby Device Provisioning Service.
* Získejte artefakty zabezpečení zařízení pro [mechanismus ověřování,](concepts-security.md#attestation-mechanism) který se používá:
    * [**Trusted Platform Module (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Individuální registrace: ID registrace a ověřovací klíč Čipu TPM z fyzického zařízení nebo ze simulátoru čipu TPM.
        * Skupina zápisu se nevztahuje na osvědčení čipu TPM.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Individuální zápis: [Leaf certifikát](/azure/iot-dps/concepts-security) z fyzického zařízení nebo z Emulátoru SDK [DICE.](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)
        * Skupina zápisu: Certifikát [certifikační autority nebo kořenový](/azure/iot-dps/concepts-security#root-certificate) [certifikát](/azure/iot-dps/concepts-security#intermediate-certificate), který se používá k výrobě certifikátu zařízení na fyzickém zařízení.  Může být také generován z emulátoru SDK DICE.
* Přesná volání rozhraní API se mohou lišit z důvodu jazykových rozdílů. Podrobnosti naleznete v ukázkách uvedených na GitHubu:
   * [Ukázky klienta služby Java Provisioning](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Ukázky klienta služby Node.js Provisioning Service](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Ukázky klienta zřizovací služby .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení
Existují dva způsoby, jak můžete zaregistrovat zařízení pomocí zřizovací služby:

* **Skupina Zápis** je položka pro skupinu zařízení, která sdílejí společný mechanismus ověřování certifikátů X.509 [podepsaných kořenovým certifikátem](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkujícím certifikátem](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Doporučujeme používat skupinu registrace pro velký počet zařízení, která sdílejí požadovanou počáteční konfiguraci, nebo pro zařízení, která přejdou do stejného klienta. Všimněte si, že můžete zaregistrovat pouze zařízení, která používají mechanismus ověřování X.509 jako *skupiny zápisu*. 

    Pomocí sad SDK můžete vytvořit skupinu zápisů pomocí sad SDK podle tohoto pracovního postupu:

    1. Pro skupinu zápisu používá mechanismus ověřování kořenový certifikát X.509.  Volání rozhraní API ```X509Attestation.createFromRootCertificate``` sady Service SDK s kořenovým certifikátem pro vytvoření potvrzení pro zápis.  Kořenový certifikát X.509 je k dispozici buď v souboru PEM, nebo jako řetězec.
    1. Vytvořte ```EnrollmentGroup``` novou proměnnou pomocí ```attestation``` ```enrollmentGroupId```vytvořené a jedinečné .  Volitelně můžete nastavit parametry, ```IoTHubHostName``` ```ProvisioningStatus```jako je ```Device ID```, , .
    2. Volání rozhraní SERVICE ```createOrUpdateEnrollmentGroup``` SDK API ```EnrollmentGroup``` v back-endové aplikaci s vytvořením skupiny zápisu.

* **Individuální registrace** je položka pro jedno zařízení, které se může zaregistrovat. Jednotlivé zápisy mohou jako mechanismy ověřování používat certifikáty X.509 nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM). Doporučujeme používat jednotlivé registrace pro zařízení, která vyžadují jedinečné počáteční konfigurace, nebo pro zařízení, která mohou používat pouze tokeny SAS prostřednictvím čipu TPM nebo virtuálního čipu TPM jako mechanismus ověřování. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

    Pomocí tohoto pracovního postupu můžete vytvořit individuální zápis pomocí sad SDK:
    
    1. Vyberte ```attestation``` si mechanismus, který může být TPM nebo X.509.
        1. **TPM**: Pomocí ověřovacího klíče z fyzického zařízení nebo ze simulátoru Čipu ```TpmAttestation``` TPM jako vstupu můžete volat rozhraní Service SDK API a vytvořit ověření pro zápis. 
        2. **X.509**: Pomocí klientského certifikátu jako vstupu můžete ```X509Attestation.createFromClientCertificate``` volat rozhraní SERVICE SDK API a vytvořit osvědčení pro zápis.
    2. Vytvořte ```IndividualEnrollment``` novou proměnnou ```attestation``` pomocí vytvořeného a jedinečného ```registrationId``` jako vstupu, který je ve vašem zařízení nebo je generován ze simulátoru Čipu TPM.  Volitelně můžete nastavit parametry, ```IoTHubHostName``` ```ProvisioningStatus```jako je ```Device ID```, , .
    3. Volání rozhraní SERVICE ```createOrUpdateIndividualEnrollment``` SDK API ```IndividualEnrollment``` v back-endové aplikaci s vytvořením individuální registrace.

Po úspěšném vytvoření registrace vrátí služba Device Provisioning Service výsledek registrace. Tento pracovní postup je demonstrován ve [vzorcích uvedených výše](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Aktualizace položky registrace

Po vytvoření položky registrace můžete registraci aktualizovat.  Potenciální scénáře zahrnují aktualizaci požadované vlastnosti, aktualizaci metody atestace nebo zrušení přístupu k zařízení.  Existují různá api pro individuální zápis a skupinový zápis, ale žádný rozdíl pro mechanismus ověřování.

Položku registrace můžete aktualizovat podle tohoto pracovního postupu:
* **Jednotlivá registrace:**
    1. Získejte nejnovější registraci ze služby zřizování nejprve pomocí rozhraní Service SDK API ```getIndividualEnrollment```.
    2. Podle potřeby upravte parametr nejnovější registrace. 
    3. Pomocí nejnovější registrace volejte rozhraní Service ```createOrUpdateIndividualEnrollment``` SDK API a aktualizujte položku registrace.
* **Zařazení do skupiny**:
    1. Získejte nejnovější registraci ze služby zřizování nejprve pomocí rozhraní Service SDK API ```getEnrollmentGroup```.
    2. Podle potřeby upravte parametr nejnovější registrace.
    3. Pomocí nejnovější registrace volejte rozhraní Service ```createOrUpdateEnrollmentGroup``` SDK API a aktualizujte položku registrace.

Tento pracovní postup je demonstrován ve [vzorcích uvedených výše](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Odebrání položky zápisu

* **Jednotlivé registrace** lze odstranit voláním rozhraní ```deleteIndividualEnrollment``` ```registrationId```SERVICE SDK API pomocí .
* **Registraci skupiny** lze odstranit voláním ```deleteEnrollmentGroup``` ```enrollmentGroupId```rozhraní API sady Service SDK pomocí .

Tento pracovní postup je demonstrován ve [vzorcích uvedených výše](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Hromadný provoz u jednotlivých zápisů

Můžete provést hromadnou operaci k vytvoření, aktualizaci nebo odebrání více jednotlivých registrací podle tohoto pracovního postupu:

1. Vytvořte proměnnou, ```IndividualEnrollment```která obsahuje více .  Implementace této proměnné se liší pro každý jazyk.  Podrobnosti navizuje ukázka hromadné operace na GitHubu.
2. Volání rozhraní SERVICE ```runBulkOperation``` SDK API s požadovanou ```BulkOperationMode``` operací a proměnnou pro jednotlivé registrace. Podporovány jsou čtyři režimy: vytvoření, aktualizace, aktualizaceIfMatchEtag a odstranění.

Po úspěšném provedení operace by služba zřizování zařízení vrátila výsledek hromadné operace.

Tento pracovní postup je demonstrován ve [vzorcích uvedených výše](#prerequisites).

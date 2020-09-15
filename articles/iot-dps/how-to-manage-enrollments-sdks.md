---
title: Správa registrace zařízení pomocí Azure DPS SDK
description: Správa registrace zařízení v IoT Hub Device Provisioning Service (DPS) pomocí sad SDK služby
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: fasttrack-edit, iot
services: iot-dps
ms.openlocfilehash: 1dc97f92e6139475d0d5ac5ea1201d6ff6b8d470
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532320"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Správa registrace zařízení pomocí sad SDK služby Azure Device Provisioning
*Registrace zařízení* vytvoří záznam o jednom zařízení nebo skupině zařízení, která se můžou v některých bodech zaregistrovat ve službě Device Provisioning. Záznam zápisu obsahuje počáteční požadovanou konfiguraci pro zařízení v rámci této registrace, včetně požadovaného centra IoT Hub. V tomto článku se dozvíte, jak spravovat registraci zařízení pro službu zřizování programově pomocí sad SDK služby zřizování pro Azure IoT.  Sady SDK jsou dostupné na GitHubu ve stejném úložišti jako sady SDK Azure IoT.

## <a name="prerequisites"></a>Požadavky
* Získejte připojovací řetězec z instance služby Device Provisioning.
* Získejte artefakty zabezpečení zařízení pro použitý [mechanismus ověřování](concepts-service.md#attestation-mechanism) :
    * [**Čip TPM (Trusted Platform Module)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Jednotlivá registrace: ID registrace a ověřovací klíč čipu TPM z fyzického zařízení nebo simulátoru TPM.
        * Skupina registrace se nevztahuje na ověření identity čipem TPM.
    * [**X. 509**](/azure/iot-dps/concepts-security):
        * Jednotlivá registrace: [listový certifikát](/azure/iot-dps/concepts-security) z fyzického zařízení nebo emulátoru [kostky](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) sady SDK.
        * Skupina registrací: [certifikační autorita/kořenový certifikát](/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkující certifikát](/azure/iot-dps/concepts-security#intermediate-certificate), který se používá k vytvoření certifikátu zařízení na fyzickém zařízení.  Dá se taky vygenerovat z emulátoru kostky sady SDK.
* Přesná volání rozhraní API se můžou lišit v důsledku jazykových rozdílů. Podrobnosti najdete v ukázkách uvedených na GitHubu:
   * [Ukázky klientů služby zřizování pro Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [ Ukázkové klientské ukázky službyNode.js Provisioning](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Ukázky klienta služby zřizování .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení
Existují dva způsoby, jak můžete zařízení zaregistrovat pomocí služby zřizování:

* **Skupina** registrací je položka pro skupinu zařízení, která sdílí běžný mechanismus ověřování certifikátů X. 509, podepsaný [kořenovým certifikátem](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkujícím certifikátem](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Doporučujeme používat skupinu registrací pro velký počet zařízení, která sdílejí požadovanou počáteční konfiguraci, nebo pro všechna zařízení, která se budou napojovat do stejného tenanta. Všimněte si, že můžete registrovat jenom zařízení, která používají mechanismus ověřování X. 509 jako *skupiny*registrací. 

    Můžete vytvořit skupinu registrací se sadami SDK, které následují tento pracovní postup:

    1. Pro skupinu registrací používá mechanismus ověřování certifikát X. 509 Root Certificate.  Volejte rozhraní API služby Service SDK ```X509Attestation.createFromRootCertificate``` s kořenovým certifikátem pro vytvoření ověření identity pro registraci.  Kořenový certifikát X. 509 je k dispozici buď v souboru PEM, nebo jako řetězec.
    1. Vytvořte novou ```EnrollmentGroup``` proměnnou pomocí ```attestation``` vytvořeného a jedinečného ```enrollmentGroupId``` .  Volitelně můžete nastavit parametry jako ```IoTHubHostName``` , ```ProvisioningStatus``` .
    2. V back-endu aplikace volejte rozhraní API služby Service SDK ```createOrUpdateEnrollmentGroup``` ```EnrollmentGroup``` a vytvořte skupinu registrace.

* **Jednotlivá registrace** je záznam pro jedno zařízení, které se může zaregistrovat. Jednotlivé registrace můžou použít buď certifikáty X. 509, nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM) jako mechanismy ověřování. Pro zařízení, která vyžadují jedinečné počáteční konfigurace, doporučujeme používat jednotlivé registrace nebo pro zařízení, která jako mechanismus ověřování používají jenom tokeny SAS přes TPM nebo virtuální čip TPM. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

    Jednotlivé registrace můžete vytvořit pomocí sad SDK, které následují tento pracovní postup:
    
    1. Vyberte ```attestation``` mechanismus, který může být TPM nebo X. 509.
        1. **TPM**: pomocí ověřovacího klíče z fyzického zařízení nebo z simulátoru TPM jako vstupu můžete volat rozhraní API služby SDK ```TpmAttestation``` a vytvořit tak ověření pro registraci. 
        2. **X. 509**: použití certifikátu klienta jako vstupu, můžete volat rozhraní Service SDK API ```X509Attestation.createFromClientCertificate``` a vytvořit ověření pro registraci.
    2. Vytvořte novou ```IndividualEnrollment``` proměnnou s použitím ```attestation``` vytvořeného a jedinečného ```registrationId``` jako vstupu, který je na vašem zařízení nebo vygenerovaný z simulátoru TPM.  Volitelně můžete nastavit parametry jako ```Device ID``` , ```IoTHubHostName``` , ```ProvisioningStatus``` .
    3. Volání rozhraní API služby Service SDK ```createOrUpdateIndividualEnrollment``` ve vaší back-endové aplikaci s ```IndividualEnrollment``` cílem vytvořit jednotlivou registraci.

Po úspěšném vytvoření registrace služba Device Provisioning vrátí výsledek registrace. Tento pracovní postup je znázorněný v ukázkách [uvedených výše](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Aktualizace položky registrace

Po vytvoření položky registrace budete chtít registraci aktualizovat.  K potenciálním scénářům patří aktualizace požadované vlastnosti, aktualizace metody ověření identity nebo odvolávání přístupu k zařízení.  Pro jednotlivé registrace a zápis skupin jsou k dispozici různá rozhraní API, ale nejsou rozlišována pro mechanismus ověřování.

V tomto pracovním postupu můžete aktualizovat položku registrace:
* **Jednotlivá registrace:**
    1. Získejte nejnovější registraci ze služby zřizování jako první s rozhraním API sady SDK služby ```getIndividualEnrollment``` .
    2. Podle potřeby upravte parametr nejnovější registrace. 
    3. Pomocí nejnovější registrace volejte rozhraní Service SDK API, ```createOrUpdateIndividualEnrollment``` abyste aktualizovali položku registrace.
* **Registrace skupiny**:
    1. Získejte nejnovější registraci ze služby zřizování jako první s rozhraním API sady SDK služby ```getEnrollmentGroup``` .
    2. Podle potřeby upravte parametr nejnovější registrace.
    3. Pomocí nejnovější registrace volejte rozhraní Service SDK API, ```createOrUpdateEnrollmentGroup``` abyste aktualizovali položku registrace.

Tento pracovní postup je znázorněný v ukázkách [uvedených výše](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Odebrání položky registrace

* **Jednotlivé registrace** je možné odstranit VOLÁNÍM rozhraní API služby SDK ```deleteIndividualEnrollment``` pomocí ```registrationId``` .
* **Zápis skupin** lze odstranit voláním rozhraní Service SDK API ```deleteEnrollmentGroup``` pomocí ```enrollmentGroupId``` .

Tento pracovní postup je znázorněný v ukázkách [uvedených výše](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Hromadná operace při individuální registraci

V rámci tohoto pracovního postupu můžete vytvořit, aktualizovat nebo odebrat několik individuálních registrací, a to prostřednictvím hromadné operace:

1. Vytvořte proměnnou, která obsahuje více ```IndividualEnrollment``` .  Implementace této proměnné je pro každý jazyk odlišná.  Podrobnosti najdete v ukázce hromadné operace na GitHubu.
2. Volejte rozhraní API služby Service SDK ```runBulkOperation``` s objektem ```BulkOperationMode``` pro požadovanou operaci a proměnnou pro jednotlivé registrace. Podporují se čtyři režimy: vytváření, aktualizace, updateIfMatchEtag a odstraňování.

Po úspěšném provedení operace by služba Device Provisioning vrátila výsledek hromadné operace.

Tento pracovní postup je znázorněný v ukázkách [uvedených výše](#prerequisites).

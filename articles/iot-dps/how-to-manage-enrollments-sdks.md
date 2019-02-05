---
title: Správa registrací zařízení pomocí SDK služby zřizování zařízení Azure | Dokumentace Microsoftu
description: Správa registrace zařízení v IoT Hubu službě Device Provisioning pomocí sady SDK pro služby
author: yzhong94
ms.author: yizhon
ms.date: 04/04/18
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: 1c7fa798c2e767aa6a21b3c56da6f69b4d3a1406
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732342"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Správa registrací zařízení pomocí SDK služby zřizování zařízení Azure
A *registrace zařízení* vytvoří záznam o jedno zařízení nebo skupině zařízení, která se v určitém okamžiku se můžou zaregistrovat do služby Device Provisioning. Záznam registrace obsahuje počáteční požadovanou konfiguraci zařízení jako součást registrace, včetně požadované centrum IoT hub. Tento článek ukazuje, jak Správa registrací zařízení pro vaši službu zřizování prostřednictvím kódu programu pomocí sad Azure IoT zřizování služby SDK.  Sady SDK jsou k dispozici na Githubu ve stejném úložišti jako sad SDK Azure IoT.

## <a name="prerequisites"></a>Požadavky
* Získání připojovacího řetězce z vaší instanci služby Device Provisioning.
* Získat artefaktů zabezpečení pro zařízení [mechanismus ověřování](concepts-security.md#attestation-mechanism) použít:
    * [**Trusted Platform Module (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Jednotlivá registrace: ID registrace a ověřovacího klíče TPM z fyzického zařízení nebo simulátor TPM.
        * Skupiny registrací se nevztahují na ověření identity čipem TPM.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Jednotlivá registrace: [Listový certifikát](/azure/iot-dps/concepts-security) z fyzického zařízení nebo ze sady SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulátoru.
        * Skupiny registrací: [Certifikační Autority nebo kořenovým certifikátem](/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkující certifikát](/azure/iot-dps/concepts-security#intermediate-certificate), která se používá k vytvoření certifikátu zařízení na fyzickém zařízení.  Může být taky vygenerovaná ze emulátorem SDK DICE.
* Přesné volání rozhraní API se můžou lišit kvůli rozdílů jazyka. Prostudujte si podrobné informace k dispozici na Githubu ukázky:
   * [Ukázky Java klienta služby zřizování](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Ukázky Node.js klienta služby zřizování](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Ukázky .NET klienta služby zřizování](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení
Existují dva způsoby, jak můžete zaregistrovat svoje zařízení pomocí zřizovací služby:

* **Skupinu registrací** je záznam pro skupinu zařízení, které sdílejí společné mechanismus ověřování certifikátů X.509, podepsány [kořenový certifikát](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkující certifikát ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Doporučujeme použít skupinu registrací pro velký počet zařízení, která sdílí požadovanou počáteční konfiguraci, nebo pro zařízení budou patřit do stejného tenanta. Všimněte si, že můžete registrovat jenom zařízení, která používají mechanismus ověřování X.509 jako *skupiny registrací*. 

    Vytvořit skupinu registrací spolu se sadami SDK následujícího pracovního postupu:

    1. Pro skupiny registrací používá mechanismus ověřování kořenového certifikátu X.509.  Volání rozhraní API sady SDK služby ```X509Attestation.createFromRootCertificate``` s kořenovým certifikátem k vytvoření ověřování pro registraci.  Kořenový certifikát X.509 je k dispozici v souboru PEM nebo jako řetězec.
    1. Vytvořte nový ```EnrollmentGroup``` pomocí proměnných ```attestation``` vytvořený a jedinečný ```enrollmentGroupId```.  Volitelně můžete nastavit parametry jako ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Volání rozhraní API sady SDK služby ```createOrUpdateEnrollmentGroup``` v back-endu aplikace pomocí ```EnrollmentGroup``` vytvořit skupinu registrací.

* **Jednotlivou registraci** je záznam pro jedno zařízení, který může zaregistrovat. Jednotlivé registrace můžou jako mechanismus ověřování pomocí certifikátů X.509 nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM). Doporučujeme používat jednotlivé registrace pro zařízení, která vyžadují jedinečnou počáteční konfiguraci, nebo zařízení, které můžou využívat pouze tokeny SAS prostřednictvím skutečného nebo virtuálního čipu TPM jako mechanismus ověřování. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

    Vytvoření jednotlivé registrace spolu se sadami SDK následujícího pracovního postupu:
    
    1. Zvolte vaši ```attestation``` mechanismus, který může být TPM nebo X.509.
        1. **TPM**: Pomocí ověřovacího klíče z fyzického zařízení nebo simulátor TPM jako vstup, můžete volat rozhraní API sady SDK služby ```TpmAttestation``` vytvořit ověřování pro registraci. 
        2. **X.509**: Pomocí klientského certifikátu jako vstup, můžete volat rozhraní API sady SDK služby ```X509Attestation.createFromClientCertificate``` vytvořit ověřování pro registraci.
    2. Vytvořte nový ```IndividualEnrollment``` proměnné s použitím ```attestation``` vytvořený a jedinečný ```registrationId``` jako vstup, který je na vašem zařízení nebo generován ze simulátoru TPM.  Volitelně můžete nastavit parametry jako ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Volání rozhraní API sady SDK služby ```createOrUpdateIndividualEnrollment``` v back-endu aplikace pomocí ```IndividualEnrollment``` k vytvoření jednotlivé registrace.

Po úspěšném vytvoření registrace do služby Device Provisioning vrátí výsledek zápisu. Tento pracovní postup je znázorněn ve vzorcích [již bylo zmíněno dříve](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Aktualizovat položku registrace

Po vytvoření položky registrace můžete aktualizovat registraci.  Potenciální scénáře zahrnují aktualizace požadované vlastnosti, aktualizuje metodu ověření nebo odvolání přístupu k zařízení.  Existují jiné rozhraní API pro jednotlivou registraci a skupinové registrace, ale žádný rozdíl pro mechanismus ověřování.

Můžete aktualizovat položku registrace následujícího pracovního postupu:
* **Jednotlivá registrace:**
    1. Získat nejnovější registrace ze služby zřizování první pomocí rozhraní API sady SDK služby ```getIndividualEnrollment```.
    2. Parametr nejnovější registrace podle potřeby změnit. 
    3. Pomocí nejnovější registrace volání rozhraní API sady SDK služby ```createOrUpdateIndividualEnrollment``` aktualizovat vaši položku registrace.
* **Skupinové registrace**:
    1. Získat nejnovější registrace ze služby zřizování první pomocí rozhraní API sady SDK služby ```getEnrollmentGroup```.
    2. Parametr nejnovější registrace podle potřeby změnit.
    3. Pomocí nejnovější registrace volání rozhraní API sady SDK služby ```createOrUpdateEnrollmentGroup``` aktualizovat vaši položku registrace.

Tento pracovní postup je znázorněn ve vzorcích [již bylo zmíněno dříve](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Odebrat položku registrace

* **Jednotlivé registrace** lze odstranit pomocí volání rozhraní API sady SDK služby ```deleteIndividualEnrollment``` pomocí ```registrationId```.
* **Skupinové registrace** lze odstranit pomocí volání rozhraní API sady SDK služby ```deleteEnrollmentGroup``` pomocí ```enrollmentGroupId```.

Tento pracovní postup je znázorněn ve vzorcích [již bylo zmíněno dříve](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Hromadné operace na jednotlivé registrace

Můžete provádět hromadné operace vytvoření, aktualizace nebo odebrání více jednotlivé registrace následujícího pracovního postupu:

1. Vytvořte proměnnou, která obsahuje více ```IndividualEnrollment```.  Implementace této proměnné se liší pro každý jazyk.  Projděte si hromadné operace ukázka na Githubu podrobnosti.
2. Volání rozhraní API sady SDK služby ```runBulkOperation``` s ```BulkOperationMode``` pro požadovanou operaci a vaše proměnná pro jednotlivé registrace. Jsou podporovány čtyř režimů: vytvořit, aktualizovat, updateIfMatchEtag a odstranit.

Po úspěšném provedení operace služby Device Provisioning vrátí výsledek operace hromadného.

Tento pracovní postup je znázorněn ve vzorcích [již bylo zmíněno dříve](#prerequisites).

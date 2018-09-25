---
title: Koncepty zabezpečení v Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Popisuje zabezpečení zřizování koncepty specifické pro zařízení pomocí služby Azure Device Provisioning a centrem IoT
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 92a30f0754decc3052bf53a64da13325ddc4f954
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946557"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Koncepty zabezpečení IoT Hub Device Provisioning Service 

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, který použijete ke konfiguraci plně automatizované zřizování zařízení pro určité Centrum IoT. Ve službě Device Provisioning Service, můžete [automatické zřizování](concepts-auto-provisioning.md) miliony zařízení bezpečným a škálovatelným způsobem. Tento článek obsahuje přehled *zabezpečení* součástí koncepty zřizování zařízení. Tento článek je relevantní pro všechny osoby, které jsou součástí probíhá příprava pro nasazení zařízení.

## <a name="attestation-mechanism"></a>Mechanismus ověřování

Mechanismus ověřování použije právě tato metoda za potvrzení identity zařízení. Mechanismus ověřování se taky hodí pro seznamu registrací, který informuje službu zřizování, jakou metodu ověření pro použití s daným zařízením.

> [!NOTE]
> IoT Hub "schéma ověřování" používá pro podobný koncept dané služby.

Služby Device Provisioning podporuje následující formy ověření identity:
* **Certifikáty X.509** založen na standardní tok ověřování certifikátu X.509.
* **Trusted Platform Module (TPM)** podle hodnoty nonce výzvu, pomocí čipu TPM standard pro klíče zobrazíte podepsaný token sdíleného přístupového podpisu (SAS). Tato forma ověření nevyžaduje fyzický čip TPM na zařízení, ale služba očekává, že ověřit pomocí ověřovacího klíče za [specifikace TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symetrický klíč** podle sdíleného přístupového podpisu (SAS) [tokeny zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-tokens), která zahrnuje hash podpisu a vložené vypršení platnosti. Další informace najdete v tématu [symetrického klíče ověření](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení nebo HSM, se používá pro zabezpečené hardwarové úložiště tajných kódů zařízení a je nejbezpečnější způsob úložiště tajných kódů. Certifikáty X.509 a tokeny SAS, mohou být uloženy v modulu HSM. Moduly hardwarového zabezpečení lze použít s oběma mechanismů ověřování zřizování podporuje.

> [!TIP]
> Důrazně doporučujeme pomocí modulu hardwarového zabezpečení zařízení pro bezpečné ukládání tajných klíčů v zařízeních.

Tajné kódy zařízení můžou být uložené taky v softwaru (paměť), ale je méně bezpečné forma úložiště než modulu hardwarového zabezpečení.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM může odkazovat na úroveň standard týkající se bezpečného ukládání klíčů používá k ověření na platformu nebo mohou odkazovat na vstupně-výstupních operací rozhraní používaných pro interakci s moduly implementace standardu. Čipy TPM můžete existuje jako diskrétní hardware, integrované hardwaru, na základě firmwaru, nebo na software. Další informace o [čipy TPM a TPM ověření](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Služby Device Provisioning Service podporuje pouze TPM 2.0.

Ověření identity čipem TPM je založená na nonce výzvy, která používá kořenové klíče ověřovací a úložiště zobrazíte podepsaný token sdíleného přístupového podpisu (SAS).

### <a name="endorsement-key"></a>Ověřovací klíč

Ověřovací klíč je obsažena v čipu TPM, které bylo generované interně nebo vložený na výrobní čas asymetrický klíč a je jedinečný pro každý čip TPM. Ověřovací klíč nelze změnit ani odebrat. Soukromá část ověřovací klíč se nikdy uvolní mimo čipu TPM, zatímco veřejnou část ověřovacího klíče slouží k rozpoznání originální čipu TPM. Další informace o [ověřovací klíč](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Kořenový klíč úložiště

Storage root key je uložená v čipu TPM a slouží k ochraně klíčů TPM vytvořené aplikacemi, tak, aby tyto klíče nelze použít bez čipu TPM. Storage root key se vygeneruje, když převzít vlastnictví TPM; Pokud zrušíte zaškrtnutí čipu TPM, tak nového uživatele může převzít vlastnictví, se vygeneruje nový kořenový klíč úložiště. Další informace o [kořenový klíč úložiště](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certifikáty X.509

Jako mechanismus ověřování pomocí certifikátů X.509 je vynikající způsob, jak škálovat produkčního prostředí a zjednodušuje zřizování zařízení. Certifikáty X.509 jsou obvykle uspořádány v řetězu certifikátů, ve kterém je každý certifikát v řetězu podepsány privátní klíč další vyšší certifikátu a tak dále, ukončuje v certifikát podepsaný svým držitelem kořenové důvěryhodnosti. Toto uspořádání se zavádí delegované řetěz certifikátů z kořenového certifikátu vygenerovaného důvěryhodné kořenové certifikační autority (CA) dolů prostřednictvím každý zprostředkující certifikační Autority pro certifikát koncové entity "typu list", nainstalované v zařízení. Další informace najdete v tématu [zařízení ověřování pomocí certifikátů X.509 certifikační Autority](/azure/iot-hub/iot-hub-x509ca-overview). 

Řetěz certifikátů často představuje některé fyzické nebo logické hierarchie přiřazená zařízení. Například může výrobce:
- certifikát podepsaný svým držitelem kořenové certifikační Autority
- vygenerovat jedinečný certifikát zprostředkující certifikační Autority pro každý objekt pro vytváření pomocí kořenového certifikátu
- vygenerovat jedinečný certifikát zprostředkující certifikační Autority pro každou výrobní linky v závodě pomocí certifikátu každá továrna
- a nakonec certifikát výrobní linky, použít k vygenerování certifikátu jedinečná zařízení (koncové entity) pro každé zařízení vyrobenými na řádku. 

Další informace najdete v tématu [koncepční znalost certifikátů webu X.509 v odvětví IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Kořenový certifikát

Kořenový certifikát je podepsaný svým držitelem certifikát X.509 představující certifikační autority (CA). Je nejzažšímu nebo kotva vztahu důvěryhodnosti, řetěz certifikátů. Kořenové certifikáty můžete samostatně vydaný organizací nebo zakoupenému od kořenové certifikační autority. Další informace najdete v tématu [certifikáty webu X.509 získat](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Kořenový certifikát, můžete také označuje jako certifikát kořenové certifikační Autority.

### <a name="intermediate-certificate"></a>Zprostředkující certifikát

Zprostředkující certifikát je certifikát X.509, který byl podepsán pomocí kořenového certifikátu (nebo jinou zprostředkující certifikát pomocí kořenového certifikátu v řetězci). Poslední zprostředkující certifikát v řetězu se používá k podepsání listový certifikát. Zprostředkující certifikát, můžete také označuje jako zprostředkující Certifikační autority.

### <a name="end-entity-leaf-certificate"></a>Certifikát koncové entity "typu list"

Listový certifikát nebo certifikát koncové entity, identifikuje držitel certifikátu. Má kořenový certifikát ve svém řetězu certifikátů a nula nebo více zprostředkující certifikáty. Listového certifikátu se nepoužívá k podepisování jiných certifikátů. Jednoznačně identifikuje zařízení ke službě zřizování a se někdy označuje jako certifikát zařízení. Při ověřování zařízení používá privátní klíč spojený s tímto certifikátem reagovat na doklad o vlastnictví challenge ze služby. Další informace najdete v tématu [ověřování zařízení podepsané certifikáty webu X.509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509

Zřizovací služba poskytuje dva typy položku registrace, který můžete použít k řízení přístupu pro zařízení, která používají mechanismus ověřování X.509:  

- [Jednotlivé registrace](./concepts-service.md#individual-enrollment) položky jsou nakonfigurovány s certifikátem zařízení spojené s určitým zařízením. Tyto položky řízení registrací pro konkrétní zařízení.
- [Skupiny registrací](./concepts-service.md#enrollment-group) položky jsou spojeny s konkrétní zprostředkující nebo kořenové Certifikační autority. Tyto položky řízení registrací pro všechna zařízení, které mají zprostředkující nebo kořenové certifikační ve svém řetězu certifikátů. 

Když se zařízení připojí ke službě zřizování, službu upřednostňuje více konkrétní položky registrace přes méně konkrétní položky registrace. To znamená jednotlivou registraci pro zařízení existuje, zřizovací služby se vztahuje na tuto položku. Pokud neexistuje žádný jednotlivou registraci pro zařízení a skupinu registrací pro první zprostředkující certifikát v řetězu certifikátů zařízení existuje, platí službu tuto položku a tak dále, řetězem do kořenového adresáře. Služba použije první použít položku, která najde, tak, aby:

- Pokud je první položku registrace nalezen zapnutá, služba mu.
- Pokud je první položku registrace, který byl nalezen zakázaný, služba není zřízení zařízení.  
- Pokud pro některý z certifikátů v řetězu certifikátů zařízení nebyl nalezen žádný záznam registrace, služba není zřízení zařízení. 

Tento mechanismus a hierarchické struktuře řetězy certifikátů poskytuje výkonné flexibilitu v tom, jak můžete řídit přístup pro jednotlivá zařízení, stejně jako u skupiny zařízení. Představte si například pět zařízení s následující řetězy certifikátů: 

- *Zařízení 1*: kořenový certifikát -> certifikát A -> certifikát zařízení 1
- *Zařízení 2*: kořenový certifikát -> certifikát A -> certifikát zařízení 2
- *Zařízení 3*: kořenový certifikát -> certifikát A -> certifikát zařízení 3
- *Zařízení 4*: kořenový certifikát -> certifikát B -> certifikát zařízení 4
- *Zařízení 5*: kořenový certifikát -> certifikát B -> certifikát zařízení 5

Na začátku může vytvoření položky registrace povolené jediné skupiny pro kořenový certifikát umožňuje přístup pro všechny pět zařízení. Pokud později certifikát B se stane dojde k ohrožení bezpečnosti, můžete vytvořit položku Zakázané registrace skupiny pro certifikát B, aby se zabránilo *zařízení 4* a *5 zařízení* registrace. Pokud stále vyšší *zařízení 3* stane dojde k ohrožení bezpečnosti, můžete vytvořit položku Zakázané jednotlivou registraci pro svůj certifikát. To odebere přístup pro *zařízení 3*, ale stále umožňuje *1 zařízení* a *zařízení 2* k registraci.
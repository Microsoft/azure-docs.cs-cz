---
title: Azure IoT Hub Device Provisioning Service – koncepty zabezpečení
description: Popisuje koncepty zřizování zabezpečení, které jsou specifické pro zařízení se službou Device Provisioning Service (DPS) a IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378885"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub Device Provisioning Service koncepce zabezpečení 

IoT Hub Device Provisioning Service je pomocná služba pro IoT Hub, která slouží ke konfiguraci zařízení s nulovým dotykem pro zadané centrum IoT. Se službou Device Provisioning můžete [automaticky zřizovat](concepts-auto-provisioning.md) miliony zařízení zabezpečeným a škálovatelným způsobem. Tento článek obsahuje přehled konceptů *zabezpečení* , které se týkají zřizování zařízení. Tento článek je relevantní pro všechny osoby zapojené do zprovoznění zařízení připravené k nasazení.

## <a name="attestation-mechanism"></a>Mechanismus ověřování

Mechanismus ověřování je metoda, která se používá k potvrzení identity zařízení. Mechanismus ověřování je také relevantní pro seznam registrací, který oznamuje službě zřizování, kterou metodu ověřování použít s daným zařízením.

> [!NOTE]
> IoT Hub používá schéma ověřování pro podobný koncept v dané službě.

Služba Device Provisioning podporuje následující formy ověření identity:
* **Certifikáty x. 509** založené na standardním toku ověřování certifikátu x. 509.
* **Trusted Platform Module (TPM)** na základě výzvy s identifikátorem nonce, který používá standard TPM pro klíče k prezentaci podepsaného tokenu sdíleného přístupového podpisu (SAS). Tato forma ověření nevyžaduje v zařízení fyzický čip TPM, ale služba očekává ověření pomocí ověřovacího klíče podle [specifikace čipu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symetrický klíč** založený na [tokenech zabezpečení](../iot-hub/iot-hub-devguide-security.md#security-tokens)sdíleného přístupového podpisu (SAS), které zahrnují signaturu s hodnotou hash a vložené vypršení platnosti. Další informace najdete v tématu [ověření identity symetrického klíče](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení (HSM) se používá k zabezpečenému hardwarovému úložišti tajných kódů zařízení a jedná se o nejbezpečnější formu tajného úložiště. Certifikáty X. 509 i tokeny SAS můžou být uložené v modulu HSM. HSM je možné použít s mechanismem ověřování, který zřizování podporuje.

> [!TIP]
> Důrazně doporučujeme používat modul HARDWAROVÉho zabezpečení a zařízení k bezpečnému ukládání tajných kódů do vašich zařízení.

Tajné kódy zařízení je také možné ukládat v softwaru (paměti), ale jedná se o méně bezpečnou podobu úložiště než modul hardwarového zabezpečení (HSM).

## <a name="trusted-platform-module"></a>Modul Trusted Platform

ČIP TPM může odkazovat na standard pro bezpečné ukládání klíčů používaných k ověřování platformy, nebo může odkazovat na vstupně-výstupní rozhraní používané pro interakci s moduly, které implementují Standard. Čipy TPM může existovat jako diskrétní hardware, integrovaný hardware, firmware nebo software založený na firmwaru nebo softwaru. Přečtěte si další informace o [ověření identity čipy TPM a čipu TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Služba Device Provisioning podporuje jenom čip TPM 2,0.

Ověření identity čipem TPM vychází z výzvy s identifikátorem nonce, která používá kořenové klíče potvrzení a úložiště k zobrazení podepsaného tokenu sdíleného přístupového podpisu (SAS).

### <a name="endorsement-key"></a>Ověřovací klíč

Ověřovací klíč je asymetrický klíč obsažený v čipu TPM, který byl interně generován nebo vložen v době výroby a je jedinečný pro každý čip TPM. Ověřovací klíč nelze změnit ani odebrat. Soukromá část ověřovacího klíče se nikdy neuvolňuje mimo čip TPM, ale veřejná část ověřovacího klíče se používá k rozpoznání originálního čipu TPM. Přečtěte si další informace o [ověřovacím klíči](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Kořenový klíč úložiště

Kořenový klíč úložiště je uložený v čipu TPM a používá se k ochraně klíčů TPM vytvořených aplikacemi, aby se tyto klíče nemohly používat bez čipu TPM. Kořenový klíč úložiště se vygeneruje, když převezmete vlastnictví čipu TPM. když vymažete čip TPM, aby mohl převzít vlastnictví nový uživatel, vygeneruje se nový kořenový klíč úložiště. Přečtěte si další informace o [kořenovém klíči úložiště](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509 – certifikáty

Používání certifikátů X. 509 jako mechanismu ověřování je skvělým způsobem, jak škálovat produkční prostředí a zjednodušit zřizování zařízení. Certifikáty X. 509 jsou obvykle uspořádány v řetězu certifikátů, ve kterém jsou jednotlivé certifikáty v řetězu podepsány privátním klíčem dalšího certifikátu, a tak dále, s ukončením kořenového certifikátu podepsaného svým držitelem. Toto uspořádání vytvoří delegovaný řetěz důvěryhodnosti z kořenového certifikátu vygenerovaného důvěryhodnou kořenovou certifikační autoritou (CA) prostřednictvím každé zprostředkující certifikační autority na certifikát "list" koncového prvku "na zařízení. Další informace najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509](/azure/iot-hub/iot-hub-x509ca-overview). 

Řetěz certifikátů často představuje logickou nebo fyzickou hierarchii přidruženou k zařízením. Například výrobce může:
- vydání certifikátu kořenové certifikační autority podepsané svým držitelem
- pro vytvoření jedinečného certifikátu zprostředkující certifikační autority pro každou továrnu použijte kořenový certifikát.
- k vygenerování jedinečného certifikátu zprostředkující certifikační autority pro každou výrobní linku v závodě použijte certifikát každého objektu pro vytváření.
- a nakonec použijte certifikát produkčního řádku k vygenerování jedinečného certifikátu zařízení (koncová entita) pro každé zařízení, které je na řádku vyrobeno. 

Další informace najdete v tématu [konceptuální porozumění certifikátům CA X. 509 v oboru IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Kořenový certifikát

Kořenový certifikát je certifikát X. 509 podepsaný svým držitelem, který představuje certifikační autoritu (CA). Je to trvají nebo kotva vztahu důvěryhodnosti řetězu certifikátů. Kořenové certifikáty mohou být samostatně vydávány organizací nebo zakoupeny od kořenové certifikační autority. Další informace najdete v tématu [Získání certifikátů certifikační autority X. 509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Kořenový certifikát může být také označován jako certifikát kořenové certifikační autority.

### <a name="intermediate-certificate"></a>Zprostředkující certifikát

Zprostředkující certifikát je certifikát X. 509, který je podepsaný kořenovým certifikátem (nebo jiným zprostředkujícím certifikátem s kořenovým certifikátem v řetězci). Poslední zprostředkující certifikát v řetězu se používá k podepsání listového certifikátu. Zprostředkující certifikát se taky může označovat jako certifikát zprostředkující certifikační autority.

### <a name="end-entity-leaf-certificate"></a>Koncová položka "list" – certifikát

Certifikát listu nebo certifikát koncové entity identifikuje držitele certifikátu. Má kořenový certifikát ve svém řetězu certifikátů a také nula nebo více zprostředkujících certifikátů. Listový certifikát se nepoužívá k podepsání jiných certifikátů. Jednoznačně identifikuje zařízení ve službě zřizování a někdy se označuje jako certifikát zařízení. Při ověřování používá zařízení privátní klíč přidružený k tomuto certifikátu, aby reagoval na důkaz o přípravení ze služby.

Listové certifikáty používané s [jednotlivou](./concepts-service.md#individual-enrollment) položkou registrace mají požadavek, aby byl **název subjektu** nastavený na ID registrace jednotlivé položky registrace. Listové certifikáty, které se používají se záznamem [skupiny](./concepts-service.md#enrollment-group) registrací, by měly mít **název subjektu** nastavený na požadované ID zařízení, které se zobrazí v **záznamech registrace** pro ověřené zařízení ve skupině pro registraci.

Další informace najdete v tématu [ověřování zařízení podepsaných pomocí certifikátů certifikační autority X. 509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X. 509

Služba zřizování zpřístupňuje dva typy záznamů registrace, které můžete použít k řízení přístupu pro zařízení, která používají mechanismus ověřování X. 509:  

- [Jednotlivé položky registrace](./concepts-service.md#individual-enrollment) jsou nakonfigurovány s certifikátem zařízení přidruženým k určitému zařízení. Tyto položky řídí registraci pro konkrétní zařízení.
- Položky [skupiny](./concepts-service.md#enrollment-group) registrací jsou spojené s konkrétním zprostředkujícím nebo kořenovým certifikátem certifikační autority. Tyto položky řídí registraci všech zařízení, která mají daný zprostředkující nebo kořenový certifikát ve svém řetězu certifikátů. 

Když se zařízení připojí ke službě zřizování, služba upřednostní konkrétnější položky registrace přes méně konkrétní položky registrace. To znamená, že pokud pro zařízení existuje individuální registrace, služba zřizování tuto položku použije. Pokud není k dispozici žádná individuální registrace zařízení a skupina registrací pro první zprostředkující certifikát v řetězu certifikátů zařízení existuje, služba tuto položku použije a tak dále, až do kořenového řetězce. Služba použije první příslušnou položku, kterou najde, například:

- Pokud je první nalezená položka registrace povolená, služba zřídí zařízení.
- Pokud je první nalezená položka registrace zakázaná, služba zařízení nezřídí.  
- Pokud se pro žádný z certifikátů v řetězu certifikátů zařízení nenajde žádná položka registrace, služba zařízení nezřídí. 

Tento mechanismus a hierarchická struktura řetězů certifikátů poskytuje výkonnou flexibilitu v tom, jak můžete řídit přístup pro jednotlivá zařízení a také pro skupiny zařízení. Představte si například pět zařízení s následujícími řetězy certifikátů: 

- *Zařízení 1*: kořenový certifikát-> certifikát a – certifikát > zařízení 1
- *Zařízení 2*: kořenový certifikát-> certifikát a – certifikát > zařízení 2
- *Zařízení 3*: kořenový certifikát-> certifikát a – certifikát > zařízení 3
- *Zařízení 4*: kořenový certifikát-> certifikát B-> zařízení 4 – certifikát
- *Zařízení 5*: kořenový certifikát-> certifikát B-> certifikát zařízení 5

Zpočátku můžete pro kořenový certifikát vytvořit jedinou povolenou položku registrace skupiny a povolit přístup pro všechna pět zařízení. Pokud dojde k ohrožení bezpečnosti certifikátu B, můžete vytvořit zakázanou položku skupiny registrací pro certifikát B, abyste zabránili registraci *zařízení 4* a *zařízení 5* . Pokud dojde k ohrožení bezpečnosti *zařízení 3* , můžete pro jeho certifikát vytvořit zakázanou jednotlivou položku registrace. Tím odvoláte přístup pro *zařízení 3*, ale pořád povolíte *zařízení 1* a *2* k registraci.
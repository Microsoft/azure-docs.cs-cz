---
title: Azure IoT Hub Device Provisioning Service – ověření identity certifikátu X. 509
description: Popisuje koncepty specifické pro použití ověření certifikátu X. 509 se službou Device Provisioning Service (DPS) a IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 911f819343f675ebe0a2604d912e6e26aa646eb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533044"
---
# <a name="x509-certificate-attestation"></a>Atestace certifikátu X.509

Tento článek obsahuje přehled konceptů, které se týkají při zřizování zařízení pomocí ověřování certifikátů X. 509. Tento článek je relevantní pro všechny osoby zapojené do zprovoznění zařízení připravené k nasazení.

Certifikáty X. 509 mohou být uloženy v modulu hardwarového zabezpečení HSM.

> [!TIP]
> Důrazně doporučujeme používat modul HARDWAROVÉho zabezpečení a zařízení k bezpečnému ukládání tajných kódů, jako je certifikát X. 509, na vašich zařízeních v produkčním prostředí.


## <a name="x509-certificates"></a>Certifikáty X. 509

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
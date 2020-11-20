---
title: Azure IoT Hub Device Provisioning Service – ověření identity certifikátu X. 509
description: Popisuje koncepty specifické pro použití ověření certifikátu X. 509 se službou Device Provisioning Service (DPS) a IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9eee315aac28847710662b463add7d6e68d8d505
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967291"
---
# <a name="x509-certificate-attestation"></a>Atestace certifikátu X.509

Tento článek obsahuje přehled konceptů služby Device Provisioning Service (DPS), které se týkají při zřizování zařízení pomocí ověřování certifikátů X. 509. Tento článek je relevantní pro všechny osoby zapojené do zprovoznění zařízení připravené k nasazení.

Certifikáty X. 509 mohou být uloženy v modulu hardwarového zabezpečení HSM.

> [!TIP]
> Důrazně doporučujeme používat modul HARDWAROVÉho zabezpečení a zařízení k bezpečnému ukládání tajných kódů, jako je certifikát X. 509, na vašich zařízeních v produkčním prostředí.


## <a name="x509-certificates"></a>Certifikáty X. 509

Používání certifikátů X. 509 jako mechanismu ověřování je skvělým způsobem, jak škálovat produkční prostředí a zjednodušit zřizování zařízení. Certifikáty X. 509 jsou obvykle uspořádány v řetězu certifikátů, ve kterém jsou jednotlivé certifikáty v řetězu podepsány privátním klíčem dalšího certifikátu, a tak dále, s ukončením kořenového certifikátu podepsaného svým držitelem. Toto uspořádání vytvoří delegovaný řetěz důvěryhodnosti z kořenového certifikátu vygenerovaného důvěryhodnou kořenovou certifikační autoritou (CA) prostřednictvím každé zprostředkující certifikační autority na certifikát "list" koncového prvku "na zařízení. Další informace najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509](../iot-hub/iot-hub-x509ca-overview.md). 

Řetěz certifikátů často představuje logickou nebo fyzickou hierarchii přidruženou k zařízením. Například výrobce může:
- vydání certifikátu kořenové certifikační autority podepsané svým držitelem
- pro vytvoření jedinečného certifikátu zprostředkující certifikační autority pro každou továrnu použijte kořenový certifikát.
- k vygenerování jedinečného certifikátu zprostředkující certifikační autority pro každou výrobní linku v závodě použijte certifikát každého objektu pro vytváření.
- a nakonec použijte certifikát produkčního řádku k vygenerování jedinečného certifikátu zařízení (koncová entita) pro každé zařízení, které je na řádku vyrobeno. 

Další informace najdete v tématu [konceptuální porozumění certifikátům CA X. 509 v oboru IoT](../iot-hub/iot-hub-x509ca-concept.md). 

### <a name="root-certificate"></a>Kořenový certifikát

Kořenový certifikát je certifikát X. 509 podepsaný svým držitelem, který představuje certifikační autoritu (CA). Je to trvají nebo kotva vztahu důvěryhodnosti řetězu certifikátů. Kořenové certifikáty mohou být samostatně vydávány organizací nebo zakoupeny od kořenové certifikační autority. Další informace najdete v tématu [Získání certifikátů certifikační autority X. 509](../iot-hub/iot-hub-security-x509-get-started.md#get-x509-ca-certificates). Kořenový certifikát může být také označován jako certifikát kořenové certifikační autority.

### <a name="intermediate-certificate"></a>Zprostředkující certifikát

Zprostředkující certifikát je certifikát X. 509, který je podepsaný kořenovým certifikátem (nebo jiným zprostředkujícím certifikátem s kořenovým certifikátem v řetězci). Poslední zprostředkující certifikát v řetězu se používá k podepsání listového certifikátu. Zprostředkující certifikát se taky může označovat jako certifikát zprostředkující certifikační autority.

##### <a name="why-are-intermediate-certs-useful"></a>Proč jsou zprostředkující certifikáty užitečné?
Zprostředkující certifikáty jsou používány různými způsoby. Například zprostředkující certifikáty se dají použít k seskupení zařízení podle produktových produktů, zákazníkům, kteří si kupují zařízení, divize společnosti nebo továrny. 

Představte si, že Contoso je velká společnost s vlastní infrastrukturou veřejných klíčů (PKI) pomocí kořenového certifikátu s názvem *ContosoRootCert*. Každá pobočka společnosti Contoso má svůj vlastní zprostředkující certifikát, který je podepsaný nástrojem *ContosoRootCert*. Každá pobočka pak bude používat zprostředkující certifikát k podepsání svých listových certifikátů pro každé zařízení. V tomto scénáři může společnost Contoso použít jednu instanci DPS, kde se *ContosoRootCert* ověřilo při použití [důkazu o vlastnictví](./how-to-verify-certificates.md). Můžou mít skupinu registrací pro každou dceřinou společnost. Tímto způsobem se nemusíte zabývat jednotlivými pobočkami při ověřování certifikátů.


### <a name="end-entity-leaf-certificate"></a>Koncová položka "list" – certifikát

Certifikát listu nebo certifikát koncové entity identifikuje držitele certifikátu. Má kořenový certifikát ve svém řetězu certifikátů a také nula nebo více zprostředkujících certifikátů. Listový certifikát se nepoužívá k podepsání jiných certifikátů. Jednoznačně identifikuje zařízení ve službě zřizování a někdy se označuje jako certifikát zařízení. Při ověřování používá zařízení privátní klíč přidružený k tomuto certifikátu, aby reagoval na důkaz o přípravení ze služby.

Listové certifikáty používané s [jednotlivou](./concepts-service.md#individual-enrollment) položkou registrace mají požadavek, aby byl **název subjektu** nastavený na ID registrace jednotlivé položky registrace. Listové certifikáty, které se používají se záznamem [skupiny](./concepts-service.md#enrollment-group) registrací, by měly mít **název subjektu** nastavený na požadované ID zařízení, které se zobrazí v **záznamech registrace** pro ověřené zařízení ve skupině pro registraci.

Další informace najdete v tématu [ověřování zařízení podepsaných pomocí certifikátů certifikační autority X. 509](../iot-hub/iot-hub-x509ca-overview.md#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X. 509

Služba zřizování zpřístupňuje dva typy registrace, které můžete použít k řízení přístupu zařízení pomocí mechanismu ověřování X. 509:  

- [Jednotlivé položky registrace](./concepts-service.md#individual-enrollment) jsou nakonfigurovány s certifikátem zařízení přidruženým k určitému zařízení. Tyto položky řídí registraci pro konkrétní zařízení.
- Položky [skupiny](./concepts-service.md#enrollment-group) registrací jsou spojené s konkrétním zprostředkujícím nebo kořenovým certifikátem certifikační autority. Tyto položky řídí registraci všech zařízení, která mají daný zprostředkující nebo kořenový certifikát ve svém řetězu certifikátů. 

#### <a name="dps-device-chain-requirements"></a>Požadavky na řetězec zařízení DPS

Při pokusu o registraci zařízení prostřednictvím DPS pomocí skupiny registrací musí zařízení odeslat řetěz certifikátů z listového certifikátu na certifikát ověřený při pokusůch [o](how-to-verify-certificates.md)přihlášení. V opačném případě se ověřování nezdaří.

Pokud je třeba ověřený jenom kořenový certifikát a do skupiny registrací se zaznamená zprostředkující certifikát, zařízení by mělo z tohoto certifikátu docházet k ověřenému kořenovému certifikátu jako řetěz certifikátů. Tento řetěz certifikátů by zahrnoval jakékoli zprostředkující certifikáty v-mezi. Ověřování se nezdaří, pokud DPS nemůže procházet řetěz certifikátů k ověřenému certifikátu.

Předpokládejme například, že společnost používá pro zařízení následující řetězec zařízení.

![Ukázkový řetěz certifikátů zařízení](./media/concepts-x509-attestation/example-device-cert-chain.png) 

Je ověřený jenom kořenový certifikát a *intermediate2* certifikát se nahraje do skupiny registrací.

![Příklad ověření kořene](./media/concepts-x509-attestation/example-root-verified.png) 

Pokud zařízení během zřizování pošle jenom následující řetězec zařízení, ověřování se nezdaří. Vzhledem k tomu, že DPS nemůže pokus o ověření za předpokladu platnosti certifikátu *intermediate1*

![Příklad neúspěšného řetězu certifikátů](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

Pokud zařízení pošle plný řetěz zařízení, jak je uvedeno během zřizování, pak se může pokusit ověřit zařízení v DPS.

![Ukázkový řetěz certifikátů zařízení](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> Zprostředkující certifikáty je také možné [ověřit pomocí ověření](how-to-verify-certificates.md)přístupnosti.


#### <a name="dps-order-of-operations-with-certificates"></a>Pořadí operací s certifikáty v DPS
Když se zařízení připojí ke službě zřizování, služba upřednostní konkrétnější položky registrace přes méně konkrétní položky registrace. To znamená, že pokud pro zařízení existuje individuální registrace, služba zřizování tuto položku použije. Pokud není k dispozici žádná individuální registrace zařízení a skupina registrací pro první zprostředkující certifikát v řetězu certifikátů zařízení existuje, služba tuto položku použije a tak dále navýšení řetězu do kořenového adresáře. Služba použije první příslušnou položku, kterou najde, například:

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
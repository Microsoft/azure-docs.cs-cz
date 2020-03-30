---
title: Služba zřizování zařízení služby Azure IoT Hub – koncepty zabezpečení
description: Popisuje koncepty zabezpečení specifické pro zařízení se službou Device Provisioning Service (DPS) a službou IoT Hub.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271561"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Koncepty zabezpečení služby Zřizování zařízení služby IoT Hub 

Služba zřizování zařízení služby IoT Hub je pomocná služba pro službu IoT Hub, kterou používáte ke konfiguraci zřizování zařízení bez dotykového ovládání do určeného centra IoT Hub. Se službou zřizování zařízení můžete [automaticky zřídit](concepts-auto-provisioning.md) miliony zařízení bezpečným a škálovatelným způsobem. Tento článek poskytuje přehled konceptů *zabezpečení* zapojených do zřizování zařízení. Tento článek je relevantní pro všechny osoby, které se podílejí na získání zařízení připraveného k nasazení.

## <a name="attestation-mechanism"></a>Atestační mechanismus

Mechanismus ověřování je metoda používaná k potvrzení identity zařízení. Mechanismus ověřování je také relevantní pro seznam zápisu, který říká zřizovací službě, kterou metodu ověřování použít s daným zařízením.

> [!NOTE]
> IoT Hub používá "schéma ověřování" pro podobný koncept v této službě.

Služba device provisioning podporuje následující formy ověřování:
* **Certifikáty X.509** založené na standardním toku ověřování certifikátu X.509.
* **Trusted Platform Module (TPM)** na základě výzvy nonce, pomocí standardu TPM pro klíče prezentovat podepsaný token sdíleného přístupového podpisu (SAS). Tato forma ověření nevyžaduje v zařízení fyzický čip TPM, ale služba očekává, že poznamená pomocí ověřovacího klíče podle [specifikace čipu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symetrický klíč** založený na [tokenech zabezpečení sdíleného](../iot-hub/iot-hub-devguide-security.md#security-tokens)přístupového podpisu (SAS) , které obsahují zahashovaný podpis a vložené vypršení platnosti. Další informace naleznete v tématu [Symetrické atestace klíče](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení nebo modul hardwarového zabezpečení se používá pro zabezpečené hardwarové úložiště tajných kódů zařízení a je nejbezpečnější formou tajného úložiště. Certifikáty X.509 a tokeny SAS mohou být uloženy v souboru hesm. HSM lze použít s oběma mechanismy ověřování zřizování podporuje.

> [!TIP]
> Důrazně doporučujeme používat bezpečnostní počítač se zařízeními k bezpečnému ukládání tajných kódů na vaše zařízení.

Tajné klíče zařízení mohou být také uloženy v softwaru (paměti), ale je to méně bezpečná forma úložiště než hsm.

## <a name="trusted-platform-module"></a>Čip TPM (Trusted Platform Module)

Tpm může odkazovat na standard pro bezpečné ukládání klíčů používaných k ověření platformy nebo může odkazovat na rozhraní vstupně-in ů používaných k interakci s moduly implementujícími standard. TPM mohou existovat jako samostatný hardware, integrovaný hardware, firmware nebo software. Další informace o [tpms a tpm atestace](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Služba zřizování zařízení podporuje pouze čip TPM 2.0.

Ověření čipu TPM je založeno na výzvě nonce, která používá klíče kořenového potvrzení a úložiště k prezentaci podepsaného tokenu s sdíleným přístupovým podpisem (SAS).

### <a name="endorsement-key"></a>Ověřovací klíč

Ověřovací klíč je asymetrický klíč obsažený uvnitř čipu TPM, který byl interně generován nebo vstřikován v době výroby a je jedinečný pro každý čip TPM. Ověřovací klíč nelze změnit ani odebrat. Soukromá část ověřovacího klíče se nikdy neuvolní mimo čip TPM, zatímco veřejná část ověřovacího klíče se používá k rozpoznání skutečného čipu TPM. Přečtěte si další informace o [klíči pro potvrzení](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Kořenový klíč úložiště

Kořenový klíč úložiště je uložen v čipu TPM a slouží k ochraně klíčů Čipu TPM vytvořených aplikacemi, takže tyto klíče nelze použít bez čipu TPM. Kořenový klíč úložiště se vygeneruje, když převezmete vlastnictví čipu TPM. když čip TPM vymažete, aby nový uživatel mohl převzít vlastnictví, vygeneruje se nový kořenový klíč úložiště. Další informace o [kořenovém klíči úložiště](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certifikáty X.509

Použití certifikátů X.509 jako mechanismu s ověřováním je vynikající způsob, jak škálovat výrobu a zjednodušit zřizování zařízení. Certifikáty X.509 jsou obvykle uspořádány v řetězu důvěryhodnosti certifikátů, ve kterém je každý certifikát v řetězci podepsán soukromým klíčem dalšího vyššího certifikátu a tak dále, končící v kořenovém certifikátu podepsaném svým držitelem. Toto uspořádání vytváří delegovaný řetězec důvěryhodnosti z kořenového certifikátu generovaného důvěryhodnou kořenovou certifikační autoritou (CA) dolů přes každý zprostředkující certifikační úřad až po certifikát "listu" koncové entity nainstalovaný v zařízení. Další informace najdete v [tématu Ověřování zařízení pomocí certifikátů Certifikační autority X.509](/azure/iot-hub/iot-hub-x509ca-overview). 

Často řetěz certifikátů představuje některé logické nebo fyzické hierarchie spojené se zařízeními. Výrobce může například:
- vydat kořenový certifikát certifikační autority podepsaný svým držitelem
- Pomocí kořenového certifikátu vygenerujte jedinečný zprostředkující certifikát certifikační autority pro každou továrnu
- Pomocí certifikátu každé továrny vygenerujete jedinečný zprostředkující certifikát certifikační autority pro každou výrobní linku v závodě
- a nakonec použijte certifikát výrobní linky ke generování jedinečného certifikátu zařízení (koncové entity) pro každé zařízení vyrobené na lince. 

Další informace najdete [v tématu Koncepční znalost certifikátů certifikační autority X.509 v oboru IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Kořenový certifikát

Kořenový certifikát je certifikát X.509 podepsaný svým držitelem, který představuje certifikační autoritu. Jedná se o konečnou stanici nebo kotvu důvěryhodnosti řetězu certifikátů. Kořenové certifikáty mohou být vydány organizací nebo zakoupeny od kořenové certifikační autority. Další informace najdete [v tématu Získání certifikátů certifikační autority X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Kořenový certifikát lze také označovat jako kořenový certifikát certifikační autority.

### <a name="intermediate-certificate"></a>Zprostředkující certifikát

Zprostředkující certifikát je certifikát X.509, který byl podepsán kořenovým certifikátem (nebo jiným zprostředkujícím certifikátem s kořenovým certifikátem v řetězci). Poslední zprostředkující certifikát v řetězci se používá k podepsání listu certifikátu. Zprostředkující certifikát lze také označovat jako zprostředkující certifikát certifikační autority.

### <a name="end-entity-leaf-certificate"></a>Certifikát "listu" koncové entity

Listový certifikát nebo certifikát koncové entity identifikuje držitele certifikátu. Má kořenový certifikát ve svém řetězu certifikátů, stejně jako nula nebo více zprostředkující certifikáty. Listový certifikát se nepoužívá k podepisování jiných certifikátů. Jednoznačně identifikuje zařízení do zřizovací služby a někdy se označuje jako certifikát zařízení. Během ověřování zařízení používá soukromý klíč přidružený k tomuto certifikátu k odpovědi na výzvu pro ověření vlastnictví ze služby.

Listové certifikáty používané s položkou [individuální registrace](./concepts-service.md#individual-enrollment) mají požadavek, aby **název subjektu** byl nastaven na ID registrace položky individuální registrace. Listové certifikáty používané s položkou [skupiny zápisu](./concepts-service.md#enrollment-group) by měly mít **název subjektu** nastavený na požadované ID zařízení, které se zobrazí v **registračních záznamech** pro ověřené zařízení ve skupině pro zápis.

Další informace naleznete v [tématu Ověřování zařízení podepsaných certifikáty Certifikační autority X.509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Řízení přístupu zařízení ke službě zřizování pomocí certifikátů X.509

Služba zřizování zpřístupňuje dva typy zápisu položky, které můžete použít k řízení přístupu pro zařízení, která používají mechanismus ověřování X.509:  

- Jednotlivé položky [zápisu](./concepts-service.md#individual-enrollment) jsou konfigurovány s certifikátem zařízení přidruženým k určitému zařízení. Tyto položky řídí registrace pro konkrétní zařízení.
- Položky [skupiny zápisu](./concepts-service.md#enrollment-group) jsou přidruženy k určitému zprostředkujícímu nebo kořenovému certifikátu certifikační autority. Tyto položky řídí zápisy pro všechna zařízení, která mají tento zprostředkující nebo kořenový certifikát v řetězci certifikátů. 

Když se zařízení připojí ke službě zřizování, služba upřednostňuje konkrétnější položky registrace před méně specifickými položkami registrace. To znamená, že pokud existuje individuální registrace pro zařízení, služba zřizování použije tuto položku. Pokud neexistuje žádný individuální zápis pro zařízení a skupina zápisu pro první zprostředkující certifikát v řetězci certifikátů zařízení existuje, služba použije tuto položku a tak dále, v řetězci ke kořenovému adresáři. Služba použije první příslušnou položku, kterou najde, takže:

- Pokud je povolena první nalezená položka registrace, služba zařízení zřídí.
- Pokud je první nalezená položka registrace zakázána, služba zařízení nezřídí.  
- Pokud není nalezena žádná položka zápisu pro některý z certifikátů v řetězci certifikátů zařízení, služba zařízení nezřídí. 

Tento mechanismus a hierarchická struktura řetězů certifikátů poskytují silnou flexibilitu v tom, jak můžete řídit přístup pro jednotlivá zařízení i pro skupiny zařízení. Představte si například pět zařízení s následujícími řetězy certifikátů: 

- *Zařízení 1:* kořenový certifikát -> certifikát -> zařízení 1 certifikát
- *Zařízení 2*: kořenový certifikát -> certifikát -> device 2
- *Zařízení 3:* kořenový certifikát -> certifikát A -> device 3
- *Zařízení 4:* kořenový certifikát -> certifikát B -> device 4 certifikát
- *Zařízení 5:* kořenový certifikát -> certifikát B -> zařízení 5

Zpočátku můžete vytvořit jednu povolenou položku zápisu skupiny pro kořenový certifikát a povolit přístup pro všech pět zařízení. Pokud dojde k pozdějšímu ohrožení zabezpečení certifikátu B, můžete vytvořit zakázanou položku skupiny zápisu pro certifikát B, která zabrání registraci *zařízení 4* a *zařízení 5.* Pokud ještě později dojde k ohrožení *zabezpečení zařízení 3,* můžete pro jeho certifikát vytvořit zakázanou jednotlivou položku zápisu. Tím se odvolá přístup pro *zařízení 3*, ale stále umožňuje *zařízení 1* a zařízení *2* zaregistrovat.
---
title: Koncepty zabezpečení ve službě Azure IoT Hub zařízení zřizování Service | Microsoft Docs
description: Popisuje zabezpečení zřizování koncepty, které jsou specifické pro zařízení s služby zřizování zařízení a služby IoT Hub
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f6410aa3ab21e7c50ec6918930f31b9e1455c464
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Koncepty zabezpečení služby zřizování zařízení IoT Hub 

Služba zřizování zařízení IoT Hub je služba Pomocník pro IoT Hub, který použijete ke konfiguraci zařízení nula touch zřizování do zadané služby IoT hub. Se službou zřizování zařízení můžete [automatického zřizování](concepts-auto-provisioning.md) miliony zařízení zabezpečené a škálovatelné způsobem. Tento článek nabízí přehled *zabezpečení* koncepty, které jsou součástí zřizování zařízení. Tento článek je relevantní pro všechny osoby, které se účastní Příprava zařízení pro nasazení.

## <a name="attestation-mechanism"></a>Ověření mechanismu

Ověření mechanismu je metoda použitá pro potvrzení identity zařízení. Ověření mechanismu se taky hodí do seznamu registrace, který informuje zřizování služby, kterou metodu ověření pro použití s daným zařízením.

> [!NOTE]
> IoT Hub používá "schéma ověřování" pro podobný koncept v této službě.

Služba zřizování zařízení podporuje dva způsoby ověření:
* **Certifikáty X.509** podle standardní tok ověřování certifikátu X.509.
* **Trusted Platform Module (TPM)** podle nonce výzvy, pomocí čipu TPM standard pro klíče podepsaný token sdíleného přístupového podpisu (SAS) k dispozici. Nevyžaduje fyzické TPM na zařízení, ale služba očekává, že ověřit identitu pomocí ověřovacího klíče na [specifikace TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení nebo modulu hardwarového zabezpečení, se používá pro zabezpečené úložiště hardwarové zařízení tajemství a je nejbezpečnější formu tajný úložiště. X.509 – certifikáty a tokeny SAS může být uložen v modulu hardwarového zabezpečení. Moduly hardwarového zabezpečení lze použít s obou mechanismů ověření zřizování podporuje.

> [!TIP]
> Důrazně doporučujeme pomocí modulu hardwarového zabezpečení zařízení na zařízení bezpečně uložit tajných klíčů.

Tajné klíče zařízení může být taky uložené v softwaru (paměť), ale je méně bezpečné formu úložiště než modulu hardwarového zabezpečení.

## <a name="trusted-platform-module"></a>Trusted Platform Module

Čip TPM mohou odkazovat na standard týkající se bezpečného ukládání klíče, které slouží k ověření platformu nebo mohou odkazovat na rozhraní vstupně-výstupních operací používají k interakci s moduly implementace standardní. Čipy TPM může existovat jako diskrétní hardware, integrované hardware, založené na firmwaru, nebo na základě softwaru. Další informace o [čipy TPM a TPM ověření](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Služba zřizování zařízení podporuje jenom čip TPM 2.0.

Ověření identity TPM je založena na nonce výzvy, které používá kořenové klíče ověřovací a úložiště k dispozici podepsaný token sdíleného přístupového podpisu (SAS).

### <a name="endorsement-key"></a>Ověřovací klíč

Ověřovací klíč je asymetrický klíč nachází v čipu TPM, které bylo generované interně nebo vložit na výrobní čas a je jedinečný pro každý čip TPM. Ověřovací klíč nelze změnit ani odebrat. Soukromá část ověřovacího klíče nikdy vydání mimo čipu TPM, přičemž veřejnou část ověřovacího klíče se používá k rozpoznání pravosti čipu TPM. Další informace o [ověřovací klíč](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Klíč kořenového úložiště

Kořenový klíč úložiště je uložená v čipu TPM a slouží k ochraně klíčů TPM, které jsou vytvořené pomocí aplikací, aby tyto klíče nejde použít bez čipu TPM. Kořenový klíč úložiště se vygeneruje, když převzít vlastnictví čipu TPM; Když zrušíte zaškrtnutí čipu TPM, takže nového uživatele můžete převzít vlastnictví, je generována nového kořenového klíče úložiště. Další informace o [úložiště kořenový klíč](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509 – certifikáty

Pomocí certifikátů X.509 jako mechanismus ověření je vynikající způsob, jak škálování produkční a zjednodušit zřizování zařízení. X.509 – certifikáty jsou obvykle uspořádány v řetězu certifikátů, ve kterém je každý certifikát v řetězu podepsané soukromým klíčem další vyšší certifikátu, a tak dále, se ukončuje v certifikát podepsaný svým držitelem kořenové důvěryhodnosti. Tím dojde k delegované řetěz certifikátů z kořenového certifikátu vygenerovaných důvěryhodné kořenové certifikační autority (CA) dolů prostřednictvím každý zprostředkující certifikační Autority "listu" certifikát koncové entity na zařízení nainstalovaná. Další informace najdete v tématu [zařízení ověřování pomocí certifikátů X.509 certifikační Autority](/azure/iot-hub/iot-hub-x509ca-overview). 

Řetěz certifikátů často představuje některé logickou nebo fyzickou hierarchie přidružená k zařízení. Například může výrobce:
- certifikát podepsaný svým držitelem kořenové certifikační Autority
- Generovat jedinečný certifikát zprostředkující certifikační Autority pro každý objekt pro vytváření pomocí kořenového certifikátu
- použití certifikátu každý objekt factory pro generování jedinečný certifikát zprostředkující certifikační Autority pro každý řádek produkční v zařízení
- a nakonec řádku produkční certifikát, použít k vygenerování certifikátu jedinečný zařízení (koncové entity) pro každé zařízení vyrobila na řádek. 

Další informace najdete v tématu [koncepční přehled o certifikáty X.509 certifikační Autority v odvětví IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Kořenový certifikát

Kořenový certifikát je certifikát podepsaný svým držitelem X.509 představující certifikační autoritu (CA). Je nejzažšímu nebo kotva vztahu důvěryhodnosti, řetěz certifikátů. Kořenové certifikáty můžete samoobslužné vystavený organizace nebo zakoupit od kořenové certifikační autority. Další informace najdete v tématu [certifikáty získat certifikační Autority X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Kořenový certifikát, můžete také označuje jako certifikát kořenové Certifikační autority.

### <a name="intermediate-certificate"></a>Zprostředkující certifikát

Zprostředkující certifikát je certifikát X.509, který byl podepsán kořenový certifikát (nebo jiný certifikát zprostředkující kořenový certifikát ve svém řetězu). Poslední zprostředkující certifikát v řetězu se používá k podepisování certifikátů typu list. Zprostředkující certifikát, můžete také označuje jako zprostředkující Certifikační autority.

### <a name="end-entity-leaf-certificate"></a>Certifikát koncové entity "listu"

Certifikát listu, nebo certifikát koncové entity, identifikuje držitele certifikátu. Má kořenový certifikát ve svém řetězu certifikátů a také v počtu nula či více zprostředkující certifikáty. Listu certifikát se používá k podepisování jiných certifikátů. Jednoznačně identifikují zařízení ke službě zřizování a se někdy označuje jako certifikát zařízení. Při ověřování zařízení používá soukromý klíč přidružený k tomuto certifikátu reagovat na ověření u sebe výzvy ze služby. Další informace najdete v tématu [ověřování zařízení podepsané certifikáty certifikační Autority X.509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Řízení přístupu k zařízení ke službě zřizování pomocí certifikátů X.509

Zřizování služby zpřístupní dva typy zápisu položky, které můžete použít k řízení přístupu pro zařízení, které používají tento mechanismus ověření X.509:  

- [Jednotlivé registrace](./concepts-service.md#individual-enrollment) položky jsou nakonfigurovány s zařízení certifikát přidružený k určitému zařízení. Tyto položky řízení registrace pro konkrétní zařízení.
- [Registrace skupiny](./concepts-service.md#enrollment-group) položky, které jsou spojené s konkrétní zprostředkující nebo certifikát kořenové certifikační Autority. Tyto položky řízení registrace pro všechna zařízení, které mají které mezilehlé nebo kořenový certifikát v jejich řetězu certifikátů. 

Když se zařízení připojí ke službě zřizování, službu upřednostňuje konkrétnější položky registrace v méně specifické položky pro zápis. To znamená jednotlivé zápisu pro zařízení existuje, službu zřizování se vztahuje na tuto položku. Pokud je bez individuálních registrace pro zařízení a skupinu registrace pro první zprostředkující certifikát v řetězu certifikátů v zařízení existuje, službu použije tuto položku a tak dále, řetězem do kořenového adresáře. Služba se vztahuje na první použít položku, která najde, tak, aby:

- Pokud je první byla nalezena položka registrace je povoleno, poskytne službu zařízení.
- Pokud je první byla nalezena položka registrace vypnutá, neposkytuje službu zařízení.  
- Pokud pro některý z certifikátů v řetězu certifikátů zařízení se nenajde žádný záznam zápisu, neposkytuje službu zařízení. 

Tento mechanismus a hierarchická struktura řetězů certifikátů poskytuje výkonné flexibilitu v tom, jak můžete řídit přístup pro jednotlivá zařízení, stejně jako u skupiny zařízení. Představte si například pět zařízení s následující řetězy certifikátu: 

- *Zařízení 1*: kořenový certifikát -> certifikát A -> certifikát zařízení 1
- *Zařízení 2*: kořenový certifikát -> certifikát A -> certifikát zařízení 2
- *Zařízení 3*: certifikát A -> certifikát zařízení 3-kořenového certifikátu >
- *Zařízení 4*: certifikát B -> certifikát zařízení 4-kořenového certifikátu >
- *Zařízení 5*: certifikát B -> certifikát zařízení 5-kořenového certifikátu >

Na začátku můžete vytvořit položku povoleno jednu skupinu registrace pro kořenový certifikát pro povolení přístupu pro všechny pět zařízení. Pokud bude k ohrožení bezpečnosti certifikátu B později, můžete vytvořit položku Zakázané registrace skupiny pro certifikát B, aby se zabránilo *4 zařízení* a *5 zařízení* z registrace. Pokud stále novější *zařízení 3* se změní na ohrožení zabezpečení, můžete vytvořit položku Zakázané jednotlivých registrace pro svůj certifikát. To odvolá přístup pro *zařízení 3*, ale stále umožňuje *zařízení 1* a *zařízení 2* k registraci.
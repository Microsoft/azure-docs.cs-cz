---
title: Správce zabezpečení Azure IoT Edge – Azure IoT Edge
description: Spravuje postoj zabezpečení zařízení IoT Edge a integritu služeb zabezpečení.
services: iot-edge
keywords: zabezpečení, zabezpečený prvek, enkláva, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548692"
---
# <a name="azure-iot-edge-security-manager"></a>Správce zabezpečení Azure IoT Edge

Správce zabezpečení Azure IoT Edge je dobře ohraničené jádro zabezpečení pro ochranu zařízení IoT Edge a všech jeho součástí abstrakcí zabezpečeného křemíkového hardwaru. Správce zabezpečení je ústředním bodem pro posílení zabezpečení a poskytuje bod integrace technologie výrobcům originálního zařízení (OEM).

![Správce zabezpečení Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Správce zabezpečení IoT Edge si klade za cíl chránit integritu zařízení IoT Edge a všechny vlastní softwarové operace. Správce zabezpečení převádí vztah důvěryhodnosti z podkladového kořenového hardwaru hardwaru důvěryhodnosti (pokud je k dispozici) na zaběhový modul IoT Edge a monitoruje probíhající operace.  Správce zabezpečení IoT Edge je software, který spolupracuje se zabezpečeným křemíkovým hardwarem (pokud je k dispozici), aby pomohl zajistit co nejvyšší možné zabezpečení zabezpečení.  

Mezi povinnosti správce zabezpečení IoT Edge patří, ale nejsou omezeny na:

* Zabezpečené a měřené zaváděcí připojení zařízení Azure IoT Edge.
* Zřizování identity zařízení a případně přechod vztahu důvěryhodnosti.
* Hostuj a chraňte součásti zařízení cloudových služeb, jako je služba zřizování zařízení.
* Bezpečně zřizte moduly IoT Edge s jedinečnými identitami.
* Gatekeeper na hardwarový kořen hardwaru zařízení prostřednictvím notářských služeb.
* Sledujte integritu operací IoT Edge za běhu.

Správce zabezpečení IoT Edge obsahuje tři součásti:

* Bezpečnostní daemon IoT Edge.
* Vrstva abstrakce platformy hardwarového zabezpečení (HSM PAL).
* Volitelný, ale vysoce doporučený hardwarový kořen křemíku důvěry nebo modulhardwarového zabezpečení.

## <a name="the-iot-edge-security-daemon"></a>Daemon zabezpečení IoT Edge

Déon zabezpečení IoT Edge je zodpovědný za logické operace správce zabezpečení IoT Edge. Představuje významnou část důvěryhodné výpočetní základny zařízení IoT Edge.

### <a name="design-principles"></a>Principy návrhu

Déon zabezpečení IoT Edge se řídí dvěma základními principy: maximalizuje provozní integritu a minimalizuje nafouknutí a konve.

#### <a name="maximize-operational-integrity"></a>Maximalizujte provozní integritu

Dém zabezpečení IoT Edge pracuje s nejvyšší možnou integritou v rámci obranných schopností libovolného kořenového adresáře důvěryhodného hardwaru. Při správné integraci kořen důvěryhodného hardwaru měří a monitoruje bezpečnostní hod staticky a za běhu, aby odolával manipulaci.

Fyzický přístup je vždy hrozbou pro zařízení IoT. Hardwarový kořen důvěryhodnosti hraje důležitou roli při obraně integrity daemonu zabezpečení IoT Edge.  Hardwarový kořen důvěry přichází ve dvou variantách:

* zabezpečené prvky pro ochranu citlivých informací, jako jsou tajné klíče a kryptografické klíče.
* zabezpečte enklávy pro ochranu tajných kódů, jako jsou klíče, a citlivé úlohy, jako je měření a fakturace.

Existují dva druhy spuštění prostředí pro použití hardwarového kořenového adresáře důvěryhodnosti:

* Standardní nebo bohaté spuštění prostředí (REE), který závisí na použití zabezpečených prvků k ochraně citlivých informací.
* Důvěryhodné prostředí pro spuštění (TEE), které spoléhá na použití zabezpečené enklávy technologie k ochraně citlivých informací a nabízí ochranu provádění softwaru.

Pro zařízení, která používají zabezpečené enklávy jako hardwarový kořen důvěryhodnosti, by citlivá logika v rámci demonu zabezpečení IoT Edge měla být uvnitř enklávy.  Necitlivé části bezpečnostního daemonu mohou být mimo TEE.  V každém případě by výrobci originálních návrhů (ODM) a výrobci originálního vybavení (OEM) měli rozšířit důvěru ze svého bezpečnostního agenta, aby změřili a bránili integritu daemonu zabezpečení IoT Edge při startu a běhu.

#### <a name="minimize-bloat-and-churn"></a>Minimalizujte nafouknutí a konve

Dalším základním principem pro zabezpečení IoT Edge daemon je minimalizovat konve.  Pro nejvyšší úroveň důvěryhodnosti může dém zabezpečení IoT Edge pevně spojit s kořenem hardwaru zařízení důvěryhodnosti a pracovat jako nativní kód.  Je běžné, že tyto typy realizací aktualizovat software daemon prostřednictvím kořene hardwaru paty zabezpečené cesty aktualizace (na rozdíl od OS za předpokladu, že aktualizace mechanismy), což může být náročné v některých scénářích.  Zatímco obnovení zabezpečení se doporučuje pro zařízení IoT, nadměrné požadavky na aktualizace nebo velké datové části aktualizací můžete rozšířit ohrožení povrchu mnoha způsoby.  Mezi příklady patří přeskočení aktualizací pro maximalizaci provozní dostupnosti nebo kořenový hardware důvěryhodnosti, který je příliš omezen na zpracování velkých datových částí aktualizací.  Jako takový návrh daemon zabezpečení IoT Edge je stručné zachovat stopu a důvěryhodné výpočetní základny malé a minimalizovat požadavky na aktualizace.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura bezpečnostního daemonu IoT Edge

![Déon zabezpečení Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Déon zabezpečení IoT Edge využívá všech dostupných hardwarových kořenových technologií důvěryhodnosti pro posílení zabezpečení.  Umožňuje také operace rozděleného světa mezi prostředím pro standardní/bohaté provádění (REE) a důvěryhodným prostředím pro spuštění (TEE), když hardwarové technologie nabízejí důvěryhodné prostředí pro spuštění. Rozhraní specifická pro role umožňují hlavní součásti IoT Edge zajistit integritu zařízení IoT Edge a jeho operace.

#### <a name="cloud-interface"></a>Cloudové rozhraní

Cloudové rozhraní umožňuje demonu zabezpečení IoT Edge přístup ke cloudovým službám, jako jsou cloudové komplimenty zabezpečení zařízení, jako je obnovení zabezpečení.  Například daemon zabezpečení IoT Edge aktuálně používá toto rozhraní pro přístup ke službě Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) pro správu životního cyklu identity zařízení.  

#### <a name="management-api"></a>Rozhraní API pro správu

Daemon zabezpečení IoT Edge nabízí rozhraní API pro správu, které je voláno agentem IoT Edge při vytváření/spouštění/zastavování/odebírání modulu IoT Edge. Bezpečnostní daemon ukládá "registrace" pro všechny aktivní moduly. Tyto registrace mapovat identitu modulu na některé vlastnosti modulu. Například tyto vlastnosti modulu zahrnují identifikátor procesu (pid) procesu spuštěného v kontejneru a hash obsahu kontejneru dockeru.

Tyto vlastnosti jsou používány rozhraní API zatížení (popsané níže) k ověření, že volající je oprávněn pro akci.

Rozhraní API pro správu je privilegované rozhraní API, které lze volat pouze od agenta IoT Edge.  Vzhledem k tomu, že daemon zabezpečení IoT Edge zaváděa a spustí agenta IoT Edge, ověří, že agent IoT Edge nebyl zfalšován, pak může vytvořit implicitní registraci pro agenta IoT Edge. Stejný proces atestace, který používá rozhraní API zatížení, také omezuje přístup k rozhraní API pro správu pouze agentovi IoT Edge.

#### <a name="container-api"></a>Rozhraní API kontejneru

Rozhraní API kontejneru spolupracuje s kontejnerovým systémem, který se používá pro správu modulů, jako je Moby nebo Docker.

#### <a name="workload-api"></a>Rozhraní API pracovního vytížení

Rozhraní API zatížení je přístupné pro všechny moduly. Poskytuje doklad totožnosti, buď jako podepsaný token zakořeněný modulem hss nebo certifikát X509 a odpovídající balíček důvěryhodnosti do modulu. Sada důvěryhodných certifikátů obsahuje certifikáty certifikační autority pro všechny ostatní servery, kterým by moduly měly důvěřovat.

Dém zabezpečení IoT Edge používá proces atestace k ochraně tohoto rozhraní API. Když modul volá toto rozhraní API, pokusí se komanodivka zabezpečení najít registraci identity. Pokud je úspěšná, používá vlastnosti registrace k měření modulu. Pokud výsledek procesu měření odpovídá registraci, je vygenerován nový doklad totožnosti. Odpovídající certifikáty certifikační autority (balíček důvěryhodnosti) jsou vráceny do modulu.  Modul používá tento certifikát pro připojení k ioT hubu, další moduly nebo spuštění serveru. Když se podpisový token nebo certifikát blíží vypršení platnosti, je odpovědností modulu požádat o nový certifikát.

### <a name="integration-and-maintenance"></a>Integrace a údržba

Microsoft udržuje hlavní základ kódu pro [demon zabezpečení IoT Edge na GitHubu](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalace a aktualizace

Instalace a aktualizace demonu zabezpečení IoT Edge jsou spravovány prostřednictvím systému správy balíčků operačního systému. Zařízení IoT Edge s hardwarovým kořenem důvěryhodnosti by měla poskytovat další posílení integrity daemonu pomocí správy jeho životního cyklu prostřednictvím zabezpečených systémů správy spouštění a aktualizací. Výrobci zařízení by měli tyto cesty prozkoumat na základě příslušných možností zařízení.

#### <a name="versioning"></a>Správa verzí

Běhový běh IoT Edge sleduje a hlásí verzi daemonu zabezpečení IoT Edge. Verze je hlášena jako atribut *runtime.platform.version* modulu agenta IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Vrstva abstrakce platformy hardwarového zabezpečení (HSM PAL)

HSM PAL abstrahuje veškerý kořen důvěryhodného hardwaru, aby izoloval vývojáře nebo uživatele IoT Edge od jejich složitosti.  Zahrnuje kombinaci aplikačního programovacího rozhraní (API) a procedur komunikace mezi doménami, například komunikace mezi standardním prostředím a zabezpečenou enklávou.  Skutečná implementace HSM PAL závisí na konkrétním zabezpečeném hardwaru, který se používá. Jeho existence umožňuje použití prakticky jakéhokoli bezpečného křemíkového hardwaru.

## <a name="secure-silicon-root-of-trust-hardware"></a>Zabezpečit kořen křemíku důvěryhodného hardwaru

Bezpečný křemík je nezbytný pro ukotvení důvěry uvnitř hardwaru zařízení IoT Edge.  Bezpečný křemík přichází v různých řadách, který zahrnuje trusted platformmodule (TPM), vestavěný secure element (eSE), ARM TrustZone, Intel SGX a vlastní zabezpečené křemíkové technologie.  Vzhledem k hrozbám spojeným s fyzickou dostupností zařízení IoT se doporučuje používat bezpečný kořen důvěry v zařízení.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integrace a údržba správce zabezpečení IoT Edge

Správce zabezpečení IoT Edge si klade za cíl identifikovat a izolovat součásti, které brání zabezpečení a integritu platformy Azure IoT Edge pro vlastní posílení. Třetí strany, stejně jako výrobci zařízení, by měly využívat vlastní funkce zabezpečení, které jsou k dispozici s hardwarem svého zařízení.  Odkazy, které ukazují, jak posílit správce zabezpečení Azure IoT pomocí modulu Trusted Platform Module (TPM) na platformách Linux a Windows, najdete v dalších krocích. Tyto příklady používají software nebo virtuální tpms, ale přímo se vztahují na použití diskrétních zařízení TPM.  

## <a name="next-steps"></a>Další kroky

Přečtěte si blog o [zabezpečení inteligentní hrany](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Vytvořte a zřizte [zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači s Linuxem](how-to-auto-provision-simulated-device-linux.md).

Vytvořte a zřizujte [zařízení IoT Edge se simulovaným čipem TPM v systému Windows](how-to-auto-provision-simulated-device-windows.md).

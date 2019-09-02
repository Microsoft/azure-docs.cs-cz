---
title: Zjistěte, jak správce zabezpečení chrání zařízení, software – Azure IoT Edge | Dokumentace Microsoftu
description: Spravuje postoj zabezpečení zařízení IoT Edge a integrity služby zabezpečení.
services: iot-edge
keywords: zabezpečení, zabezpečené element, enklávy, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f137070cb8a62f2c11f9e2688b5c7db47c1b866f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208212"
---
# <a name="azure-iot-edge-security-manager"></a>Správce zabezpečení služby Azure IoT Edge

Správce zabezpečení Azure IoT Edge je základní dobře ohraničené zabezpečení pro ochranu zařízení IoT Edge a všechny její součásti podle abstrahovat zabezpečené silicon hardwaru. Je to ústřední bod pro posílení zabezpečení a poskytuje integrační bod pro technologie výrobcům OEM (Original Equipment Manufacturer).

![Správce zabezpečení služby Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Správce zabezpečení IoT Edge, zaměřuje se chránit integritu zařízení IoT Edge a všechny operace vlastní software. Správce zabezpečení přechází z základní hardwarové kořene hardwaru důvěryhodnosti (Pokud je k dispozici), aby mohl spustit modul runtime IoT Edge a monitorovat průběžné operace.  IoT Edge Security Manager je software pracující společně s zabezpečeným Silicon hardwarem (Pokud je k dispozici), který vám umožní zajistit nejvyšší možný bezpečnostní ujištění.  

Mezi odpovědnosti IoT Edge Security Manageru patří mimo jiné:

* Zabezpečené a měří spuštění zařízení Azure IoT Edge.
* Zřízení identit zařízení a přechod důvěryhodnosti kde je to možné.
* Hostování a chránit zařízení komponentami cloudových služeb, jako jsou služby Device Provisioning.
* Bezpečně zřídí IoT Edge moduly s jedinečnými identitami.
* Gatekeepera, aby zařízení hardwaru důvěryhodný kořenový certifikát prostřednictvím notář služeb.
* Monitorování integrity operací IoT Edge v době běhu.

Správce zabezpečení IoT Edge zahrnuje tři komponenty:

* Démon procesu zabezpečení IoT Edge.
* Zabezpečení modulu platformy abstrakce hardwaru vrstvy (PAL modulu hardwarového zabezpečení).
* Volitelné, ale důrazně doporučené hardwarové silicon kořenové důvěryhodnosti nebo modulu hardwarového zabezpečení.

## <a name="the-iot-edge-security-daemon"></a>Démon zabezpečení IoT Edge

Démon zabezpečení IoT Edge zodpovídá za logické operace nástroje IoT Edge Security Manager. Představuje významnou část základu IoT Edgeho zařízení pro účely důvěryhodných počítačů. 

### <a name="design-principles"></a>Principy návrhu

Démon zabezpečení IoT Edge se skládá ze dvou základních principů: maximalizace provozní integrity a minimalizace dispozici determinističtější a změn.

#### <a name="maximize-operational-integrity"></a>Maximalizovat provozní integrity

IoT Edge démon zabezpečení funguje s nejvyšší možnou integritou v rámci schopnosti obrany kteréhokoli daného kořene hardwaru důvěryhodnosti. Integrace správná kořenové důvěryhodnosti hardwaru opatření a monitoruje démona zabezpečení staticky a za běhu proti manipulaci.

Fyzický přístup je vždy hrozbou pro zařízení IoT. Hardwarový kořen vztahu důvěryhodnosti hraje důležitou roli při zachování integrity procesu démona zabezpečení IoT Edge.  Kořenová kořenová složka vztahu důvěryhodnosti se dodává ve dvou variantách:

* tajných kódů a kryptografických klíčů, jako je zabezpečené prvky pro ochranu citlivých informací.
* zabezpečené enklávy pro ochranu tajné kódy, jako jsou klíče a citlivých úloh, jako jsou měření a fakturace.

Existují dva druhy prováděcích prostředí, které používají kořenový adresář hardwaru důvěryhodnosti:

* Standardní nebo bohatá spouštěcí prostředí (REE), které spoléhá na použití zabezpečených prvků pro ochranu citlivých informací.
* Prostředí pro důvěryhodné spuštění (TEE), které spoléhá na použití zabezpečené technologie enklávy k ochraně citlivých informací a poskytování ochrany před spuštěním softwaru.

Pro zařízení, která používají zabezpečený enclaves jako hardwarový kořen vztahu důvěryhodnosti, by měla být citlivá logika v rámci služby IoT Edge Security daemon uvnitř enklávy.  Necitlivá část démona zabezpečení může být mimo TEE.  V každém případě by výrobci originálního prostředí (ODM) a výrobci OEM měli pro účely měření a ochrany integrity procesu démona zabezpečení IoT Edge při spuštění a běhu chránit důvěryhodnost.

#### <a name="minimize-bloat-and-churn"></a>Minimalizovat determinističtější a četnosti změn

Další základní zásadou pro démona zabezpečení IoT Edge je minimalizace změn.  Pro nejvyšší úroveň důvěryhodnosti může démon zabezpečení IoT Edge pevně spojit s hardwarem hardwaru zařízení, který důvěřuje, a fungovat jako nativní kód.  Je běžné, že tyto typy oddělení aktualizuje software démona prostřednictvím kořenového adresáře hardwaru cest aktualizace důvěryhodnosti (na rozdíl od mechanismů aktualizace poskytovaných operačním systémem), což může být v některých případech náročné.  I když se pro zařízení IoT doporučuje obnovování zabezpečení, nadměrné požadavky na aktualizace nebo rozsáhlé datové části aktualizace mohou plochu hrozby rozšířit mnoha způsoby.  Mezi příklady patří přeskočení aktualizací, aby se maximalizovala dostupnost provozní nebo kořenové důvěryhodnosti hardwaru moc omezené ke zpracování velkých aktualizací datových částí.  V takovém případě je návrh démona zabezpečení IoT Edge stručnější, aby se zajistila Základnová a důvěryhodná výpočetní databáze a minimalizovala požadavky na aktualizaci.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura démon zabezpečení IoT Edge

![Démon zabezpečení Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge démon zabezpečení využívá všechny dostupné hardwarové kořenové složky důvěryhodných technologií pro posílení zabezpečení.  Umožňuje taky rozdělit operace mezi standardním a bohatým spouštěcím prostředím (REE) a prostředím TEE (Trusted Execution Environment), když hardwarové technologie nabízejí prostředí pro důvěryhodné spouštění. Rozhraní specifická pro role povolují hlavní součásti IoT Edge, aby se zajistila integrita IoT Edge zařízení a jeho operací.

#### <a name="cloud-interface"></a>Cloudové rozhraní

Cloudové rozhraní umožňuje, aby démon zabezpečení IoT Edge mohl přistupovat ke cloudovým službám, jako je cloudový přístup k zabezpečení zařízení, jako je třeba obnovení zabezpečení.  Například démon zabezpečení IoT Edge aktuálně používá toto rozhraní pro přístup ke službě Azure IoT Hub [Device](https://docs.microsoft.com/azure/iot-dps/) Provisioning pro správu životního cyklu identity zařízení.  

#### <a name="management-api"></a>Rozhraní API pro správu

IoT Edge démon zabezpečení nabízí rozhraní API pro správu, které je voláno agentem IoT Edge při vytváření, spouštění, zastavování/odebírání modulu IoT Edge. Démon zabezpečení ukládá "registrace" pro všechny aktivní moduly. Tyto registrace mapování identit modul na některé vlastnosti modulu. Pár příkladů, pro tyto vlastnosti jsou identifikátor procesu (pid) proces, který běží v kontejneru nebo hodnotu hash obsahu kontejneru dockeru.

Tyto vlastnosti používá rozhraní API úlohy (popsané níže) k ověření, že volající je autorizován k provedení akce.

Rozhraní API pro správu je privilegované rozhraní API, které lze volat pouze z agenta IoT Edge.  Vzhledem k tomu, že démon zabezpečení IoT Edge bootstraps a spuštění agenta IoT Edge, může vytvořit implicitní registrace agenta IoT Edge po má ověřuje, jestli nebylo manipulováno agenta IoT Edge. Stejný proces ověření identity, jaký používá rozhraní API pro úlohy, taky omezuje přístup k rozhraní API pro správu jenom na agenta IoT Edge.

#### <a name="container-api"></a>Kontejner rozhraní API

Rozhraní API kontejneru spolupracuje se systémem kontejneru, který se používá ke správě modulů, jako je Moby nebo Docker.

#### <a name="workload-api"></a>Úloha rozhraní API

Rozhraní API úlohy je dostupné pro všechny moduly. Poskytuje důkaz identity, buď jako podepsaný token HSM root nebo certifikát x509, a odpovídající sadu důvěryhodnosti pro modul. Sada důvěryhodnosti obsahuje certifikáty certifikační autority pro všechny ostatní servery, na které mají tyto moduly důvěřovat.

Démon zabezpečení IoT Edge pro ochranu tohoto rozhraní API používá proces ověření identity. Když modul toto rozhraní API volá, pokusí se démon zabezpečení najít registraci pro identitu. V případě úspěchu se používá vlastnosti registrace k měření modulu. Pokud výsledek procesu měření odpovídá registraci, je vygenerován nový důkaz identity. Do modulu se vrátí odpovídající certifikáty certifikační autority (sada Trust).  Modul používá tento certifikát k připojení ke službě IoT Hub, ostatní moduly, nebo spuštění serveru. Pokud se podepsaný token nebo certifikát blíží k vypršení platnosti, je zodpovědností modulu požádat o nový certifikát. 

### <a name="integration-and-maintenance"></a>Integrace a údržba

Společnost Microsoft udržuje hlavní kód základu [démon zabezpečení IoT Edge na Githubu](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalace a aktualizace

Instalace a aktualizace procesu démona zabezpečení IoT Edge jsou spravovány prostřednictvím systém správy balíčků operačního systému. Zařízení IoT Edge s hardwarovým kořenem důvěry by měla poskytovat další posílení zabezpečení démona tím, že spravuje svůj životní cyklus prostřednictvím systémů pro správu zabezpečeného spouštění a aktualizací. Tvůrci zařízení by měli prozkoumat tyto cesty vedoucíy na základě příslušných možností zařízení.

#### <a name="versioning"></a>Správa verzí

Modul runtime IoT Edge sleduje a sestavy verzi démona zabezpečení IoT Edge. Verze je hlášena jako atribut *runtime. Platform. version* IoT Edgeho modulu agenta, který ohlásil vlastnost.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Zabezpečení modulu platformy HAL (PAL modulu hardwarového zabezpečení)

HSM PAL abstrahuje všechny kořenové důvěryhodnosti hardwaru Izolovat uživatele služby IoT Edge z jejich složitosti nebo vývojáře.  Zahrnuje kombinaci rozhraní API (Application Programming Interface) a komunikačních postupů napříč doménami, například komunikaci mezi standardním spouštěcím prostředím a zabezpečenou enklávy.  Skutečná implementace HSM PAL, závisí na konkrétní zabezpečený hardware používá. Jeho existence umožňuje použití prakticky jakéhokoli zabezpečeného karbidu hardwaru.

## <a name="secure-silicon-root-of-trust-hardware"></a>Zabezpečené silicon kořenové důvěryhodnosti hardwaru

Zabezpečené silicon je potřeba ukotvení důvěryhodnosti uvnitř hardwaru zařízení IoT Edge.  Zabezpečené silicon se dělí na různých Trusted Platform Module (TPM), zabezpečené vložený Element (technologii eSE), ARM TrustZone, Intel SGX a vlastní zabezpečené silicon technologie.  Použití zabezpečeného křemíkového kořene důvěryhodnosti v zařízeních se doporučuje vzhledem k hrozbám přidruženým k fyzické dostupnosti zařízení IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integrace správce zabezpečení ve službě IoT Edge a údržba

IoT Edge Security Manager se zaměřuje na identifikaci a izolaci komponent, které chrání zabezpečení a integritu Azure IoT Edge platformy pro vlastní posílení zabezpečení. Třetí strany, jako jsou tvůrci zařízení, by měli používat vlastní funkce zabezpečení, které jsou k dispozici u svého hardwaru zařízení.  V části Další kroky najdete odkazy, které ukazují, jak posílit správce zabezpečení Azure IoT pomocí čipu TPM (Trusted Platform Module) na platformách Linux a Windows. Tyto příklady používají software nebo Virtual čipy TPM, ale přímo se používají pro používání diskrétních zařízení TPM.  

## <a name="next-steps"></a>Další postup

Přečtěte si blog na [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Vytvoření a zřízení [zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači se systémem Linux](how-to-auto-provision-simulated-device-linux.md).

Vytvoření a zřízení [IoT Edge zařízení s simulovaným čipem TPM ve Windows](how-to-auto-provision-simulated-device-windows.md).

---
title: Zjistěte, jak správce zabezpečení chrání zařízení, software – Azure IoT Edge | Dokumentace Microsoftu
description: Spravuje postoj zabezpečení zařízení IoT Edge a integrity služby zabezpečení.
services: iot-edge
keywords: zabezpečení, zabezpečené element, enklávy, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: eda572c0f4abedd59c912dcf1de2cf3c6e226b5c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091920"
---
# <a name="azure-iot-edge-security-manager"></a>Správce zabezpečení služby Azure IoT Edge

Správce zabezpečení Azure IoT Edge je základní dobře ohraničené zabezpečení pro ochranu zařízení IoT Edge a všechny její součásti podle abstrahovat zabezpečené silicon hardwaru. Je ústředním bodem pro posílení zabezpečení a poskytuje integrační bod technologie k původní výrobci zařízení (OEM).

![Správce zabezpečení služby Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

Správce zabezpečení IoT Edge, zaměřuje se chránit integritu zařízení IoT Edge a všechny operace vlastní software.  Dělá to tak přechod vztah důvěryhodnosti z základní hardware kořenové důvěryhodnosti hardwaru bezpečně spustit modul runtime IoT Edge a pokračovat v monitorování integrity jeho operace (Pokud je k dispozici).  Správce zabezpečení IoT Edge v podstatě zahrnuje software funguje ve spojení s hardwarem zabezpečené silicon tam, kde je k dispozici a povoleno usnadňují doručovat nejvyšší záruky zabezpečení, je to možné.  

Odpovědnosti správce zabezpečení IoT Edge patří (mimo jiné):

* Zabezpečené a měří spuštění zařízení Azure IoT Edge.
* Zřízení identit zařízení a přechod důvěryhodnosti kde je to možné.
* Hostování a chránit zařízení komponentami cloudových služeb, jako jsou služby Device Provisioning.
* Zabezpečené zřizování moduly IoT Edge s jedinečnou identitu modulu.
* Gatekeepera, aby zařízení hardwaru důvěryhodný kořenový certifikát prostřednictvím notář služeb.
* Monitorování integrity operací IoT Edge v době běhu.

Správce zabezpečení IoT Edge zahrnuje tři komponenty:

* Démon procesu zabezpečení IoT Edge.
* Zabezpečení modulu platformy abstrakce hardwaru vrstvy (PAL modulu hardwarového zabezpečení).
* Volitelné, ale důrazně doporučené hardwarové silicon kořenové důvěryhodnosti nebo modulu hardwarového zabezpečení.

## <a name="the-iot-edge-security-daemon"></a>Démon zabezpečení IoT Edge

Démon zabezpečení IoT Edge je zodpovědná za logické operace správce zabezpečení IoT Edge softwaru.  To se skládá z část důvěryhodný výpočetní základ zařízení IoT Edge. 

### <a name="design-principles"></a>Principy návrhu

Odpovídajícím způsobem a prakticky provést její odpovědnosti, IoT Edge zabezpečení démon cílem je v souladu s těmito zásadami core:

#### <a name="maximize-operational-integrity"></a>Maximalizovat provozní integrity

Démon zabezpečení IoT Edge je navržená pracovat s nejvyšší integrity v rámci funkce ochrany před mobilními jakékoli dané kořenové důvěryhodnosti hardwaru je to možné. Integrace správná kořenové důvěryhodnosti hardwaru opatření a monitoruje démona zabezpečení staticky a za běhu proti manipulaci.

Fyzický přístup je vždy hrozeb pro zařízení IoT obecně.  Hardware důvěryhodnému kořenovému proto hraje důležitou roli v ochrana integrity démona zabezpečení IoT Edge.  Hardware důvěryhodnému kořenovému přicházet mají dvě varianty:

* tajných kódů a kryptografických klíčů, jako je zabezpečené prvky pro ochranu citlivých informací.
* zabezpečené enklávy pro ochranu tajné kódy, jako jsou klíče a citlivých úloh, jako jsou měření a fakturace.

Existují tyto dva modely pro využívání hardwarové důvěryhodnému kořenovému vyvolává dva druhy prostředí spuštění:

* Standardní nebo bohaté prostředí pro spuštění (RÍ), která závisí na použití prvků zabezpečení k ochraně citlivých informací.
* Důvěryhodné spouštění prostředí (TEE), které spoléhají na použití zabezpečeného enklávy technologie k ochraně citlivých informací a nabízí ochranu pro spuštění softwaru.

Pro zařízení pomocí zabezpečeného enklávy jako důvěryhodný kořenový certifikát hardwaru citlivé logiky v rámci démon zabezpečení IoT Edge očekává se, které se nacházejí v enklávě.  Nejsou citliví části démona zabezpečení se může nacházet mimo TEE.  V každém případě očekává se původní návrh výrobců (ODM) a výrobce OEM (OEM) k rozšíření vztahu důvěryhodnosti z jejich modulu hardwarového zabezpečení pro měření a chránit integritu démona Edge zabezpečení při spuštění a modulu runtime.

#### <a name="minimize-bloat-and-churn"></a>Minimalizovat determinističtější a četnosti změn

Jiné základní princip pro démona Edge zabezpečení je minimalizace změn.  Nejvyšší úrovně důvěryhodnosti můžete proces démon zabezpečení IoT Edge úzce spárovat se důvěryhodnému kořenovému hardwarové zařízení, pokud je k dispozici a fungují jako nativní kód.  Je běžné, že tyto typy realizations aktualizaci softwaru démon prostřednictvím hardwaru kořen cesty vztah důvěryhodnosti pro zabezpečený aktualizace (na rozdíl od operačního systému poskytuje mechanismus aktualizace), což může být náročné, v závislosti na konkrétní scénář hardware a nasazení.  Při obnovení zabezpečení silné doporučení pro zařízení IoT, to znamená z důvodu, že požadavky nadměrné aktualizace nebo aktualizace velkých datových částí můžete rozšířit na plochu hrozbu mnoha způsoby.  Mezi příklady patří přeskočení aktualizací, aby se maximalizovala dostupnost provozní nebo kořenové důvěryhodnosti hardwaru moc omezené ke zpracování velkých aktualizací datových částí.  V důsledku toho je stručné zachovat nároky na návrh démon zabezpečení IoT Edge a důvěryhodný výpočetní základní malé a minimalizovat požadavky na aktualizaci.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura démon zabezpečení IoT Edge

![Démon zabezpečení Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Démon zabezpečení IoT Edge je navržený výhod dostupného hardwaru kořenový technologie vztahu důvěryhodnosti pro posílení zabezpečení.  Více tedy je navržený pro operace rozdělení reálného světa mezi Standard/bohaté prostředí provádění (RÍ) a prostředí pro důvěryhodného spouštění (TEE) a využijte hardwaru technologií, které nabízí prostředí důvěryhodné spouštění (TEE).  Základem architektury démona zabezpečení IoT Edge je specifické pro role rozhraní umožňující souhra hlavní součásti hraničními zařízeními a zajistit integritu na hraničních zařízeních IoT zařízení a jeho operace.

#### <a name="cloud-interface"></a>Cloudové rozhraní

Cloudové rozhraní umožňuje démon zabezpečení IoT Edge pro přístup ke cloudovým službám, jako poskytuje tým cloudu do zařízení zabezpečení, jako je obnovení zabezpečení.  Například démon zabezpečení IoT Edge aktuálně používá toto rozhraní pro přístup k Azure IoT Hub [služby zřizování zařízení (DPS)](https://docs.microsoft.com/azure/iot-dps/) pro správu životního cyklu identit zařízení.  

#### <a name="management-api"></a>Rozhraní API pro správu

Démon zabezpečení IoT Edge nabízí rozhraní API, správy, které je voláno rozhraním se Agent Edge při vytváření nebo spuštění/zastavení/odebírání modul edge. Démon zabezpečení IoT Edge uloží "registrace" pro všechny aktivní moduly. Tyto registrace mapování identit modul na některé vlastnosti modulu. Pár příkladů, pro tyto vlastnosti jsou identifikátor procesu (pid) proces, který běží v kontejneru nebo hodnotu hash obsahu kontejneru dockeru.

Tyto vlastnosti používá rozhraní API (popsaných níže) ověřit, jestli má volající autorizaci provést akci úlohy.

Rozhraní API pro správu je privilegovaných API, kterou lze volat pouze z agenta IoT Edge.  Vzhledem k tomu, že démon zabezpečení IoT Edge bootstraps a spuštění agenta IoT Edge, může vytvořit implicitní registrace agenta IoT Edge po má ověřuje, jestli nebylo manipulováno agenta IoT Edge. Stejné proces ověření, který používá zatížení rozhraní API slouží k omezení přístupu k rozhraní API pro správu pouze agenta IoT Edge.

#### <a name="container-api"></a>Kontejner rozhraní API

Démon zabezpečení IoT Edge nabízí kontejner rozhraní pro interakci se systémem kontejner používá jako Moby a Docker pro vytvoření instance modulu.

#### <a name="workload-api"></a>Úloha rozhraní API

Úloha rozhraní API je démon zabezpečení IoT Edge rozhraní API, které jsou přístupné pro všechny moduly, včetně agenta IoT Edge. Poskytuje ověření identity ve formě modulu hardwarového zabezpečení root podepsaný token nebo X509 certifikát a odpovídající vztah důvěryhodnosti sady prostředků modulu. Vztah důvěryhodnosti sady obsahuje certifikáty certifikační Autority pro všechny servery, které by měla důvěřovat moduly.

Démon zabezpečení IoT Edge se používá procesu ověření identity pro ochranu tohoto rozhraní API. Když modul volá tato rozhraní API, proces démon zabezpečení IoT Edge se pokusí najít registrace pro identitu. V případě úspěchu se používá vlastnosti registrace k měření modulu. Pokud výsledek procesu shod měření, které root registrace nového modulu HSM podepsaný token nebo X509 se vygeneruje certifikát a odpovídající certifikáty certifikační Autority (vztahu důvěryhodnosti svazku) jsou vráceny do modulu.  Modul používá tento certifikát k připojení ke službě IoT Hub, ostatní moduly, nebo spuštění serveru. Pokud je token podepsaný držitelem nebo certifikát přiblíží konec platnosti, odpovídá modulu požádat o nový certifikát.  Další informace jsou součástí návrhu provést proces prodloužení tam identity jako bezproblémové co nejvíc.

### <a name="integration-and-maintenance"></a>Integrace a údržba

Společnost Microsoft udržuje hlavní kód základu [démon zabezpečení IoT Edge na Githubu](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalace a aktualizace

Nativní proces, instalace a aktualizace zabezpečení démon IoT Edge je spravován přes systém správy balíčků operačního systému.  Nicméně se očekává, že IoT hraniční zařízení s důvěryhodnému kořenovému hardwaru a poskytují další posílení zabezpečení integrity démona tím, že spravuje jeho životní cyklus prostřednictvím zařízení zabezpečené spouštění a aktualizace systémy pro správu.  Je ponecháno na zařízení pravomocí prozkoumat tyto možnosti v souladu s jejími funkcemi odpovídajících zařízení.

#### <a name="versioning"></a>Správa verzí

Modul runtime IoT Edge sleduje a sestavy verzi démona zabezpečení IoT Edge. Verze je uvedená jako *runtime.platform.version* atribut modulu agenta Edge ohlásil vlastnost.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Zabezpečení modulu platformy HAL (PAL modulu hardwarového zabezpečení)

HSM PAL abstrahuje všechny kořenové důvěryhodnosti hardwaru Izolovat uživatele služby IoT Edge z jejich složitosti nebo vývojáře.  To se skládá z kombinace rozhraní API (Application Programming) a trans domény komunikace postupy, například komunikace mezi standardní spouštěcí prostředí a zabezpečenou enklávy.  Skutečná implementace HSM PAL, závisí na konkrétní zabezpečený hardware používá.  Jeho existenci umožňuje použití prakticky jakoukoli zabezpečené silicon hardwaru v ekosystému IoT.

## <a name="secure-silicon-root-of-trust-hardware"></a>Zabezpečené silicon kořenové důvěryhodnosti hardwaru

Zabezpečené silicon je potřeba ukotvení důvěryhodnosti uvnitř hardwaru zařízení IoT Edge.  Zabezpečené silicon se dělí na různých Trusted Platform Module (TPM), zabezpečené vložený Element (technologii eSE), ARM TrustZone, Intel SGX a vlastní zabezpečené silicon technologie.  Použití zabezpečeného silicon kořenové důvěryhodnosti v zařízení důrazně doporučujeme zadaný rizika související s usnadněním fyzicky zařízení IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integrace správce zabezpečení ve službě IoT Edge a údržba

Jedním z hlavních cílů správce zabezpečení IoT Edge je do identity a izolovat komponenty za úkol obrana zabezpečení a integrity platformy Azure IoT Edge pro vlastní posílení zabezpečení.  V důsledku se očekává třetích stran, jako jsou zařízení chcete-li využívají vlastní bezpečnostní funkce, které jsou dostupné v jejich hardwaru zařízení.  V části Další kroky níže na odkazy na příklad toho, jak k posílení zabezpečení Správce Azure IoT s Trusted Platform Module (TPM) na platformách Linux a Windows.  Tyto příklady používat software nebo virtuální čipy TPM, ale přímo platí pro používání samostatných zařízení TPM.  

## <a name="next-steps"></a>Další postup

Přečtěte si blog na [zabezpečení inteligentních hraničních zařízení](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Vytvoření a zřízení s hraničním zařízením [virtuální čip TPM na virtuální počítač s Linuxem](how-to-auto-provision-simulated-device-linux.md).

Vytvoření a zřízení [simulovaného zařízení TPM Edge ve Windows](how-to-auto-provision-simulated-device-windows.md).

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/

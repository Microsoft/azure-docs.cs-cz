---
title: Azure IoT Edge Security Manager – Azure IoT Edge
description: Spravuje IoT Edge zabezpečení zařízení potřebujete pomoc podporují a integritu služeb zabezpečení.
services: iot-edge
keywords: zabezpečení, zabezpečení elementu, enklávy, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 468f1b91d6c6157cd2af6de9599bad7f43c1ad8f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492553"
---
# <a name="azure-iot-edge-security-manager"></a>Správce zabezpečení Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge Security Manager je dobře ohraničená jádro zabezpečení pro ochranu zařízení IoT Edge a všech jeho součástí abstrakcí zabezpečeného karbidu hardwaru. Správce zabezpečení je ústředním bodem pro posílení zabezpečení a poskytuje technologii pro integraci technologie výrobcům OEM (Original Equipment Manufacturer).

![Správce zabezpečení Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge Security Manager se zaměřuje na obranu integrity IoT Edge zařízení a všech podstatných softwarových operací. Správce zabezpečení přechází z základní hardwarové kořene hardwaru důvěryhodnosti (Pokud je k dispozici), aby mohl spustit modul runtime IoT Edge a monitorovat průběžné operace.  IoT Edge Security Manager je software pracující společně s zabezpečeným Silicon hardwarem (Pokud je k dispozici), který vám umožní zajistit nejvyšší možný bezpečnostní ujištění.  

Mezi odpovědnosti IoT Edge Security Manageru patří mimo jiné:

* Zabezpečený a měřený zaváděcí modul Azure IoT Edgeho zařízení.
* Zřizování identit zařízení a převod vztahu důvěryhodnosti, pokud je to možné.
* Hostovat a chránit součásti zařízení Cloud Services, jako je služba Device Provisioning.
* Bezpečně zřídí IoT Edge moduly s jedinečnými identitami.
* Server Gatekeeper hardwarového kořene důvěryhodnosti v zařízení prostřednictvím služby notář.
* Monitoruje integritu operací IoT Edge za běhu.

IoT Edge Security Manager obsahuje tři komponenty:

* IoT Edge démon zabezpečení.
* Vrstva abstrakce modulu hardwarového zabezpečení (HSM PAL).
* Volitelný, ale vysoce doporučený hardware Silicon root trustu nebo HSM.

## <a name="the-iot-edge-security-daemon"></a>Démon zabezpečení IoT Edge

Démon zabezpečení IoT Edge zodpovídá za logické operace nástroje IoT Edge Security Manager. Představuje významnou část základu IoT Edgeho zařízení pro účely důvěryhodných počítačů.

### <a name="design-principles"></a>Principy návrhu

Démon zabezpečení IoT Edge se skládá ze dvou základních principů: maximalizace provozní integrity a minimalizace dispozici determinističtější a změn.

#### <a name="maximize-operational-integrity"></a>Maximalizovat provozní integritu

IoT Edge démon zabezpečení funguje s nejvyšší možnou integritou v rámci schopnosti obrany kteréhokoli daného kořene hardwaru důvěryhodnosti. Se správnou integrací řídí kořen důvěryhodných hardwarových měr a monitoruje démona zabezpečení staticky a za běhu, aby odolal manipulaci.

Fyzický přístup je vždy hrozbou pro zařízení IoT. Hardwarový kořen vztahu důvěryhodnosti hraje důležitou roli při zachování integrity procesu démona zabezpečení IoT Edge.  Kořenová kořenová složka vztahu důvěryhodnosti se dodává ve dvou variantách:

* zabezpečit prvky pro ochranu citlivých informací, jako jsou tajné klíče a kryptografické klíče.
* Zabezpečte enclaves na ochranu tajných kódů, jako jsou klíče a citlivá zatížení, jako je měření a fakturace.

Existují dva druhy prováděcích prostředí, které používají kořenový adresář hardwaru důvěryhodnosti:

* Standardní nebo bohatá spouštěcí prostředí (REE), které spoléhá na použití zabezpečených prvků pro ochranu citlivých informací.
* Prostředí pro důvěryhodné spuštění (TEE), které spoléhá na použití zabezpečené technologie enklávy k ochraně citlivých informací a poskytování ochrany před spuštěním softwaru.

Pro zařízení, která používají zabezpečený enclaves jako hardwarový kořen vztahu důvěryhodnosti, by měla být citlivá logika v rámci služby IoT Edge Security daemon uvnitř enklávy.  Necitlivá část démona zabezpečení může být mimo TEE.  V každém případě by výrobci originálního prostředí (ODM) a výrobci OEM měli pro účely měření a ochrany integrity procesu démona zabezpečení IoT Edge při spuštění a běhu chránit důvěryhodnost.

#### <a name="minimize-bloat-and-churn"></a>Minimalizace dispozici determinističtější a změn

Další základní zásadou pro démona zabezpečení IoT Edge je minimalizace změn.  Pro nejvyšší úroveň důvěryhodnosti může démon zabezpečení IoT Edge pevně spojit s hardwarem hardwaru zařízení, který důvěřuje, a fungovat jako nativní kód.  Je běžné, že tyto typy oddělení aktualizuje software démona prostřednictvím kořenového adresáře hardwaru cest aktualizace důvěryhodnosti (na rozdíl od mechanismů aktualizace poskytovaných operačním systémem), což může být v některých případech náročné.  I když se pro zařízení IoT doporučuje obnovování zabezpečení, nadměrné požadavky na aktualizace nebo rozsáhlé datové části aktualizace mohou plochu hrozby rozšířit mnoha způsoby.  Mezi příklady patří přeskočení aktualizací pro maximalizaci provozní dostupnosti nebo kořene důvěryhodného hardwaru pro zpracování rozsáhlých datových částí aktualizace.  V takovém případě je návrh démona zabezpečení IoT Edge stručnější, aby se zajistila Základnová a důvěryhodná výpočetní databáze a minimalizovala požadavky na aktualizaci.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura démona zabezpečení IoT Edge

![Démon zabezpečení Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge démon zabezpečení využívá všechny dostupné hardwarové kořenové složky důvěryhodných technologií pro posílení zabezpečení.  Umožňuje taky rozdělit operace mezi standardním a bohatým spouštěcím prostředím (REE) a prostředím TEE (Trusted Execution Environment), když hardwarové technologie nabízejí prostředí pro důvěryhodné spouštění. Rozhraní specifická pro role povolují hlavní součásti IoT Edge, aby se zajistila integrita IoT Edge zařízení a jeho operací.

#### <a name="cloud-interface"></a>Cloudové rozhraní

Cloudové rozhraní umožňuje, aby démon zabezpečení IoT Edge mohl přistupovat ke cloudovým službám, jako je cloudový přístup k zabezpečení zařízení, jako je třeba obnovení zabezpečení.  Například démon zabezpečení IoT Edge aktuálně používá toto rozhraní pro přístup ke službě Azure IoT Hub [Device Provisioning](../iot-dps/index.yml) pro správu životního cyklu identity zařízení.  

#### <a name="management-api"></a>Rozhraní API pro správu

IoT Edge démon zabezpečení nabízí rozhraní API pro správu, které je voláno agentem IoT Edge při vytváření, spouštění, zastavování/odebírání modulu IoT Edge. Démon zabezpečení ukládá "registrace" pro všechny aktivní moduly. Tyto registrace mapují identitu modulu na některé vlastnosti modulu. Mezi tyto vlastnosti modulu patří například identifikátor procesu (PID) procesu spuštěného v kontejneru a hodnota hash obsahu kontejneru Docker.

Tyto vlastnosti používá rozhraní API úlohy (popsané níže) k ověření, že volající je autorizovaný pro akci.

Rozhraní API pro správu je privilegované rozhraní API, které lze volat pouze z agenta IoT Edge.  Vzhledem k tomu, že IoT Edge démon zabezpečení a spustí agenta IoT Edge, ověří, že agent IoT Edge nebyl zfalšován, a pak může vytvořit implicitní registraci agenta IoT Edge. Stejný proces ověření identity, jaký používá rozhraní API pro úlohy, taky omezuje přístup k rozhraní API pro správu jenom na agenta IoT Edge.

#### <a name="container-api"></a>Rozhraní API kontejneru

Rozhraní API kontejneru spolupracuje se systémem kontejneru, který se používá ke správě modulů, jako je Moby nebo Docker.

#### <a name="workload-api"></a>Rozhraní API pro úlohy

Rozhraní API úlohy je dostupné pro všechny moduly. Poskytuje důkaz identity, buď jako podepsaný token HSM root nebo certifikát x509, a odpovídající sadu důvěryhodnosti pro modul. Sada důvěryhodnosti obsahuje certifikáty certifikační autority pro všechny ostatní servery, na které mají tyto moduly důvěřovat.

Démon zabezpečení IoT Edge pro ochranu tohoto rozhraní API používá proces ověření identity. Když modul toto rozhraní API volá, pokusí se démon zabezpečení najít registraci pro identitu. Pokud je úspěšná, použije se k měření modulu vlastnosti registrace. Pokud výsledek procesu měření odpovídá registraci, je vygenerován nový důkaz identity. Do modulu se vrátí odpovídající certifikáty certifikační autority (sada Trust).  Modul používá tento certifikát pro připojení k IoT Hub, jiným modulům nebo spuštění serveru. Pokud se podepsaný token nebo certifikát blíží k vypršení platnosti, je zodpovědností modulu požádat o nový certifikát.

### <a name="integration-and-maintenance"></a>Integrace a údržba

Společnost Microsoft udržuje hlavní základ kódu pro [démona zabezpečení IoT Edge na GitHubu](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalace a aktualizace

Instalace a aktualizace procesu démona zabezpečení IoT Edge jsou spravovány prostřednictvím systém správy balíčků operačního systému. Zařízení IoT Edge s hardwarovým kořenem důvěry by měla poskytovat další posílení zabezpečení démona tím, že spravuje svůj životní cyklus prostřednictvím systémů pro správu zabezpečeného spouštění a aktualizací. Tvůrci zařízení by měli prozkoumat tyto cesty vedoucíy na základě příslušných možností zařízení.

#### <a name="versioning"></a>Správa verzí

Modul runtime IoT Edge sleduje a hlásí verzi procesu démona zabezpečení IoT Edge. Verze je hlášena jako atribut *runtime. Platform. version* IoT Edgeho modulu agenta, který ohlásil vlastnost.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Vrstva abstrakce platformy modulu hardwarového zabezpečení (HSM PAL)

Modul hardwarového zabezpečení (HSM), který vyabstrakce všechna kořenová zařízení důvěryhodnosti, izoluje vývojáře nebo uživatele IoT Edge od jejich složitosti.  Zahrnuje kombinaci rozhraní API (Application Programming Interface) a komunikačních postupů napříč doménami, například komunikaci mezi standardním spouštěcím prostředím a zabezpečenou enklávy.  Vlastní implementace modulu HSM PAL závisí na konkrétním zabezpečeném hardwaru, který se používá. Jeho existence umožňuje použití prakticky jakéhokoli zabezpečeného karbidu hardwaru.

## <a name="secure-silicon-root-of-trust-hardware"></a>Zabezpečená Silicon root důvěryhodnosti hardwaru

Zabezpečený Silicon je nutný k ukotvení vztahu důvěryhodnosti uvnitř IoT Edge hardwaru zařízení.  Zabezpečený silikon přináší různé prvky, které zahrnují čip TPM (Trusted Platform Module), integrovaný zabezpečený element (eSE), ARM TrustZone, Intel SGX a vlastní zabezpečené Silicon technologie.  Použití zabezpečeného křemíkového kořene důvěryhodnosti v zařízeních se doporučuje vzhledem k hrozbám přidruženým k fyzické dostupnosti zařízení IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge integrace a údržby správce zabezpečení

IoT Edge Security Manager se zaměřuje na identifikaci a izolaci komponent, které chrání zabezpečení a integritu Azure IoT Edge platformy pro vlastní posílení zabezpečení. Třetí strany, jako jsou tvůrci zařízení, by měli používat vlastní funkce zabezpečení, které jsou k dispozici u svého hardwaru zařízení.  V části Další kroky najdete odkazy, které ukazují, jak posílit správce zabezpečení Azure IoT pomocí čipu TPM (Trusted Platform Module) na platformách Linux a Windows. Tyto příklady používají software nebo Virtual čipy TPM, ale přímo se používají pro používání diskrétních zařízení TPM.  

## <a name="next-steps"></a>Další kroky

Přečtěte si blog o [zabezpečení inteligentního Edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Vytvoření a zřízení [zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači se systémem Linux](how-to-auto-provision-simulated-device-linux.md).

Vytvoření a zřízení [IoT Edge zařízení s simulovaným čipem TPM ve Windows](how-to-auto-provision-simulated-device-windows.md).
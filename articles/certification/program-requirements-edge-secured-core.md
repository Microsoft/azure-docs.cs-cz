---
title: Zabezpečení Edge – požadavky na certifikaci Core
description: Zabezpečení Edge – požadavky na jádro certifikačního programu
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166900"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Zařízení s certifikací Azure – hranice zabezpečení – jádro (Preview) #

## <a name="edge-secured-core-certification-requirements"></a>Požadavky na certifikaci Secured-Core Edge ##

Tento dokument popisuje funkce a požadavky specifické pro zařízení, které budou splněné, aby bylo možné dokončit certifikaci a vypsat zařízení v katalogu zařízení Azure IoT pomocí popisku služby Edge Secure-Core.

### <a name="program-purpose"></a>Účel programu ###
Secure Edge – jádro je přírůstkové certifikace v programu zařízení s certifikací Azure Certified pro zařízení IoT, na kterých běží úplný operační systém, jako je Linux nebo Windows 10 IoT. Tento program umožňuje partnerům zařízení odlišit svá zařízení tím, že splňuje další sadu kritérií zabezpečení. Zařízení, která splňují tato kritéria, umožňují tyto příslibů:
1. Hardwarová identita zařízení 
2. Schopnost vynucování integrity systému 
3. Zůstane v aktuálním stavu a lze ji vzdáleně spravovat.
4. Poskytuje ochranu dat při REST.
5. Poskytuje ochranu před přenosem dat.
6. Vestavěný Agent zabezpečení a posílení zabezpečení
### <a name="requirements"></a>Požadavky ###

---
|Name|SecuredCore. integred. Security|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je zajistit, aby zařízení mohla hlásit informace o zabezpečení a události odesláním dat do služby Azure Defender pro IoT.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování |Zařízení musí generovat protokoly zabezpečení a výstrahy. Protokoly zařízení a zprávy upozorňují na Azure Security Center.<ol><li>Stažení a nasazení agenta zabezpečení z GitHubu</li><li>Ověří zprávu výstrahy z Azure Defenderu pro IoT.</li></ol>|
|Zdroje informací|[Azure docs IoT Defender pro IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Name|SecuredCore. Encryption. Storage|
|:---|:---|
|Status|Povinné|
|Popis|Účel testu pro ověření, že citlivá data mohou být šifrována v nestálém úložišti.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů pro zajištění, že je povolené šifrování úložiště, a výchozí algoritmus je XTS-AES s délkou klíče 128 nebo vyšší.|
|Zdroje informací||

---
|Name|SecuredCore. hardware. SecureEnclave|
|:---|:---|
|Status|Volitelné|
|Description|Účel testu, který ověří existenci zabezpečeného enklávyu a enklávy je přístupný z zabezpečeného agenta.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů pro zajištění, že Agent zabezpečení Azure může komunikovat se zabezpečeným enklávy|
|Zdroje informací|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore. hardware. identity|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, jestli je identifikace zařízení v hardwaru označená rootem.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby se zajistilo, že zařízení má TPM a že se dá zřídit prostřednictvím IoT Hub pomocí ověřovacího klíče TPM.|
|Zdroje informací|[Nastavení automatického zřizování pomocí DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore. Update|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, jestli zařízení může přijmout a aktualizovat jeho firmware a software.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Partner s potvrzením, že byl schopný Odeslat aktualizaci zařízení prostřednictvím služby Microsoft Update, aktualizace zařízení Azure nebo jiných schválených služeb.|
|Zdroje informací|[Aktualizace zařízení pro IoT Hub](../iot-hub-device-update/index.yml)|

---
|Name|SecuredCore.Manageability.Configuration|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, jestli zařízení podporují vzdálenou správu zabezpečení.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby bylo zajištěno, že zařízení bude podporovat vzdálenou správu a specifickou konfiguraci zabezpečení. A stav se hlásí zpátky do IoT Hub/Azure Defenderu pro IoT.|
|Zdroje informací||

---
|Name|SecuredCore. manage. Reset|
|:---|:---|
|Status|Povinné|
|Popis|Účelem tohoto testu je ověřit zařízení proti dvěma případům použití: a) schopnost provést resetování (odebrání uživatelských dat, odebrání uživatelských konfigurací), b) obnovit zařízení na poslední známé v případě aktualizace způsobující problémy.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit kombinací sady nástrojů a Odeslaná dokumentace, kterou zařízení podporuje. Výrobce zařízení může určit, jestli se tyto funkce mají implementovat, aby podporovaly vzdálené resetování nebo jenom místní resetování.|
|Zdroje informací||

---
|Name|SecuredCore. Updates. Duration|
|:---|:---|
|Status|Povinné|
|Popis|Účelem této zásady je zajistit, že zařízení zůstane zabezpečené.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční|
|Ověřování|Závazek od předložení zařízení bude potřeba zajistit, aby zařízení byla v aktuálním stavu po dobu 60 měsíců od jejich odeslání. Specifikace dostupná pro kupujícího a zařízení samotné by měly indikovat dobu, po kterou se bude software aktualizovat.|
|Zdroje informací||

---
|Name|SecuredCore. Policy. Vuln. zpřístupnění|
|:---|:---|
|Status|Povinné|
|Popis|Účelem této zásady je zajistit, aby existoval mechanizmus shromažďování a distribuce zpráv o chybách zabezpečení v produktu.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční|
|Ověřování|Dokumentace k procesu odesílání a přijímání zpráv o ohroženích zabezpečení pro certifikovaná zařízení bude přezkoumána.|
|Zdroje informací||

---
|Name|SecuredCore. Policy. Vuln. opravy|
|:---|:---|
|Status|Povinné|
|Popis|Účelem této zásady je zajistit, aby chyby zabezpečení, které jsou vysoké/kritické (pomocí CVSS 3,0), byly řešeny do 180 dnů od dostupné opravy.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční|
|Ověřování|Dokumentace k procesu odesílání a přijímání zpráv o ohroženích zabezpečení pro certifikovaná zařízení bude přezkoumána.|
|Zdroje informací||


---
|Name|SecuredCore. Encryption. TLS|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit podporu požadovaných verzí TLS a šifrovacích sad.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby bylo zajištěno, že zařízení podporuje minimální verzi TLS 1,2 a podporuje následující požadované šifrovací sady TLS.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Zdroje informací| [Podpora TLS v IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [Šifrovací sady TLS ve Windows 10](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Name|SecuredCore. Protection. SignedUpdates|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, zda musí být aktualizace podepsány.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby se zajistilo, že se aktualizace operačního systému, ovladačů, softwaru, knihoven, balíčků a firmwaru nepoužijí, pokud není správně podepsaná a ověřená.
|Zdroje informací||

---
|Name|SecuredCore. firmware. funkce SecureBoot|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit integritu spouštění zařízení.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby se ověřilo, že signatury firmwaru a jádra se ověřují při každém spuštění zařízení. <ul><li>UEFI: Zabezpečené spouštění je povolené.</li><li>Uboot: ověřované spuštění je povolené.</li></ul>|
|Zdroje informací||

---
|Name|SecuredCore. Protection. Codeintegrity nedodržuje|
|:---|:---|
|Status|Povinné|
|Popis|Účelem tohoto testu je ověřit, zda je v tomto zařízení k dispozici integrita kódu.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit pomocí sady nástrojů, aby se zajistilo, že je povolená integrita kódu </br> Windows: HYPERVISOREM HVCI </br> Linux: DM-Verity a IMA|
|Zdroje informací||

---
|Name|SecuredCore. Protection. NetworkService|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, že aplikace, které přijímají vstup ze sítě, nejsou spuštěny se zvýšenými oprávněními.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby se zajistilo, že se služby, které přijímají síťová připojení, nespouštějí s oprávněními systému nebo kořen|
|Zdroje informací||

---
|Name|SecuredCore. Protection. úrovně|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, zda systém odpovídá základní konfiguraci zabezpečení.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby bylo zajištěno, že testy konfigurace systému v programu Defender IOT byly spuštěny.|
|Zdroje informací| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore. firmware. Protection|
|:---|:---|
|Status|Povinné|
|Popis|Účelem tohoto testu je zajistit, aby zařízení mělo odpovídající hrozby ze zabezpečení firmwaru.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů pro potvrzení, že je chráněné před bezpečnostními hrozbami firmwaru prostřednictvím jednoho z následujících přístupů: <ul><li>Omezení zmírňování režimu správy DRTM + UEFI</li><li>DRTM + posílení zabezpečení v režimu správy rozhraní UEFI</li><li>Schválená firmware, který dělá posílení zabezpečení SRTM + runtime</li></ul> |
|Zdroje informací| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore. firmware. Attestation|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je zajistit, aby se zařízení mohl vzdáleně ověřit ve službě Microsoft Azure Attestation Identity.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby se zajistilo, že protokoly spuštění platformy a měření spouštěcí aktivity se můžou shromažďovat a vzdáleně potvrzovat pro službu Microsoft Azure Attestation Identity.|
|Zdroje informací| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Name|SecuredCore. hardware. MemoryProtection|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, jestli není povolený přístup k DMA na externě přístupných portech.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Pokud v zařízení existují Externí porty podporující DMA, aby se ověřilo, že jsou pro tyto porty povolené a nakonfigurované IOMMU nebo SMMU.|
|Zdroje informací||

---
|Name|SecuredCore. Protection. Debug|
|:---|:---|
|Status|Povinné|
|Popis|Účelem testu je ověřit, zda je funkce ladění v zařízení zakázána.|
|Cílová dostupnost|2021|
|Platí pro|Jakékoli zařízení|
|Operační systém|Nelze určit|
|Typ ověření|Ruční/nástroje|
|Ověřování|Zařízení, které se má ověřit prostřednictvím sady nástrojů, aby bylo zajištěno, že funkce ladění vyžaduje povolení k povolení.|
|Zdroje a prostředky||

---
title: Azure Security srovnávací test v2 – Endpoint Security
description: Zabezpečení koncového bodu Azure Security test v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3eab0080f80b26fc7074d8a64885148c0568d8b1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280614"
---
# <a name="security-control-v2-endpoint-security"></a>Řízení zabezpečení v2: zabezpečení koncového bodu

Služba Endpoint Security pokrývá ovládací prvky pro detekci a odezvu koncového bodu. To zahrnuje použití zjišťování koncových bodů a odezvy (EDR) a antimalwarové služby pro koncové body v prostředích Azure.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: použití detekce a odpovědi koncového bodu (EDR)

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ES-1 | 8.1 | -2, SI-3, SC-3 |

Povolte funkce zjišťování koncových bodů a odezva (EDR) pro servery a klienty a integrujte je s procesy SIEM a operací zabezpečení.

Rozšířená ochrana před internetovými útoky v programu Microsoft Defender poskytuje možnost EDR jako součást platformy zabezpečení podnikového koncového bodu, aby nedocházelo k pokročilým hrozbám, jejich detekci, vyšetřování a reakci 

- [Přehled rozšířené ochrany před internetovými útoky v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Služba ochrany ATP v programu Microsoft Defender pro servery Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Služba ochrany ATP v programu Microsoft Defender pro servery jiné než Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: použití centrálně spravovaného malwarového softwaru pro moderní správu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ES – 2 | 8.1 | -2, SI-3, SC-3 |

Použití centralizovaného antimalwarového řešení spravovaného koncovým bodem, který je schopný v reálném čase a pravidelné kontrole

Azure Security Center může automaticky identifikovat použití řady oblíbených antimalwarových řešení pro vaše virtuální počítače a ohlásit stav spouštění služby Endpoint Protection a dávat doporučení. 

Microsoft Antimalware pro Azure Cloud Services je výchozí Antimalwarový program pro virtuální počítače s Windows (VM). Pro virtuální počítače se systémem Linux použijte antimalwarové řešení třetích stran.  Také můžete použít detekci hrozeb Azure Security Center pro datové služby ke zjištění malwaru nahraného v Azure Storagech účtech. 

- [Jak nakonfigurovat Microsoft Antimalware pro Cloud Services a Virtual Machines](../fundamentals/antimalware.md)

- [Podporovaná řešení ochrany koncových bodů](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: kontrola, jestli je antimalwarový software a signatury aktualizované

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| ES – 3 | 8.2 | KDE-2, SI-3 |

Ujistěte se, že signatury antimalwaru se aktualizují rychle a konzistentně. 

Použijte doporučení v Azure Security Center: "COMPUTE &amp; Apps", abyste zajistili aktuálnost všech koncových bodů s nejnovějšími podpisy. Microsoft Antimalware bude ve výchozím nastavení automaticky instalovat nejnovější podpisy a aktualizace stroje. Pro Linux použijte antimalwarové řešení třetích stran.

- [Jak nasadit Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../fundamentals/antimalware.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)


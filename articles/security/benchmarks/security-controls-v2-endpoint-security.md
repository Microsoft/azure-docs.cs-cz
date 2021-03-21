---
title: Azure Security srovnávací test v2 – Endpoint Security
description: Zabezpečení koncového bodu Azure Security test v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 48b22ba913370b27cd01319a14a2a627d7589ce4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051510"
---
# <a name="security-control-v2-endpoint-security"></a>Řízení zabezpečení v2: zabezpečení koncového bodu

Služba Endpoint Security pokrývá ovládací prvky pro detekci a odezvu koncového bodu. To zahrnuje použití zjišťování koncových bodů a odezvy (EDR) a antimalwarové služby pro koncové body v prostředích Azure.

Pokud se chcete podívat na příslušný integrovaný Azure Policy, přečtěte si [Podrobnosti o integrované iniciativě Azure Security test dodržování předpisů: zabezpečení koncového bodu](../../governance/policy/samples/azure-security-benchmark.md#endpoint-security)

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: použití detekce a odpovědi koncového bodu (EDR)

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| ES-1 | 8.1 | -2, SI-3, SC-3 |

Povolte funkce zjišťování koncových bodů a odezva (EDR) pro servery a klienty a integrujte je s procesy SIEM a operací zabezpečení.

Microsoft Defender pro koncové body poskytuje možnost EDR jako součást platformy zabezpečení podnikového koncového bodu, aby nedocházelo k pokročilým hrozbám, jejich detekci, prozkoumání a reakci na ně.

- [Přehled programu Microsoft Defender pro koncové body](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender pro koncové body pro servery Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender pro koncové body pro jiné servery než Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: použití centrálně spravovaného malwarového softwaru pro moderní správu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| ES – 2 | 8.1 | -2, SI-3, SC-3 |

Použití centralizovaného antimalwarového řešení spravovaného koncovým bodem, který je schopný v reálném čase a pravidelné kontrole

Azure Security Center může automaticky identifikovat použití řady oblíbených antimalwarových řešení pro vaše virtuální počítače a ohlásit stav spouštění služby Endpoint Protection a dávat doporučení. 

Microsoft Antimalware pro Azure Cloud Services je výchozí Antimalwarový program pro virtuální počítače s Windows (VM). Pro virtuální počítače se systémem Linux použijte antimalwarové řešení třetích stran. Také můžete použít detekci hrozeb Azure Security Center pro datové služby ke zjištění malwaru nahraného v Azure Storagech účtech. 

- [Jak nakonfigurovat Microsoft Antimalware pro Cloud Services a Virtual Machines](../fundamentals/antimalware.md)

- [Podporovaná řešení ochrany koncových bodů](../../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: kontrola, jestli je antimalwarový software a signatury aktualizované

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| ES – 3 | 8.2 | KDE-2, SI-3 |

Ujistěte se, že signatury antimalwaru se aktualizují rychle a konzistentně.

Použijte doporučení v Azure Security Center: "COMPUTE & Apps", abyste zajistili aktuálnost všech koncových bodů s nejnovějšími podpisy. Microsoft Antimalware bude ve výchozím nastavení automaticky instalovat nejnovější podpisy a aktualizace stroje. V případě systému Linux zajistěte, aby se podpisy aktualizovaly v antimalwarovém řešení třetí strany.

- [Jak nasadit Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../fundamentals/antimalware.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security)

- [Analýza hrozeb](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Posouzení a doporučení služby Endpoint Protection v Azure Security Center](../../security-center/security-center-endpoint-protection.md)
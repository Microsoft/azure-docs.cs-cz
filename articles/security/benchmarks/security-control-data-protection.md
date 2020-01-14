---
title: Řízení zabezpečení Azure – Ochrana dat
description: Ochrana dat kontroly zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934521"
---
# <a name="security-control-data-protection"></a>Řízení zabezpečení: Ochrana dat

Doporučení ochrany dat se zaměřují na řešení problémů souvisejících s šifrováním, seznamů řízení přístupu, řízení přístupu na základě identity a protokolování auditu pro přístup k datům.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.1 | 13,1 | Zákazník |

Pomocí značek můžete posloužit ke sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.2 | 13.2 | Zákazník |

Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené podle virtuální sítě a podsítě, musí se vhodně označit a zabezpečit pomocí NSG nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být dostatečně izolované. Pokud Virtual Machines ukládáte nebo zpracováváte citlivá data, implementujte zásady a postupy pro jejich vypnutí, pokud se nepoužívají.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Postup vytvoření Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Vytvoření NSG s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Postup nasazení Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak nakonfigurovat výstrahu nebo výstrahu a odepřít pomocí Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.3 | 13,3 | Zákazník |

Nasaďte automatizovaný nástroj na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.4 | 14,4 | Shared |

Šifrování všech citlivých informací během přenosu. Ujistěte se, že klienti, kteří se připojují k prostředkům Azure, můžou vyjednávat TLS 1,2 nebo vyšší.

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

Pochopení šifrování při přenosu pomocí Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.5 | 14,5 | Zákazník |

Pokud není k dispozici žádná funkce pro konkrétní službu v Azure, použijte k identifikaci všech citlivých informací uložených, zpracovávaných nebo odeslaných technologickými systémy organizace, které jsou uložené v lokalitě nebo na poskytovatel vzdálených služeb a aktualizuje inventář citlivých informací organizace.

K identifikaci citlivých informací v dokumentech Office 365 použijte Azure Information Protection.

Využijte Azure SQL Information Protection pro pomoc při klasifikaci a označování informací uložených v databázích SQL Azure.

Jak implementovat Azure SQL data Discovery:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Postup implementace Azure Information Protection:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.6 | 14,6 | Zákazník |

Využijte Azure AD RBAC k řízení přístupu k datům a prostředkům, jinak použijte metody řízení přístupu specifické pro službu.

Porozumění službě Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Jak nakonfigurovat RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.7 | 14,7 | Zákazník |

Implementujte nástroj třetí strany, jako je například automatizované řešení ochrany před únikem informací na hostiteli, které vynutilo řízení přístupu k datům i v případě, že se data zkopírují mimo systém.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4.8 | 14,8 | Zákazník |

Pro všechny prostředky Azure použijte šifrování v klidovém provozu. Microsoft doporučuje povolit správu šifrovacích klíčů v Azure, ale v některých případech je k dispozici možnost spravovat vlastní klíče. 

Vysvětlení šifrování v klidovém umístění v Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Postup konfigurace šifrovacích klíčů spravovaných zákazníkem:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 4,9 | 14,9 | Zákazník |

Pomocí Azure Monitor s protokolem aktivit Azure můžete vytvářet výstrahy, které se použijí v případě, že se změny projeví u kritických prostředků Azure.

Vytváření upozornění pro události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Další kroky

Zobrazit další kontrolu zabezpečení: [Správa ohrožení](security-control-vulnerability-management.md) zabezpečení

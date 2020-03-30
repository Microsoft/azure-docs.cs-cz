---
title: Ovládací prvek zabezpečení Azure – ochrana dat
description: Ochrana dat zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934521"
---
# <a name="security-control-data-protection"></a>Řízení zabezpečení: Ochrana dat

Doporučení pro ochranu dat se zaměřují na řešení problémů souvisejících s šifrováním, seznamy řízení přístupu, řízenípřístupu na základě identity a protokolování auditu pro přístup k datům.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.1 | 13.1 | Zákazník |

Značky slouží ke sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.2 | 13.2 | Zákazník |

Implementujte samostatné předplatná nebo skupiny pro správu pro vývoj, testování a výrobu. Prostředky by měly být odděleny virtuální sítí/podsítí, odpovídajícím způsobem označeny a zabezpečeny skupinou zabezpečení sítě zabezpečení sítě nebo bránou Azure Firewall. Zdroje, které ukládají nebo zpracovávají citlivé údaje, by měly být dostatečně izolovány. Pro virtuální počítače ukládání nebo zpracování citlivých dat, implementovat zásady a postupy, chcete-li je vypnout, když se nepoužívá.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak nasadit Bránu Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak nakonfigurovat výstrahu nebo výstrahu a odmítnout pomocí Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.3 | 13.3 | Zákazník |

Nasaďte automatizovaný nástroj na obvody sítě, který monitoruje neoprávněný přenos citlivých informací a blokuje takové přenosy a zároveň upozorňuje odborníky na zabezpečení informací.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.4 | 14.4 | Sdílená |

Šifrujte všechny citlivé informace při přenosu. Ujistěte se, že všichni klienti připojení k prostředkům Azure jsou schopni vyjednat TLS 1.2 nebo vyšší.

Postupujte podle doporučení Azure Security Center pro šifrování v klidovém stavu a šifrování při přenosu, pokud je to možné.

Pochopte šifrování při přenosu s Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.5 | 14.5 | Zákazník |

Pokud pro vaši konkrétní službu v Azure není k dispozici žádná funkce, použijte nástroj pro aktivní zjišťování třetí strany k identifikaci všech citlivých informací uložených, zpracovávaných nebo přenášených technologickými systémy organizace, včetně těch, které se nacházejí na místě, nebo na vzdáleného poskytovatele služeb a aktualizovat inventář citlivých informací organizace.

Azure Information Protection slouží k identifikaci citlivých informací v dokumentech Office 365.

Azure SQL Information Protection vám pomůže s klasifikací a označováním informací uložených v databázích Azure SQL.

Jak implementovat Azure SQL Data Discovery:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Jak implementovat Azure Information Protection:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.6 | 14.6 | Zákazník |

Azure AD RBAC slouží k řízení přístupu k datům a prostředkům, jinak použijte metody řízení přístupu specifické pro službu.

Principy Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Jak nakonfigurovat RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.7 | 14.7 | Zákazník |

Implementujte nástroj třetí strany, jako je například automatizované řešení zabránění ztrátám dat založené na hostiteli, k vynucení řízení přístupu k datům i v případě, že jsou data zkopírována ze systému.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.8 | 14.8 | Zákazník |

Šifrování používejte v klidovém stavu u všech prostředků Azure. Společnost Microsoft doporučuje povolit Azure spravovat šifrovací klíče, ale v některých případech máte možnost spravovat vlastní klíče. 

Principy šifrování v klidovém stavu v Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Konfigurace šifrovacích klíčů spravovaných zákazníkem:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.9 | 14.9 | Zákazník |

Pomocí Azure Monitoru s protokolem aktivit Azure můžete vytvářet výstrahy, když dojde ke změnám v důležitých prostředcích Azure.

Jak vytvořit výstrahy pro události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Správa chyb zabezpečení](security-control-vulnerability-management.md)

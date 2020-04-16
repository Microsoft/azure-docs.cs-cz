---
title: Ovládací prvek zabezpečení Azure – ochrana dat
description: Ochrana dat řízení zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408549"
---
# <a name="security-control-data-protection"></a>Řízení zabezpečení: Ochrana dat

Doporučení pro ochranu dat se zaměřují na řešení problémů souvisejících s šifrováním, seznamy řízení přístupu, řízenípřístupu na základě identity a protokolování auditu pro přístup k datům.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.1 | 13.1 | Zákazník |

Značky slouží ke sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Jak vytvořit a používat značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.2 | 13.2, 2.10 | Zákazník |

Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je typ prostředí a úroveň citlivosti dat. Můžete omezit úroveň přístupu k prostředkům Azure, které vaše aplikace a podniková prostředí vyžadují. Přístup k prostředkům Azure můžete řídit pomocí řízení přístupu založeného na rolích služby Azure Active Directory. 

- [Jak vytvořit další předplatná Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Jak vytvořit skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Jak vytvářet a používat značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.3 | 13.3 | Sdílená |

Využijte řešení třetích stran z Azure Marketplace na hraničních obvodech sítě, které monitoruje neoprávněný přenos citlivých informací a blokuje takové přenosy a zároveň upozorňuje odborníky na zabezpečení informací.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft zachází s veškerým zákaznickým obsahem jako s citlivým a chrání před ztrátou a expozicí zákaznických dat. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

- [Principy ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.4 | 14.4 | Sdílená |

Šifrujte všechny citlivé informace při přenosu. Ujistěte se, že všichni klienti připojení k prostředkům Azure jsou schopni vyjednat TLS 1.2 nebo vyšší.

Postupujte podle doporučení Azure Security Center pro šifrování v klidovém stavu a šifrování při přenosu, pokud je to možné.

- [Principy šifrování při přenosu s Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.5 | 14.5 | Sdílená |

Pokud pro vaši konkrétní službu v Azure není k dispozici žádná funkce, použijte nástroj pro aktivní zjišťování třetí strany k identifikaci všech citlivých informací uložených, zpracovávaných nebo přenášených technologickými systémy organizace, včetně těch, které se nacházejí na místě nebo u vzdáleného poskytovatele služeb, a aktualizujte inventář citlivých informací organizace.

Azure Information Protection slouží k identifikaci citlivých informací v dokumentech Office 365.

Azure SQL Information Protection vám pomůže s klasifikací a označováním informací uložených v databázích Azure SQL.

- [Jak implementovat Azure SQL Data Discovery](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Jak implementovat Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Principy ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Řízení přístupu na základě rolí k řízení přístupu k prostředkům

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.6 | 14.6 | Zákazník |

Azure AD RBAC slouží k řízení přístupu k datům a prostředkům, jinak použijte metody řízení přístupu specifické pro službu.

- [Jak nakonfigurovat RBAC v Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.7 | 14.7 | Sdílená |

Pokud je to nutné pro dodržování předpisů na výpočetních prostředcích, implementujte nástroj třetí strany, jako je například automatizované řešení ochrany dat založené na hostiteli, k vynucení řízení přístupu k datům, i když jsou data zkopírována ze systému.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

- [Principy ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.8 | 14.8 | Zákazník |

Šifrování používejte v klidovém stavu u všech prostředků Azure. Společnost Microsoft doporučuje povolit Azure spravovat šifrovací klíče, ale v některých případech máte možnost spravovat vlastní klíče. 

- [Principy šifrování v klidovém stavu v Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Konfigurace šifrovacích klíčů spravovaných zákazníkem](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 4.9 | 14.9 | Zákazník |

Pomocí Azure Monitoru s protokolem aktivit Azure můžete vytvářet výstrahy, když dojde ke změnám v důležitých prostředcích Azure.

- [Jak vytvořit výstrahy pro události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Další kroky

- Další ovládací prvek zabezpečení: [Správa ohrožení zabezpečení](security-control-vulnerability-management.md)
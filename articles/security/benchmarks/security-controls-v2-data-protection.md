---
title: Azure Security test benchmark v2 – ochrana dat
description: Ochrana dat benchmark v2 pro Azure Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 687c344aefc70729c85fb37d615ec0a272ff4fde
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368864"
---
# <a name="security-control-v2-data-protection"></a>Řízení zabezpečení v2: Ochrana dat

Ochrana dat pokrývá kontrolu ochrany dat v klidovém provozu, při přenosu a prostřednictvím autorizovaných mechanismů přístupu. To zahrnuje zjišťování, klasifikaci, ochranu a monitorování citlivých datových prostředků pomocí řízení přístupu, šifrování a protokolování v Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Zjišťování, klasifikace a označení citlivých dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Zjišťování, klasifikace a označování citlivých dat, abyste mohli navrhovat příslušné ovládací prvky pro zajištění, že se citlivé informace ukládají, zpracovávají a bezpečně odesílají technologické systémy organizace. 

V případě citlivých informací v rámci dokumentů Office v Azure, místním prostředí, Office 365 a dalších umístěních můžete využít službu Azure Information Protection (a přidružený nástroj pro zjišťování). 

Služba Azure SQL Information Protection vám může pomoct s klasifikací a označováním informací uložených v databázích Azure SQL.

- [Označování citlivých informací s využitím služby Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementace zjišťování dat Azure SQL](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpovědnost:** Sdílené

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP – 2 | 13,2, 2,10 | SC-7, AC-4 |

Chraňte citlivá data omezením přístupu pomocí řízení přístupu založeného na rolích Azure (Azure RBAC), řízení přístupu na základě sítě a specifických ovládacích prvků ve službách Azure (například šifrování v SQL a dalších databázích). 

Aby se zajistilo konzistentní řízení přístupu, všechny typy řízení přístupu by měly být v souladu s vaší podnikovou strategií segmentace. Podniková strategie segmentace by měla vycházet také z umístění citlivých nebo důležitých obchodních dat a systémů.

Pro základní platformu spravovanou Microsoftem platí, že Microsoft považuje veškerý obsah zákazníků za citlivý a zajišťuje ochranu před ztrátou a vystavením zákaznických dat. V zájmu zajištění zabezpečení zákaznických dat v rámci Azure Microsoft implementovat určité výchozí kontrolní mechanismy a funkce ochrany dat.

- [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Principy ochrany zákaznických dat v Azure](../fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorování neoprávněného přenosu citlivých dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Monitorujte neoprávněný přenos dat do umístění mimo nepodnikovou viditelnost a kontrolu. To obvykle zahrnuje monitorování neobvyklých aktivit (velké nebo neobvyklé přenosy), které můžou značit neautorizovanou exfiltraci dat. 

Rozšířená ochrana před internetovými útoky (ATP) pro službu Azure Storage a ochrana ATP pro Azure SQL dokáží upozorňovat na neobvyklé přenosy informací, které můžou značit neautorizované přenosy citlivých informací. 

Azure Information Protection (AIP) nabízí funkce monitorování důvěrných a označených informací. 

Pokud to v rámci ochrany před únikem informací vyžaduje dodržování předpisů, můžete k vynucování kontrolních mechanismů detekce a prevence za účelem zajištění ochrany před exfiltrací dat využít řešení ochrany před únikem informací na hostiteli.

- [Povolení ochrany ATP pro Azure SQL](../../azure-sql/database/threat-detection-overview.md)

- [Povolení ochrany ATP pro službu Azure Storage](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Odpovědnost:** Sdílené

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Šifrování citlivých informací při přenosu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Aby bylo možné doplňovat řízení přístupu, je třeba chránit data při přenosu proti útokům typu "vzdálené správy" (např. zachytávání přenosů) pomocí šifrování, aby útočníci mohli snadno číst nebo upravovat data. 

I když je to pro přenosy v privátních sítích volitelné, je to pro provoz v externích i veřejných sítích velmi důležité. U přenosů HTTP zajistěte, aby všichni klienti připojující se k prostředkům Azure mohli vyjednávat TLS v 1.2 nebo vyšší verzi. Pro vzdálenou správu použijte místo nešifrovaného protokolu SSH (pro Linux) nebo RDP/TLS (pro Windows). Zastaralé verze a protokoly SSL, TLS a SSH a slabé šifry by měly být zakázané.  

Ve výchozím nastavení poskytuje Azure šifrování dat při přenosu mezi datovými centry Azure. 

- [Pochopení šifrování při přenosu pomocí Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informace o zabezpečení TLS](/security/engineering/solving-tls1-problem)

- [Dvojité šifrování pro Azure data při přenosu](../fundamentals/double-encryption.md#data-in-transit)

**Odpovědnost:** Sdílené

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Šifrování citlivých neaktivních uložených dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Pro doplnění řízení přístupu by měla být neaktivní data chráněná před útoky typu "vzdálené správy" (například přístup k základnímu úložišti) pomocí šifrování. To pomáhá zajistit, že útočníci nemohou snadno číst nebo upravovat data. 

Azure ve výchozím nastavení poskytuje šifrování pro neaktivní data. U vysoce citlivých dat máte možnost implementovat další šifrování v klidovém umístění všech prostředků Azure, kde jsou k dispozici. Azure spravuje vaše šifrovací klíče ve výchozím nastavení, ale Azure poskytuje možnosti pro správu vlastních klíčů (zákaznických klíčů) pro určité služby Azure.

- [Principy šifrování neaktivních uložených dat v Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Model šifrování a tabulka správy klíčů](../fundamentals/encryption-models.md)

- [Data s dvojitým šifrováním v Azure](../fundamentals/double-encryption.md#data-at-rest)

**Odpovědnost:** Sdílené

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)
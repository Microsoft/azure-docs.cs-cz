---
title: Azure Security test benchmark v2 – ochrana dat
description: Ochrana dat benchmark v2 pro Azure Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ab42acbc07072f48d28b3adb5bc4c65672f257bc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314313"
---
# <a name="security-control-v2-data-protection"></a>Řízení zabezpečení v2: Ochrana dat

Ochrana dat pokrývá kontrolu ochrany dat v klidovém provozu, při přenosu a prostřednictvím autorizovaných mechanismů přístupu. To zahrnuje zjišťování, klasifikaci, ochranu a monitorování citlivých datových prostředků pomocí řízení přístupu, šifrování a protokolování v Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: zjišťování, klasifikace a označení citlivých dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Zjišťování, klasifikace a označování citlivých dat, abyste mohli navrhovat příslušné ovládací prvky pro zajištění, že se citlivé informace ukládají, zpracovávají a bezpečně odesílají technologické systémy organizace. 

Pro citlivé informace v dokumentech Office v Azure, v místním prostředí, v sadě Office 365 a v jiných umístěních použijte Azure Information Protection (a příslušný nástroj pro kontrolu). 

Pomocí Azure SQL Information Protection můžete pomoct při klasifikaci a označování informací uložených v databázích Azure SQL.

- [Označení citlivých informací pomocí Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementace zjišťování dat SQL Azure](/azure/sql-database/sql-database-data-discovery-and-classification)

**Odpovědnost**: sdílená

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DP – 2 | 13,2, 2,10 | SC-7, AC-4 |

Chraňte citlivá data omezením přístupu pomocí Access Control založeného na rolích Azure (Azure RBAC), řízení přístupu na základě sítě a specifických ovládacích prvků v rámci služeb Azure (například šifrování v SQL a dalších databázích). 

Aby se zajistilo jednotné řízení přístupu, měly by být všechny typy řízení přístupu zarovnané na vaši strategii podnikového segmentace. Strategie segmentace podniku by měla být také informována o umístění citlivých nebo důležitých podnikových dat a systémů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a expozicí zákaznických dat. Aby bylo zajištěno, že zákaznická data v Azure zůstanou zabezpečená, Microsoft implementuje některé výchozí ovládací prvky a možnosti ochrany dat.

- [Access Control na základě rolí Azure (RBAC)](../../role-based-access-control/overview.md)

- [Pochopení ochrany zákaznických dat v Azure](../fundamentals/protection-customer-data.md)

**Odpovědnost**: sdílená

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: monitorování neoprávněného přenosu citlivých dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Monitorujte neoprávněný přenos dat do umístění mimo nepodnikovou viditelnost a kontrolu. To obvykle zahrnuje monitorování neobvykléch aktivit (velkých nebo neobvyklých přenosů), které by mohly označovat neoprávněné datové exfiltracey. 

Azure Storageá ochrana před internetovými útoky (ATP) a Azure SQL ATP může upozorňovat na neobvyklé přenos informací, které by mohly označovat neoprávněné přenosy citlivých informací. 

Azure Information Protection (AIP) poskytuje možnosti monitorování pro informace klasifikované a označené. 

Pokud je to vyžadováno pro zajištění kompatibility ochrany před únikem informací (DLP), můžete použít řešení ochrany před únikem informací, které vynutilo vyšetřovací nástroj a/nebo preventivní ovládací prvky, aby se zabránilo datovým exfiltrace.

- [Povolit Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [Povolit Azure Storage ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Odpovědnost**: sdílená

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: šifrování citlivých informací při přenosu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Aby bylo možné doplňovat řízení přístupu, je třeba chránit data při přenosu proti útokům typu "vzdálené správy" (např. zachytávání přenosů) pomocí šifrování, aby útočníci mohli snadno číst nebo upravovat data. 

I když je to pro přenosy v privátních sítích volitelné, je to pro provoz v externích i veřejných sítích velmi důležité. U přenosů HTTP zajistěte, aby všichni klienti připojující se k prostředkům Azure mohli vyjednávat TLS v 1.2 nebo vyšší verzi. Pro vzdálenou správu použijte místo nešifrovaného protokolu SSH (pro Linux) nebo RDP/TLS (pro Windows). Zastaralé verze a protokoly SSL, TLS a SSH a slabé šifry by měly být zakázané.  

Ve výchozím nastavení poskytuje Azure šifrování dat při přenosu mezi datovými centry Azure. 

- [Pochopení šifrování při přenosu pomocí Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informace o zabezpečení TLS](/security/engineering/solving-tls1-problem)

- [Dvojité šifrování pro Azure data při přenosu](../fundamentals/double-encryption.md#data-in-transit)

**Odpovědnost**: sdílená

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: šifrování citlivých dat v klidovém umístění

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Pro doplnění řízení přístupu by měla být neaktivní data chráněná před útoky typu "vzdálené správy" (například přístup k základnímu úložišti) pomocí šifrování. To pomáhá zajistit, že útočníci nemohou snadno číst nebo upravovat data. 

Azure ve výchozím nastavení poskytuje šifrování pro neaktivní data. U vysoce citlivých dat máte možnost implementovat další šifrování v klidovém umístění všech prostředků Azure, kde jsou k dispozici. Azure spravuje vaše šifrovací klíče ve výchozím nastavení, ale Azure poskytuje možnosti pro správu vlastních klíčů (zákaznických klíčů) pro určité služby Azure.

- [Vysvětlení šifrování v klidovém umístění v Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](../../storage/common/storage-encryption-keys-portal.md)

- [Model šifrování a tabulka správy klíčů](../fundamentals/encryption-models.md)

- [Data s dvojitým šifrováním v Azure](../fundamentals/double-encryption.md#data-at-rest)

**Odpovědnost**: sdílená

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zabezpečení aplikací a DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)


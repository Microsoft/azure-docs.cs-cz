---
title: Azure Security benchmark v2 – zálohování a obnovení
description: Zálohování a obnovení Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b2e54545fb79120a3f9d66067da267df3b151b3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322117"
---
# <a name="security-control-v2-backup-and-recovery"></a>Řízení zabezpečení v2: zálohování a obnovení

Zálohování a obnovení pokrývá ovládací prvky, aby se zajistilo, že se budou provádět, ověřovat a chránit data a konfigurace v různých úrovních služeb.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zajištění pravidelného automatizovaného zálohování

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Ujistěte se, že zálohujete systémy a data a udržujete provozní kontinuitu po neočekávané události. To by mělo být definováno všemi cíli pro cíl bodu obnovení (RPO) a plánovaný čas obnovení (RTO).

Povolte Azure Backup a nakonfigurujte zdroj zálohy (například virtuální počítače Azure, SQL Server, databáze HANA nebo sdílené složky) a také požadovanou frekvenci a dobu uchování.  

V případě vyšší úrovně ochrany můžete povolit geograficky redundantní možnosti úložiště pro replikaci zálohovaných dat do sekundární oblasti a obnovení pomocí obnovení mezi oblastmi.

- [Provozní kontinuita a zotavení po havárii na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Postup povolení Azure Backup](/azure/backup/)

- [Jak povolit obnovení mezi oblastmi](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zásady a standardy](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: šifrování zálohovaných dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| BR – 2 | 10,2 | CP-9 |

Zajistěte, aby byly zálohy chráněny proti útokům. To by mělo zahrnovat šifrování záloh v zájmu ochrany před ztrátou důvěrných informací.   

U místních záloh pomocí Azure Backup je k dispozici šifrování v klidovém formátu pomocí hesla, které zadáte. Pro pravidelné zálohování služeb Azure se zálohovaná data automaticky šifrují pomocí klíčů spravovaných platformou Azure. Zálohy můžete šifrovat pomocí spravovaného klíče zákazníka. V takovém případě zajistěte, aby byl tento klíč spravovaný zákazníkem v trezoru klíčů také v oboru zálohování. 

Použití řízení přístupu na základě role v Azure Backup, Azure Key Vault nebo jiných prostředcích k ochraně záloh a spravovaných klíčů zákazníků. Kromě toho můžete povolit pokročilé funkce zabezpečení, které budou vyžadovat vícefaktorové ověřování, aby bylo možné změnit nebo odstranit zálohy.

- [Přehled funkcí zabezpečení v Azure Backup](/azure/backup/security-overview)

- [Šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem](/azure/backup/encryption-at-rest-with-cmk) 

- [Postup zálohování klíčů Key Vault v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Funkce zabezpečení, které vám pomůžou chránit hybridní zálohy před útoky](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: ověření všech záloh včetně klíčů spravovaných zákazníkem

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Pravidelně provádějte obnovování dat zálohování. Ujistěte se, že můžete obnovit zálohovaných klíčů spravovaných zákazníkem.

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](/azure/backup/backup-azure-restore-files-from-vm)

- [Postup obnovení klíčů Key Vault v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: zmírnění rizika ztracených klíčů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Ujistěte se, že máte k dispozici míry, abyste zabránili ztrátě klíčů a obnovili je. Povolí v Azure Key Vault ochranu před náhodným odstraněním a vyprázdněním, aby se chránily klíče proti náhodnému nebo škodlivému odstranění.  

- [Jak povolit ochranu před odstraněním a vyprázdněním v Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)


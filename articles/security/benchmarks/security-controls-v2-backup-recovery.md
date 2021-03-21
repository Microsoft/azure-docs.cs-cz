---
title: Azure Security benchmark v2 – zálohování a obnovení
description: Zálohování a obnovení Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 39466ad621eff1a7d3490c936c90fbff6f63e0fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051544"
---
# <a name="security-control-v2-backup-and-recovery"></a>Řízení zabezpečení v2: zálohování a obnovení

Zálohování a obnovení pokrývá ovládací prvky, aby se zajistilo, že se budou provádět, ověřovat a chránit data a konfigurace v různých úrovních služeb.

Pokud se chcete podívat na příslušný integrovaný Azure Policy, přečtěte si [Podrobnosti o integrované iniciativě Azure Security test dodržování předpisů: zálohování a obnovení.](../../governance/policy/samples/azure-security-benchmark.md#backup-and-recovery)

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zajištění pravidelného automatizovaného zálohování

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Ujistěte se, že zálohujete systémy a data a udržujete provozní kontinuitu po neočekávané události. To by mělo být definováno všemi cíli pro cíl bodu obnovení (RPO) a plánovaný čas obnovení (RTO).

Povolte Azure Backup a nakonfigurujte zdroj zálohy (například virtuální počítače Azure, SQL Server, databáze HANA nebo sdílené složky) a také požadovanou frekvenci a dobu uchování.

V případě vyšší úrovně ochrany můžete povolit možnost geograficky redundantního úložiště pro replikaci zálohovaných dat do sekundární oblasti a obnovení pomocí obnovení mezi oblastmi.

- [Provozní kontinuita a zotavení po havárii na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Postup povolení Azure Backup](../../backup/index.yml)

- [Povolení obnovení mezi oblastmi](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zásady a standardy](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: šifrování zálohovaných dat

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR – 2 | 10,2 | CP-9 |

Zajistěte, aby byly zálohy chráněny proti útokům. To by mělo zahrnovat šifrování záloh v zájmu ochrany před ztrátou důvěrných informací.

U místních záloh pomocí Azure Backup je k dispozici šifrování v klidovém formátu pomocí hesla, které zadáte. Pro pravidelné zálohování služeb Azure se zálohovaná data automaticky šifrují pomocí klíčů spravovaných platformou Azure. Zálohy můžete šifrovat pomocí spravovaného klíče zákazníka. V takovém případě zajistěte, aby byl tento klíč spravovaný zákazníkem v trezoru klíčů také v oboru zálohování.

Pomocí řízení přístupu na základě role Azure v Azure Backup, Azure Key Vault nebo jiných prostředcích můžete chránit zálohy a spravované klíče zákazníka. Kromě toho můžete povolit pokročilé funkce zabezpečení, které budou vyžadovat vícefaktorové ověřování, aby bylo možné změnit nebo odstranit zálohy.

- [Přehled funkcí zabezpečení v Azure Backup](../../backup/security-overview.md)

- [Šifrování zálohovaných dat s využitím klíčů spravovaných zákazníkem](../../backup/encryption-at-rest-with-cmk.md) 

- [Postup zálohování klíčů Key Vault v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Funkce zabezpečení, které vám pomůžou chránit hybridní zálohy před útoky](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR-3 | 10.3 | CP-4, CP-9 |

Pravidelně provádějte obnovování dat zálohování. Ujistěte se, že můžete obnovit zálohovaných klíčů spravovaných zákazníkem.

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Postup obnovení klíčů Key Vault v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmírnění rizika ztracených klíčů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Ujistěte se, že máte k dispozici míry, abyste zabránili ztrátě klíčů a obnovili je. Povolte v Azure Key Vaultu obnovitelné odstranění a ochranu před vymazáním, aby byly klíče chráněné proti náhodnému nebo zlomyslnému odstranění.

- [Jak povolit obnovitelné odstranění a ochranu před vymazáním v Key Vaultu](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Příprava na incidenty](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Zabezpečení dat](/azure/cloud-adoption-framework/organize/cloud-security-data-security)
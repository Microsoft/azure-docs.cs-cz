---
title: Řízení zabezpečení Azure – obnovení dat
description: Obnovení dat řízení zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 835e4f681d514bb6b92caa5ee076e3794ed59236
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698814"
---
# <a name="security-control-data-recovery"></a>Řízení zabezpečení: obnovení dat

Ujistěte se, že jsou pravidelně zálohována všechna systémová data, konfigurace a tajné klíče.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,1 | 10.1 | Zákazník |

Povolte Azure Backup a nakonfigurujte zdroj zálohy (virtuální počítače Azure, SQL Server nebo sdílené složky) a také požadovanou frekvenci a dobu uchování.

- [Postup povolení Azure Backup](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,2 | 10,2 | Zákazník |

Povolte Azure Backup a cílové virtuální počítače a také požadovanou četnost a dobu uchování. Zálohujte spravované klíče zákazníka v rámci Azure Key Vault.

- [Postup povolení Azure Backup](../../backup/index.yml)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,3 | 10,3 | Zákazník |

Zajistěte, aby možnost pravidelně prováděla obnovování obsahu v rámci Azure Backup. Test obnovení zálohovaných spravovaných klíčů zákazníka

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,4 | 10,4 | Zákazník |

V případě místního zálohování se šifrování v klidovém stavu zajišťuje pomocí hesla, které zadáte při zálohování do Azure. Pro virtuální počítače Azure jsou neaktivní uložená data zašifrovaná pomocí Šifrování služby Storage (SSE). Pomocí řízení přístupu na základě role Azure můžete chránit zálohy a spravované klíče zákazníka.  

Povolit Soft-Delete a vyprázdnit ochranu v Key Vault a chránit tak klíče proti náhodnému nebo škodlivému odstranění.  Pokud se pro ukládání záloh používá Azure Storage, povolte obnovitelné odstranění, abyste mohli data ukládat a obnovovat při odstraňování objektů BLOB nebo snímků objektů BLOB. 

- [Pochopení Azure RBAC](../../role-based-access-control/overview.md)

- [Postup povolení ochrany Soft-Delete a vyprázdnění v Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Obnovitelné odstranění objektů blob služby Azure Storage](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>Další kroky

- Zobrazit další řízení zabezpečení:  [reakce na incidenty](security-control-incident-response.md)
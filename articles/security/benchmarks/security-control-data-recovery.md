---
title: Řízení zabezpečení Azure – obnovení dat
description: Obnovení dat řízení zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81408604"
---
# <a name="security-control-data-recovery"></a>Řízení zabezpečení: obnovení dat

Ujistěte se, že jsou pravidelně zálohována všechna systémová data, konfigurace a tajné klíče.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,1 | 10.1 | Zákazník |

Povolte Azure Backup a nakonfigurujte zdroj zálohy (virtuální počítače Azure, SQL Server nebo sdílené složky) a také požadovanou frekvenci a dobu uchování.

- [Postup povolení Azure Backup](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,2 | 10,2 | Zákazník |

Povolte Azure Backup a cílové virtuální počítače a také požadovanou četnost a dobu uchování. Zálohujte spravované klíče zákazníka v rámci Azure Key Vault.

- [Postup povolení Azure Backup](https://docs.microsoft.com/azure/backup/)

- [Postup zálohování klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,3 | 10,3 | Zákazník |

Zajistěte, aby možnost pravidelně prováděla obnovování obsahu v rámci Azure Backup. Test obnovení zálohovaných spravovaných klíčů zákazníka

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Postup obnovení klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 9,4 | 10,4 | Zákazník |

V případě místního zálohování se šifrování v klidovém stavu zajišťuje pomocí hesla, které zadáte při zálohování do Azure. Pro virtuální počítače Azure jsou neaktivní uložená data zašifrovaná pomocí Šifrování služby Storage (SSE). Použijte řízení přístupu na základě rolí k ochraně záloh a spravovaných klíčů zákazníků.  

Povolit Soft-Delete a vyprázdnit ochranu v Key Vault a chránit tak klíče proti náhodnému nebo škodlivému odstranění.  Pokud se pro ukládání záloh používá Azure Storage, povolte obnovitelné odstranění, abyste mohli data ukládat a obnovovat při odstraňování objektů BLOB nebo snímků objektů BLOB. 

- [Pochopení Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Postup povolení ochrany Soft-Delete a vyprázdnění v Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Obnovitelné odstranění objektů blob služby Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Další kroky

- Zobrazit další řízení zabezpečení:  [reakce na incidenty](security-control-incident-response.md)
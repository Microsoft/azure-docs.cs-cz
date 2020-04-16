---
title: Ovládací prvek zabezpečení Azure – obnovení dat
description: Obnovení dat ovládacího prvku zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408604"
---
# <a name="security-control-data-recovery"></a>Řízení zabezpečení: Obnovení dat

Ujistěte se, že všechna systémová data, konfigurace a tajné klíče jsou automaticky zálohovány v pravidelných intervalech.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.1 | 10.1 | Zákazník |

Povolte Azure Backup a nakonfigurujte zdroj zálohování (virtuální počítače Azure, SQL Server nebo sdílené složky) a také požadovanou frekvenci a dobu uchovávání.

- [Jak povolit Azure Backup](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.2 | 10.2 | Zákazník |

Povolte Azure Backup a cílové virtuální počítače, stejně jako požadovanou frekvenci a dobu uchovávání. Zálohování klíčů spravovaných zákazníkem v rámci služby Azure Key Vault.

- [Jak povolit Azure Backup](https://docs.microsoft.com/azure/backup/)

- [Jak zálohovat klíče trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.3 | 10.3 | Zákazník |

Zajistěte možnost pravidelně provádět obnovení dat obsahu v rámci služby Azure Backup. Otestujte obnovení zálohovaných klíčů spravovaných zákazníkem.

- [Jak obnovit soubory ze zálohy virtuálního počítače Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Jak obnovit klíče trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.4 | 10.4 | Zákazník |

V případě místního zálohování se šifrování v klidovém stavu zajišťuje pomocí hesla, které zadáte při zálohování do Azure. Pro virtuální počítače Azure jsou neaktivní uložená data zašifrovaná pomocí Šifrování služby Storage (SSE). Pomocí řízení přístupu založeného na rolích můžete chránit zálohy a klíče spravované zákazníkem.  

Povolte funkci Obnovitelné odstranění a vyčištění v trezoru klíčů, abyste ochránili klíče před náhodným nebo škodlivým odstraněním.  Pokud se Azure Storage používá k ukládání záloh, povolte obnovitelné odstranění pro uložení a obnovení dat při odstranění objektů blob nebo snímků objektů blob. 

- [Principy Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Jak povolit ochranu proti odstranění a vymazání pomocí funkce Soft-Delete a Purge v trezoru klíčů](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Obnovitelné odstranění objektů blob služby Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [Reakce na incidenty](security-control-incident-response.md)
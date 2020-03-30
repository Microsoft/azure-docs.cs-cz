---
title: Ovládací prvek zabezpečení Azure – obnovení dat
description: Obnovení dat řízení zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934495"
---
# <a name="security-control-data-recovery"></a>Řízení zabezpečení: Obnovení dat

Ujistěte se, že všechna systémová data, konfigurace a tajné klíče jsou automaticky zálohovány v pravidelných intervalech.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.1 | 10.1 | Zákazník |

Povolte Azure Backup a nakonfigurujte zdroj zálohování (virtuální počítače Azure, SQL Server nebo sdílené složky) a také požadovanou frekvenci a dobu uchovávání.

Jak povolit Azure Backup:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.2 | 10.2 | Zákazník |

Povolte Azure Backup a cílové virtuální počítače, stejně jako požadovanou frekvenci a dobu uchovávání. Zálohování klíčů spravovaných zákazníkem v rámci služby Azure Key Vault.

Jak povolit Azure Backup:

https://docs.microsoft.com/azure/backup/

Jak zálohovat klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.3 | 10.3 | Zákazník |

Zajistěte možnost pravidelně provádět obnovení dat obsahu v rámci služby Azure Backup. V případě potřeby otestujte obnovení na izolované síti VLAN. Otestujte obnovení zálohovaných klíčů spravovaných zákazníkem.

Jak obnovit soubory ze zálohy virtuálního počítače Azure:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Jak obnovit klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 9.4 | 10.4 | Zákazník |

V případě místního zálohování se šifrování v klidovém stavu zajišťuje pomocí hesla, které zadáte při zálohování do Azure. Pro virtuální počítače Azure jsou neaktivní uložená data zašifrovaná pomocí Šifrování služby Storage (SSE). Můžete povolit Obnovitelné odstranění v trezoru klíčů k ochraně klíčů před náhodným nebo škodlivým odstraněním.

Jak povolit obnovitelné odstranění v trezoru klíčů:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Reakce na incidenty](security-control-incident-response.md)

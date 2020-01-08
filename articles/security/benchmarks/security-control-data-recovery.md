---
title: Řízení zabezpečení Azure – obnovení dat
description: Obnovení dat kontroly zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 1cd3e39fd504b5095a83192a4a6314c71d3ca980
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564293"
---
# <a name="security-control-data-recovery"></a>Řízení zabezpečení: obnovení dat

Ujistěte se, že jsou pravidelně zálohována všechna systémová data, konfigurace a tajné klíče.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 9,1 | 10.1 | Zákazník |

Povolte Azure Backup a nakonfigurujte zdroj zálohy (virtuální počítače Azure, SQL Server nebo sdílené složky) a také požadovanou frekvenci a dobu uchování.

Postup povolení Azure Backup: https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 9.2 | 10.2 | Zákazník |

Povolte Azure Backup a cílové virtuální počítače a také požadovanou četnost a dobu uchování. Zálohujte spravované klíče zákazníka v rámci Azure Key Vault.

Postup povolení Azure Backup: https://docs.microsoft.com/azure/backup/

Postup zálohování klíčů trezoru klíčů v Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 9,3 | 10,3 | Zákazník |

Zajistěte, aby možnost pravidelně prováděla obnovování obsahu v rámci Azure Backup. V případě potřeby test obnovování na izolovanou síť VLAN. Test obnovení zálohovaných spravovaných klíčů zákazníka

Postup obnovení souborů ze zálohy virtuálního počítače Azure:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Postup obnovení klíčů trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 9.4 | 10,4 | Zákazník |

V případě místního zálohování se šifrování v klidovém prostředí zajišťuje pomocí hesla, které zadáte při zálohování do Azure. Pro virtuální počítače Azure se data zašifrují – na REST pomocí Šifrování služby Storage (SSE). Můžete povolit obnovitelné odstranění v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

Jak povolit obnovitelné odstranění v Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Další kroky

Zobrazit další řízení zabezpečení: [reakce na incidenty](security-control-incident-response.md)

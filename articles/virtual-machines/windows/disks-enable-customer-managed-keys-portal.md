---
title: Použití Azure Portal k povolení klíčů spravovaných zákazníkem s disky spravovanými SSE
description: Pomocí Azure Portal můžete povolit šifrování na straně serveru pomocí klíčů spravovaných zákazníkem na vašich spravovaných discích.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4e3699e1fab928db5a466073c411b701ffc1a51d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235835"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Povolení klíčů spravovaných zákazníkem pomocí disků spravovaných šifrováním na straně serveru – portál

Pokud zvolíte možnost použití šifrování na straně serveru (SSE) pro spravované disky, umožňuje vám Azure Disk Storage spravovat vlastní klíče. Koncepční informace o SSE se spravovanými klíči zákazníků a s jinými typy šifrování spravovaného disku najdete v části [klíče spravované zákazníkem](disk-encryption.md#customer-managed-keys) v článku věnovaném šifrování disku.

## <a name="restrictions"></a>Omezení

Klíče spravované zákazníkem teď mají následující omezení:

- Pokud je tato funkce pro disk povolená, nemůžete ji zakázat.
    Pokud potřebujete tento problém obejít, musíte [zkopírovat všechna data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) na zcela jiný spravovaný disk, který nepoužívá klíče spravované zákazníkem.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

Následující části obsahují informace o tom, jak povolit a používat klíče spravované zákazníkem pro spravované disky:

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte šablony Azure Resource Manager pro vytváření šifrovaných disků pomocí klíčů spravovaných zákazníkem](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
- [Replikace počítačů s povolenými disky spravovanými zákazníky](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Nastavení zotavení po havárii virtuálních počítačů VMware do Azure pomocí PowerShellu](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V pomocí PowerShellu a Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
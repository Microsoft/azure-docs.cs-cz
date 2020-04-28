---
title: Povolit Azure Disk Encryption pro Virtual Machine Scale Sets
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278975"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption pro Virtual Machine Scale Sets

Azure Disk Encryption poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů, což pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení organizace a dodržování předpisů. Další informace najdete v tématu [Azure Disk Encryption: virtuální počítače Linux](../virtual-machines/linux/disk-encryption-overview.md) a [Azure Disk Encryption: virtuální počítače s Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption můžete použít taky pro virtuální počítače s Windows a Linux, a to v těchto případech:
- Sady škálování vytvořené pomocí spravovaných disků. Azure Disk Encryption se nepodporuje u nativních (nebo nespravovaných) sad škálování disku.
- OPERAČNÍ systémy a datové svazky ve Windows Scale Sets.
- Datové svazky v systému Linux Scale Sets. Šifrování disku s operačním systémem se v současné době nepodporuje pro systémy Linux Scale Sets.

Základní informace o Azure Disk Encryption pro Virtual Machine Scale Sets se dozvíte během několika minut pomocí [šifrování virtuálních počítačů s využitím Azure CLI](disk-encryption-cli.md) nebo [šifrování virtuálních počítačů pomocí Azure PowerShellch](disk-encryption-powershell.md) kurzů.

## <a name="next-steps"></a>Další kroky

- [Šifrování virtuálních počítačů pomocí Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s pořadím rozšíření pro škálování sady virtuálních počítačů](disk-encryption-extension-sequencing.md)

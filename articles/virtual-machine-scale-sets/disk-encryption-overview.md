---
title: Povolit Azure Disk Encryption pro Virtual Machine Scale Sets
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 69197b9c0d2a60e1833d03a3f12802221a8307e2
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530833"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption pro Virtual Machine Scale Sets

Azure Disk Encryption poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů, což pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení organizace a dodržování předpisů. Další informace najdete v tématu [Azure Disk Encryption: virtuální počítače Linux](../virtual-machines/linux/disk-encryption-overview.md) a [Azure Disk Encryption: virtuální počítače s Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption můžete použít taky pro virtuální počítače s Windows a Linux, a to v těchto případech:
- Sady škálování vytvořené pomocí spravovaných disků. Azure Disk Encryption se nepodporuje u nativních (nebo nespravovaných) sad škálování disku.
- OPERAČNÍ systémy a datové svazky ve Windows Scale Sets.
- Datové svazky v systému Linux Scale Sets. Šifrování disku s operačním systémem se v současné době nepodporuje pro systémy Linux Scale Sets.

Základní informace o Azure Disk Encryption pro Virtual Machine Scale Sets se dozvíte během několika minut pomocí [šifrování virtuálních počítačů s využitím rozhraní příkazového řádku Azure CLI](disk-encryption-cli.md) nebo [šifrování virtuálních počítačů pomocí Azure CLI](disk-encryption-powershell.md) .

## <a name="next-steps"></a>Další kroky

- [Šifrování virtuálních počítačů pomocí Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s pořadím rozšíření pro škálování sady virtuálních počítačů](disk-encryption-extension-sequencing.md)
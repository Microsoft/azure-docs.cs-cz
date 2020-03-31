---
title: Povolení šifrování disku Azure pro škálovací sady virtuálních počítačů
description: Tento článek obsahuje pokyny k povolení šifrování disku Microsoft Azure pro škálovací sady virtuálních počítačů
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278975"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Šifrování disku Azure pro škálovací sady virtuálních počítačů

Azure Disk Encryption poskytuje šifrování svazků pro operační systém a datové disky vašich virtuálních počítačů, což pomáhá chránit a chránit vaše data tak, aby splňovala závazky organizace v oblasti zabezpečení a dodržování předpisů. Další informace najdete [v tématu Azure Disk Encryption: Linux Virtuální počítače](../virtual-machines/linux/disk-encryption-overview.md) a Azure Disk [Encryption: Virtuální počítače s Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption lze použít také pro škálovací sady virtuálních počítačů windows a linux, v těchto případech:
- Škálovací sady vytvořené pomocí spravovaných disků. Šifrování disku Azure není podporováno pro nativní (nebo nespravované) sady škálovacího systému disku.
- OS a datové svazky v škálovacích sadách Windows.
- Datové svazky v škálovacích sadách Linuxu. Šifrování disku operačního systému není v současné době podporováno pro škálovací sady Linuxu.

Základy škálovacích sad virtuálních počítačů pro škálovací sady virtuálních počítačů se můžete naučit během několika minut pomocí [škálovacích sad Encrypt a virtual machine pomocí azure cli](disk-encryption-cli.md) nebo [šifrovacích sad škálování virtuálních počítačů pomocí kurzů Azure PowerShell.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Další kroky

- [Šifrování škálovacích sad virtuálních strojů pomocí Správce prostředků Azure](disk-encryption-azure-resource-manager.md)
- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure](disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s řazením rozšíření škálovací sady virtuálních počítačů](disk-encryption-extension-sequencing.md)

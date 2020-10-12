---
title: Virtuální pevný disk se nepodporuje při vytváření virtuálního počítače v Azure | Microsoft Docs
description: Tento článek pomáhá opravovat chyby VHD při spuštění virtuálního počítače v Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: 23f31e8d36d6ba615b21a4043c7d39716fc6d89a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653490"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>Virtuální pevný disk se nepodporuje při vytváření virtuálního počítače v Azure.

Tento článek pomáhá opravovat chyby VHD při spuštění virtuálního počítače v systému Windows nebo Linux.

## <a name="symptoms"></a>Příznaky

Když vytvoříte virtuální počítač v Microsoft Azure pomocí nahraného virtuálního pevného disku, nasazení se nepovede a vrátí se tato chybová zpráva: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Příčina

K tomuto problému dochází z jednoho z následujících důvodů:

- Virtuální pevný disk nesplňuje zarovnání na 1 MB (posun). Podporovaná velikost disku by měla být 1 MB × N. Disk by měl mít velikost například 102 401 MB.
- Virtuální pevný disk je poškozený nebo se nepodporuje. 

## <a name="resolution"></a>Řešení

> [!NOTE]
> Aby bylo možné provést následující opravu, zákazník bude muset provést tyto kroky před nahráním virtuálního pevného disku do Azure.

Chcete-li tento problém vyřešit, změňte velikost disku tak, aby odpovídala velikosti 1 MB:

- Pokud chcete vyřešit problém ve Windows, použijte [rutinu změnit velikost-VHD PowerShell](/powershell/module/hyper-v/resize-vhd). Všimněte si, že **Změna velikosti – VHD** není Azure PowerShell rutinou.

  1. [Instalace role Hyper-V na Windows serveru](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [Převést virtuální disk na virtuální pevný disk s pevnou velikostí](../windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)

- K vyřešení problému v systému Linux použijte [příkaz qemu-img](../linux/create-upload-generic.md).

Další informace o tom, jak vytvořit a nahrát VHD pro vytváření virtuálních počítačů Azure, najdete v následujících článcích:

- [Nahrání a vytvoření virtuálního počítače se systémem Linux z vlastní image disku pomocí Azure CLI 1,0](../linux/upload-vhd.md)
- [Vytvořte a nahrajte virtuální pevný disk Windows Serveru na Azure](../windows/upload-generalized-managed.md)

Pokračování v potížích může znamenat poškozený virtuální pevný disk. V takové situaci doporučujeme znovu sestavit VHD od začátku.

Další informace najdete v následujícím článku:

- [O VHD](../managed-disks-overview.md)

---
title: Povolení kompletního šifrování pomocí šifrování na hostitelských discích Azure Portal – spravované disky
description: Pomocí šifrování na hostiteli povolte komplexní šifrování na Azure Managed disks – Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e969fe34e7e7723218586f24807ad70944ba5716
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171519"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>Povolení kompletního šifrování pomocí šifrování na hostiteli – Azure Portal

Pokud povolíte šifrování na hostiteli, data uložená na hostiteli virtuálního počítače se zašifrují v klidovém stavu a toky se zašifrují do služby úložiště. Koncepční informace o šifrování na hostiteli a také o dalších typech šifrování spravovaného disku najdete v tématu [šifrování v rámci hostitele – koncové šifrování pro data virtuálních počítačů](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Další kroky

[Ukázky šablon Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
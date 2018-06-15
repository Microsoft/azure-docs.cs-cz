---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: e9f2f6eaab1b3504e549171ce6c60ecd9b3b5c7b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664452"
---
### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

|  |  |
|---------|---------|
| [Provedení auditu virtuálního počítače, který nepoužívá spravovaný disk](../articles/azure-policy/scripts/create-vm-managed-disk.md) | Provede audit, pokud se vytvoří virtuální počítač, který nepoužívá spravované disky.|
| [Vytvoření virtuálního počítače, který používá spravovaný disk](../articles/azure-policy/scripts/use-managed-disk-vm.md) | Vyžaduje, aby virtuální počítače používaly spravované disky.|
| [Odepření zvýhodněného hybridního využití](../articles/azure-policy/scripts/deny-hybrid-use.md) | Zakáže používat Zvýhodněné hybridní využití Azure (AHUB). To využijete v případech, že nechcete povolit, aby se používaly místní licence. |
| [Povolení jenom určité image platformy virtuálního počítače](../articles/azure-policy/scripts/allow-certain-vm-image.md) | Vyžaduje, aby virtuální počítače používaly konkrétní verzi UbuntuServeru. |
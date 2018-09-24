---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: eadff6f61bd9e5979ae835230de53562ff8542d1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003689"
---
### <a name="virtual-machines"></a>Virtuální počítače

|  |  |
|---------|---------|
| [Povolení vlastní image virtuálního počítače ze skupiny prostředků](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Vyžaduje, aby vlastní image pocházely ze schválené skupiny prostředků. Zadejte název schválené skupiny prostředků. |
| [Povolené skladové položky účtů úložišť a virtuálních počítačů](../articles/governance/policy/samples/allowed-skus-storage.md) | Vyžaduje, aby účty úložišť a virtuální počítače používaly schválené skladové položky. K ověření schválených skladových položek slouží integrované zásady. Pole schválených skladových položek virtuálních počítačů a pole schválených skladových položek účtů úložišť zadáváte vy. |
| [Schválené image virtuálních počítačů](../articles/governance/policy/samples/allowed-custom-images.md) | Vyžaduje, aby se ve vašem prostředí nasazovaly jenom schválené vlastní image. Zadejte pole ID schválených imagí. |
| [Provedení auditu, když neexistuje rozšíření](../articles/governance/policy/samples/audit-ext-not-exist.md) | Provede audit, pokud není s virtuálním počítačem nasazené rozšíření. Zadáte vydavatele a typ rozšíření, u kterého chcete zkontrolovat, jestli je nasazené. |
| [Nepovolená rozšíření virtuálního počítače](../articles/governance/policy/samples/not-allowed-vm-ext.md) | Zakáže použití zadaných rozšíření. Zadáte pole obsahující typy zakázaných rozšíření. |
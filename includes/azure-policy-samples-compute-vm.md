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
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318168"
---
### <a name="virtual-machines"></a>Virtuální počítače

|  |  |
|---------|---------|
| [Povolení vlastní image virtuálního počítače ze skupiny prostředků](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Vyžaduje, aby vlastní image pocházely ze schválené skupiny prostředků. Zadejte název schválené skupiny prostředků. |
| [Povolené skladové položky účtů úložišť a virtuálních počítačů](../articles/governance/policy/samples/allowed-skus-storage.md) | Vyžaduje, aby účty úložišť a virtuální počítače používaly schválené skladové položky. K ověření schválených skladových položek slouží integrované zásady. Pole schválených skladových položek virtuálních počítačů a pole schválených skladových položek účtů úložišť zadáváte vy. |
| [Schválené image virtuálních počítačů](../articles/governance/policy/samples/allowed-custom-images.md) | Vyžaduje, aby se ve vašem prostředí nasazovaly jenom schválené vlastní image. Zadejte pole ID schválených imagí. |
| [Provedení auditu, když neexistuje rozšíření](../articles/governance/policy/samples/audit-extension-not-exist.md) | Provede audit, pokud není s virtuálním počítačem nasazené rozšíření. Zadáte vydavatele a typ rozšíření, u kterého chcete zkontrolovat, jestli je nasazené. |
| [Nepovolená rozšíření virtuálního počítače](../articles/governance/policy/samples/not-allowed-vm-extension.md) | Zakáže použití zadaných rozšíření. Zadáte pole obsahující typy zakázaných rozšíření. |
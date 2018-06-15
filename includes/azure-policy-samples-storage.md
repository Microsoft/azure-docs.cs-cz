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
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664506"
---
## <a name="storage"></a>Úložiště

|  |  |
|---------|---------|
| [Povolené skladové položky účtů úložišť a virtuálních počítačů](../articles/azure-policy/scripts/allowed-skus-storage.md) | Vyžaduje, aby účty úložišť a virtuální počítače používaly schválené skladové položky. K ověření schválených skladových položek slouží integrované zásady. Pole schválených skladových položek virtuálních počítačů a pole schválených skladových položek účtů úložišť zadáváte vy. |
| [Povolené skladové položky účtu úložiště](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | Vyžaduje, aby účty úložiště používaly schválenou skladovou položku. Zadejte pole schválených skladových položek. |
| [Odepření studených úrovní přístupu pro účty úložiště](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | Zakáže použití studených úrovní přístupu pro účty úložiště objektů blob.  |
| [Zajištění výhradně provozu HTTPS pro účet úložiště](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | Vyžaduje, aby účty úložiště používaly provoz HTTPS.  |
| [Zajištění šifrování souborů úložiště](../articles/azure-policy/scripts/ensure-store-file-enc.md) | Vyžaduje, aby bylo pro účty úložiště povolené šifrování souborů.  |
| [Vyžadovat šifrování účtu úložiště](../articles/azure-policy/scripts/req-store-acct-enc.md) | Vyžaduje, aby účet úložiště používal šifrování objektů blob.  |
---
title: Modul PowerShellu pro Azure Lab Services | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o modulu Prostředí PowerShell, který pomáhá při správě artefaktů ve službě Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609395"
---
# <a name="azlabservices-powershell-module-preview"></a>Modul PowerShellu Az.LabServices (Preview)
Az.LabServices je modul Prostředí PowerShell, který zjednodušuje správu služeb Azure Lab. Poskytuje kompozitelné funkce pro vytváření, dotazování, aktualizaci a odstraňování testovacích účtů, testovacích prostředí, virtuálních počítačů a obrázků. Další informace o tomto modulu naleznete na [domovské stránce Az.LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Tento modul je ve **verzi preview**. 

## <a name="example-command"></a>Ukázkový příkaz
Tady je příklad použití příkazu PowerShell k zastavení všech spuštěných virtuálních< ů ve všech testovacích prostředích.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Začínáme
1. Nainstalujte [Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) pokud ve vašem počítači neexistuje. 
2. Stáhněte si [az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) do svého počítače.
3. Import modulu:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Spusťte následující příkaz a seznam všech testovacích prostředí ve vašem předplatném.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Další kroky
Podívejte se na [domovskou stránku Az.LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

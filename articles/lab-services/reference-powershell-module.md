---
title: Modul PowerShell pro Azure Lab Services | Microsoft Docs
description: Tento článek poskytuje informace o modulu PowerShellu, který pomáhá spravovat artefakty v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646521"
---
# <a name="azlabservices-powershell-module-preview"></a>Modul PowerShellu Az.LabServices (Preview)
AZ. LabServices je modul prostředí PowerShell, který zjednodušuje správu Azure Lab Services. Poskytuje funkce pro vytváření, dotazování, aktualizaci a odstraňování účtů testovacího prostředí, cvičení, virtuálních počítačů a imagí. Další informace o tomto modulu najdete na [domovské stránce AZ. LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Tento modul je ve **verzi Preview**. 

## <a name="example-command"></a>Ukázkový příkaz
Tady je příklad použití příkazu PowerShellu pro zastavení všech spuštěných virtuálních počítačů ve všech cvičeních.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Začínáme
1. Pokud na vašem počítači neexistuje, nainstalujte [Azure PowerShell](/powershell/azure/) . 
2. Stáhněte si do počítače [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) .
3. Importovat modul:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Spuštěním následujícího příkazu zobrazíte seznam všech cvičení v rámci vašeho předplatného.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Další kroky
Podívejte se na [domovskou stránku AZ. LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
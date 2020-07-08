---
title: Modul PowerShell pro Azure Lab Services | Microsoft Docs
description: Tento článek poskytuje informace o modulu PowerShellu, který pomáhá spravovat artefakty v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d9c184f3917be378eb77d1bf4096bfebb5ee1884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445589"
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
1. Pokud na vašem počítači neexistuje, nainstalujte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) . 
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

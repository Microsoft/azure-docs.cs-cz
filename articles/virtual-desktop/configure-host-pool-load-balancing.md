---
title: Konfigurace vyrovnávání zatížení virtuálních počítačů s Windows – Azure
description: Jak nakonfigurovat metodu vyrovnávání zatížení pro prostředí virtuálních počítačů s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2c57ac10fbd318dd4bbb2dc86457e186dd824834
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91951651"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurace metody vyrovnávání zatížení Windows Virtual Desktop

Konfigurace metody vyrovnávání zatížení pro fond hostitelů vám umožní upravit prostředí virtuálních počítačů s Windows, aby lépe vyhovovalo vašim potřebám.

>[!NOTE]
> To se nevztahuje na fond hostitelů trvalé plochy, protože uživatelé mají vždycky mapování 1:1 na hostitele relace v rámci fondu hostitelů.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste postupovali podle pokynů v tématu [nastavení modulu PowerShellu pro virtuální plochu](powershell-module.md) pro stažení a instalaci modulu PowerShell a přihlásíte se ke svému účtu Azure.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurace šířky – první vyrovnávání zatížení

Šířka – první vyrovnávání zatížení je výchozí konfigurace pro nové netrvalé fondy hostitelů. Šířka – první vyrovnávání zatížení distribuuje nové uživatelské relace napříč všemi dostupnými hostiteli relací ve fondu hostitelů. Při konfiguraci škály vyrovnávání zatížení můžete nastavit maximální počet relací na hostitele relace ve fondu hostitelů.

Pokud chcete nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení po prvním použití bez úprav maximálního limitu relací, spusťte následující rutinu PowerShellu:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Až to uděláte, ujistěte se, že jste nastavili metodu vyrovnávání zatížení první, spusťte následující rutinu:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

Pokud chcete nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení a používal nový limit maximálního počtu relací, spusťte následující rutinu PowerShellu:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurace hloubky – první vyrovnávání zatížení

Hloubka při prvním vyrovnávání zatížení distribuuje nové uživatelské relace k dostupnému hostiteli relace s největším počtem připojení, ale nedosáhla maximální prahové hodnoty limitu relací.

>[!IMPORTANT]
>Při konfiguraci vyrovnávání zatížení prvního rozsahu musíte nastavit maximální počet relací na hostitele relace ve fondu hostitelů.

Pokud chcete nakonfigurovat fond hostitelů tak, aby prováděl vyrovnávání zatížení první hloubky, spusťte následující rutinu PowerShellu:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> Algoritmus pro vyrovnávání zatížení s první hloubkou distribuuje relace na hostitele relace na základě maximálního limitu hostitele relace ( `-MaxSessionLimit` ). Výchozí hodnota tohoto parametru je `999999` , což je také nejvyšší možný počet, na který můžete nastavit tuto proměnnou. Tento parametr se vyžaduje, když použijete algoritmus vyrovnávání zatížení první hloubky. Pro nejlepší možné prostředí uživatele nezapomeňte změnit parametr maximální limit počtu hostitelů relace na číslo, které nejlépe vyhovuje vašemu prostředí.

Pokud se chcete ujistit, že se nastavení aktualizovalo, spusťte tuto rutinu:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Konfigurace vyrovnávání zatížení pomocí Azure Portal

Vyrovnávání zatížení můžete také nakonfigurovat pomocí Azure Portal.

Konfigurace vyrovnávání zatížení:

1. Přihlaste se k Azure Portal na https://portal.azure.com .
2. Vyhledejte a v části služby vyberte **virtuální plochu Windows** .
3. Na stránce virtuální počítač s Windows vyberte **fondy hostitelů**.
4. Vyberte název fondu hostitelů, který chcete upravit.
5. Vyberte **Vlastnosti**.
6. Do pole zadejte **maximální časový limit relace** a v rozevírací nabídce vyberte **algoritmus vyrovnávání zatížení** , který chcete pro tento fond hostitelů.
7. Vyberte **Uložit**. To platí pro nové nastavení vyrovnávání zatížení.
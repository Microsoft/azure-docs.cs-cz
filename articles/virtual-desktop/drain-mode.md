---
title: Nastavení režimu vyprazdňování virtuálních počítačů s Windows – Azure
description: Jak nakonfigurovat a používat režim vyprázdnit na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509054"
---
# <a name="set-drain-mode"></a>Nastavit režim vyprázdnění

Režim vyprázdnění izoluje hostitele relace, pokud chcete použít opravy a provést údržbu bez přerušení uživatelských relací. Při izolaci hostitel relace nepřijme nové uživatelské relace. Všechna nová připojení budou přesměrována na dalšího dostupného hostitele relace. Existující připojení na hostiteli relace budou dál fungovat, dokud se uživatel odhlásí nebo Správce ukončí relaci. Pokud je hostitel relace v režimu vyprázdnění, správci se můžou vzdáleně připojit k serveru bez toho, aby procházeli přes službu virtuální plochy Windows. Toto nastavení můžete použít pro fond i osobní plochy.

## <a name="set-drain-mode-using-the-azure-portal"></a>Nastavení režimu vyprázdnění pomocí Azure Portal

Zapnutí režimu vyprázdnění v Azure Portal:

1. Otevřete Azure Portal a pokračujte na fond hostitelů, který chcete izolovat.

2. V navigační nabídce vyberte **hostitelé relace**.

3. V dalším kroku vyberte hostitele, u kterých chcete zapnout režim vyprázdnění, a pak vyberte **zapnout režim vyprázdnění**.

4. Pokud chcete vypnout režim vyprázdnění, vyberte fondy hostitelů, u kterých je zapnutý režim vyprázdnění, a pak vyberte **zapnout režim vyprázdnění**.

## <a name="set-drain-mode-using-powershell"></a>Nastavení režimu vyprázdnění pomocí prostředí PowerShell

Režim vyprázdnění v PowerShellu můžete nastavit pomocí parametru *AllowNewSessions* , který je součástí příkazu [Update-AzWvdSessionhost](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true) .

Spuštěním této rutiny povolíte režim vyprázdnění:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Spuštěním této rutiny zakážete režim vyprázdnění:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>Tento příkaz budete muset spustit pro každého hostitele relace, na který aplikujete nastavení.

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o Azure Portal pro virtuální počítače s Windows, přečtěte si [naše kurzy](create-host-pools-azure-marketplace.md). Pokud jste již obeznámeni se základy, podívejte se na některé z dalších funkcí, které můžete použít s Azure Portal, jako je například [připojení aplikace MSIX](app-attach-azure-portal.md) a [Azure Advisor](azure-advisor.md).

Pokud používáte metodu PowerShellu a chcete zjistit, co může modul dělat, Projděte si část [nastavení modulu PowerShell pro virtuální počítač s Windows](powershell-module.md) a naše [Reference k prostředí PowerShell](/powershell/module/az.desktopvirtualization/).
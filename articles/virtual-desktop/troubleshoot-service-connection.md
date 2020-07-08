---
title: Poradce při potížích s virtuálním počítačem s Windows s připojením služby – Azure
description: Řešení problémů při nastavování připojení klienta v prostředí klienta virtuální plochy Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 56a31caeefb3589527fdbbac118fa3a544a0d1a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208889"
---
# <a name="windows-virtual-desktop-service-connections"></a>Připojení ke službě virtuální plochy Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí tohoto článku můžete vyřešit problémy s připojením klienta k virtuální ploše Windows.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Můžete nám sdělit svůj názor a diskutovat o službě Virtual Desktop Windows s produktovým týmem a dalšími aktivními členy komunity na [technické komunitě pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Uživatel se připojuje, ale nic se nezobrazuje (bez kanálu).

Uživatel může spustit klienty vzdálené plochy a je schopen ho ověřit, ale uživatel nevidí žádné ikony v informačním kanálu webového zjišťování.

1. Pomocí tohoto příkazového řádku zkontrolujte, jestli se uživateli, který nahlásí problémy, přiřadí ke skupinám aplikací:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Potvrďte, že se uživatel přihlašuje se správnými přihlašovacími údaji.

3. Pokud je webový klient používán, zkontrolujte, zda nejsou k dispozici žádné problémy s přihlašovacími údaji v mezipaměti.

4. Pokud je uživatel součástí skupiny uživatelů Azure Active Directory (AD), zajistěte, aby skupina uživatelů byla skupinou zabezpečení místo distribuční skupiny. Virtuální počítač s Windows nepodporuje distribuční skupiny Azure AD.

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Informace o řešení potíží při vytváření prostředí virtuálních počítačů s Windows a fondu hostitelů v prostředí virtuálních ploch Windows najdete v tématu [Vytvoření fondu prostředí a hostitele](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).

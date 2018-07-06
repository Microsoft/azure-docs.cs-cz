---
title: Uživatelé Azure Cloud Shell pro Windows | Dokumentace Microsoftu
description: Příručka pro uživatele, kteří nejsou obeznámeni se systémy Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861638"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Prostředí PowerShell ve službě Azure Cloud Shell pro Windows uživatelům

V květnu 2018 byly změny [oznámili](https://azure.microsoft.com/blog/pscloudshellrefresh/) powershellu ve službě Azure Cloud Shell.  Prostředí PowerShell ve službě Azure Cloud Shell je nyní 6 Powershellu Core v Linuxu.
Díky této změně jsou některé aspekty Powershellu ve službě Cloud Shell, který může být jiný než bylo očekáváno ve Windows PowerShell 5.1.

## <a name="case-sensitivity"></a>Rozlišování velikosti písmen

Ve Windows systém souborů je velká a malá písmena.  V systému Linux systém souborů je velká a malá písmena.
To znamená, že dříve `file.txt` a `FILE.txt` byly považovány za stejný soubor, nyní jsou považovány za na různých souborech.
Správná velká a malá písmena musí být použita při `tab` dokončení v systému souborů.  Specifické pro prostředí PowerShell dojde, jako například `tab` rutiny, nejsou malá a velká písmena. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Prostředí Windows PowerShell alias vs nástroje pro Linux

Existující příkazy v systému Linux, jako například `ls`, `sort`, a `sleep` přednost před jejich aliasy prostředí PowerShell.  Níže jsou uvedeny běžné odebrané aliasy, stejně jako ekvivalentní příkazy:  

|Odebrat Alias   |Ekvivalentní příkaz   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>Zachování $home vs $home\clouddrive

Pro uživatele, který jako trvalý, skripty a další soubory v Cloudovou jednotku adresář $HOME teď trvalá napříč relacemi.

## <a name="powershell-profile"></a>Profil prostředí PowerShell

Ve výchozím nastavení není vytvořen profil prostředí PowerShell.  Chcete-li vytvořit svůj profil, vytvořte `PowerShell` adresáře v rámci `$HOME/.config`.  V `$HOME/.config/PowerShell`, můžete vytvořit svůj profil pod názvem `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Co je nového v Powershellu Core 6

Další informace o tom, co je nového v Powershellu Core 6 [PowerShell docs](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) a [Getting Started with PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blogový příspěvek

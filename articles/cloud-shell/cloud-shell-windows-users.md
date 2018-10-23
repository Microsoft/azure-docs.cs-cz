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
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645010"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Prostředí PowerShell ve službě Azure Cloud Shell pro Windows uživatelům

V květnu 2018 byly změny [oznámili](https://azure.microsoft.com/blog/pscloudshellrefresh/) powershellu ve službě Azure Cloud Shell.
Prostředí PowerShell ve službě Azure Cloud Shell nyní spuštění [PowerShell Core 6](https://github.com/powershell/powershell) v prostředí Linux.
Díky této změně mohou existovat prostředí určité rozdíly v prostředí PowerShell ve službě Cloud Shell ve srovnání s očekávání ve Windows Powershellu.

## <a name="file-system-case-sensitivity"></a>Rozlišování velikosti písmen systému souborů

Systém souborů je velká a malá písmena ve Windows, zatímco v Linuxu, systém souborů je velká a malá písmena.
Dříve `file.txt` a `FILE.txt` považovány za stejný soubor, ale teď jsou považovány za na různých souborech.
Správná velká a malá písmena musí být použita při `tab-completing` v systému souborů.
Specifické pro prostředí PowerShell dojde, jako například `tab-completing` názvy rutin, parametry a hodnoty, nejsou malá a velká písmena.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Vs aliasy prostředí Windows PowerShell nástroje pro Linux

Některé stávající aliasy prostředí PowerShell mají stejné názvy jako integrované Linuxové příkazy, jako například `cat`,`ls`, `sort`, `sleep`atd. V prostředí PowerShell Core 6 byly odebrány aliasy, které kolidují s integrovanou Linuxové příkazy.
Níže jsou uvedeny běžné aliasy, které byly odebrány, jakož i jejich ekvivalentní příkazy:  

|Odebrat Alias   |Ekvivalentní příkaz   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Zachování $HOME

Starší uživatelů může zachovat jenom skripty a další soubory v Cloudovou jednotku.
Nyní adresářem $HOME uživatele je také zachována napříč relacemi.

## <a name="powershell-profile"></a>Profil prostředí PowerShell

Ve výchozím nastavení není vytvořen uživatelského profilu, prostředí PowerShell.
Chcete-li vytvořit svůj profil, vytvořte `PowerShell` adresáře v rámci `$HOME/.config`.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

V části `$HOME/.config/PowerShell`, můžete vytvořit soubory profilu - `profile.ps1` a/nebo `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Co je nového v Powershellu Core 6

Další informace o tom, co je nového v Powershellu Core 6 [PowerShell docs](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) a [Getting Started with PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blogový příspěvek.

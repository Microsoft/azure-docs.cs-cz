---
title: Azure Cloud Shell pro uživatele Windows | Microsoft Docs
description: Příručka pro uživatele bez znalostí systémů Linux
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
ms.openlocfilehash: 8cc1934cc97ecf821de8644dda45d867b3ca3e85
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508942"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell ve službě Azure Cloud Shell pro uživatele Windows

V květnu 2018 byly změny [oznámeny](https://azure.microsoft.com/blog/pscloudshellrefresh/) prostředí PowerShell v Azure Cloud Shell.
Prostředí PowerShell v Azure Cloud Shell teď spouští [PowerShell Core 6](https://github.com/powershell/powershell) v prostředí Linux.
V důsledku této změny můžou v prostředí PowerShell v Cloud Shell v porovnání s tím, co se očekává v prostředí Windows PowerShell, několik rozdílů.

## <a name="file-system-case-sensitivity"></a>Rozlišování velkých a malých písmen v systému souborů

Systém souborů rozlišuje velká a malá písmena v systému Windows, zatímco v systému Linux systém souborů rozlišuje velká a malá písmena.
Dříve `file.txt` a `FILE.txt` byly považovány za stejný soubor, ale nyní jsou považovány za jiné soubory.
V systému souborů musí být použita vhodná velká a malá písmena `tab-completing` .
Prostředí specifická pro prostředí PowerShell, jako `tab-completing` jsou názvy rutin, parametry a hodnoty, nerozlišují velká a malá písmena.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Aliasy Windows PowerShellu vs nástroje pro Linux

Některé existující aliasy PowerShellu mají stejné názvy jako integrované příkazy pro Linux, například `cat` , `ls` , `sort` , `sleep` atd. V PowerShellu Core 6 se odebraly aliasy, které jsou v konfliktu s integrovanými příkazy pro Linux.
Níže jsou uvedeny běžné aliasy, které byly odebrány, a také jejich ekvivalentní příkazy:  

|Odstraněný alias   |Ekvivalentní příkaz   |
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

## <a name="persisting-home"></a>Trvalé $HOME

Starší uživatelé mohli zachovat jenom skripty a jiné soubory ve své cloudové jednotce.
Nyní je $HOME adresář uživatele také trvale v rámci relací.

## <a name="powershell-profile"></a>Profil PowerShellu

Ve výchozím nastavení se profil PowerShell uživatele nevytvoří.
Chcete-li vytvořit profil, vytvořte `PowerShell` v adresáři `$HOME/.config` .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

V části `$HOME/.config/PowerShell` můžete vytvořit své soubory profilu – `profile.ps1` a/nebo `Microsoft.PowerShell_profile.ps1` .

## <a name="whats-new-in-powershell-core-6"></a>Co je nového v PowerShellu Core 6

Další informace o tom, co je v prostředí PowerShell Core 6 novinkou, najdete v blogovém příspěvku [PowerShellu](/powershell/scripting/whats-new/what-s-new-in-powershell-70) a [Začínáme s jádrem prostředí PowerShell](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) .
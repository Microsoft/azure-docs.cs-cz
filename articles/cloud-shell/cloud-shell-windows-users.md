---
title: Azure Cloud Shell pro uživatele Windows | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204143"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell ve službě Azure Cloud Shell pro uživatele Windows

V květnu 2018 byly v Prostředí PowerShell v Azure Cloud [Shellu oznámeny](https://azure.microsoft.com/blog/pscloudshellrefresh/) změny.
Prostředí PowerShellu ve službě Azure Cloud Shell teď spouští [PowerShell Core 6](https://github.com/powershell/powershell) v prostředí Linuxu.
S touto změnou může být některé rozdíly v prostředí PowerShell v prostředí Cloud ve srovnání s tím, co se očekává v prostředí Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Rozlišování malých a velkých písmen systému souborů

Systém souborů je v systému Windows nerozlišována malá a velká písmena, zatímco v systému Linux je systém souborů rozlišován malá a velká písmena.
Dříve `file.txt` `FILE.txt` a byly považovány za stejný soubor, ale nyní jsou považovány za různé soubory.
V systému souborů `tab-completing` musí být použito správné pouzdro.
Prostředí PowerShell specifické, `tab-completing` jako jsou názvy rutin, parametry a hodnoty, nejsou malá a velká písmena.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Aliasy prostředí Windows PowerShell vs linuxové nástroje

Některé existující aliasy prostředí PowerShell mají stejné názvy jako `cat``ls`předdefinované příkazy Linuxu, například , , `sort`, `sleep`atd. V PowerShell Core 6 aliasy, které se srazí s vestavěnými příkazy Linuxu byly odebrány.
Níže jsou uvedeny běžné aliasy, které byly odstraněny, stejně jako jejich ekvivalentní příkazy:  

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

## <a name="persisting-home"></a>Přetrvávající $HOME

Dřívější uživatelé mohli zachovat pouze skripty a další soubory v jejich Cloud Drive.
Nyní je $HOME adresář uživatele také trvalý napříč relacemi.

## <a name="powershell-profile"></a>Profil prostředí PowerShell

Ve výchozím nastavení není vytvořen profil prostředí PowerShell uživatele.
Chcete-li vytvořit profil, `PowerShell` `$HOME/.config`vytvořte v části .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

V `$HOME/.config/PowerShell`části můžete vytvářet `profile.ps1` soubory profilu `Microsoft.PowerShell_profile.ps1`- a/nebo .

## <a name="whats-new-in-powershell-core-6"></a>Co je nového v PowerShellcore 6

Další informace o tom, co je nového v PowerShellu Core 6, nalezni na [dokumenty powershellu](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) a na blogový příspěvek [Začínáme s PowerShellem Core.](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)

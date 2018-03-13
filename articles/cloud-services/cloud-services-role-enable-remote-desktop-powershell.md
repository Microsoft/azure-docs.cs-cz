---
title: "Povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí prostředí PowerShell"
description: "Postup konfigurace aplikace služby azure cloud pomocí prostředí PowerShell umožňující připojení ke vzdálené ploše"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 84fe7ba418399562b6e36ed009c5e6e47fbe24da
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role, která běží v Azure. Připojení ke vzdálené ploše můžete odstraňovat a diagnostikovat problémy s aplikací, když je spuštěná.

Tento článek popisuje povolení vzdálené plochy na role cloudové služby pomocí prostředí PowerShell. V tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) pro součásti potřebné k tomuto článku. Prostředí PowerShell využívá příponou vzdálené plochy, Vzdálená plocha můžete povolit, jakmile je aplikace nasazená.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurace vzdálené plochy z prostředí PowerShell

[Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) rutina umožňuje povolit vzdálené plochy na určené role nebo všechny role nasazení cloudové služby. Rutina umožňuje zadat uživatelské jméno a heslo pro uživatele vzdálené plochy prostřednictvím *pověření* parametr, který přijímá objekt PSCredential.

Pokud používáte prostředí PowerShell interaktivně, můžete snadno nastavit objekt PSCredential voláním [Get-pověření](https://technet.microsoft.com/library/hh849815.aspx) rutiny.

```
$remoteusercredentials = Get-Credential
```

Tento příkaz zobrazí dialogové okno umožňuje zadejte uživatelské jméno a heslo pro vzdálené uživatele zabezpečeným způsobem.

Vzhledem k tomu, že prostředí PowerShell pomáhá scénáře automatizace, můžete také nastavit **PSCredential** objekt způsobem, který nevyžaduje zásah uživatele. Nejdřív musíte nastavit zabezpečeného hesla. Začínat zadání hesla v podobě prostého textu ho převést na zabezpečený řetězec pomocí [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Dále je třeba k pomocí zašifrovaný standardní řetězec převést tento zabezpečený řetězec [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Teď tento šifrovaný řetězec standardní můžete uložit do souboru pomocí [obsah sady](https://technet.microsoft.com/library/ee176959.aspx).

Můžete také vytvořit soubor zabezpečeného hesla tak, aby je nemuseli zadávat heslo pokaždé, když. Navíc soubor zabezpečeného hesla je lepší, než soubor ve formátu prostého textu. Vytvořte soubor zabezpečeného hesla pomocí prostředí PowerShell následující:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Při nastavování hesla, ujistěte se, že splňujete [požadavky na složitost](https://technet.microsoft.com/library/cc786468.aspx).

K vytvoření objektu pověření ze zabezpečeného heslo souboru, musí přečíst obsah souboru a je převést zpět na zabezpečený řetězec pomocí [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

[Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) rutina také umožňuje *vypršení platnosti* parametr, který určuje **data a času** ve který vyprší platnost uživatelského účtu. Například můžete nastavit účet, který chcete několik dní od aktuální datum a čas vypršení platnosti.

Tento příklad PowerShell ukazuje, jak nastavit vzdálenou plochu rozšíření pro cloudové služby:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Můžete volitelně specifikovat slot nasazení a role, které chcete povolit vzdálená plocha na. Pokud tyto parametry nejsou zadané, rutina umožňuje vzdálená plocha u všech rolí v **produkční** nasazovací slot.

Rozšíření vzdálené plochy je přidruženým k nasazení. Pokud vytvoříte nové nasazení pro službu, budete muset povolit vzdálené plochy na toto nasazení. Pokud chcete vždy mít povolena Vzdálená plocha, měli zvážit, integraci skriptů prostředí PowerShell do pracovního postupu nasazení.

## <a name="remote-desktop-into-a-role-instance"></a>Vzdálená plocha do role instance

[Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) rutina se používá ke vzdálené ploše do instance určité role cloudové služby. Můžete použít *Místnícesta* parametr ke stažení protokol RDP soubor místně. Nebo můžete použít *spusťte* parametr přímo spustit dialogové okno připojení ke vzdálené ploše pro přístup k instanci role cloudové služby.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Zkontrolujte, zda rozšíření vzdálené plochy je povolena ve službě

[Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) zobrazuje rutina, která povolená nebo zakázaná na nasazení služby Vzdálená plocha. Rutina vrátí uživatelské jméno pro uživatele vzdálené plochy a rolí, ve kterých je povolené vzdálené plochy rozšíření pro. Ve výchozím nastavení k tomu dochází slotu nasazení a můžete místo toho použít přípravný slot.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Odebrání služby Vzdálená plocha rozšíření

Pokud jste povolili vzdálené plochy rozšíření na nasazení a potřeba aktualizovat nastavení vzdálené plochy, nejprve odeberte rozšíření. A znovu ji povolte s novým nastavením. Například pokud chcete nastavit nové heslo pro účet vzdáleného uživatele nebo platnost účtu. To je potřeba na existující nasazení, které mají vzdálené plochy rozšíření povolené. Pro nová nasazení můžete jednoduše provést rozšíření přímo.

Chcete-li odebrat z nasazení vzdálené plochy rozšíření, můžete použít [odebrat AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) rutiny. Můžete volitelně specifikovat slot nasazení a role, ze kterého chcete odebrat příponou vzdálené plochy.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> K úplnému odebrání konfigurace rozšíření, by měly volat *odebrat* rutiny s **UninstallConfiguration** parametr.
>
> **UninstallConfiguration** parametr odinstaluje jakoukoli konfiguraci rozšíření, která se použije ke službě. Všechny konfigurace rozšíření je přidružen konfiguraci služby. Volání *odebrat* rutiny bez **UninstallConfiguration** zrušíte <mark>nasazení</mark> z konfigurace rozšíření, proto efektivně odebírání rozšíření. Konfigurace rozšíření však zůstane službě přidružen.

## <a name="additional-resources"></a>Další zdroje informací:

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)

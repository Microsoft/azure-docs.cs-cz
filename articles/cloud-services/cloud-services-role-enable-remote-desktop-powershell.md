---
title: Povolení připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí Powershellu
description: Jak nakonfigurovat aplikaci služby cloudu azure pomocí Powershellu povolit připojení ke vzdálené ploše
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 1b1aa8105ab90b0016863f0bf3c47f6aa815d3e7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001030"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Povolení připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí Powershellu

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role, která běží v Azure. Připojení ke vzdálené ploše můžete použít k odstranění potíží a Diagnostikujte problémy s vaší aplikací je spuštěný.

Tento článek popisuje povolení vzdálené plochy na role cloudové služby pomocí prostředí PowerShell. Zobrazit [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) pro součásti potřebné pro účely tohoto článku. Prostředí PowerShell využívá vzdálené plochy rozšíření, tak Vzdálená plocha můžete povolit, jakmile je aplikace nasazená.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurace vzdálené plochy z prostředí PowerShell

[Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) rutina umožňuje povolit vzdálenou plochu na určené role nebo všechny role vašeho nasazení cloudové služby. Rutina umožňuje zadat uživatelské jméno a heslo pro uživatele vzdálené plochy prostřednictvím *přihlašovacích údajů* parametr, který přijímá objekt PSCredential.

Pokud používáte PowerShell interaktivně, můžete snadno nastavit objekt PSCredential voláním [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) rutiny.

```
$remoteusercredentials = Get-Credential
```

Tento příkaz zobrazí dialogové okno umožňuje zadat uživatelské jméno a heslo pro vzdálené uživatele bezpečným způsobem.

Protože Powershellu pomáhají ve scénářích automatizace, můžete také nastavit **PSCredential** objektu způsobem, který nevyžaduje zásah uživatele. Nejprve budete muset nastavit zabezpečené heslo. Začněte zadáním hesla jako prostý text převést na zabezpečený řetězec pomocí [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Dál je potřeba tento zabezpečený řetězec převést na objekt pomocí šifrovaného standardní řetězec [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nyní tento šifrovaný standardní řetězec můžete uložit do souboru pomocí [sadu obsahu](https://technet.microsoft.com/library/ee176959.aspx).

Můžete také vytvořit soubor zabezpečené heslo tak, aby je nemuseli zadávat heslo pokaždé, když. Navíc je lepší než textový soubor. soubor zabezpečené heslo. Vytvoření zabezpečené heslo souboru použijte následující příkaz Powershellu:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Při nastavování hesla, ujistěte se, že splňujete [požadavky na složitost](https://technet.microsoft.com/library/cc786468.aspx).

Chcete-li vytvořit objekt přihlašovacích údajů ze souboru zabezpečené heslo, musí přečíst obsah souboru a je převést zpět na zabezpečený řetězec pomocí [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

[Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) rutina také přijímá *vypršení platnosti* parametr, který určuje **data a času** ve který vyprší platnost uživatelského účtu. Můžete například nastavit účet vyprší několik dní od aktuálního data a času.

Tento příklad Powershellu ukazuje, jak nastavit rozšíření vzdálené plochy pro cloudové služby:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Můžete také v případě potřeby zadejte slot pro nasazení a role, které chcete povolit vzdálenou plochu na. Pokud nejsou zadané tyto parametry, rutina umožňuje vzdálená plocha u všech rolí v **produkční** slot pro nasazení.

Rozšíření vzdálené plochy je přidruženým k nasazení. Pokud vytvoříte nové nasazení služby, budete muset povolit vzdálená plocha u tohoto nasazení. Pokud chcete vždy povolena Vzdálená plocha, pak byste měli zvážit integrace Powershellové skripty do vašich pracovních postupů nasazení.

## <a name="remote-desktop-into-a-role-instance"></a>Role instance pomocí vzdálené plochy

[Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) rutina se používá pro vzdálené plochy konkrétní instance role cloudové služby. Můžete použít *LocalPath* parametr se stáhnout protokol RDP soubor místně. Nebo můžete použít *spuštění* parametr přímo spustit dialogové okno připojení ke vzdálené ploše pro přístup k instanci role cloudové služby.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Zkontrolujte, jestli je povolené rozšíření Vzdálená plocha ve službě

[Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) rutina, která zobrazí povolit nebo zakázat na nasazení služby Vzdálená plocha. Rutina vrátí uživatelské jméno pro uživatele vzdálené plochy a rolí, ve kterých je povolené vzdálené plochy rozšíření pro. Ve výchozím nastavení k tomu dojde u slotu nasazení a můžete zvolit místo toho použít přípravný slot.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Odebrat rozšíření Vzdálená plocha ze služby

Pokud jste povolili vzdálené plochy rozšíření na nasazení a, musíte si aktualizovat nastavení vzdálené plochy, nejprve odeberte rozšíření. A znovu ji povolte s novým nastavením. Například, pokud chcete nastavit nové heslo pro vzdálený uživatelský účet nebo účet vypršela. To je potřeba na existující nasazení vzdálené plochy rozšíření povoleno. Pro nová nasazení můžete jednoduše použít rozšíření přímo.

Odebrání nasazení vzdálené plochy rozšíření, můžete použít [odebrat AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) rutiny. Můžete také v případě potřeby zadejte slot pro nasazení a role, ze kterého chcete odebrat rozšíření vzdálené plochy.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Pokud chcete úplně odebrat konfiguraci rozšíření, měli byste zavolat *odebrat* rutinu s **UninstallConfiguration** parametru.
>
> **UninstallConfiguration** parametr odinstaluje všechny konfigurace rozšíření, která se použije ke službě. Všechny konfigurace rozšíření je přidružen ke konfiguraci služby. Volání *odebrat* rutiny bez **UninstallConfiguration** zruší přidružení <mark>nasazení</mark> z konfigurace rozšíření, proto by znamenalo vyjmutí rozšíření. Ale zůstává konfigurace rozšíření související se službou.

## <a name="additional-resources"></a>Další zdroje informací:

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)

---
title: Povolení funkce Vzdálená plocha pro roli pomocí PowerShellu
titleSuffix: Azure Cloud Services
description: Jak nakonfigurovat aplikaci azure cloudové služby pomocí PowerShellu pro povolení připojení ke vzdálené ploše
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386115"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Povolení připojení ke vzdálené ploše pro roli v Cloudových službách Azure pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Portál Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení a diagnostice problémů s aplikací, když je spuštěna.

Tento článek popisuje, jak povolit vzdálenou plochu v rolích cloudových služeb pomocí PowerShellu. Přečtěte [si téma Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) pro požadavky potřebné pro tento článek. Prostředí PowerShell využívá rozšíření Vzdálené plochy, takže po nasazení aplikace můžete povolit vzdálenou plochu.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurace vzdálené plochy z PowerShellu
Rutina [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) umožňuje povolit vzdálenou plochu na určených rolích nebo ve všech rolích nasazení cloudové služby. Rutina umožňuje zadat uživatelské jméno a heslo pro uživatele vzdálené plochy prostřednictvím parametru *Credential,* který přijímá objekt PSCredential.

Pokud používáte prostředí PowerShell interaktivně, můžete snadno nastavit objekt PSCredential voláním [rutiny Get-Credentials.](https://technet.microsoft.com/library/hh849815.aspx)

```powershell
$remoteusercredentials = Get-Credential
```

Tento příkaz zobrazí dialogové okno, které umožňuje bezpečné zadání uživatelského jména a hesla vzdáleného uživatele.

Vzhledem k tomu, že Prostředí PowerShell pomáhá ve scénářích automatizace, můžete také nastavit objekt **PSCredential** způsobem, který nevyžaduje interakci s uživatelem. Nejprve je třeba nastavit zabezpečené heslo. Začnete zadáním hesla ve formátu prostého textu převést na zabezpečený řetězec pomocí [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Dále je třeba převést tento zabezpečený řetězec na šifrovaný standardní řetězec pomocí [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nyní můžete uložit tento šifrovaný standardní řetězec do souboru pomocí [Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Můžete také vytvořit soubor zabezpečeného hesla, abyste nemuseli heslo zadávat pokaždé. Také soubor se zabezpečeným heslem je lepší než soubor ve formátu prostého textu. K vytvoření zabezpečeného souboru s heslem použijte následující Prostředí PowerShell:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Při nastavování hesla se ujistěte, že splňujete [požadavky na složitost](https://technet.microsoft.com/library/cc786468.aspx).

Chcete-li vytvořit objekt pověření ze zabezpečeného souboru hesel, je nutné přečíst obsah souboru a převést jej zpět na zabezpečený řetězec pomocí [convertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Rutina [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) také přijímá parametr *Expiration,* který určuje **dateTime,** kdy vyprší platnost uživatelského účtu. Můžete například nastavit, aby platnost účtu vypršela několik dní od aktuálního data a času.

Tento příklad prostředí PowerShell ukazuje, jak nastavit rozšíření vzdálené plochy v cloudové službě:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Můžete také volitelně určit slot nasazení a role, které chcete povolit vzdálenou plochu. Pokud tyto parametry nejsou zadány, rutina umožňuje vzdálenou plochu na všech rolích v slotu nasazení **produkčního** prostředí.

Rozšíření Vzdálená plocha je přidruženo k nasazení. Pokud vytvoříte nové nasazení pro službu, budete muset povolit vzdálenou plochu na toto nasazení. Pokud chcete mít vždy povolenou vzdálenou plochu, měli byste zvážit integraci skriptů prostředí PowerShell do pracovního postupu nasazení.

## <a name="remote-desktop-into-a-role-instance"></a>Vzdálená plocha do instance role

Rutina [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) se používá ke vzdálené ploše do instance konkrétní role vaší cloudové služby. Parametr *LocalPath* můžete použít ke stažení souboru RDP místně. Nebo můžete použít parametr *Spuštění* k přímému spuštění dialogového okna Připojení ke vzdálené ploše pro přístup k instanci role cloudové služby.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Kontrola, zda je ve službě povoleno rozšíření Vzdálené plochy

Rutina [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) zobrazuje, že vzdálená plocha je povolená nebo zakázaná při nasazení služby. Rutina vrátí uživatelské jméno pro uživatele vzdálené plochy a role, pro které je povoleno rozšíření vzdálené plochy. Ve výchozím nastavení k tomu dochází v slotu nasazení a můžete místo toho použít pracovní slot.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Odebrání rozšíření Vzdálené plochy ze služby

Pokud jste již povolili rozšíření vzdálené plochy v nasazení a potřebujete aktualizovat nastavení vzdálené plochy, nejprve rozšíření odeberte. A znovu jej povolte s novým nastavením. Chcete-li například nastavit nové heslo pro vzdálený uživatelský účet nebo vypršela jeho platnost. To je vyžadováno u existujících nasazení, která mají povoleno rozšíření vzdálené plochy. Pro nová nasazení můžete jednoduše použít rozšíření přímo.

Chcete-li odebrat rozšíření vzdálené plochy z nasazení, můžete použít rutinu [Remove-AzureServiceRemoteDesktopExtension.](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) Volitelně můžete také určit slot pro nasazení a roli, ze které chcete odebrat rozšíření vzdálené plochy.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Chcete-li zcela odebrat konfiguraci rozšíření, měli byste zavolat *odebranou* rutinu s parametrem **UninstallConfiguration.**
>
> Parametr **UninstallConfiguration** odinstaluje konfiguraci rozšíření, která je použita pro službu. Každá konfigurace rozšíření je přidružena ke konfiguraci služby. Volání *odebrat* rutinu bez **UninstallConfiguration** ruší <mark>nasazení</mark> z konfigurace rozšíření, čímž efektivně odebere rozšíření. Konfigurace rozšíření však zůstane přidružena ke službě.

## <a name="additional-resources"></a>Další zdroje

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)



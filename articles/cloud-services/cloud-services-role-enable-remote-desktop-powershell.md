---
title: Použití PowerShellu k povolení vzdálené plochy pro roli
titleSuffix: Azure Cloud Services
description: Konfigurace aplikace cloudové služby Azure pomocí prostředí PowerShell pro povolení připojení ke vzdálené ploše
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 13d5be5b1e0bcdf84001f21a45364296d67a5da4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87013893"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Povolení Připojení ke vzdálené ploše pro roli v Azure Cloud Services pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení potíží a diagnostikování problémů s aplikací v době, kdy je spuštěná.

Tento článek popisuje, jak povolit vzdálenou plochu na vašich rolích cloudových služeb pomocí PowerShellu. Potřebné předpoklady pro tento článek najdete v tématu [Postup instalace a konfigurace Azure PowerShell](/powershell/azure/) . Prostředí PowerShell využívá rozšíření vzdálené plochy, aby bylo možné povolit vzdálenou plochu po nasazení aplikace.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurace vzdálené plochy z PowerShellu
Rutina [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) umožňuje povolit vzdálenou plochu na zadaných rolích nebo na všech rolích nasazení cloudové služby. Rutina umožňuje zadat uživatelské jméno a heslo pro uživatele vzdálené plochy prostřednictvím parametru *přihlašovacích údajů* , který přijímá objekt PSCredential.

Pokud používáte prostředí PowerShell interaktivně, můžete snadno nastavit objekt PSCredential voláním rutiny [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) .

```powershell
$remoteusercredentials = Get-Credential
```

Tento příkaz zobrazí dialogové okno, které vám umožní bezpečným způsobem zadat uživatelské jméno a heslo pro vzdáleného uživatele.

Vzhledem k tomu, že prostředí PowerShell pomáhá ve scénářích automatizace, můžete také nastavit objekt **PSCredential** způsobem, který nevyžaduje zásah uživatele. Nejdřív je potřeba nastavit zabezpečené heslo. Začnete zadáním hesla pro prostý text převedete ho na zabezpečený řetězec pomocí [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Dále je potřeba převést tento zabezpečený řetězec na zašifrovaný standardní řetězec pomocí [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nyní můžete tento zašifrovaný standardní řetězec Uložit do souboru pomocí [Set-content](https://technet.microsoft.com/library/ee176959.aspx).

Můžete také vytvořit zabezpečený soubor hesla, abyste heslo nemuseli zadávat pokaždé. Zabezpečený soubor hesla je také lepší než soubor s prostým textem. Pomocí následujícího prostředí PowerShell vytvořte zabezpečený soubor s heslem:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Při nastavování hesla ověřte, že splňujete [požadavky na složitost](https://technet.microsoft.com/library/cc786468.aspx).

Chcete-li vytvořit objekt přihlašovacích údajů ze souboru zabezpečeného hesla, je nutné číst obsah souboru a převést je zpět na zabezpečený řetězec pomocí [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Rutina [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) také přijímá parametr *vypršení platnosti* , který určuje **Datum a čas** , kdy platnost uživatelského účtu vyprší. Můžete například nastavit, aby platnost účtu vyprší několik dní od aktuálního data a času.

V tomto příkladu PowerShellu se dozvíte, jak nastavit rozšíření vzdálené plochy v cloudové službě:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Volitelně můžete také zadat slot nasazení a role, na kterých chcete povolit vzdálenou plochu. Nejsou-li tyto parametry zadány, rutina povolí vzdálenou plochu na všech rolích v **produkčním** slotu nasazení.

Rozšíření vzdálené plochy je přidruženo k nasazení. Pokud vytvoříte nové nasazení pro službu, musíte na tomto nasazení povolit vzdálenou plochu. Pokud chcete mít vždycky zapnutou vzdálenou plochu, měli byste zvážit integraci skriptů PowerShellu do pracovního postupu nasazení.

## <a name="remote-desktop-into-a-role-instance"></a>Vzdálená plocha do instance role

Rutina [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile?view=azuresmps-3.7.0) se používá ke vzdálené ploše do konkrétní instance role vaší cloudové služby. K místnímu stažení souboru RDP můžete použít parametr *LocalPath* . Případně můžete použít parametr *Spustit* k přímému spuštění připojení ke vzdálené ploše dialogového okna pro přístup k instanci role cloudové služby.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Zjistit, jestli je u služby povolené rozšíření vzdálené plochy

Pomocí rutiny [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile?view=azuresmps-3.7.0) se zobrazí, že je vzdálená plocha v nasazení služby povolená nebo zakázaná. Rutina vrátí uživatelské jméno pro uživatele vzdálené plochy a role, pro které je povolena přípona vzdálené plochy. Ve výchozím nastavení se k tomu dochází na slotu pro nasazení a místo toho se můžete rozhodnout pro použití přípravného slotu.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Odebrání rozšíření vzdálené plochy ze služby

Pokud jste již v nasazení povolili rozšíření vzdálené plochy a potřebujete aktualizovat nastavení vzdálené plochy, nejprve odeberte rozšíření. A znovu ji povolte s novým nastavením. Například pokud chcete nastavit nové heslo pro vzdálený uživatelský účet nebo platnost účtu vypršela. To se vyžaduje u existujících nasazení, která mají povolené rozšíření vzdálené plochy. Pro nová nasazení můžete jednoduše použít rozšíření přímo.

Pokud chcete z nasazení odebrat rozšíření vzdálené plochy, můžete použít rutinu [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) . Volitelně můžete také zadat slot nasazení a roli, ze které chcete odebrat rozšíření vzdálené plochy.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Pro úplné odebrání konfigurace rozšíření byste měli zavolat rutinu *Remove* s parametrem **UninstallConfiguration** .
>
> Parametr **UninstallConfiguration** odinstaluje veškerou konfiguraci rozšíření, která se pro službu používá. Každá konfigurace rozšíření je přidružená ke konfiguraci služby. Volání rutiny *Remove* bez **UninstallConfiguration** zruší přidružení <mark>nasazení</mark> od konfigurace rozšíření, čímž se toto rozšíření efektivně odebírá. Konfigurace rozšíření ale zůstane přidružená ke službě.

## <a name="additional-resources"></a>Další zdroje

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)



---
title: Použití PowerShellu k povolení vzdálené plochy pro roli
description: Konfigurace aplikace cloudové služby Azure pomocí prostředí PowerShell pro povolení připojení ke vzdálené ploše
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5b1650edb575de8fd59ad2495dafcd628a717c02
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610395"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>Povolení Připojení ke vzdálené ploše pro roli v Azure Cloud Services (Classic) pomocí PowerShellu

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení potíží a diagnostikování problémů s aplikací v době, kdy je spuštěná.

Tento článek popisuje, jak povolit vzdálenou plochu na vašich rolích cloudových služeb pomocí PowerShellu. Potřebné předpoklady pro tento článek najdete v tématu [Postup instalace a konfigurace Azure PowerShell](/powershell/azure/) . Prostředí PowerShell využívá rozšíření vzdálené plochy, aby bylo možné povolit vzdálenou plochu po nasazení aplikace.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurace vzdálené plochy z PowerShellu
Rutina [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) umožňuje povolit vzdálenou plochu na zadaných rolích nebo na všech rolích nasazení cloudové služby. Rutina umožňuje zadat uživatelské jméno a heslo pro uživatele vzdálené plochy prostřednictvím parametru *přihlašovacích údajů* , který přijímá objekt PSCredential.

Pokud používáte prostředí PowerShell interaktivně, můžete snadno nastavit objekt PSCredential voláním rutiny [Get-Credentials](/powershell/module/microsoft.powershell.security/get-credential) .

```powershell
$remoteusercredentials = Get-Credential
```

Tento příkaz zobrazí dialogové okno, které vám umožní bezpečným způsobem zadat uživatelské jméno a heslo pro vzdáleného uživatele.

Vzhledem k tomu, že prostředí PowerShell pomáhá ve scénářích automatizace, můžete také nastavit objekt **PSCredential** způsobem, který nevyžaduje zásah uživatele. Nejdřív je potřeba nastavit zabezpečené heslo. Začnete zadáním hesla pro prostý text převedete ho na zabezpečený řetězec pomocí [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). Dále je potřeba převést tento zabezpečený řetězec na zašifrovaný standardní řetězec pomocí [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring). Nyní můžete tento zašifrovaný standardní řetězec Uložit do souboru pomocí [Set-content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10)).

Můžete také vytvořit zabezpečený soubor hesla, abyste heslo nemuseli zadávat pokaždé. Zabezpečený soubor hesla je také lepší než soubor s prostým textem. Pomocí následujícího prostředí PowerShell vytvořte zabezpečený soubor s heslem:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Při nastavování hesla ověřte, že splňujete [požadavky na složitost](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10)).

Chcete-li vytvořit objekt přihlašovacích údajů ze souboru zabezpečeného hesla, je nutné číst obsah souboru a převést je zpět na zabezpečený řetězec pomocí [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

Rutina [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) také přijímá parametr *vypršení platnosti* , který určuje **Datum a čas** , kdy platnost uživatelského účtu vyprší. Můžete například nastavit, aby platnost účtu vyprší několik dní od aktuálního data a času.

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

Rutina [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) se používá ke vzdálené ploše do konkrétní instance role vaší cloudové služby. K místnímu stažení souboru RDP můžete použít parametr *LocalPath* . Případně můžete použít parametr *Spustit* k přímému spuštění připojení ke vzdálené ploše dialogového okna pro přístup k instanci role cloudové služby.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Zjistit, jestli je u služby povolené rozšíření vzdálené plochy

Pomocí rutiny [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) se zobrazí, že je vzdálená plocha v nasazení služby povolená nebo zakázaná. Rutina vrátí uživatelské jméno pro uživatele vzdálené plochy a role, pro které je povolena přípona vzdálené plochy. Ve výchozím nastavení se k tomu dochází na slotu pro nasazení a místo toho se můžete rozhodnout pro použití přípravného slotu.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Odebrání rozšíření vzdálené plochy ze služby

Pokud jste již v nasazení povolili rozšíření vzdálené plochy a potřebujete aktualizovat nastavení vzdálené plochy, nejprve odeberte rozšíření. A znovu ji povolte s novým nastavením. Například pokud chcete nastavit nové heslo pro vzdálený uživatelský účet nebo platnost účtu vypršela. To se vyžaduje u existujících nasazení, která mají povolené rozšíření vzdálené plochy. Pro nová nasazení můžete jednoduše použít rozšíření přímo.

Pokud chcete z nasazení odebrat rozšíření vzdálené plochy, můžete použít rutinu [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) . Volitelně můžete také zadat slot nasazení a roli, ze které chcete odebrat rozšíření vzdálené plochy.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Pro úplné odebrání konfigurace rozšíření byste měli zavolat rutinu *Remove* s parametrem **UninstallConfiguration** .
>
> Parametr **UninstallConfiguration** odinstaluje veškerou konfiguraci rozšíření, která se pro službu používá. Každá konfigurace rozšíření je přidružená ke konfiguraci služby. Volání rutiny *Remove* bez **UninstallConfiguration** zruší přidružení <mark>nasazení</mark> od konfigurace rozšíření, čímž se toto rozšíření efektivně odebírá. Konfigurace rozšíření ale zůstane přidružená ke službě.

## <a name="additional-resources"></a>Další zdroje informací

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
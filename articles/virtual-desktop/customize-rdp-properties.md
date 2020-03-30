---
title: Přizpůsobení vlastností protokolu RDP pomocí PowerShellu – Azure
description: Jak přizpůsobit vlastnosti protokolu RDP pro virtuální plochu windows pomocí rutin prostředí PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128060"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokolu Vzdálené plochy pro fond hostitelů

Přizpůsobení vlastností protokolu RDP (RdP) hostitelského fondu, jako je prostředí s více monitory a přesměrování zvuku, umožňuje uživatelům poskytovat optimální prostředí na základě jejich potřeb. Vlastnosti protokolu RDP ve virtuální ploše systému Windows můžete přizpůsobit pomocí parametru **-CustomRdpProperty** v rutině **Set-RdsHostPool.**

Úplný seznam podporovaných vlastností a jejich výchozích hodnot naleznete v [části Podporovaná nastavení souborů PROTOKOLU RDP.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)

Nejprve [si stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili. Poté spusťte následující rutinu a přihlaste se ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Výchozí vlastnosti protokolu RDP

Ve výchozím nastavení obsahují publikované soubory RDP následující vlastnosti:

|Vlastnosti protokolu RDP | Desktops | RemoteApps |
|---|---| --- |
| Režim více monitorů | Povoleno | Není dostupné. |
| Přesměrování jednotek povolena | Jednotky, schránka, tiskárny, porty COM, zařízení USB a čipové karty| Jednotky, schránky a tiskárny |
| Režim vzdáleného zvuku | Hrát lokálně | Hrát lokálně |

Všechny vlastní vlastnosti, které definujete pro fond hostitelů, přepíší tyto výchozí hodnoty.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Přidání nebo úprava jedné vlastní vlastnosti RDP

Chcete-li přidat nebo upravit jednu vlastní vlastnost RDP, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Snímek obrazovky rutiny prostředí PowerShell Get-RDSRemoteApp se zvýrazněným názvem a popisným názvem.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Přidání nebo úprava více vlastních vlastností protokolu RDP

Chcete-li přidat nebo upravit více vlastních vlastností protokolu RDP, spusťte následující rutiny prostředí PowerShell poskytnutím vlastních vlastností PROTOKOLU RDP jako řetězec oddělený středníkem:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Snímek obrazovky rutiny prostředí PowerShell Get-RDSRemoteApp se zvýrazněným názvem a popisným názvem.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Obnovit všechny vlastní vlastnosti PROTOKOLU RDP

Jednotlivé vlastní vlastnosti protokolu RDP můžete obnovit na výchozí hodnoty podle pokynů v části [Přidání nebo úprava jedné vlastní vlastnosti protokolu RDP](#add-or-edit-a-single-custom-rdp-property)nebo můžete obnovit všechny vlastní vlastnosti protokolu RDP pro fond hostitelů spuštěním následující rutiny prostředí PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Snímek obrazovky rutiny prostředí PowerShell Get-RDSRemoteApp se zvýrazněným názvem a popisným názvem.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Další kroky

Nyní, když jste přizpůsobili vlastnosti protokolu RDP pro daný fond hostitelů, můžete se přihlásit ke klientovi virtuální plochy systému Windows a otestovat je jako součást relace uživatele. Tyto další dva způsoby vám řeknou, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení s desktopovým klientem Windows](connect-windows-7-and-10.md)
- [Připojení k webovému klientovi](connect-web.md)

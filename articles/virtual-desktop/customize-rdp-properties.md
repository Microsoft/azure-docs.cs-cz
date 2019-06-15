---
title: Přizpůsobení vlastností RDP pomocí Powershellu – Azure
description: Jak přizpůsobit RDP vlastnosti pro Windows virtuální plochy pomocí rutin prostředí PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082648"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení Remote Desktop Protocol vlastností pro hostitele fondu

Úpravy vlastností hostitele fondu protokolu RDP (Remote Desktop), jako je například prostředí více monitorů a přesměrování zvuku, umožňuje poskytovat optimální výkon pro vaše uživatele na základě svých potřeb. Můžete upravit vlastnosti protokolu RDP s použitím virtuální plochy Windows **- CustomRdpProperty** parametr **Set-RdsHostPool** rutiny.

Zobrazit [nastavení souboru protokolu RDP Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) úplný seznam podporovaných vlastností a jejich výchozí hodnoty.

Nejprve je potřeba [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Přidat nebo upravit jednu vlastní vlastnost protokolu RDP

Pokud chcete přidat nebo upravit jednu vlastní vlastnost RDP, spusťte následující rutiny Powershellu:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Snímek obrazovky rutiny Powershellu Get-RDSRemoteApp s názvem a FriendlyName zvýrazní.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Přidat nebo upravit více vlastních vlastností protokolu RDP

Přidat nebo upravit více vlastních vlastností RDP, spusťte následující rutiny prostředí PowerShell zadáním vlastních vlastností RDP jako řetězec se středníkem oddělený:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Snímek obrazovky rutiny Powershellu Get-RDSRemoteApp s názvem a FriendlyName zvýrazní.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Resetovat všechny vlastní vlastnosti protokolu RDP

Jednotlivé vlastnosti vlastního protokolu RDP můžete resetovat na výchozí hodnoty podle pokynů v [přidat nebo upravit jednu vlastní vlastnost RDP](#add-or-edit-a-single-custom-rdp-property), nebo všechny vlastní vlastnosti protokolu RDP pro fond hostitele můžete obnovit spuštěním následujícího Rutiny Powershellu:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Snímek obrazovky rutiny Powershellu Get-RDSRemoteApp s názvem a FriendlyName zvýrazní.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Další postup

Teď, když jste přizpůsobili vlastnosti protokolu RDP pro daného hostitele fondu, můžete přihlásit ke klientovi virtuální plochy Windows k testování je v rámci relace uživatele. Uděláte to tak, i nadále připojit k virtuální ploše postupy Windows:

- [Připojení z Windows 10 a Windows 7](connect-windows-7-and-10.md)
- [Připojte se z webového prohlížeče](connect-web.md)

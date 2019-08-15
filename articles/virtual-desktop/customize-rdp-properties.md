---
title: Přizpůsobení vlastností protokolu RDP pomocí prostředí PowerShell – Azure
description: Postup přizpůsobení vlastností protokolu RDP pro virtuální počítače s Windows pomocí rutin prostředí PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: helohr
ms.openlocfilehash: 624edaea9a0fb56e34eb83f033dfdab64985bd5c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950707"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů

Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) fondu hostitelů, jako je například prostředí pro více monitorů a přesměrování zvuku, umožňuje poskytovat optimální prostředí pro uživatele podle svých potřeb. Vlastnosti protokolu RDP můžete přizpůsobit ve virtuální ploše Windows pomocí parametru **-CustomRdpProperty** v rutině **set-RdsHostPool** .

Úplný seznam podporovaných vlastností a jejich výchozích hodnot najdete v tématu [nastavení souboru RDP vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) .

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Přidat nebo upravit jednu vlastní vlastnost RDP

Pokud chcete přidat nebo upravit jednu vlastní vlastnost RDP, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Přidat nebo upravit více vlastních vlastností protokolu RDP

Chcete-li přidat nebo upravit více vlastních vlastností protokolu RDP, spusťte následující rutiny prostředí PowerShell zadáním vlastních vlastností protokolu RDP jako řetězce odděleného středníkem:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Resetovat všechny vlastní vlastnosti protokolu RDP

Jednotlivé vlastní vlastnosti protokolu RDP můžete obnovit na výchozí hodnoty podle pokynů v tématu [Přidání nebo úprava jedné vlastní vlastnosti protokolu RDP](#add-or-edit-a-single-custom-rdp-property), nebo můžete obnovit všechny vlastní vlastnosti protokolu RDP pro fond hostitelů spuštěním následující rutiny prostředí PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Další postup

Teď, když jste přizpůsobili vlastnosti protokolu RDP pro daný fond hostitelů, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat je jako součást uživatelské relace. Provedete to tak, že přejdete do části s postupy pro připojení k virtuálnímu počítači s Windows:

- [Připojení ze systému Windows 10 a Windows 7](connect-windows-7-and-10.md)
- [Připojení z webového prohlížeče](connect-web.md)

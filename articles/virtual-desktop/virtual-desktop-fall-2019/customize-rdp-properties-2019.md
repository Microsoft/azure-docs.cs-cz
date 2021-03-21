---
title: Přizpůsobení vlastností protokolu RDP pomocí virtuálního počítače s Windows PowerShellu (Classic) – Azure
description: Postup přizpůsobení vlastností protokolu RDP pro virtuální plochu Windows (Classic) pomocí rutin prostředí PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95020704"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) fondu hostitelů pro virtuální počítače s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../customize-rdp-properties.md).

Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) fondu hostitelů, jako je například prostředí pro více monitorů a přesměrování zvuku, umožňuje poskytovat optimální prostředí pro uživatele podle svých potřeb. Vlastnosti protokolu RDP můžete přizpůsobit ve virtuální ploše Windows pomocí parametru **-CustomRdpProperty** v rutině **set-RdsHostPool** .

Úplný seznam podporovaných vlastností a jejich výchozích hodnot najdete v tématu [podporované nastavení souboru RDP](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) .

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Výchozí vlastnosti protokolu RDP

Publikované soubory RDP ve výchozím nastavení obsahují následující vlastnosti:

|Vlastnosti protokolu RDP | Desktops | Vzdálené aplikace RemoteApp |
|---|---| --- |
| Režim více monitorů | Povoleno | – |
| Přesměrování jednotky povolena | Jednotky, schránka, tiskárny, porty COM, zařízení USB a čipové karty| Jednotky, schránka a tiskárny |
| Režim vzdáleného zvuku | Přehrát místně | Přehrát místně |

Tato výchozí nastavení se přepíšou všemi vlastními vlastnostmi, které definujete pro fond hostitelů.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Přidat nebo upravit jednu vlastní vlastnost RDP

Pokud chcete přidat nebo upravit jednu vlastní vlastnost RDP, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s rutinou prostředí PowerShell Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName pro úpravu vlastní vlastnosti R D P](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Přidat nebo upravit více vlastních vlastností protokolu RDP

Chcete-li přidat nebo upravit více vlastních vlastností protokolu RDP, spusťte následující rutiny prostředí PowerShell zadáním vlastních vlastností protokolu RDP jako řetězce odděleného středníkem:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s rutinou prostředí PowerShell Set-RDSRemoteApp se zvýrazněným názvem a FriendlyName pro úpravu vlastní vlastnosti R D P](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Resetovat všechny vlastní vlastnosti protokolu RDP

Jednotlivé vlastní vlastnosti protokolu RDP můžete obnovit na výchozí hodnoty podle pokynů v tématu [Přidání nebo úprava jedné vlastní vlastnosti protokolu RDP](#add-or-edit-a-single-custom-rdp-property), nebo můžete obnovit všechny vlastní vlastnosti protokolu RDP pro fond hostitelů spuštěním následující rutiny prostředí PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s rutinou prostředí PowerShell Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste přizpůsobili vlastnosti protokolu RDP pro daný fond hostitelů, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat je jako součást uživatelské relace. Tyto další dva postupy se dozvíte, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení s desktopovým klientem Windows](connect-windows-7-10-2019.md)
- [Připojení k webovému klientovi](connect-web-2019.md)
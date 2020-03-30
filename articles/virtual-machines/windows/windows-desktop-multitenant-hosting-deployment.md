---
title: Jak nasadit Windows 10 do Azure s právy k hostování ve více tenantských zařízeních
description: Zjistěte, jak maximalizovat výhody windows software assurance a přenést místní licence do Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 9ff8cc64266375a2d439763b222870843136f67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101494"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Jak nasadit Windows 10 do Azure s právy k hostování ve více tenantských zařízeních 
Pro zákazníky s Windows 10 Enterprise E3/E5 na uživatele nebo Windows Virtual Desktop Access na uživatele (licence k předplatnému uživatele nebo licence k předplatnému pro uživatele doplňku) vám práva k hostování ve více klientech pro Windows 10 umožňují přenést licence windows 10 do cloudu a provozovat virtuální počítače windows 10 v Azure, aniž byste museli platit za jinou licenci. Další informace najdete v [tématu Víceklientský hosting pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Tento článek ukazuje, že můžete implementovat výhody licencování pro ibi Plochy Windows 10 Pro na Azure Marketplace.
> - Pro Windows 7, 8.1, 10 Enterprise (x64) image na Azure Marketplace pro předplatná MSDN, naleznete v [klientovi Windows v Azure pro vývoj a testování scénářů](client-images.md)
> - Výhody licencování windows serveru najdete v části [Výhody hybridního využití Azure pro bitové kopie Windows Serveru](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Nasazení image Windows 10 z Azure Marketplace 
Pro nasazení šablon Powershellu, CLI a Azure Resource Manageru najdete bitovou kopii Windows 10 s následujícím názvem vydavatele, nabídkou a sku.

| Operační systém  |      Název vydavatele      |  Nabídka | Skladová jednotka (SKU) |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Nahrání virtuálního pevného disku Windows 10 do Azure
Pokud nahráváte generalizovaný virtuální disk Windows 10, vezměte prosím na vědomí, že systém Windows 10 nemá ve výchozím nastavení povolený vestavěný účet správce. Chcete-li povolit předdefinovaný účet správce, zahrňte jako součást rozšíření Vlastní skript následující příkaz.

```powershell
Net user <username> /active:yes
```

Následující fragment prostředí powershellu označuje všechny účty správce jako aktivní, včetně integrovaného správce. Tento příklad je užitečný, pokud není známo předdefinované uživatelské jméno správce.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Další informace najdete tady: 
* [Jak nahrát Virtuální pevný disk do Azure](upload-generalized-managed.md)
* [Jak připravit virtuální pevný disk Windows k nahrání do Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Nasazení Windows 10 s víceklientským hostingovým právem
Ujistěte se, že jste [nainstalovali a nakonfigurovali nejnovější Azure PowerShell](/powershell/azure/overview). Po přípravě virtuálního pevného disku nahrajte virtuální pevný `Add-AzVhd` disk do účtu služby Azure Storage pomocí rutiny následujícím způsobem:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Nasazení pomocí nasazení šablony Správce prostředků Azure** V šablonách Správce prostředků lze `licenseType` zadat další parametr pro. Další informace o [vytváření šablon Azure Resource Manageru](../../resource-group-authoring-templates.md)najdete v článku . Až si nahrajete virtuální pevný disk do Azure, upravte šablonu Správce prostředků tak, aby zahrnovala typ licence jako součást poskytovatele výpočetních prostředků, a nasadit šablonu jako obvykle:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Nasazení přes PowerShell** Při nasazování virtuálního počítače se systémem Windows Server `-LicenseType`prostřednictvím prostředí PowerShell máte další parametr pro aplikaci . Po nahrání virtuálního pevného disku do Azure `New-AzVM` vytvoříte virtuální počítač pomocí a zadejte typ licencování následujícím způsobem:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ověřte, že váš virtuální počítač využívá výhody licencování
Po nasazení virtuálního počítače pomocí metody nasazení PowerShellu nebo Správce prostředků `Get-AzVM` ověřte typ licence následujícím způsobem:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup je podobný následujícímu příkladu pro Windows 10 se správným typem licence:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Tento výstup kontrastuje s následujícím virtuálním počítačem nasazeným bez licencování výhod hybridního využití Azure, jako je například virtuální počítač nasazený přímo z Galerie Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Další informace o připojení k Azure AD
>[!NOTE]
>Azure zřdí všechny virtuální počítače s Windows s předdefinovaným účtem správce, který nejde použít k připojení k AAD. Například *Nastavení > účtu > přístupu k práci nebo > +Připojení* nebude fungovat. Musíte vytvořit a přihlásit se jako druhý účet správce, abyste se k Azure AD připojili ručně. Můžete také nakonfigurovat Azure AD pomocí zřizovacíbalíček, použijte odkaz je *další kroky.*
>
>

## <a name="next-steps"></a>Další kroky
- Další informace o [konfiguraci VDA pro Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Další informace o [víceklientských hostingech pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)



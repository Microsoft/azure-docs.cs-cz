---
title: Jak nasadit Windows 10 v Azure s právy pro hostování s více klienty
description: Přečtěte si, jak maximalizovat výhody Software Assurance v systému Windows, aby se zajistilo poskytování místních licencí do Azure s právy pro hostování s více klienty.
author: xujing
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: ceb8b8b31963317ccbbd1aee9f1b2606afc5a5db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91279021"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Jak nasadit Windows 10 v Azure s právy pro hostování s více klienty 
Pro zákazníky s Windows 10 Enterprise E3/E5 podle uživatele nebo přístup k virtuálnímu počítači s Windows na úrovni uživatele (licence pro předplatné uživatele nebo licence k předplatnému uživatele) umožňuje víceklientské hostování pro Windows 10 přístup k cloudu a spouštění Windows 10 Virtual Machines v Azure bez placení na jinou licenci. Další informace najdete v tématu hostování s více [klienty pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Tento článek vás seznámí s implementací výhod licencování pro desktopové image Windows 10 pro na Azure Marketplace.
> - V případě [scénářů pro vývoj a testování](client-images.md) pro Windows 7, 8,1, 10 Enterprise (x64) imagí na Azure Marketplace pro Předplatná MSDN se podívejte na klienta Windows v Azure.
> - Výhody licencování Windows serveru najdete v tématu [výhody hybridního použití Azure pro image Windows serveru](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Nasazení image Windows 10 z Azure Marketplace 
Pro prostředí PowerShell, rozhraní příkazového řádku a Azure Resource Manager nasazení šablon můžete najít image Windows 10 s následujícím vydavatelem, nabídkou, SKU.

| Operační systém  |      Název vydavatele      |  Nabídka | Skladová jednotka (SKU) |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Nahrání virtuálního pevného disku s Windows 10 do Azure
Pokud nahráváte zobecněný virtuální pevný disk s Windows 10, pamatujte na to, že ve výchozím nastavení není ve Windows 10 povolený vestavěný účet správce. Pokud chcete povolit integrovaný účet správce, zahrňte jako součást rozšíření vlastních skriptů následující příkaz.

```powershell
Net user <username> /active:yes
```

Následujícím fragmentem kódu prostředí PowerShell označíte všechny účty správců jako aktivní, včetně předdefinovaného správce. Tento příklad je užitečný v případě, že předdefinované uživatelské jméno správce není známé.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Další informace najdete tady: 
* [Postup nahrání virtuálního pevného disku do Azure](upload-generalized-managed.md)
* [Příprava virtuálního pevného disku s Windows pro nahrání do Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Nasazení Windows 10 s právy pro hostování s více klienty
Ujistěte se, že máte [nainstalovanou a nakonfigurovanou nejnovější Azure PowerShell](/powershell/azure/). Po přípravě virtuálního pevného disku nahrajte virtuální pevný disk na účet Azure Storage pomocí `Add-AzVhd` rutiny následujícím způsobem:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


Nasazení **pomocí Azure Resource Managerho nasazení šablony** V rámci šablon Správce prostředků lze zadat další parametr pro `licenseType` . Můžete si přečíst další informace o [vytváření šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Po nahrání virtuálního pevného disku do Azure upravte Správce prostředků šablonu tak, aby obsahovala typ licence jako součást zprostředkovatele výpočetní služby, a šablonu nasaďte jako normální:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Nasazení prostřednictvím PowerShellu** Při nasazení virtuálního počítače s Windows serverem prostřednictvím PowerShellu máte další parametr pro `-LicenseType` . Po nahrání virtuálního pevného disku do Azure vytvoříte virtuální počítač pomocí příkazu `New-AzVM` a zadáte typ licencování následujícím způsobem:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ověřte, že váš virtuální počítač využívá výhod licencování.
Po nasazení virtuálního počítače pomocí metody nasazení PowerShellu nebo Správce prostředků Ověřte typ licence následujícím `Get-AzVM` způsobem:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup se podobá následujícímu příkladu pro Windows 10 se správným typem licence:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Tento výstup se liší od tohoto virtuálního počítače nasazeného bez programu Azure Hybrid Use zvýhodněné licencování, jako je například virtuální počítač nasazený přímo z Galerie Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Další informace o připojení k Azure AD
>[!NOTE]
>Azure zřídí všechny virtuální počítače s Windows s předdefinovaným účtem správce, který se nedá použít k připojení AAD. Například *nastavení > účet > přístup do práce nebo do školy > + připojení* nebude fungovat. Abyste mohli službu Azure AD připojit ručně, musíte vytvořit a přihlásit se jako druhý účet správce. Službu Azure AD můžete nakonfigurovat také pomocí zřizovacího balíčku. Další informace najdete v části *Další kroky* .
>
>

## <a name="next-steps"></a>Další kroky
- Další informace o [konfiguraci VDA pro Windows 10](/windows/deployment/vda-subscription-activation)
- Další informace o [víceklientské hostování pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)

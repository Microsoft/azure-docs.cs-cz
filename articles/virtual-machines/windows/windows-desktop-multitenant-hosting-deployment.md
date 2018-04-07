---
title: Postup nasazení Windows 10 v Azure s oprávnění víceklientské hostování
description: Zjistěte, jak chcete maximalizovat své výhody Windows Software Assurance a dovést místní licencí do Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 5952c602a90568a9ce9e71dfa2c0dd383aed4e16
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Postup nasazení Windows 10 v Azure s oprávnění víceklientské hostování 
Víceklientská hostování práva pro Windows 10 pro zákazníky s Windows 10 Enterprise E3/E5 za Windows virtuální plochy přístup uživatele nebo na uživatele (licence předplatného uživatele nebo licence předplatného rozšíření uživatele), umožňuje uvést vaše licence Windows 10 do cloudu a spouštět virtuální počítače s Windows 10 v Azure bez placení pro jiné licence. Další informace najdete v tématu [víceklientské hostování pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Tento článek ukazuje implementaci licencování benefit pro Windows 10 Pro Desktop obrázky v Azure Marketplace.
> - Windows 7, 8.1, 10 Enterprise (x64) imagí v Azure Marketplace pro předplatné MSDN, naleznete v [klienta systému Windows v Azure pro scénáře vývoje/testování](client-images.md)
> - Licencování výhody serveru Windows, naleznete v [hybridní Azure používat výhody pro bitové kopie systému Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Nasazení bitové kopie systému Windows 10 z Azure Marketplace 
Pro prostředí Powershell, rozhraní příkazového řádku a Azure Resource Manager šablony nasazení image Windows 10 najdete s následující publishername, nabídky, sku.

| Operační systém  |      Název vydavatele      |  Nabídka | Skladová jednotka (SKU) |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2 ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Odesílání Windows 10 virtuálního pevného disku do Azure
Pokud nahráváte virtuální pevný disk zobecněný Windows 10, uvědomte si, že Windows 10 nemá předdefinovaný účet správce ve výchozím nastavení povolené. Pokud chcete povolit předdefinovaný účet správce, zahrnují následující příkaz jako součást rozšíření vlastních skriptů.

```powershell
Net user <username> /active:yes
```

Následující fragment kódu prostředí powershell je označte všechny účty správce jako aktivní, včetně předdefinovaného účtu administrator. V tomto příkladu je užitečné, pokud uživatelské jméno předdefinovaný účet správce neznámý.
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
* [Tom, jak připravit virtuální pevný disk Windows nahrát do Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Nasazení systému Windows 10 pomocí oprávnění víceklientské hostování
Zajistěte, aby byla [nainstalovaný a nakonfigurovaný nejnovější prostředí Azure PowerShell](/powershell/azure/overview). Jakmile připravíte svůj disk VHD, odeslání disku VHD do vašeho účtu úložiště Azure pomocí `Add-AzureRmVhd` rutiny následujícím způsobem:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Nasazení pomocí Azure Resource Manager šablony nasazení** v rámci šablony Resource Manageru, dalším parametr pro `licenseType` lze zadat. Další informace o [vytváření šablon Azure Resource Manager](../../resource-group-authoring-templates.md). Až budete mít svůj disk VHD nahrán do Azure, upravte šablony Resource Manageru zahrnout typ licence jako součást poskytovatele výpočetních a nasazení vaší šablony jako za normálních okolností:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**Nasazení pomocí prostředí PowerShell** při nasazování virtuálního počítače Windows serveru pomocí prostředí PowerShell, máte další parametr pro `-LicenseType`. Až budete mít svůj disk VHD nahrán do Azure, vytvoříte virtuální počítač pomocí `New-AzureRmVM` a zadejte typ licencování následujícím způsobem:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ověřte, zda že je virtuální počítač využívá licencování benefit
Po nasazení virtuálního počítače prostřednictvím buď prostředí PowerShell nebo správce prostředků metodu nasazení ověřte typ licence s `Get-AzureRmVM` následujícím způsobem:
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup bude vypadat v následujícím příkladu pro Windows 10 s typem správnou licenci:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Tento výstup uvádí vedle sebe s následující virtuální počítač nasadit bez licencování Azure hybridní použití výhody, jako je například virtuální počítač nasadit přímo z Galerie Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Další informace o připojení Azure AD
>[!NOTE]
>Azure poskytuje všechny virtuální počítače Windows se předdefinovaný účet správce, který nelze použít pro připojení AAD. Například *Nastavení > účet > přístup k práci nebo škole > + Připojit* nebude fungovat. Musíte vytvořit a přihlaste se jako druhého správce účtu pro připojení služby Azure AD ručně. Můžete také konfigurovat Azure AD pomocí balíčku pro zřizování, použijte tento odkaz *další kroky* části Další informace.
>
>

## <a name="next-steps"></a>Další kroky
- Další informace o [konfigurace VDA pro Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Další informace o [víceklientské hostování pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)



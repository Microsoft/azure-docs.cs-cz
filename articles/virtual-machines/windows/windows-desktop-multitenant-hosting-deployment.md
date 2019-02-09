---
title: Jak nasadit Windows 10 v Azure s práv hostování více klientů
description: Zjistěte, jak maximalizovat své výhody programu Software Assurance pro Windows a využít místní licence do Azure
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
ms.openlocfilehash: dc798dc78ed0cdbf11bbe3bc2dd805433b127a4d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976910"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Jak nasadit Windows 10 v Azure s práv hostování více klientů 
Víceklientská hostování práva pro Windows 10 pro zákazníky s Windows 10 Enterprise E3 nebo E5 na Windows Virtual Desktop přístup uživatele nebo na uživatele (licence uživatelského předplatného nebo licence uživatelského předplatného doplněk), umožňuje přenést vaše licence na Windows 10 do cloudu a spouštět virtuální počítače s Windows 10 v Azure bez nutnosti platit za další licenci. Další informace najdete v tématu [Multitenant hostování pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Tento článek ukazuje implementaci výhodu licencování naplno pro Windows 10 Pro plochu Image na webu Azure Marketplace.
> - Windows 7, 8.1, 10 Enterprise (x64) imagí na webu Azure Marketplace pro předplatná MSDN najdete [klienta Windows v Azure pro scénáře vývoje/testování](client-images.md)
> - Výhody licencí Windows serveru, najdete [hybridní Azure využít výhody pro Image Windows serveru](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Nasazení Image Windows 10 z webu Azure Marketplace 
Pro nasazení šablony prostředí Powershell, rozhraní příkazového řádku a Azure Resource Manageru image Windows 10 najdete následující název_vydavatele, nabídky, skladové položky.

| Operační systém  |      Název vydavatele      |  Nabídka | Skladová jednotka (SKU) |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Nahrává se Windows 10 virtuálního pevného disku do Azure
Pokud nahráváte virtuální pevný disk zobecněný Windows 10, nezapomeňte prosím, že Windows 10 nemá předdefinovaný účet správce ve výchozím nastavení povolená. Chcete-li předdefinovaný účet správce, zahrnují následující příkaz jako součást rozšíření vlastních skriptů.

```powershell
Net user <username> /active:yes
```

Následující fragment kódu powershellu je označit všechny účty správců jako aktivní, včetně předdefinovaný účet administrator. V tomto příkladu je užitečné, pokud uživatelské jméno předdefinovaného účtu administrator neznámý.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Další informace najdete tady: 
* [Jak nahrát virtuální pevný disk do Azure](upload-generalized-managed.md)
* [Příprava virtuálního pevného disku Windows k nahrání do Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Nasazení systému Windows 10 pomocí víceklientské práv hostování
Ujistěte se, že máte [nainstalovanou a nakonfigurovanou nejnovější Azure PowerShell](/powershell/azure/overview). Jakmile připravíte virtuální pevný disk nahrát virtuální pevný disk ke svému účtu Azure Storage pomocí `Add-AzVhd` rutiny následujícím způsobem:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Nasazení pomocí Azure Resource Manageru šablony nasazení** v rámci své šablony Resource Manageru, další parametr pro `licenseType` lze zadat. Další informace o [Tvorba šablon Azure Resource Manageru](../../resource-group-authoring-templates.md). Jakmile budete mít virtuální pevný disk nahrát do Azure, upravte šablonu Resource Manageru, které chcete zahrnout jako součást poskytovatele výpočetních typ licence a nasazení vaší šablony jako za normálních okolností:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**Nasazení přes PowerShell** po nasazení virtuálního počítače s Windows serverem pomocí Powershellu, budete mít další parametr pro `-LicenseType`. Jakmile budete mít virtuální pevný disk nahrát do Azure, můžete vytvořit virtuální počítač pomocí `New-AzVM` a zadejte licencování následujícím způsobem:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Ověřte, že váš virtuální počítač využívá výhodu licencování naplno
Po nasazení virtuálního počítače prostřednictvím buď Powershellu nebo správce prostředků metodu nasazení ověřte typ licence s `Get-AzVM` následujícím způsobem:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Výstup se podobá následujícímu příkladu pro Windows 10 s typem správné licence:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Tento výstup uvádí vedle sebe s následující virtuální počítač nasadit bez programu Azure Hybrid Use Benefit licencování, jako je například virtuální počítač nasadit přímo z Galerie Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Další informace o připojení k Azure AD
>[!NOTE]
>Azure se zřídí všech virtuálních počítačů s Windows pomocí předdefinovaného účtu správce, který nejde použít k připojení AAD. Například *Nastavení > účet > přístup práce nebo do školy > + Připojit* nebude fungovat. Musíte vytvořit a přihlaste se jako druhý účet správce pro připojení k Azure AD ručně. Můžete také konfigurovat Azure AD pomocí zřizovací balíček, je použití odkazu *další kroky* naleznete další informace.
>
>

## <a name="next-steps"></a>Další kroky
- Další informace o [konfigurace VDA pro Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Další informace o [Multitenant hostování pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)



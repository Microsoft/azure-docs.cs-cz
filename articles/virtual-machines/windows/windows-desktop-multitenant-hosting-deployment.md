---
title: Jak nasadit Windows 10 v Azure s právy pro hostování s více klienty
description: Přečtěte si, jak maximalizovat výhody Software Assurance v systému Windows, aby se zajistilo poskytování místních licencí do Azure s právy pro hostování s více klienty.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: 6e6f6ced1cdba429abd914354a5eba861ab127ec
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670045"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Jak nasadit Windows 10 v Azure s právy pro hostování s více klienty 
Pro zákazníky s Windows 10 Enterprise E3/E5 podle uživatele nebo přístup k virtuálnímu počítači s Windows na úrovni uživatele (licence pro předplatné uživatele nebo licence k předplatnému uživatele) umožňuje víceklientské hostování pro Windows 10 přístup k cloudu a spouštění Windows 10 Virtual Machines v Azure bez placení na jinou licenci. Práva pro hostování více tenantů jsou dostupná jenom pro Windows 10 (verze 1703 nebo novější).

Další informace najdete v tématu [hostování víceklientské architektury pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - Používání imagí Windows 7, 8,1 a 10 pro vývoj nebo testování najdete v tématu [klient Windows v Azure pro scénáře vývoje a testování](client-images.md) .
> - Výhody licencování Windows serveru najdete v tématu [výhody hybridního použití Azure pro image Windows serveru](hybrid-use-benefit-licensing.md).

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Licence pro předplatné, které mají nárok na práva hostování s více klienty

Pomocí [centra pro správu Microsoft](/microsoft-365/admin/admin-overview/about-the-admin-center?preserve-view=true&view=o365-worldwide)můžete ověřit, jestli se uživateli přiřadila podporovaná licence pro Windows 10.

> [!IMPORTANT]
> Aby uživatelé mohli používat image Windows 10 v Azure, musí mít jednu z následujících licencí předplatného. Pokud nemáte jednu z těchto licencí předplatného, můžete si ji koupit prostřednictvím svého [partnera cloudových služeb](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) nebo přímo prostřednictvím [Microsoftu](https://www.microsoft.com/microsoft-365?rtc=1).

**Opravňující licence předplatného:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 a3/a5 
-   Windows 10 Enterprise E3/E5
-   Windows 10 vzdělávání a3/a5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Nasazení image Windows 10 z Azure Marketplace 
Pro prostředí PowerShell, rozhraní příkazového řádku a Azure Resource Manager nasazení šablon Windows 10 je možné najít image s Windows 10 pomocí `PublisherName: MicrosoftWindowsDesktop` a `Offer: Windows-10` . Windows 10 Creators Update (1809) nebo novější se podporuje pro práva hostování více tenantů. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Další informace o dostupných imagí najdete v tématu [vyhledání a použití Azure Marketplace imagí virtuálních počítačů s Azure PowerShell](./cli-ps-findimage.md)

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
Azure zřídí všechny virtuální počítače s Windows s předdefinovaným účtem správce, který se nedá použít k připojení AAD. Například *nastavení > účet > přístup do práce nebo do školy > + připojení* nebude fungovat. Abyste mohli službu Azure AD připojit ručně, musíte vytvořit a přihlásit se jako druhý účet správce. Službu Azure AD můžete nakonfigurovat také pomocí zřizovacího balíčku. Další informace najdete v části *Další kroky* .

## <a name="next-steps"></a>Další kroky
- Další informace o [konfiguraci VDA pro Windows 10](/windows/deployment/vda-subscription-activation)
- Další informace o [víceklientské hostování pro Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
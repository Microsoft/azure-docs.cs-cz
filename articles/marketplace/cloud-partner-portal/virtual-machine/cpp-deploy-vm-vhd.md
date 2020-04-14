---
title: Nasazení virtuálního počítače z virtuálních dispon ů na Azure Marketplace
description: Vysvětluje, jak zaregistrovat virtuální počítač z virtuálního pevného disku nasazeného v Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: f13e4066137e0d76612040d9f6e5ff3d0aa399c8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273896"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Nasazení virtuálního počítače z virtuálních dispon

> [!IMPORTANT]
> dubna 2020 začneme s přesouváním nabídky virtuálních strojů Azure do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření technických prostředků virtuálního počítače Azure](https://aka.ms/AzureVMTechAsset) a spravujte migrované nabídky.

Tato část vysvětluje, jak nasadit virtuální počítač (VM) z virtuálního pevného disku (VHD) nasazeného v Azure.  Obsahuje seznam požadovaných nástrojů a jejich použití k vytvoření image virtuálního počítače uživatele a následné nasazení do Azure pomocí skriptů PowerShellu.

Po nahrání virtuálních pevných disků (VHD) – zobecněného virtuálního pevného disku operačního systému a nulových nebo více virtuálních pevných disků s datovým nebo více datovými disky – do účtu úložiště Azure je můžete zaregistrovat jako image uživatele virtuálního počítače. Pak můžete otestovat ten obrázek. Vzhledem k tomu, že je virtuální pevný disk operačního systému zobecněný, nelze virtuální počítač přímo nasadit poskytnutím adresy URL virtuálního pevného disku.

Další informace o obrázcích virtuálních obrazů najdete v následujících příspěvcích blogu:

- [Image virtuálního virtuálního montova](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell 'Jak'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Předpoklad: instalace potřebných nástrojů

Pokud jste tak ještě neučinili, nainstalujte Azure PowerShell a Azure CLI podle následujících pokynů:

- [Instalace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalace azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Kroky nasazení

K vytvoření a nasazení bitové kopie virtuálního virtuálního uživatele použijete následující kroky:

1. Vytvořte image virtuálního virtuálního uživatele, což znamená zachycení a generalizaci bitové kopie. 
2. Vytvořte certifikáty a uložte je do nového trezoru klíčů Azure. Pro navázání zabezpečeného připojení WinRM k virtuálnímu virtuálnímu soudu je vyžadován certifikát.  K dispozici jsou šablona Azure Resource Manager a skript Azure PowerShell. 
3. Nasazení virtuálního počítače z image uživatele virtuálního počítače pomocí zadané šablony a skriptu.

Po nasazení virtuálního počítače jste připraveni [certifikovat image virtuálního počítače](./cpp-certify-vm.md).

1. Klikněte na **Nový** a **vyhledejte nasazení šablony**a vyberte Vytvořit **vlastní šablonu v editoru**.  <br/>
   ![Vytvoření šablony nasazení virtuálního pevného disku na webu Azure Portal](./media/publishvm_021.png)

1. Zkopírujte a vložte tuto [šablonu JSON](./cpp-deploy-json-template.md) do editoru a klepněte na tlačítko **Uložit**. <br/>
   ![Uložení šablony nasazení virtuálního pevného disku na webu Azure Portal](./media/publishvm_022.png)

1. Zadejte hodnoty parametrů pro zobrazené vlastní stránky vlastností **nasazení.**

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametr**              |   **Popis**                                                            |
   |  -------------              |   ---------------                                                            |
   | Název účtu uživatelského úložiště   | Název účtu úložiště, kde se nachází generalizovaný virtuální pevný disk                    |
   | Název kontejneru úložiště uživatelů | Název kontejneru, kde se nachází generalizovaný virtuální pevný disk                          |
   | Název DNS pro veřejnou IP adresu      | Veřejný název DNS IP. Název DNS je virtuálního počítače, definujete to na webu Azure Portal, jakmile se nabídka nasadí.  |
   | Uživatelské jméno správce             | Uživatelské jméno účtu správce pro nový virtuální virtuální účet                                  |
   | Heslo správce              | Heslo účtu správce pro nový virtuální účet                                  |
   | OS Type                     | Operační systém virtuálního provozu: `Windows` \|`Linux`                                    |
   | ID předplatného             | Identifikátor vybraného předplatného                                      |
   | Umístění                    | Zeměpisná poloha nasazení                                        |
   | Velikost virtuálního počítače                     | [Velikost virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), například`Standard_A2` |
   | Název veřejné IP adresy      | Název vaší veřejné IP adresy                                               |
   | Název virtuálního počítače                     | Název nového virtuálního soudu                                                           |
   | Název virtuální sítě        | Název virtuální sítě používané virtuálním počítačem                                   |
   | Název nepřenosné_nekvi                    | Název karty síťového rozhraní s virtuální sítí               |
   | Adresa URL virtuálního disku                     | Úplná adresa URL virtuálního pevného disku operačního systému                                                     |
   |  |  |
            
1. Po zadání těchto hodnot klepněte na tlačítko **Koupit**. 

Azure začne nasazení: vytvoří nový virtuální počítač se zadaným nespravovaným virtuálním diskem v cestě k účtu úložiště.  Průběh na webu Azure Portal můžete sledovat kliknutím na **virtuální počítače** na levé straně portálu.  Po vytvoření virtuálního virtuálního ms `Starting` se `Running`stav změní z na . 


### <a name="deploy-a-vm-from-powershell"></a>Nasazení virtuálního virtuálního montova z PowerShellu

Chcete-li nasadit velký virtuální virtuální ms z právě vytvořené zobecněné image virtuálního moniku, použijte následující rutiny.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Další kroky

Dále [vytvoříte image virtuálního počítače uživatele](cpp-create-user-image.md) pro vaše řešení.


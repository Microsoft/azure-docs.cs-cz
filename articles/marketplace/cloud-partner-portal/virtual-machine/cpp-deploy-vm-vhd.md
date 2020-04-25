---
title: Nasazení virtuálního počítače z virtuálních pevných disků pro Azure Marketplace
description: Vysvětluje, jak zaregistrovat virtuální počítač z virtuálního pevného disku nasazeného v Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: b02fda545ac135735186885d7db597885bf6cc21
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147969"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Nasazení virtuálního počítače z virtuálních pevných disků

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesunovat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Podle pokynů v tématu [Vytvoření technických prostředků virtuálních počítačů Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) spravujte vaše migrované nabídky.

V této části se dozvíte, jak nasadit virtuální počítač z virtuálního pevného disku nasazeného v Azure (VHD).  Seznam požadovaných nástrojů a jejich použití k vytvoření uživatelské image virtuálního počítače a jeho nasazení do Azure pomocí skriptů PowerShellu.

Po nahrání virtuálních pevných disků (VHD) – zobecněný virtuální pevný disk operačního systému a žádný nebo více virtuálních pevných disků (VHD) na účet služby Azure Storage můžete zaregistrovat jako uživatelskou image virtuálního počítače. Potom můžete tuto image otestovat. Vzhledem k tomu, že virtuální pevný disk s operačním systémem je zobecněný, nejde virtuální počítač nasadit přímo zadáním adresy URL virtuálního pevného disku.

Další informace o imagí virtuálních počítačů najdete v těchto blogových příspěvcích:

- [Image virtuálního počítače](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Postup PowerShellu pro image virtuálního počítače](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Předpoklad: instalace nezbytných nástrojů

Pokud jste to ještě neudělali, nainstalujte Azure PowerShell a Azure CLI pomocí následujících pokynů:

- [Instalace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Kroky nasazení

K vytvoření a nasazení uživatelské image virtuálního počítače použijete následující postup:

1. Vytvoří uživatelskou image virtuálního počítače, která zahrnuje zachytávání a generalizaci image. 
2. Vytvořte certifikáty a uložte je v novém Azure Key Vault. K navázání zabezpečeného připojení WinRM k virtuálnímu počítači se vyžaduje certifikát.  Je k dispozici šablona Azure Resource Manager a skript Azure PowerShell. 
3. Nasaďte virtuální počítač z uživatelské image virtuálního počítače pomocí dodané šablony a skriptu.

Po nasazení virtuálního počítače budete připraveni k [certifikaci vaší image virtuálního počítače](./cpp-certify-vm.md).

1. Klikněte na **Nový** a vyhledejte **nasazení šablony**a potom **v editoru vyberte vytvořit vlastní šablonu**.  <br/>
   ![Sestavení šablony nasazení VHD v Azure Portal](./media/publishvm_021.png)

1. Zkopírujte tuto [šablonu JSON](./cpp-deploy-json-template.md) a vložte ji do editoru a klikněte na **Uložit**. <br/>
   ![Uložit šablonu nasazení VHD do Azure Portal](./media/publishvm_022.png)

1. Zadejte hodnoty parametrů pro zobrazené stránky vlastností **vlastního nasazení** .

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Ukazatele**              |   **Popis**                                                            |
   |  -------------              |   ---------------                                                            |
   | Název účtu úložiště uživatele   | Název účtu úložiště, kde se nachází zobecněný virtuální pevný disk                    |
   | Název kontejneru úložiště uživatele | Název kontejneru, kde se nachází zobecněný virtuální pevný disk                          |
   | Název DNS pro veřejnou IP adresu      | Název DNS veřejné IP adresy. Název DNS je na virtuálním počítači, budete ho definovat na webu Azure Portal po nasazení nabídky.  |
   | Uživatelské jméno správce             | Uživatelské jméno účtu správce pro nový virtuální počítač                                  |
   | Heslo správce              | Heslo účtu správce pro nový virtuální počítač                                  |
   | OS Type                     | Operační systém virtuálního počítače `Windows` \| :`Linux`                                    |
   | ID předplatného             | Identifikátor vybraného předplatného                                      |
   | Umístění                    | Zeměpisná poloha nasazení                                        |
   | Velikost virtuálního počítače                     | [Velikost virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), například`Standard_A2` |
   | Název veřejné IP adresy      | Název vaší veřejné IP adresy                                               |
   | Název virtuálního počítače                     | Název nového virtuálního počítače                                                           |
   | Název Virtual Network        | Název virtuální sítě používané virtuálním počítačem                                   |
   | Název síťové karty                    | Název síťové karty, na které běží virtuální síť               |
   | ADRESA URL VIRTUÁLNÍHO PEVNÉHO DISKU                     | Úplná adresa URL virtuálního pevného disku s operačním systémem                                                     |
   |  |  |
            
1. Po poskytnutí těchto hodnot klikněte na **koupit**. 

Azure zahájí nasazení: vytvoří nový virtuální počítač se zadaným nespravovaným virtuálním pevným diskem v zadané cestě k účtu úložiště.  Průběh můžete sledovat v Azure Portal kliknutím na **Virtual Machines** na levé straně portálu.  Po vytvoření virtuálního počítače se stav změní z `Starting` na. `Running` 


### <a name="deploy-a-vm-from-powershell"></a>Nasazení virtuálního počítače z PowerShellu

Pokud chcete nasadit velký virtuální počítač z právě vytvořeného počítače generalizované image, použijte následující rutiny.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Další kroky

V dalším kroku [vytvoříte uživatelskou image virtuálního počítače](cpp-create-user-image.md) pro vaše řešení.


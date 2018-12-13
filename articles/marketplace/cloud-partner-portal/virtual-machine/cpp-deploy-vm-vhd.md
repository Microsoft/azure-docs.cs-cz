---
title: Nasazení virtuálního počítače z virtuálních pevných disků pro Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje postup při registraci virtuálního počítače z VHD nasazení Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 9157ce7f8f16bc60a6d5c16fa992a5402cf2d7ad
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190726"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Nasazení virtuálního počítače z virtuálních pevných disků

Tato část vysvětluje, jak nasadit virtuální počítač (VM) z nasazených Azure virtuálního pevného disku (VHD).  Vypíše nástroje potřebné a jak se dají použít k vytvoření uživatelské image virtuálního počítače a pak ji nasadit do Azure pomocí skriptů Powershellu.

Po odeslání virtuální pevné disky (VHD) – zobecněný virtuální pevný disk operačního systému a nula nebo více datových pevných disků – ke svému účtu Azure storage, můžete je zaregistrovat jako uživatelskou image virtuálního počítače. Pak můžete otestovat této bitové kopie. Protože je zobecněný virtuální pevný disk operačního systému, nelze přímo nasadit virtuální počítač zadáním adresy URL virtuálního pevného disku.

Další informace o imagích virtuálních počítačů, najdete v těchto příspěvcích na blogu:

- [Image virtuálního počítače](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Image virtuálního počítače pomocí Powershellu "Jak"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="prerequisite-install-the-necessary-tools"></a>Předpoklad: instalace potřebné nástroje

Pokud jste tak již neučinili, nainstalujte prostředí Azure PowerShell a rozhraní příkazového řádku Azure, pomocí následujících pokynů:

- [Instalace prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Nainstalujte Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="deployment-steps"></a>Kroky nasazení

Vytvářet a nasazovat uživatelské image virtuálního počítače použijete následující kroky:

1. Vytvoření uživatelské image virtuálního počítače, který zahrnuje zaznamenávání a zobecňuje bitovou kopii. 
2. Vytvoření certifikátů a uložit je do nové služby Azure Key Vault. Certifikát je nezbytné pro navázání zabezpečeného připojení WinRM k virtuálnímu počítači.  Jsou k dispozici šablony Azure Resource Manageru a skript Azure Powershellu. 
3. Nasazení virtuálního počítače z uživatelské image virtuálního počítače pomocí dodané šablony a skriptů.

Po nasazení virtuálního počítače, jste připraveni [certifikaci vaší image virtuálního počítače](./cpp-certify-vm.md).

2.  Klikněte na tlačítko **nový** a vyhledejte **nasazení šablony**a pak vyberte **vytvořit vlastní šablonu v editoru**.  <br/>
  ![Vytvoření šablony nasazení virtuálního pevného disku na webu Azure portal](./media/publishvm_021.png)

3. Zkopírovat a vložit [šablonu JSON](./cpp-deploy-json-template.md) do editoru a klikněte na tlačítko **Uložit**. <br/>
  ![Uložit šablonu nasazení virtuálního pevného disku na webu Azure portal](./media/publishvm_022.png)

4. Zadejte hodnoty parametrů pro zobrazených **vlastní nasazení** stránky vlastností.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametr**              |   **Popis**                                                            |
   |  -------------              |   ---------------                                                            |
   | Název uživatelského účtu úložiště   | Název účtu úložiště, kde se nachází generalizovaného virtuálního pevného disku                    |
   | Název kontejneru úložiště uživatele | Název kontejneru, ve kterém se nachází generalizovaného virtuálního pevného disku                          |
   | Název DNS pro veřejné IP adresy      | Název veřejné IP adresy DNS                                                           |
   | Uživatelské jméno správce             | Uživatelské jméno účtu správce pro nový virtuální počítač                                  |
   | Heslo správce              | Heslo účtu správce pro nový virtuální počítač                                  |
   | OS Type                     | Operační systém virtuálního počítače: `Windows` \| `Linux`                                    |
   | ID předplatného             | Identifikátor vybraného předplatného                                      |
   | Umístění                    | Geografické umístění nasazení                                        |
   | Velikost virtuálního počítače                     | [Velikost virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), například `Standard_A2` |
   | Název veřejné IP adresy      | Název veřejné IP adresy                                               |
   | Název virtuálního počítače                     | Název nového virtuálního počítače                                                           |
   | Název virtuální sítě        | Název virtuální sítě používané virtuálním Počítačem                                   |
   | Název síťové karty                    | Název síťové karty používající službu virtual network               |
   | URL VIRTUÁLNÍHO PEVNÉHO DISKU                     | Dokončení URL virtuálního pevného disku pro Disk s operačním systémem                                                     |
   |  |  |
            
5. Po zadání těchto hodnot, klikněte na tlačítko **nákupní**. 

Azure se začne nasazení: vytvoří nový virtuální počítač s zadaný nespravovaný virtuálního pevného disku, v cestě zadané úložiště účtu.  Průběh na webu Azure Portal můžete sledovat kliknutím na **virtuálních počítačů** na levé straně na portálu.  Po vytvoření virtuálního počítače, stav se změní z `Starting` k `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Nasazení virtuálního počítače z prostředí PowerShell

Pokud chcete nasadit velký virtuální počítač z image generalizovaného virtuálního počítače právě vytvořili, použijte následující rutiny.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Další postup

V dalším kroku bude [vytvoření uživatelské image virtuálního počítače](cpp-create-user-image.md) pro vaše řešení.


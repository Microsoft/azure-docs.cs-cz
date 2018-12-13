---
title: Nasazení virtuálního počítače z virtuálních pevných disků pro Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje postup při registraci virtuálního počítače z VHD nasazení Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 06ef4247d3cd7f87d763feb3f61cb8101d17a2e4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877112"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Nasazení virtuálního počítače z virtuálních pevných disků

Tento článek vysvětluje, jak zaregistrovat virtuální počítač (VM) z nasazených Azure virtuálního pevného disku (VHD).  Obsahuje nástroje potřebné a jak se dají použít k vytvoření uživatelské image virtuálního počítače a pak ji nasadit do Azure s využitím buď [portálu Microsoft Azure](https://ms.portal.azure.com/) nebo skripty prostředí PowerShell. 

Po odeslání virtuální pevné disky (VHD) – zobecněný virtuální pevný disk operačního systému a nula nebo více datových pevných disků – ke svému účtu Azure storage, můžete je zaregistrovat jako uživatelskou image virtuálního počítače. Pak můžete otestovat této bitové kopie. Protože je zobecněný virtuální pevný disk operačního systému, nelze přímo nasadit virtuální počítač zadáním adresy URL virtuálního pevného disku.

Další informace o imagích virtuálních počítačů, najdete v těchto příspěvcích na blogu:

- [Image virtuálního počítače](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Image virtuálního počítače pomocí Powershellu "Jak"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>Nastavit potřebné nástroje

Pokud jste tak již neučinili, nainstalujte prostředí Azure PowerShell a rozhraní příkazového řádku Azure, pomocí následujících pokynů:

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Instalace prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Nainstalujte Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="create-a-user-vm-image"></a>Vytvoření uživatelské image virtuálního počítače

V dalším kroku bude vytvoření nespravované image z generalizovaného virtuálního pevného disku.

#### <a name="capture-the-vm-image"></a>Zachycení image virtuálního počítače

Postupujte podle pokynů v článku o zachycení virtuálního počítače, který odpovídá vašemu přístupu k přístupu:

-  Prostředí PowerShell: [vytvoření nespravované image virtuálního počítače z virtuálního počítače Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [jak vytvořit image virtuálního počítače nebo virtuálního pevného disku](../../../virtual-machines/linux/capture-image.md)
-  Rozhraní API: [Virtual Machines – zachycení](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>Zobecněte image virtuálního počítače

Protože jste vygenerovali uživatelská image z dříve generalizovaného virtuálního pevného disku, jeho by měl také zobecnit.  Znovu vyberte v následujícím článku, který odpovídá vaší mechanismus přístupu.  (Můžete může mít již zobecněný disk když jste zachytili.)

-  Prostředí PowerShell: [generalizace virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [krok 2: image vytvořit virtuální počítač.](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  Rozhraní API: [Virtual Machines – Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Nasazení virtuálního počítače z uživatelské image virtuálního počítače

V dalším kroku nasadíte virtuálního počítače z uživatelské image virtuálního počítače pomocí webu Azure portal nebo Powershellu.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Nasazení virtuálního počítače z webu Azure portal

Použijte následující postup nasazení uživatele virtuálního počítače z portálu Azure portal.

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).

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

Po nasazení virtuálního počítače, jste připraveni [konfigurace virtuálního počítače](./cpp-configure-vm.md).

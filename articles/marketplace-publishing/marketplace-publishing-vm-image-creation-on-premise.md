---
title: "Vytvoření bitové kopie virtuálního počítače místní pro Azure Marketplace | Microsoft Docs"
description: "Pochopení a provést kroky pro vytvoření image virtuálního počítače místní a nasadit do Azure Marketplace pro ostatní k nákupu."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: 6b927ce6032092ce258eeebca49da0571439dbfb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Vytvořte bitovou kopii virtuálního počítače místní pro Azure Marketplace
Důrazně doporučujeme vývoji Azure virtuální pevné disky (VHD) přímo v cloudu pomocí protokolu RDP. Ale pokud je potřeba, je možné stáhnout virtuální pevný disk a vytvořte ho pomocí místní infrastruktury.  

Pro místní vývoj je nutné stáhnout operační systém virtuálního pevného disku vytvoření virtuálního počítače. Tyto kroky by proběhnout jako součást kroku 3.3, výše.  

## <a name="download-a-vhd-image"></a>Stažení bitové kopie virtuálního pevného disku
### <a name="locate-a-blob-url"></a>Vyhledejte adresu URL objektu blob
Chcete-li stáhnout virtuální pevný disk, nejprve vyhledejte adresu URL objektu blob pro disk operačního systému.

Vyhledejte adresu URL objektu blob z nové [portálu Microsoft Azure](https://portal.azure.com):

1. Přejděte na **Procházet** > **virtuální počítače**a potom vyberte virtuální počítač nasazený.
2. V části **konfigurace**, vyberte **disky** dlaždici, což otevře okno disky.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Vyberte **Disk s operačním systémem**, což otevře další okno, které zobrazuje vlastnosti disku, včetně umístění virtuálního pevného disku.
4. Zkopírujte tuto adresu URL objektu blob.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Nyní odstraňte nasazených virtuálních počítačů bez odstranění základní disky. Můžete také zastavit virtuální počítač místo odstranění. Nestahovat operačního systému virtuálního pevného disku, když je virtuální počítač spuštěný.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Stažení virtuálního pevného disku
Po zjištění adresy URL objektu blob, si můžete stáhnout virtuální pevný disk pomocí [portál Azure](http://manage.windowsazure.com/) nebo prostředí PowerShell.  

> [!NOTE]
> V době vytvoření této příručce funkce pro stažení virtuální pevný disk ještě není součástí nového portálu Microsoft Azure.  
> 
> 

**Stáhnout operační systém virtuálního pevného disku prostřednictvím aktuální [portálu Azure](http://manage.windowsazure.com/)**

1. Pokud jste tak již neučinili, přihlaste se k portálu Azure.
2. Klikněte **úložiště** kartě.
3. Vyberte účet úložiště, ve kterém je uložený virtuální pevný disk.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Zobrazí vlastnosti účtu úložiště. Vyberte **kontejnery** kartě.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Vyberte kontejner, ve kterém je uložený virtuální pevný disk. Ve výchozím nastavení když vytvořená na portálu, virtuální pevný disk uložený v kontejner virtuálních pevných disků.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Vyberte správný operační systém virtuálního pevného disku tak, že porovnáte adresu URL, které jste uložili.
7. Klikněte na **Stáhnout**.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Stáhnout virtuální pevný disk pomocí prostředí PowerShell
Kromě pomocí portálu Azure, můžete použít [uložit AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) rutiny stáhnout operační systém virtuálního pevného disku.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Například uložit AzureVhd-zdroje "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" - LocalFilePath "C:\Users\Administrator\Desktop\baseimagevm.vhd" - klíč úložiště <String>

> [!NOTE]
> **Uložit AzureVhd** má také **NumberOfThreads** možnost, která můžete použít ke zvýšení paralelismus za účelem co nejlepší využití dostupné šířky pásma pro stažení.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Nahrání virtuálních pevných disků do účtu úložiště Azure
Pokud jste připravili virtuální pevné disky místní, budete muset odešlete do účtu úložiště v Azure. Tento krok se provádí po vytvoření svůj disk VHD místní ale před jeho získání certifikační pro bitové kopie virtuálního počítače.

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru
Doporučujeme vám, že virtuální pevné disky se nahraje do účtu úložiště v oblasti ve Spojených státech amerických. Všechny virtuální pevné disky pro jednu SKU musí být umístěny v jednom kontejneru v rámci účtu jedno úložiště.

Pokud chcete vytvořit účet úložiště, můžete použít [portálu Microsoft Azure](https://portal.azure.com/), prostředí PowerShell nebo sady Linux nástroj příkazového řádku.  

**Vytvořit účet úložiště z portálu Microsoft Azure**

1. Klikněte na **Vytvořit prostředek**.
2. Vyberte **úložiště**.
3. Zadejte název účtu úložiště a pak vyberte umístění.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Klikněte na možnost **Vytvořit**.
5. V okně pro účet vytvořený úložiště by se mělo otevřít. Pokud ne, vyberte **Procházet** > **účty úložiště**. V okně účtu úložiště vyberte účet úložiště vytvořit.
6. Vyberte **kontejnery**.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. V okně kontejnery vyberte **přidat**a pak zadejte název kontejneru a kontejneru oprávnění. Vyberte **privátní** kontejneru oprávnění.

> [!TIP]
> Doporučujeme, abyste vytvořili jednoho kontejneru typu jedné SKU, které chcete publikovat.
> 
> 

  ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Vytvořit účet úložiště pomocí prostředí PowerShell
Pomocí prostředí PowerShell vytvořit účet úložiště pomocí [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) rutiny.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Potom můžete vytvořit kontejner v rámci tohoto účtu úložiště pomocí [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) rutiny.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Těchto příkazů se předpokládá, že aktuální kontext účtu úložiště již byla nastavena v prostředí PowerShell.   Odkazovat na [nastavení prostředí Azure PowerShell](marketplace-publishing-powershell-setup.md) Další informace o instalaci prostředí PowerShell.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Vytvořit účet úložiště pomocí nástroje příkazového řádku pro Mac a Linux
> Z [nástroj příkazového řádku Linux](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), vytvoření účtu úložiště následujícím způsobem.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Vytvořte kontejner následujícím způsobem.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku
Po vytvoření účtu úložiště a kontejneru, můžete nahrát připravit virtuální pevné disky. Můžete použít PowerShell, nástroje příkazového řádku systému Linux nebo jiné nástroje pro správu Azure Storage.

### <a name="upload-a-vhd-via-powershell"></a>Nahrání virtuálního pevného disku pomocí prostředí PowerShell
Použití [přidat AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) rutiny.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Nahrát VHD pomocí nástroje příkazového řádku pro Mac a Linux
S [nástroj příkazového řádku Linux](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), použijte následující: vytvoření bitové kopie virtuálního počítače azure <image name> – umístění <Location of the data center> – operačního systému Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytváření bitové kopie virtuálního počítače pro Marketplace.](marketplace-publishing-vm-image-creation.md)
* [Nastavení prostředí Azure PowerShell](marketplace-publishing-powershell-setup.md)


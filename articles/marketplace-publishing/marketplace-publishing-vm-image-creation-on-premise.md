---
title: Vytvoření image virtuálního počítače místní pro Azure Marketplace | Dokumentace Microsoftu
description: Pochopení a proveďte kroky k vytvoření image virtuálního počítače v místním a nasazení na webu Azure Marketplace pro ostatní uživatele k nákupu.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: e5571a0933284a52d5567db0505ecaced6c6c336
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253495"
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Image virtuálního počítače místní vývoj pro Azure Marketplace
Důrazně doporučujeme při vývoji Azure virtuálních pevných disků (VHD) přímo v cloudu pomocí protokolu RDP. Nicméně pokud je to nutné, je možné stáhnout virtuální pevný disk a vývoj s využitím služby v místní infrastruktuře.  

Pro místní vývoj je nutné stáhnout operační systém virtuálního pevného disku vytvořeného virtuálního počítače. Tyto kroky by se použijí jako součást kroku 3.3, výše.  

## <a name="download-a-vhd-image"></a>Stáhněte si bitovou kopii virtuálního pevného disku
### <a name="locate-a-blob-url"></a>Vyhledejte adresu URL objektu blob
Aby bylo možné stáhnout virtuální pevný disk, nejprve vyhledejte adresu URL objektu blob disku s operačním systémem.

Vyhledejte adresu URL objektu blob z nové [portálu Microsoft Azure](https://portal.azure.com):

1. Přejděte na **Procházet** > **virtuálních počítačů**a potom vyberte virtuální počítač nasazený.
2. V části **konfigurovat**, vyberte **disky** dlaždici, otevře se okno disky.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Vyberte **Disk s operačním systémem**, který otevře se další okno, které zobrazuje vlastnosti disku, včetně umístění virtuálního pevného disku.
4. Zkopírujte tuto adresu URL objektu blob.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Nyní odstraňte nasazený virtuální počítač bez odstranění základní disky. Můžete také zastavit virtuální počítač místo jeho odstranění. Nestahovat operační systém virtuálního pevného disku, když je virtuální počítač spuštěný.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Stažení virtuálního pevného disku
Až budete vědět, adresa URL objektu blob, si můžete stáhnout virtuální pevný disk s použitím [webu Azure portal](http://manage.windowsazure.com/) nebo prostředí PowerShell.  

> [!NOTE]
> V době vytváření tohoto průvodce funkce pro stažení virtuálního pevného disku ještě není k dispozici na novém portálu Microsoft Azure.  
> 
> 

**Stáhnout operační systém virtuálního pevného disku prostřednictvím aktuální [webu Azure portal](http://manage.windowsazure.com/)**

1. Pokud jste tak již neučinili, přihlaste se k webu Azure portal.
2. Klikněte na tlačítko **úložiště** kartu.
3. Vyberte účet úložiště, ve kterém jsou uloženy virtuální pevný disk.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Zobrazí se vlastnosti účtu úložiště. Vyberte **kontejnery** kartu.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Vyberte kontejner, ve kterém jsou uloženy virtuální pevný disk. Ve výchozím nastavení když se vytvoří na portálu je virtuální pevný disk uložený v kontejneru virtuální pevné disky.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Vyberte správný operační systém virtuálního pevného disku porovnáním adresu URL, který jste uložili.
7. Klikněte na **Stáhnout**.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Stažení virtuálního pevného disku pomocí prostředí PowerShell
Kromě webu Azure portal, můžete použít [Save-AzureVhd](https://msdn.microsoft.com/library/dn495297.aspx) rutina pro stažení virtuálního pevného disku operačního systému.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Například Save-AzureVhd-zdroj "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" - LocalFilePath "C:\Users\Administrator\Desktop\baseimagevm.vhd" - klíč úložiště <String>

> [!NOTE]
> **Uložit-AzureVhd** má také **NumberOfThreads** možnost, která slouží ke zvýšení paralelismus, aby co nejlíp využít dostupnou šířku pásma pro stažení.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Odeslání virtuálních pevných disků do účtu služby Azure storage
Pokud jste si připravili virtuální pevné disky na místní, musíte nahrát do účtu úložiště v Azure. Tento krok se provádí po vytvoření vašeho virtuálního pevného disku v místním, ale před získání certifikace pro vaši image virtuálního počítače.

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru
Doporučujeme vám, že virtuální pevné disky nahrát do účtu úložiště v oblasti v USA. Všechny virtuální pevné disky pro jeden skladová položka musí být umístěné ve jedním kontejnerem v rámci jednoho účtu úložiště.

Chcete-li vytvořit účet úložiště, můžete použít [portálu Microsoft Azure](https://portal.azure.com/), Powershellu nebo Linuxu nástroj příkazového řádku.  

**Vytvořit účet úložiště z portálu Microsoft Azure**

1. Klikněte na **Vytvořit prostředek**.
2. Vyberte **Úložiště**.
3. Zadejte název účtu úložiště a pak vyberte umístění.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Klikněte na možnost **Vytvořit**.
5. V okně na vytvořený účet úložiště by měl být otevřený. Pokud ne, vyberte **Procházet** > **účty úložiště**. V okně účtu úložiště vyberte účet úložiště vytvoří.
6. Vyberte **kontejnery**.
   
   ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. V okně kontejnery, vyberte **přidat**a potom zadejte název kontejneru a oprávnění kontejneru. Vyberte **privátní** kontejneru oprávnění.

> [!TIP]
> Doporučujeme vytvořit jeden kontejner na skladovou jednotku, kterou máte v úmyslu publikovat.
> 
> 

  ![Kreslení](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Vytvoření účtu úložiště pomocí prostředí PowerShell
Pomocí Powershellu, vytvořit účet úložiště pomocí [New-AzureStorageAccount](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azurestorageaccount) rutiny.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Poté vytvoříte kontejner v účtu úložiště pomocí [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) rutiny.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Těchto příkazů se předpokládá, že aktuální kontext účtu úložiště již byla nastavena v prostředí PowerShell.   Odkazovat na [nastavení prostředí Azure PowerShell](marketplace-publishing-powershell-setup.md) podrobné informace o instalaci prostředí PowerShell.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Vytvoření účtu úložiště pomocí nástroje příkazového řádku pro Mac a Linux
> Z [nástroj pro příkazový řádek Linuxu](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), následujícím způsobem vytvořte účet úložiště.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Následujícím způsobem vytvořte kontejner.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku
Po vytvoření účtu úložiště a kontejner, můžete nahrát připravený virtuální pevné disky. Můžete použít PowerShell, nástroje příkazového řádku systému Linux nebo jiné nástroje pro správu služby Azure Storage.

### <a name="upload-a-vhd-via-powershell"></a>Nahrát VHD pomocí Powershellu
Použití [Add-AzureVhd](https://msdn.microsoft.com/library/dn495173.aspx) rutiny.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Nahrání virtuálního pevného disku s použitím nástroje příkazového řádku pro Mac a Linux
S [nástroj pro příkazový řádek Linuxu](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), použijte následující: vytvořit image virtuálního počítače azure <image name> – umístění <Location of the data center> – operační systém Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření image virtuálního počítače pro Marketplace](marketplace-publishing-vm-image-creation.md)
* [Zřízení prostředí Azure PowerShell](marketplace-publishing-powershell-setup.md)


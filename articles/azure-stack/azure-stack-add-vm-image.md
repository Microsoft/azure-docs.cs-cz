---
title: Přidání a odebrání image virtuálního počítače do protokolů Azure | Microsoft Docs
description: Přidat nebo odebrat vaší organizace vlastní Windows nebo virtuálního počítače s Linuxem image pro klienty použít.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 714afa1da5d2c8c5695dfe33edd0257f69af149d
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287700"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Zpřístupnit bitovou kopii virtuálního počítače v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

V zásobník Azure můžete mít bitové kopie virtuálních počítačů, které jsou k dispozici pro vaše uživatele. Tyto bitové kopie lze odkazovat pomocí šablony Azure Resource Manager nebo je můžete přidat do Azure Marketplace rozhraní jako položku Marketplace. Můžete použít buď image formuláře globální Azure Marketplace nebo přidejte svoji vlastní image. Můžete přidat virtuální počítač pomocí portálu nebo prostředí Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Přidání bitové kopie virtuálního počítače přes portál.

> [!NOTE]
> Pomocí této metody musíte vytvořit položku Marketplace samostatně.

Bitové kopie musí být schopen odkazovat úložiště objektů blob identifikátor URI. Příprava image operačního systému Windows nebo Linux ve formátu VHD (ne VHDX) a pak nahrajte image na účet úložiště v Azure nebo Azure zásobníku. Pokud vaší image je již nahrán do úložiště objektů blob v Azure nebo Azure zásobníku, můžete přeskočit krok 1.

1. [Nahrajte image virtuálního počítače Windows Azure pro nasazení Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) nebo bitovou kopii systému Linux, postupujte podle pokynů v tématu [nasazení Linuxové virtuální počítače v Azure zásobníku](azure-stack-linux.md). Před nahráním image, je důležité vzít v úvahu následující faktory:

   - Azure zásobníku podporuje formát virtuálního pevného disku pevný disk. Opravené formátu struktury logický disk lineárně v souboru tak, že disku Posun X je uložený na objekt blob posunu X. Malé zápatí na konec objektu blob popisuje vlastnosti virtuálního pevného disku. Chcete-li potvrdit, pokud je pevný disk, použijte [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) příkaz prostředí PowerShell.  

    > [!IMPORTANT]
    >  Azure zásobník nepodporuje dynamického disku VHD. Změna velikosti dynamický disk, který je připojen k virtuálnímu počítači ponechá virtuální počítač ve stavu selhání. Ke zmírnění tohoto problému, odstraňte virtuální počítač bez odstranění disku Virtuálního počítače, objekt blob souboru VHD v účtu úložiště. Převod virtuálního pevného disku z dynamický disk na pevný disk a znovu vytvořte virtuální počítač.

   * Je efektivnější Odeslat bitovou kopii do úložiště objektů blob Azure zásobníku než do Azure blob storage vzhledem k tomu, jak dlouho trvá méně času k nabízení bitovou kopii do úložiště bitové kopie zásobník Azure.

   * Když nahrajete [image virtuálního počítače Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), je nezbytné nahradit **přihlášení k Azure** s krokem [nakonfigurovat prostředí PowerShell Azure zásobníku operátor](azure-stack-powershell-configure-admin.md) krok.  

   * Úložiště objektů blob identifikátor URI, kde můžete nahrát bitovou kopii si poznamenejte. Identifikátor URI úložiště objektů blob má následující formát: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   * Zpřístupněte objektu blob anonymně, přejděte do kontejneru objektů blob účet úložiště kde byl odeslán image virtuálního počítače virtuální pevný disk. Vyberte **Blob**a potom vyberte **zásady přístupu**. Volitelně můžete místo toho vygenerovat sdílený přístupový podpis kontejneru a vložit jako součást identifikátor URI objektu blob.

   ![Přejděte na účet úložiště objektů BLOB](./media/azure-stack-add-vm-image/image1.png)

   ![Sada objektů blob přístup k veřejné](./media/azure-stack-add-vm-image/image2.png)

2. Přihlaste se k Azure zásobníku jako operátor. V nabídce vyberte **další služby** > **zprostředkovatelé prostředků**. Pak vyberte **výpočetní** > **Image virtuálních počítačů** > **přidat**.

3. V části **přidat bitovou kopii virtuálního počítače**, zadejte vydavatele, nabídky, SKU a verzi bitové kopie virtuálního počítače. Tyto segmenty název odkazovat na bitovou kopii virtuálního počítače v šablonách Resource Manageru. Je nutné vybrat **osType** hodnota správně. Pro **identifikátor URI objektu Blob disku operačního systému**, zadejte identifikátor URI objektu Blob, kde byl odeslán bitovou kopii. Pak vyberte **vytvořit** zahajte proces vytváření bitové kopie virtuálního počítače.

   ![Zahajte vytvoření bitové kopie](./media/azure-stack-add-vm-image/image4.png)

   Po úspěšném vytvoření image stav bitové kopie virtuálního počítače se změní na **úspěšné**.

4. Chcete-li více snadno dostupné pro potřeby koncových uživatelů image virtuálního počítače v uživatelském rozhraní, je vhodné [vytvořit položku Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Odeberte image virtuálního počítače přes portál.

1. Otevřete portálu pro správu v [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Vyberte **Marketplace správu**a potom vyberte virtuální počítač, který chcete odstranit.

3. Klikněte na **Odstranit**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Přidání image virtuálního počítače do Marketplace s použitím prostředí PowerShell

1. [Instalace prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).  

2. Přihlaste se k Azure zásobníku jako operátor. Pokyny najdete v tématu [Přihlaste se k Azure zásobníku jako operátor](azure-stack-powershell-configure-admin.md).

3. Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a spusťte:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  **Přidat AzsPlatformimage** rutiny určuje hodnoty, které slouží k odkazování image virtuálního počítače pomocí šablony Azure Resource Manager. Hodnoty patří:
  - **publisher**  
    Příklad: `Canonical`  
    Segment název vydavatele bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii. Příkladem je **Microsoft**. V tomto poli nezahrnují mezery nebo speciální znaky.  
  - **Nabídka**  
    Příklad: `UbuntuServer`  
    Segment nabídka název bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Příkladem je **Windows Server**. V tomto poli nezahrnují mezery nebo speciální znaky.  
  - **sku**  
    Příklad: `14.04.3-LTS`  
    Segment SKU název bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Příkladem je **Datacenter2016**. V tomto poli nezahrnují mezery nebo speciální znaky.  
  - **Verze**  
    Příklad: `1.0.0`  
    Verze bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Tato verze je ve formátu  *\#.\#. \#*. Příkladem je **1.0.0**. V tomto poli nezahrnují mezery nebo speciální znaky.  
  - **osType**  
    Příklad: `Linux`  
    OsType bitové kopie musí být buď **Windows** nebo **Linux**.  
  - **OSUri**  
    Příklad: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Můžete zadat úložiště objektů blob identifikátor URI pro `osDisk`.  

    Další informace najdete v tématu odkaz na prostředí PowerShell pro [přidat AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) rutiny a [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) rutiny.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Přidat vlastní image virtuálního počítače do Marketplace pomocí prostředí PowerShell

1. [Instalace prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. Pokud používáte **Active Directory Federation Services**, použijte následující rutinu:

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Přihlaste se k Azure zásobníku jako operátor. Pokyny najdete v tématu [Přihlaste se k Azure zásobníku jako operátor](azure-stack-powershell-configure-admin.md).

4. Vytvořte účet úložiště v globální Azure nebo Azure zásobníku ukládat vlastní bitovou kopii virtuálního počítače. Pokyny naleznete v části [rychlý start: odesílání, stahování a seznamu objektů BLOB pomocí portálu Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Příprava image operačního systému Windows nebo Linux ve formátu VHD (ne VHDX), Odeslat bitovou kopii do svého účtu úložiště a získat identifikátor URI, kde lze načíst image virtuálního počítače pomocí prostředí PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Volitelně) Můžete nahrát pole datové disky jako součást image virtuálního počítače. Vytvoření vaší datových disků pomocí rutiny New-DataDiskObject. Otevřete prostředí PowerShell řádku se zvýšenými oprávněními a spusťte:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a spusťte:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Další informace o rutinu Add-AzsPlatformimage a rutiny New-DataDiskObject najdete v tématu Microsoft PowerShell [dokumentaci modulu Azure zásobníku operátor](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Odebrání image virtuálního počítače pomocí prostředí PowerShell

Pokud již nepotřebujete bitovou kopii virtuálního počítače, který jste nahráli, chcete-li odstranit z Marketplace pomocí následující rutiny:

1. [Instalace prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).

2. Přihlaste se k Azure zásobníku jako operátor.

3. Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a spusťte:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  **Odebrat AzsPlatformImage** rutiny určuje hodnoty, které slouží k odkazování image virtuálního počítače pomocí šablony Azure Resource Manager. Hodnoty patří:
  - **publisher**  
    Příklad: `Canonical`  
    Segment název vydavatele bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii. Příkladem je **Microsoft**. V tomto poli nezahrnují mezery nebo speciální znaky.  
  - **Nabídka**  
    Příklad: `UbuntuServer`  
    Segment nabídka název bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Příkladem je **Windows Server**. V tomto poli nezahrnují mezery nebo speciální znaky.  
  - **sku**  
    Příklad: `14.04.3-LTS`  
    Segment SKU název bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Příkladem je **Datacenter2016**. V tomto poli nezahrnují mezery nebo speciální znaky.  
  - **Verze**  
    Příklad: `1.0.0`  
    Verze bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Tato verze je ve formátu  *\#.\#. \#*. Příkladem je **1.0.0**. V tomto poli nezahrnují mezery nebo speciální znaky.  
    
    Další informace o rutině Remove-AzsPlatformImage najdete v tématu Microsoft PowerShell [dokumentaci modulu Azure zásobníku operátor](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Další postup

[Zřízení virtuálního počítače](azure-stack-provision-vm.md)

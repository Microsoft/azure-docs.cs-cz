---
title: Přidání bitové kopie virtuálních počítačů do protokolů Azure | Microsoft Docs
description: Přidejte vaše organizace vlastní Windows nebo virtuálního počítače s Linuxem bitovou kopii pro klienty použít.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: mabrigg
ms.openlocfilehash: 3680777439678a93b1283cf94b9f8a173965feeb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Zpřístupnit image vlastní virtuálního počítače v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

V zásobníku Azure operátory můžete vlastního virtuálního počítače bitové kopie zpřístupnit uživatelům. Tyto bitové kopie lze odkazovat pomocí šablony Azure Resource Manager nebo je můžete přidat do Azure Marketplace rozhraní jako položku Marketplace.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Přidání image virtuálního počítače do Marketplace s použitím prostředí PowerShell

Spusťte následující předpoklady, buď z [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) nebo externí klienta se systémem Windows, pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Instalace prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).  

2. Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).  

3. Příprava systému Windows nebo Linux image virtuálního pevného disku operačního systému ve formátu VHD (nepoužívejte formátu VHDX).

   * Bitových kopií systému Windows, informace o ručním bitovou kopii, najdete v tématu [nahrajte image virtuálního počítače Windows Azure pro nasazení Resource Manager](../virtual-machines/windows/upload-generalized-managed.md).

   * Pro Linux Image, najdete v části [nasazení Linuxové virtuální počítače v Azure zásobníku](azure-stack-linux.md). Proveďte kroky připravte bitovou kopii nebo použít stávající image Azure zásobníku Linux, jak je popsáno v článku.    

   Azure zásobníku podporuje formát virtuálního pevného disku pevný disk. Opravené formátu struktury logický disk lineárně v souboru tak, že disku Posun X je uložený na objekt blob posunu X. Malé zápatí na konec objektu blob popisuje vlastnosti virtuálního pevného disku. Chcete-li potvrdit, pokud je pevný disk, použijte [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) příkaz prostředí PowerShell.  

   > [!IMPORTANT]
   >  Azure zásobník nepodporuje dynamického disku VHD. Změna velikosti dynamický disk, který je připojen k virtuálnímu počítači ponechá virtuální počítač ve stavu selhání. Ke zmírnění tohoto problému, odstraňte virtuální počítač bez odstranění disku Virtuálního počítače, objekt blob souboru VHD v účtu úložiště. Potom převést virtuální pevný disk na dynamický disk na pevný disk a znovu vytvořte virtuální počítač.

Chcete-li přidat bitovou kopii do Azure Marketplace zásobníku, proveďte následující kroky:

1. Naimportujte moduly Connect a ComputeAdmin:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ```

2. Přihlaste se do prostředí Azure zásobníku. Spusťte jeden z následujících skriptů, v závislosti na tom, jestli jste nasadili prostředí zásobníku Azure pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS). (Nahraďte Azure AD `tenantName`, `GraphAudience` koncovému bodu, a `ArmEndpoint` hodnoty tak, aby odrážela konfiguraci prostředí.)

    * **Azure Active Directory**. Použijte následující rutinu:

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"

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

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID
      ```

   * **Služba Active Directory Federation Services**. Použijte následující rutinu:

        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
        Add-AzureRMEnvironment `
          -Name "AzureStackAdmin" `
          -ArmEndpoint $ArmEndpoint

        Set-AzureRmEnvironment `
          -Name "AzureStackAdmin" `
          -GraphAudience $GraphAudience `
          -EnableAdfsAuthentication:$true

        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin

        Connect-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. Přidání bitové kopie virtuálních počítačů vyvoláním `Add-AzsVMImage` rutiny. V `Add-AzsVMImage` rutiny, zadejte `osType` jako Windows nebo Linux. Zahrnují vydavatele, nabídky, SKU a verze pro bitovou kopii virtuálního počítače. Informace o povolených parametrech najdete v tématu [parametry](#parameters). Parametry jsou používány šablon Azure Resource Manageru, chcete-li image virtuálního počítače. Následující příklad popisuje vyvolání skriptu:

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```


Příkaz provede následující akce:

* Ověřuje do prostředí Azure zásobníku.
* Ukládání místního virtuálního pevného disku na účet nově vytvořený dočasné úložiště.
* Přidá image virtuálního počítače do úložiště bitové kopie virtuálního počítače.
* Vytvoří položku Marketplace.

Chcete-li ověřit, jestli se příkaz úspěšně spustila na portálu, přejděte na Marketplace. Ověřte, zda je k dispozici v imagi virtuálního počítače **výpočetní** kategorie.

![Image virtuálního počítače úspěšně přidán](./media/azure-stack-add-vm-image/verify-vm.png)

## <a name="remove-a-vm-image-by-using-powershell"></a>Odebrání image virtuálního počítače pomocí prostředí PowerShell

Pokud již nepotřebujete bitovou kopii virtuálního počítače, který jste nahráli, chcete-li odstranit z Marketplace pomocí následující rutiny:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Parametry

| Parametr | Popis |
| --- | --- |
| **publisher** |Segment název vydavatele bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii. Příkladem je **Microsoft**. V tomto poli nezahrnují mezery nebo speciální znaky. |
| **Nabídka** |Segment nabídka název bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Příkladem je **Windows Server**. V tomto poli nezahrnují mezery nebo speciální znaky. |
| **sku** |Segment SKU název bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Příkladem je **Datacenter2016**. V tomto poli nezahrnují mezery nebo speciální znaky. |
| **Verze** |Verze bitové kopie virtuálního počítače, který uživatelé používají, když nasadí bitovou kopii virtuálního počítače. Tato verze je ve formátu  *\#.\#. \#*. Příkladem je **1.0.0**. V tomto poli nezahrnují mezery nebo speciální znaky. |
| **osType** |OsType bitové kopie musí být buď **Windows** nebo **Linux**. |
| **osDiskLocalPath** |Místní cesta k disku operačního systému virtuálního pevného disku, který odesíláte jako image virtuálního počítače do protokolů Azure. |
| **dataDiskLocalPaths** |Volitelné pole místní cesty pro datové disky, které mohou být nahrány jako součást image virtuálního počítače. |
| **CreateGalleryItem** |Logický příznak, který určuje, jestli se mají vytvořit položku Marketplace. Ve výchozím nastavení je nastavena na **true**. |
| **Název** |Zobrazovaný název položky Marketplace. Ve výchozím nastavení je nastavena na `Publisher-Offer-Sku` hodnotu image virtuálního počítače. |
| **Popis** |Popis položky Marketplace. |
| **location** |Umístění, kde image virtuálního počítače je nutné ji publikovat. Ve výchozím nastavení je tato hodnota nastavena **místní**.|
| **osDiskBlobURI** |(Volitelné) Tento skript také přijímá úložiště objektů Blob identifikátor URI pro `osDisk`. |
| **dataDiskBlobURIs** |(Volitelné) Tento skript také přijímá pole úložiště objektů Blob identifikátory URI pro přidání datových disků na bitovou kopii. |

## <a name="add-a-vm-image-through-the-portal"></a>Přidání bitové kopie virtuálního počítače přes portál.

> [!NOTE]
> Pomocí této metody musíte vytvořit položku Marketplace samostatně.

Bitové kopie musí být schopen odkazovat úložiště objektů Blob identifikátor URI. Příprava image operačního systému Windows nebo Linux ve formátu VHD (ne VHDX) a pak nahrajte image na účet úložiště v Azure nebo Azure zásobníku. Pokud vaší image je již nahrán do úložiště objektů Blob v Azure nebo Azure zásobníku, můžete přeskočit krok 1.

1. [Nahrajte image virtuálního počítače Windows Azure pro nasazení Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) nebo bitovou kopii systému Linux, postupujte podle pokynů v tématu [nasazení Linuxové virtuální počítače v Azure zásobníku](azure-stack-linux.md). Před nahráním image, je důležité vzít v úvahu následující faktory:

   * Azure zásobníku podporuje formát virtuálního pevného disku pevný disk. Opravené formátu struktury logický disk lineárně v souboru tak, že disku Posun X je uložený na objekt blob posunu X. Malé zápatí na konec objektu blob popisuje vlastnosti virtuálního pevného disku. Chcete-li potvrdit, pokud je pevný disk, použijte [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) příkaz prostředí PowerShell.  

    > [!IMPORTANT]
   >  Azure zásobník nepodporuje dynamického disku VHD. Změna velikosti dynamický disk, který je připojen k virtuálnímu počítači ponechá virtuální počítač ve stavu selhání. Ke zmírnění tohoto problému, odstraňte virtuální počítač bez odstranění disku Virtuálního počítače, objekt blob souboru VHD v účtu úložiště. Převod virtuálního pevného disku z dynamický disk na pevný disk a znovu vytvořte virtuální počítač.

   * Je efektivnější Odeslat bitovou kopii do úložiště objektů Blob v Azure zásobníku než do úložiště objektů Azure Blob, protože trvá méně času k nabízení bitovou kopii do úložiště bitové kopie zásobník Azure.

   * Když nahrajete [image virtuálního počítače Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), je nezbytné nahradit **přihlášení k Azure** s krokem [nakonfigurovat prostředí PowerShell Azure zásobníku operátor](azure-stack-powershell-configure-admin.md) krok.  

   * Úložiště objektů Blob identifikátor URI, kde můžete nahrát bitovou kopii si poznamenejte. Identifikátor URI úložiště objektů Blob má následující formát: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   * Zpřístupněte objektu blob anonymně, přejděte do kontejneru objektů blob účet úložiště kde byl odeslán image virtuálního počítače virtuální pevný disk. Vyberte **Blob**a potom vyberte **zásady přístupu**. Volitelně můžete místo toho vygenerovat sdílený přístupový podpis kontejneru a vložit jako součást identifikátor URI objektu blob.

   ![Přejděte na účet úložiště objektů BLOB](./media/azure-stack-add-vm-image/image1.png)

   ![Sada objektů blob přístup k veřejné](./media/azure-stack-add-vm-image/image2.png)

2. Přihlaste se k Azure zásobníku jako operátor. V nabídce vyberte **další služby** > **zprostředkovatelé prostředků**. Pak vyberte **výpočetní** > **Image virtuálních počítačů** > **přidat**.

3. V části **přidat bitovou kopii virtuálního počítače**, zadejte vydavatele, nabídky, SKU a verzi bitové kopie virtuálního počítače. Tyto segmenty název odkazovat na bitovou kopii virtuálního počítače v šablonách Resource Manageru. Je nutné vybrat **osType** hodnota správně. Pro **identifikátor URI objektu Blob disku operačního systému**, zadejte identifikátor URI objektu Blob, kde byl odeslán bitovou kopii. Pak vyberte **vytvořit** zahajte proces vytváření bitové kopie virtuálního počítače.

   ![Zahajte vytvoření bitové kopie](./media/azure-stack-add-vm-image/image4.png)

   Po úspěšném vytvoření image stav bitové kopie virtuálního počítače se změní na **úspěšné**.

4. Chcete-li více snadno dostupné pro potřeby koncových uživatelů image virtuálního počítače v uživatelském rozhraní, je vhodné [vytvořit položku Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Další postup

[Zřízení virtuálního počítače](azure-stack-provision-vm.md)

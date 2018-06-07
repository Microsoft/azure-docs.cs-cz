---
title: Stažení položky marketplace z Azure | Microsoft Docs
description: Operátor cloudu můžete stáhnout položky marketplace z Azure na svém nasazení zásobník Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5d403f7c1d0fff466f6c0fb9942ec777ab820eab
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604528"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Stažení položky marketplace z Azure do Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Jako operátor cloudu stažení položek z Azure Marketplace a zpřístupnit v zásobníku Azure. Se položky, které můžete vybrat ze seznamu spravovaných položek Azure Marketplace, které jsou předem testovány a podporovány pro práci s Azure zásobníku. Další položky, které jsou často přidané do tohoto seznamu, tak i nadále vraťte se zpět pro nový obsah. 

Existují dva scénáře pro připojení k Azure Marketplace: 

- **Připojené scénář** -vyžadující prostředí zásobníku Azure k připojení k Internetu. Použití portálu Azure zásobníku a vyhledat a stáhnout položky. 
- **Scénář odpojených nebo připojených částečně** -který vyžaduje přístup k Internetu pomocí nástroje syndikace marketplace ke stažení položky marketplace. Potom přeneste vaše soubory ke stažení do vaší instalace Azure zásobníku. Tento scénář používá prostředí PowerShell.

V tématu [Azure Marketplace položek pro Azure zásobníku](azure-stack-marketplace-azure-items.md) seznam položky marketplace si můžete stáhnout.


## <a name="connected-scenario"></a>Připojené scénář
Pokud Azure zásobníku se připojuje k Internetu, můžete portál pro správu ke stažení položky marketplace.

### <a name="prerequisites"></a>Požadavky
Zásobník Azure nasazení musí mít připojení k Internetu a být [registrované v Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Použití portálu ke stažení položky marketplace.  
1. Přihlaste se k portálu správce Azure zásobníku.

2.  Zkontrolujte místo na úložiště k dispozici před stažením položky marketplace. Později při výběru položek ke stažení, můžete porovnat velikost stahování pro váš dostupné kapacity úložiště. Pokud kapacita je omezená, zvažte možnosti [Správa dostupné místo](azure-stack-manage-storage-shares.md#manage-available-space). 

    Chcete-li zkontrolovat dostupné místo v **oblast správy** vyberte oblast, kterou chcete prozkoumat a potom přejděte na **zprostředkovatelé prostředků** > **úložiště**.

    ![Zkontrolujte místo úložiště](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Otevřete Azure Marketplace zásobníku a připojit se k Azure. Chcete-li to provést, vyberte **Marketplace správu**a potom vyberte **přidat z Azure**.

    ![Přidat z Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Portál zobrazí seznam položek, které jsou k dispozici ke stažení z Azure Marketplace. Kliknutím na každé položky k zobrazení jeho popis a další informace o tom, včetně jeho velikost stahování. 

    ![Seznam Marketplace.](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Vyberte položku a pak vyberte **Stáhnout**. Stahování se liší.

    ![Stažení zprávy](media/azure-stack-download-azure-marketplace-item/image04.png)

    Po dokončení stahování, můžete nasadit novou položku marketplace jako operátor zásobník Azure nebo uživatele.

5. Chcete-li nasadit stažené položku, vyberte **+ nový**a potom vyhledejte mezi kategorie pro novou položku marketplace. Potom vyberte položku k zahájení procesu nasazení. Proces se liší u položky různých marketplace. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odpojení nebo částečně připojené scénáře

Pokud zásobník Azure je v odpojeném režimu a bez připojení k Internetu, můžete použít prostředí PowerShell a *marketplace syndikace nástroj* ke stažení položky marketplace na počítač s připojením k Internetu. Potom přeneste položky do prostředí Azure zásobníku. V odpojeném prostředí nelze stáhnout položky marketplace pomocí portálu Azure zásobníku. 

Nástroj syndikace marketplace mohou sloužit také v případě připojené. 

Existují tento scénář se skládá ze dvou částí:
- **Část 1:** stáhnout z webu Azure Marketplace. Na počítači s přístupem k Internetu nakonfigurovat prostředí PowerShell, stáhněte si nástroj syndikace a pak stáhnout formuláře položky v Azure Marketplace.  
- **Část 2:** nahrát a publikovat do Azure Marketplace zásobníku. Přesunutí souborů, které jste stáhli do prostředí Azure zásobníku, importujte je do Azure zásobníku a publikovat je do Azure Marketplace zásobníku.  


### <a name="prerequisites"></a>Požadavky
- Musí být nasazením Azure zásobníku [registrované v Azure](azure-stack-register.md).  

- Počítač, který má připojení k Internetu, musí mít **modulu PowerShell zásobník Azure verze 1.2.11** nebo vyšší. Pokud ještě není přítomný, [nainstalovat konkrétní moduly Powershellu zásobník Azure](azure-stack-powershell-install.md).  

- Povolit import položku stažené marketplace [prostředí PowerShell pro Azure zásobníku operátor](azure-stack-powershell-configure-admin.md) musí být nakonfigurovaná.  

- Musíte mít [účet úložiště](azure-stack-manage-storage-accounts.md) v zásobníku Azure, který má veřejně přístupná kontejner (což je objekt blob úložiště). Kontejner se používá jako dočasné úložiště pro soubory marketplace položky galerie. Pokud nejste obeznámeni s účty úložiště a kontejnerů, přečtěte si téma [pracovat s objekty BLOB - portál Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) v dokumentaci k Azure.

- Při první postup je stažena nástroj syndikace marketplace. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Pomocí nástroje syndikace marketplace stáhnout položky marketplace.

1. Na počítači pro připojení k Internetu otevřete konzolu prostředí PowerShell jako správce.

2. Přidáte účet Azure, který jste použili k registraci Azure zásobníku. Chcete-li přidat účet, v prostředí PowerShell, spusťte `Add-AzureRmAccount` bez parametrů. Zobrazí se výzva k zadání přihlašovacích údajů účtu Azure a možná budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

3. Pokud máte více předplatných, vyberte tu, kterou jste použili pro registraci následujícího příkazu:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Stáhněte nejnovější verzi nástroje syndikace marketplace pomocí následující skript:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Naimportujte modul syndikace a poté spusťte nástroj spuštěním následujícího skriptu. Nahraďte *cestu k cílové složce* s umístění pro uložení souborů, můžete stáhnout z webu Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Když je nástroj spuštěn, zobrazí se výzva k zadání přihlašovacích údajů účtu Azure. Přihlaste se k účtu Azure, který jste použili k registraci Azure zásobníku. Po přihlášení úspěšné, měli byste vidět obrazovky jako na následujícím obrázku se seznamem položky marketplace. k dispozici.  

   ![Místní položky Azure Marketplace.](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Vyberte položku, kterou chcete stáhnout a poznamenejte si *verze*. (Můžete vybrat více bitových kopií tím, že se *Ctrl* klíč.) Budete odkazovat *verze* při importu položky v dalším postupu. 
   
   Můžete také filtrovat seznam bitové kopie pomocí **přidat kritéria** možnost.

8. Vyberte **OK**a pak si přečtěte a přijměte právní podmínky. 

9. Čas, který přebírá stahování závisí na velikosti položky. Po dokončení stahování, položka je k dispozici ve složce, kterou jste zadali ve skriptu. Stahování obsahuje soubor virtuálního pevného disku (pro virtuální počítače) nebo. Soubor ZIP (pro rozšíření virtuálního počítače). Zahrnuje také balíček Galerie v *.azpkg* formátu. (A *.azpkg* balíček *.zip* souboru.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Import stahování a publikovat do Azure Marketplace zásobníku
1. Soubory pro bitové kopie virtuálních počítačů nebo šablony řešení, které máte [dříve stažené](#use-the-marketplace-syndication-tool-to-download-marketplace-items) musí být k dispozici místně do prostředí Azure zásobníku.  

2. Import. Soubory virtuálního pevného disku do protokolů Azure. Chcete-li úspěšně importovat image virtuálního počítače (VM), musíte mít následující informace o virtuálním počítači:
   - *Verze*, jak jsme uvedli v kroku 7 v předchozím postupu.
   - Hodnoty pro virtuální počítače *vydavatele*, *nabízejí*, a *sku*. K získání těchto hodnot, přejmenujte kopii **.azpkg** soubor a změnit jeho přípona souboru pro **.zip**. Potom můžete v textovém editoru otevřete **DeploymentTemplates\CreateUiDefinition.json**. V soubor .json, vyhledejte *elementu imageReference* část, která obsahuje tyto hodnoty pro položku marketplace. Následující příklad ukazuje, jak se zobrazí tyto informace:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Import bitovou kopii do zásobníku Azure pomocí **přidat AzsPlatformimage** rutiny. Při použití této rutiny, nezapomeňte nahradit *vydavatele*, *nabízejí*a další hodnoty parametrů s hodnotami bitové kopie, který chcete importovat. Můžete získat *vydavatele*, *nabízejí*, a *sku* hodnoty bitové kopie z textového souboru, který je stáhnout spolu s AZPKG soubor a uložit v cílovém umístění . 

   V následujícím příkladu skriptu použijí se hodnoty pro Windows Server 2016 Datacenter - jádra serveru virtuálního počítače. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **O šablonách řešení:** některé šablony může obsahovat malá 3 MB. Soubor virtuálního pevného disku s názvem **fixed3.vhd**. Nemusíte importovat tento soubor do protokolů Azure. Fixed3.VHD.  Tento soubor je součástí některé šablony řešení ke splnění publikování požadavky pro Azure Marketplace.

   Přečtěte si popis šablony a stáhněte a pak importovat další požadavky, jako jsou virtuální pevné disky jsou nutné k práci s šablona řešení.

3. Pomocí portálu pro správu pro nahrání balíčku pro položku marketplace (.azpkg soubor) do úložiště objektů Blob Azure zásobníku. Nahrávání balíčku ji zpřístupní k zásobník Azure, můžete později publikovat položky zásobníku Azure Marketplace.

   Nahrávání vyžaduje, abyste tak, aby měl účet úložiště s veřejně přístupná kontejner (viz požadavky pro tento scénář)   
   1. Na portálu správy Azure zásobníku přejít na **další služby** > **účty úložiště**.  
   
   2. Vyberte účet úložiště, ze svého předplatného a pak v části **služby objektů BLOB**, vyberte **kontejnery**.  
      ![Služby objektů BLOB](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Vyberte kontejner, kterou chcete použít a potom vyberte **nahrát** otevřete **nahrávání blob** podokně.  
      ![kontejner](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. V podokně nahrání objektu blob Procházet soubory, které chcete načíst do úložiště a pak vyberte **nahrát**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Soubory můžete nahrávat na server se zobrazí v podokně kontejneru. Vyberte soubor a zkopírujte adresu URL z **Blob vlastnosti** podokně. Tato adresa URL budete používat v dalším kroku, při importu položky marketplace do protokolů Azure.  Na následujícím obrázku je kontejner *úložiště objektů blob test* a soubor je *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Adresa URL je soubor *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Vlastnosti objektu BLOB](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Pomocí prostředí PowerShell publikovat položku marketplace. zásobník Azure pomocí **přidat AzsGalleryItem** rutiny. Příklad:  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Po publikování položky galerie, můžete zobrazit z **další služby** > **Marketplace**.  Pokud stahování šablonu řešení, nezapomeňte že přidat všechny závislé image virtuálního pevného disku pro danou šablonu řešení.  
  ![Zobrazení marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> S vydáním Azure PowerShell zásobníku 1.3.0 nyní můžete přidat rozšíření virtuálního počítače.

Příklad:

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Další postup
[Vytvoření a publikování položku Marketplace.](azure-stack-create-and-publish-marketplace-item.md)
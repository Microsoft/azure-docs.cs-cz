---
title: Stažení položky marketplace z Azure | Microsoft Docs
description: Položky marketplace lze stáhnout z Azure do nasazení Moje zásobník Azure.
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
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 9f24dd917f4197f933fd58f7c646c18372da8593
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Stažení položky marketplace z Azure do Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*


Jak se rozhodnete, který obsah, který chcete zahrnout do vaší zásobník Azure marketplace, měli byste zvážit obsah dostupný v Azure Marketplace. Můžete stáhnout ze seznamu spravovaných položek Azure marketplace, které byly předem otestované ke spuštění v Azure zásobníku. Často přidání nových položek do tohoto seznamu, takže nezapomeňte zaškrtnout zpět pro nový obsah.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Stažení položky marketplace ve scénáři připojené (s připojením k Internetu)

1. Chcete-li stáhnout položky marketplace, je nutné nejprve [zaregistrovat zásobník Azure s Azure](azure-stack-register.md).
2. Přihlaste se k portálu správce Azure zásobníku (pro ASDK, použijte https://portal.local.azurestack.external).
3. Některé položky marketplace. může být velký. Zkontrolujte a ujistěte se, zda máte dostatek místa v systému klepnutím na tlačítko **zprostředkovatelé prostředků** > **úložiště**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Klikněte na tlačítko **další služby** > **Marketplace správu**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Klikněte na tlačítko **přidat z Azure** zobrazíte seznam položek, které jsou k dispozici ke stažení. Kliknutím na každou položku v seznamu k zobrazení jeho popis a velikost souboru ke stažení.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Vyberte položku v seznamu a pak klikněte na **Stáhnout**. Image virtuálního počítače pro položku vybranou spustí ke stažení. Stahování se liší.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Po dokončení stahování, můžete nasadit novou položku marketplace jako operátor zásobník Azure nebo uživatele. Klikněte na tlačítko **+ nový**, hledání mezi kategorie pro novou položku marketplace. a potom vyberte položku.
7. Klikněte na tlačítko **vytvořit** otevřete vytváření prostředí pro nově stažených položek. Podrobné pokyny k nasazení vaší položky.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Stáhnout položky marketplace v odpojený nebo částečně připojené scénář (s omezenou připojení k Internetu)

Když nasadíte Azure zásobníku v odpojeném režimu (bez žádné připojení k Internetu), nelze stáhnout položky marketplace pomocí portálu Azure zásobníku. Můžete však použít nástroj syndikace marketplace ke stažení položky marketplace pro počítač, který má připojení k Internetu a potom přenést do prostředí Azure zásobníku.

### <a name="prerequisites"></a>Požadavky
Před použitím nástroje syndikace marketplace, ujistěte se, že máte [zaregistrován zásobník Azure s předplatným Azure](azure-stack-register.md).  

Z počítače, který má připojení k Internetu použijte následující postup ke stažení položky marketplace. vyžaduje:

1. Otevřete konzolu prostředí PowerShell jako správce a [nainstalovat konkrétní moduly Powershellu zásobník Azure](azure-stack-powershell-install.md). Ujistěte se, že instalujete **modulu PowerShell zásobník Azure verze 1.2.11 nebo vyšší**.  

2. Přidáte účet Azure, který jste použili k registraci Azure zásobníku. Chcete-li přidat účet, spusťte **Add-AzureRmAccount** rutiny bez parametrů. Zobrazí se výzva k zadání přihlašovacích údajů účtu Azure a možná budete muset použít 2 ověřování na základě konfigurace vašeho účtu.  

3. Pokud máte více předplatných, vyberte tu, kterou jste použili pro registraci následujícího příkazu:  

   ```powershell
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
   cd \AzureStack-Tools-master

   ```

5. Naimportujte modul syndikace a spusťte nástroj spuštěním následujících příkazů:  

   ```powershell
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Když je nástroj spuštěn, zobrazí se výzva k zadání přihlašovacích údajů účtu Azure. Přihlaste se k účtu Azure, který jste použili k registraci Azure zásobníku. Po přihlášení úspěšné, měli byste vidět následující obrazovka s seznamu položky marketplace. k dispozici.  

   ![Místní položky Azure Marketplace.](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Vyberte bitovou kopii, kterou chcete stáhnout a poznamenejte si verzi obrázku. Tím, že podržíte klávesu Ctrl můžete vybrat více bitových kopií. Používáte verzi obrázku pro import bitovou kopii v další části.  Klikněte na tlačítko **Ok**a pak přijměte právní podmínky kliknutím na **Ano**. Můžete také filtrovat seznam bitové kopie pomocí **přidat kritéria** možnost. 

   Stahování trvá několik minut v závislosti na velikosti obrázku. Po stažení bitové kopie a je k dispozici v cílové cestě, která jste zadali dříve. Stahování obsahuje soubor virtuálního pevného disku (pro virtuální počítače) nebo. Soubor ZIP (pro rozšíření virtuálního počítače) a položky galerie v Azpkg formátu.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Importovat image a publikujete ho v zásobníku Azure marketplace
Existují tři různé typy položek na webu Marketplace: virtuálních počítačů, rozšíření virtuálního počítače a šablony řešení. Šablony řešení jsou popsané dále.
> [!NOTE]
> Rozšíření virtuálního počítače nelze přidat do Azure zásobníku v tuto chvíli.

1. Po stažení balíčku bitové kopie a galerie, uložte je a obsah ve složce AzureStack. nástroje pro hlavní na vyměnitelnou diskovou jednotku a zkopírujte jej do prostředí Azure zásobníku (můžete ji zkopírovat místně do libovolného umístění, jako například: "C:\MarketplaceImages").     

2. Před importem bitovou kopii, je nutné připojit k Azure zásobníku operátor prostředí pomocí kroků popsaných v [nakonfigurovat prostředí PowerShell Azure zásobníku operátor](azure-stack-powershell-configure-admin.md).  

3. Pokud stahování zahrnuty malý soubor 3MB virtuální pevný disk s názvem fixed3.vhd, je šablona řešení. Tento soubor není potřeba; přejděte ke kroku 5. Zajistěte, aby že si stáhnout všechny závislé položky, které je uvedené v popisu pro stahování.

4. Pomocí rutiny Add-AzsVMImage importujte bitovou kopii do protokolů Azure. Při použití této rutiny, nezapomeňte nahradit *vydavatele*, *nabízejí*a další hodnoty parametrů s hodnotami bitové kopie, který chcete importovat. Můžete získat *vydavatele*, *nabízejí*, a *sku* hodnoty bitové kopie z objektu elementu imageReference Azpkg souboru, který jste předtím stáhli a  *verze* hodnotu z kroku 6 v předchozí části.

Pokud chcete najít elementu imageReference, budete muset přejmenovat soubor AZPKG s. ZIP rozšíření, rozbalte ho do dočasného umístění a otevřete soubor DeploymentTemplates\CreateUiDefinition.json v textovém editoru. Najděte v této části:

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Nahraďte hodnoty parametrů a spusťte následující příkaz:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

5. Použití portálu k nahrání vaší položku Marketplace. (. Azpkg) do úložiště objektů Blob Azure zásobníku. Můžete nahrát do místního úložiště zásobník Azure nebo nahrát do Azure Storage. (Je dočasné umístění balíčku.) Ujistěte se, že objekt blob je veřejně přístupný a poznamenejte si identifikátor URI.  

6. Publikovat položku marketplace. zásobník Azure pomocí **přidat AzsGalleryItem**. Příklad:

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

7. Po publikování položky galerie, můžete ji z zobrazit **nový** > **Marketplace** podokně. Pokud stahování byla šablona řešení, ujistěte se, že jste si stáhli také závislé image virtuálního pevného disku.

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Další postup

[Vytvoření a publikování položku Marketplace.](azure-stack-create-and-publish-marketplace-item.md)

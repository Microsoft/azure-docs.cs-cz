---
title: Použijte sadu nástrojů Marketplace vytvoření a publikování položky marketplace. | Microsoft Docs
description: Zjistěte, jak rychle vytvořit položky marketplace s publikování Toolkit
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61ee3296429f9641643f1c9268ae89e3691fcfa1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386873"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Přidat publikování nástroji položky marketplace.
Přidání svůj obsah do [Azure Marketplace zásobníku](azure-stack-marketplace.md) zpřístupní řešení pro vás i vaši klienti pro nasazení.  Sada nástrojů Marketplace vytvoří soubory balíčků Azure Marketplace (.azpkg) na základě šablony správce prostředků Azure IaaS nebo rozšíření virtuálního počítače.  Sada nástrojů Marketplace můžete použít také k publikování .azpkg soubory vytvořené pomocí nástroje nebo pomocí [ruční](azure-stack-create-and-publish-marketplace-item.md) kroky.  Toto téma vás provede stažení nástroje, vytvoření položky marketplace na základě šablony virtuálního počítače a poté publikujete daná položka pro Azure Marketplace zásobníku.     


## <a name="prerequisites"></a>Požadavky
 - Musíte spustit sadu nástrojů na hostiteli zásobník Azure nebo mít [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) připojení z počítače, na kterém jste spustili nástroj.

 - Stažení [šablony Azure Quickstart zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) a extrahovat.

 - Stažení [nástroj Azure Galerie balení](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Publikování na webu marketplace vyžaduje ikony a soubor miniatur.  Můžete použít vlastní nebo Uložit [ukázka](azure-stack-marketplace-publisher.md#support-files) soubory místně pro tento příklad.

## <a name="download-the-tool"></a>Stáhněte si nástroj
Může být Marketplace Toolkit [stažené z nástroje Azure zásobníku úložiště](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Vytvoření položky marketplace.
V této části použijete k vytvoření balíčku pro položku marketplace ve formátu .azpkg Marketplace Toolkit.  

### <a name="provide-marketplace-information-with-wizard"></a>Zadejte informace marketplace pomocí Průvodce
1. Spuštění nástrojů Marketplace z relace prostředí PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Klikněte **řešení** kartě.  Tato obrazovka přijímá informace o vaší položku marketplace. Zadejte informace o vaší položky, jak se bude zobrazovat v marketplace.  Můžete také zadat [soubor parametrů](azure-stack-marketplace-publisher.md#use-a-parameters-file) předem formuláře.  
    
    ![snímek obrazovky první obrazovce Toolkit Marketplace.](./media/azure-stack-marketplace-publisher/image7.png)
3. Klikněte na tlačítko **Procházet** a vyberte soubor obrazu pro každé pole ikonu a snímek.  Můžete použít vlastní ikony nebo ukázka ikony v [podpůrných souborů](azure-stack-marketplace-publisher.md#support-files) části.
4. Po zaplnění všech polí, vyberte "Preview řešení" pro náhled řešení v Marketplace.  Můžete zkontrolovat a upravit text, obrázky a snímek před kliknutím na tlačítko **Další**.  

### <a name="import-template-and-create-package"></a>Import šablony a vytvoření balíčku
V této části import šablony a pracovat s vstup pro vaše řešení.

1.  Klikněte na tlačítko **Procházet** a vyberte *azuredeploy.json* ze složky 101jednoduché-Windows-virtuálních počítačů v šablonách stažené.

    ![snímek obrazovky druhý Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image8.png)
2.  Se zobrazí v Průvodci nasazením *základní* krok a vstup položky pro všechny parametry zadané v šabloně.  Můžete přidat další kroky a přesunout vstupy mezi kroky.  Například můžete chtít "Konfigurace Front-End" a "Konfigurace Back-End" kroky pro vaše řešení.
3.  Zadejte cestu k AzureGalleryPackager.exe.  
4.  Klikněte na tlačítko **vytvořit** a sady nástrojů Marketplace balíčků řešení do souboru .azpkg.  Po dokončení Průvodce zobrazuje cestu k souboru řešení a získáte možnost pokračovat v publikování vašeho balíčku do protokolů Azure.


## <a name="publish-marketplace-items"></a>Publikování položky marketplace.
V této části publikujete položku marketplace. vaše Azure Marketplace zásobníku.

![snímek obrazovky první obrazovce Toolkit Marketplace.](./media/azure-stack-marketplace-publisher/image9.png)

1.  Průvodce vyžaduje informace k publikování řešení:
    
    |Pole|Popis|
    |-----|-----|
    | Jméno správce služby | Účet správce služby.  Příklad:  ServiceAdmin@mydomain.onmicrosoft.com |
    | Heslo | Heslo pro účet správce služby. |
    | Koncový bod rozhraní API | Koncový bod Azure zásobník Azure Resource Manager.  Příklad: management.local.azurestack.external |
2.  Klikněte na tlačítko **publikovat** a zobrazí se protokol publikování.
3.  Je nyní možné nasadit publikované položky prostřednictvím portálu Azure zásobníku.


## <a name="use-a-parameters-file"></a>Použít soubor parametrů
Soubor parametrů můžete použít také k dokončení informací položky marketplace.  

Zahrnuje sadu nástrojů Marketplace *solution.parameters.ps1* můžete použít k vytvoření souboru parametrů.


## <a name="support-files"></a>Soubory podpory aplikace
| Popis | Ukázka |
| ----- | ----- |
| ikona .png 40 x 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| ikona .png 90 x 90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| ikona .png 115 x 115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255x115 .png icon | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 .png miniaturu | ![](./media/azure-stack-marketplace-publisher/image5.png) |



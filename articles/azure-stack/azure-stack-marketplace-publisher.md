---
title: Použijte sadu nástrojů Marketplace k vytvoření a publikování položky marketplace | Dokumentace Microsoftu
description: Zjistěte, jak rychle vytvořit pomocí Toolkit publikování položky marketplace
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 98a7ae31aeeae29b81a1053b38992d3bc4e0e03f
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085305"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Přidat pomocí nástroje pro publikování položky marketplace

Přidání obsahu [Azure Stack Marketplace](azure-stack-marketplace.md) zpřístupní řešení pro vás a vašich klientů pro nasazení. Sada nástrojů Marketplace vytvoří soubory balíčku (.azpkg) Azure Marketplace na základě šablony správce prostředků Azure IaaS nebo rozšíření virtuálních počítačů. Marketplace toolkit můžete také použít k publikování .azpkg soubory vytvořené pomocí nástroje nebo pomocí [ruční](azure-stack-create-and-publish-marketplace-item.md) kroky. Toto téma popisuje stažení nástroje, vytvoření položky marketplace na základě šablony virtuálního počítače a pak tuto aplikaci publikovat tuto položku na Azure Marketplace zásobníku.     

## <a name="prerequisites"></a>Požadavky

 - Musíte spustit sadu nástrojů na hostiteli služby Azure Stack nebo mít [VPN](.\asdk\asdk-connect.md#connect-with-vpn) připojení k hostiteli ASDK z počítače, na kterém jste nástroj spustili.

 - Stáhněte si [šabloně QuickStart pro Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) a extrahovat.

 - Stáhněte si [nástroje balení Galerie Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Publikování na webu Marketplace vyžaduje ikony a souboru s miniaturami. Můžete použít vlastní nebo Uložit [ukázka](azure-stack-marketplace-publisher.md#support-files) soubory místně pro účely tohoto příkladu.

## <a name="download-the-tool"></a>Stáhněte si nástroj

Sada nástrojů Marketplace si můžete stáhnout [z úložiště Azure Stack nástroje](azure-stack-powershell-download.md).

##  <a name="create-marketplace-items"></a>Vytvoření položky marketplace

V této části použijete k vytvoření balíčku pro položku marketplace ve formátu .azpkg Marketplace toolkit.  

### <a name="provide-marketplace-information-with-wizard"></a>Zadejte informace z marketplace pomocí Průvodce

1. Spuštění nástrojů Marketplace z relace prostředí PowerShell:
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. Klikněte na tlačítko **řešení** kartu. Tato obrazovka přijímá informace o vaší položce marketplace. Zadejte informace o vaší položce, jak chcete, aby se zobrazí na webu Marketplace. Můžete také určit [soubor parametrů](azure-stack-marketplace-publisher.md#use-a-parameters-file) předem formuláře.  
    
    ![snímek obrazovky s první obrazovka Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image7.png)
3. Klikněte na tlačítko **Procházet** a vyberte soubor obrázku pro každé pole, ikona a snímku obrazovky. Můžete použít vlastní ikony nebo ikony ukázka v [podpůrných souborů](azure-stack-marketplace-publisher.md#support-files) oddílu.
4. Po zaplnění všech polí, vyberte "Ve verzi Preview řešení" pro verzi preview řešení na webu Marketplace. Můžete zkontrolovat a upravit text, obrázky a snímku obrazovky před kliknutím na tlačítko **Další**.  

### <a name="import-template-and-create-package"></a>Import šablony a vytvoření balíčku

V této části importovat šablonu a pracovat se vstupem pro vaše řešení.

1.  Klikněte na tlačítko **Procházet** a vyberte *azuredeploy.json* ze složky 101-Simple-Windows-VM v stažené šablony.

    ![snímek obrazovky s druhou obrazovku Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image8.png)
2.  Průvodce nasazením se vyplní *základní* krok a vstupní položky pro každý parametr v šabloně zadané. Můžete přidat další kroky a přesuňte vstupy mezi kroky. Například můžete chtít "Konfigurace front-endový" a "Konfigurace Back-End" kroky pro vaše řešení.
3.  Zadejte cestu k AzureGalleryPackager.exe.  
4.  Klikněte na možnost **Vytvořit**. Marketplace toolkit balíčky do souboru .azpkg vašeho řešení. Po dokončení Průvodce zobrazí cestu k souboru řešení a poskytnout možnost pokračovat v publikování balíčku do služby Azure Stack.

## <a name="publish-marketplace-items"></a>Publikování položky marketplace

V této části publikování položky marketplace na Azure Marketplace zásobníku.

![snímek obrazovky s první obrazovka Marketplace Toolkit](./media/azure-stack-marketplace-publisher/image9.png)

1.  Průvodce vyžaduje informace o publikování řešení:
    
    |Pole|Popis|
    |-----|-----|
    | Jméno správce služby | Účet správce služeb.  Příklad: ServiceAdmin@mydomain.onmicrosoft.com |
    | Heslo | Heslo pro účet správce služeb. |
    | Koncový bod rozhraní API | Koncový bod Azure stacku Azure Resource Manageru. Příklad: management.local.azurestack.external |
2.  Klikněte na tlačítko **publikovat** a protokolu publikování se zobrazí.
3.  Teď mají možnost nasadit položky publikované prostřednictvím portálu Azure Stack.

## <a name="use-a-parameters-file"></a>Použít soubor parametrů

K dokončení informací položky marketplace můžete také použít soubor parametrů.  

Zahrnuje sadu nástrojů Marketplace *solution.parameters.ps1* můžete použít k vytvoření souboru parametrů.

## <a name="support-files"></a>Soubory podpory

| Popis | Ukázka |
| ----- | ----- |
| Ikona ve formátu PNG 40 x 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| Ikona ve formátu PNG 90 x 90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| Ikona 115 x 115 PNG | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| Ikona 255 x 115 PNG | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| Miniatura 533 x 324 PNG | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>Další postup

[Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
---
title: Chyba správce spouštění systému Windows – hodnota 0xC0000428 neplatné hodnoty hash obrázku
titlesuffix: Azure Virtual Machines
description: Tento článek popisuje kroky pro řešení problémů, ve kterých se použil obrázek verze Preview, a zkušební období vypršelo, což zabrání spuštění virtuálního počítače Azure (VM).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: 22db9c7966b6f988ca0ea799104275f3f86c77ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629568"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Chyba správce spouštění systému Windows – hodnota 0xC0000428 neplatné hodnoty hash obrázku

Tento článek popisuje kroky pro řešení problémů, ve kterých se použil obrázek verze Preview, a zkušební období vypršelo, což zabrání spuštění virtuálního počítače Azure (VM).

## <a name="symptom"></a>Příznak

Když použijete [diagnostiku spouštění](./boot-diagnostics.md) k zobrazení snímku obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje správce spouštění systému Windows s touto zprávou:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![Obrázek 1: zobrazí správce spouštění systému Windows se stavem "Ox0000428" a informacemi "systém Windows nemůže ověřit digitální podpis tohoto souboru".](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

nebo zpráva:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![Obrázek 2: zobrazí správce spouštění systému Windows se stavem "Ox0000428" a informace "digitální podpis tohoto souboru nelze ověřit".](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Příčina

Image, která se použila k vytvoření virtuálního počítače, byla image verze Preview s datem vypršení platnosti, ne s imagí RTM (Release to Manufacturing). 

Image ve verzi Preview mají stanovený životní cyklus a snímek obrazovky, který vidíte, se zobrazí, když předáte datum vypršení platnosti, což znamená, že zkušební verze Image je mimo.

### <a name="example-of-preview-images"></a>Příklad obrázků ve verzi Preview

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Datum ukončení platnosti image verze Preview není možné oddálit. Po vypršení platnosti verze Preview už virtuální počítač nebude možné spustit.

- V závislosti na produktu se zkušební období může lišit. Například image Windows Preview mají zkušební období 180.

- V Azure všechny image pro Windows, ve kterých jsou verze Preview, obsahují poznámku v jejich popisu, že nejsou určené pro produkční prostředí a že jsou k dispozici pro použití jenom pro zadanou zkušební dobu nebo jako verzi Preview.

## <a name="solution"></a>Řešení

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Pokud se jedná o image ve verzi Preview, neexistuje žádný způsob, jak pro použitou image rozšíříte datum vypršení platnosti, budete muset [nasadit nový virtuální počítač](../windows/quick-create-portal.md) pomocí Image, která není ve verzi Preview. Následující postup vám pomůže zjistit, jestli jste použili bitovou kopii verze Preview, a poskytnout prostředky, které vám pomůžou při přenosu dat z tohoto virtuálního počítače do nového virtuálního počítače. Pokud jste image označili jako image ve verzi Preview, image se nedá obnovit, protože už vypršela její platnost.

V závislosti na vaší předvolbě můžete k dotazování na Image použít buď Azure PowerShell nebo Azure CLI, abyste zjistili, jestli se jedná o image ve verzi Preview. Pomocí těchto příkazů můžete ověřit, že se jedná o obrázek verze Preview.

### <a name="query-using-azure-powershell"></a>Dotaz pomocí Azure PowerShell

1. Otevřete aplikaci Windows PowerShell.
1. Spusťte následující příkazy:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- V předchozích příkazech nahraďte `<LOCATION>` , `<PUBLISHER NAME>` , `<OFFER NAME>` a `<YEAR WHEN THIS IMAGE WAS RELEASED>` informacemi uvedenými. Odstraňte také symboly "<" a ">".

  Prohlédněte si následující příklad:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Dotazování pomocí Azure CLI

1. Pokud jste to ještě neudělali, budete muset [Azure CLI nainstalovat](/cli/azure/install-azure-cli).
1. Po stažení použijte příkazový řádek nebo PowerShell k zadání `az login` příkazu a pak se přihlaste pomocí přihlašovacích údajů k účtu.
1. Po přihlášení zadejte následující příkazy:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- V předchozích příkazech nahraďte `<LOCATION>` , `<PUBLISHER NAME>` , `<OFFER NAME>` a `<YEAR WHEN THIS IMAGE WAS RELEASED>` informacemi uvedenými. Odstraňte také symboly "<" a ">".

  Prohlédněte si následující příklad:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```
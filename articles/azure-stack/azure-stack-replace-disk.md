---
title: Nahraďte fyzický disk v zásobníku Azure | Microsoft Docs
description: Popisuje proces pro jak nahradit fyzický disk v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: f168c005c729ae75a5369b80b3dc5eab03ee0243
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Nahraďte fyzický disk v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Tento článek popisuje obecný postup k nahrazení fyzický disk v zásobníku Azure. Pokud fyzický disk selže, měli byste ho nahradit co nejdříve.

Tento postup můžete použít pro integrované systémy a pro vývoj kit nasazení, které mají za provozu disky.

Nahrazení skutečný disk, který pokyny se budou lišit podle výrobce (OEM) dodavatele hardwaru. Dokumentaci od dodavatele pole replaceable jednotka (FRU) podrobné kroky, které jsou specifické pro systém. 

## <a name="review-disk-alert-information"></a>Zkontrolujte výstrahy informace o disku
Když disk selže, obdržíte výstrahu, která vám ukáže, že připojení bylo ztraceno fyzického disku. 

 ![Výstrahy zobrazuje připojení ztratilo fyzického disku](media/azure-stack-replace-disk/DiskAlert.png)

Pokud otevřete výstrahu, popis výstrahy obsahuje uzel jednotek škálování a umístění přesnou pozici fyzického disku, který je třeba nahradit. Další Azure zásobníku pomáhá identifikovat pomocí možnosti indikátor LED selhání disku.

 ## <a name="replace-the-disk"></a>Výměna disku

Postupujte podle pokynů dodavatele hardwaru OEM FRU určena k nahrazení skutečný disk.

> [!note]
> Nahradí disky pro jeden uzel jednotky škálování najednou. Počkejte na dokončení než budete pokračovat na další uzel jednotky škálování úloh oprava virtuálního disku

Aby se zabránilo použití nepodporované disku v integrovaném systému, bude systém blokovat disky, které nejsou podporované od dodavatele. Pokud se pokusíte použít nepodporovaný disk, nová výstraha se zprávou, že disk je z důvodu nepodporovaný model nebo firmware v karanténě.

Po nahradíte disku, zásobník Azure automaticky zjistí nový disk a spustí proces opravy virtuálního disku.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Zkontrolujte stav oprava virtuálního disku
 
 Po nahradíte disk, můžete sledovat stav virtuálního disku a opravit průběh úlohy pomocí privilegované koncový bod. Z libovolného počítače, který má síťové připojení ke koncovému bodu privilegované podle těchto kroků.

1. Otevřete relaci prostředí Windows PowerShell a připojte se do privilegované koncového bodu.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Spusťte následující příkaz k zobrazení stavu virtuální disk:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Výstup prostředí PowerShell Get-VirtualDisk příkazu](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Spusťte následující příkaz, chcete-li zobrazit aktuální stav úlohy úložiště:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Výstup prostředí PowerShell Get-StorageJob příkazu](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Řešení potíží s oprava virtuálního disku

Pokud oprava virtuálního disku úlohy se zobrazí zablokované, pomocí následujícího příkazu restartujete úlohu:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 

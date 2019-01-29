---
title: Nahraďte fyzický disk ve službě Azure Stack | Dokumentace Microsoftu
description: Popisuje proces pro nahrazení fyzický disk ve službě Azure Stack.
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
ms.date: 01/22/2019
ms.author: mabrigg
ms.openlocfilehash: b1fb1166e05eba209eddf72d97d20011c9ee4b78
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102999"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Nahraďte fyzický disk ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek popisuje obecný postup k nahrazení fyzický disk ve službě Azure Stack. Pokud fyzický disk selže, by ho nahradit co nejdříve.

Tento postup můžete použít pro integrované systémy a pro nasazení development kit, které mají disky vyměnitelné za provozu.

Výměna skutečné disku, které kroky se budou lišit podle dodavatele hardwaru, výrobce OEM (OEM). Dokumentaci od dodavatele pole vyměnitelná jednotka (FRU) podrobné pokyny, které jsou specifické pro váš systém.

## <a name="review-disk-alert-information"></a>Projděte si informace o výstrahách disku
Pokud některý disk selže, obdržíte výstrahu, která říká, že připojení bylo ztraceno fyzický disk.

 ![Fyzický disk ke ztrátě připojení k zobrazení výstrah](media/azure-stack-replace-disk/DiskAlert.png)

Pokud otevřete výstrahy, popis výstrahy obsahuje uzel jednotek škálování a slotu přesné fyzické umístění disku, který je třeba nahradit. Další Azure Stack pomáhá identifikovat pomocí možnosti indikátor LED selhání disku.

 ## <a name="replace-the-disk"></a>Výměna disku

Postupujte podle pokynů výrobce OEM dodavatele hardwaru FRU určena k nahrazení skutečné disku.

> [!note]
> Nahraďte disky pro jeden uzel jednotek škálování v čase. Počkejte na dokončení před přechodem na další uzel jednotek škálování úloh oprava virtuálního disku

Zabránit používání nepodporované disku v integrovaný systém, bude systém blokovat disky, které nejsou podporovány od dodavatele. Pokud se pokusíte použít nepodporovaný disk, nová výstraha se říká, že disk má byl umístěn do karantény z důvodu nepodporované modelu, nebo firmware.

Po vyměňujete disk, Azure Stack automaticky zjistí nový disk a spustí proces opravy virtuálního disku.
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Kontrola stavu oprava virtuálního disku
 
 Po vyměňujete disk, můžete sledovat stav virtuálního disku a opravit průběh úlohy pomocí privilegovaných koncový bod. Z libovolného počítače, který má síťové připojení ke koncovému bodu privileged postupujte podle těchto kroků.

1. Otevřete relaci Windows Powershellu a připojte se k privilegovaným koncový bod.
    ```PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Spuštěním následujícího příkazu zobrazíte stav virtuálního disku:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![Výstup prostředí PowerShell Get-VirtualDisk příkazu](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Spuštěním následujícího příkazu zobrazíte aktuální stav úlohy úložiště:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Výstup prostředí PowerShell Get-StorageJob příkazu](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Řešení potíží s oprava virtuálního disku

Pokud oprava virtuálního disku úlohy se zobrazí zablokované, spuštěním následujícího příkazu restartujete úlohu:
  ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 

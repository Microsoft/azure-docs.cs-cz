---
title: Zálohování Azure zásobníku | Microsoft Docs
description: Proveďte zálohu na vyžádání v zásobníku Azure se zálohováním na místě.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-azure-stack"></a>Zálohování Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Proveďte zálohu na vyžádání v zásobníku Azure se zálohováním na místě. Pokud potřebujete aktivovat službu zálohování infrastruktury, najdete v části [povolit zálohování pro zásobník Azure z portálu pro správu](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Pokyny ke konfiguraci prostředí PowerShell najdete v tématu [instalaci prostředí PowerShell pro Azure zásobníku ](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Spustit zálohování Azure zásobníku

Otevřete prostředí Windows PowerShell se v prostředí správy operátor řádku se zvýšenými oprávněními a spusťte následující příkazy:

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Potvrďte zálohování v portálu pro správu bylo dokončeno

1. Otevřete na portálu správy Azure zásobníku na [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Vyberte **další služby** > **infrastruktura zálohování**. Zvolte **konfigurace** v **infrastruktura zálohování** okno.
3. Najít **název** a **datum dokončení** zálohy v **dostupné zálohy** seznamu.
4. Ověřte **stavu** je **úspěšné**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>Další postup

- Další informace o pracovním postupu pro obnovení z před událostí ztráty dat. V tématu [zotavit závažné ztráty dat](azure-stack-backup-recover-data.md).

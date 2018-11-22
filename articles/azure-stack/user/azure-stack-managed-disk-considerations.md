---
title: Rozdíly a aspekty u služby Managed Disks ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o rozdíly a aspekty při práci se službou Managed Disks v Azure stacku.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 4bd36744cc417e85f49e58f9a08d2b9006da9fe4
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284025"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Managed Disks zásobníku: Rozdíly a aspekty
Tento článek shrnuje známé rozdíly mezi Azure Stack Managed Disks a Managed Disks pro Azure. Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

Spravované disky zjednodušují správu disků pro virtuální počítače IaaS pomocí správy [účty úložiště](/azure/azure-stack/azure-stack-manage-storage-accounts) přidružené k diskům virtuálních počítačů.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Tahák: spravovaného disku rozdíly

| Funkce | Azure (globální) | Azure Stack |
| --- | --- | --- |
|Šifrování pro neaktivní uložená Data |Šifrování služby Azure Storage (SSE), Azure Disk Encryption (ADE)     |Šifrování AES 128-bit nástroje BitLocker      |
|Image          | Podpora spravovanou vlastní image |Není dosud podporován.|
|Možnosti zálohování |Podpora služby Azure Backup |Není dosud podporován. |
|Možnosti zotavení po havárii |Podpora Azure Site Recovery |Není dosud podporován.|
|Typy disků     |Premium SSD, SSD na úrovni Standard (Preview) a Standard pevný disk |Premium SSD, Standard HDD |
|Disky Premium  |Plně podporované. |Je možné zřídit, ale bez omezení výkonu nebo záruk  |
|Prémiové disky vstupně-výstupních operací  |Závisí na velikosti disku  |2300 IOPs na disk |
|Propustnost disků úrovně Premium |Závisí na velikosti disku |145 MB za sekundu na disk |
|Velikost disku  |Disk Azure typu Premium: P4 (32 GB) na P80 (32 TB)<br>Disk Azure SSD na úrovni Standard: E10 (128 GB) na E80 (32 TB)<br>Disk Azure Standard HDD: S4 (32 GB) na S80 (32 TB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GB<br>M15: 256 GB<br>M20: 512 GB<br>M30: 1024 GB |
|Výkon disků analýzy |Agregace metrik a metriky pro disk podporované |Není dosud podporován. |
|Migrace      |Poskytuje nástroj pro migraci z existující nespravované správce prostředků virtuálních počítačů Azure bez nutnosti znovu vytvořte virtuální počítač  |Není dosud podporován. |


## <a name="metrics"></a>Metriky
Existují také rozdíly pomocí metrik storage:
- Pomocí služby Azure Stack nerozlišují data transakcí v metrikách storage šířky pásma sítě interní nebo externí.
- Azure Stack data transakcí v metrikách storage neobsahuje virtuální počítač přístup k připojené disky.


## <a name="api-versions"></a>Verze rozhraní API
Služba Azure Managed Disks zásobníku podporuje následující verze rozhraní API:
- 2017-03-30


## <a name="next-steps"></a>Další postup
[Další informace o virtuálních počítačích Azure Stack](azure-stack-compute-overview.md)

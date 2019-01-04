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
ms.openlocfilehash: 4bad339ca704f14f57c120db5e731d0dbfeb8d59
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793429"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack spravované disky: Rozdíly a aspekty
Tento článek shrnuje známé rozdíly mezi Azure Stack Managed Disks a Managed Disks pro Azure. Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

Spravované disky zjednodušují správu disků pro virtuální počítače IaaS pomocí správy [účty úložiště](/azure/azure-stack/azure-stack-manage-storage-accounts) přidružené k diskům virtuálních počítačů.

> [!Note]  
> Spravované disky ve službě Azure Stack je k dispozici. 1808.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Tahák: Spravovaného disku rozdíly

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
|Velikost disku  |Disk Azure typu Premium: P4 (32 GB) na P80 (32 TB)<br>Disk Azure SSD na úrovni Standard: E10 (128 GB) na E80 (32 TB)<br>Pevný disk Azure standardní Disk: S4 (32 GB) na S80 (32 TB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GB |
|Kopie snímků disků|Pořízení snímku Azure připojené do spuštěného virtuálního počítače nepodporuje spravované disky|Není dosud podporován. |
|Výkon disků analýzy |Agregace metrik a metriky pro disk podporované |Není dosud podporován. |
|Migrace      |Poskytuje nástroj pro migraci z existující nespravované správce prostředků virtuálních počítačů Azure bez nutnosti znovu vytvořte virtuální počítač  |Není dosud podporován. |

> [!Note]  
> Spravované disky vstupně-výstupních operací a propustnosti ve službě Azure Stack je číslo zakončení místo zřízené číslo, které může vliv hardwaru a pracovní postupy spouštěné ve službě Azure Stack.


## <a name="metrics"></a>Metriky
Existují také rozdíly pomocí metrik storage:
- Pomocí služby Azure Stack nerozlišují data transakcí v metrikách storage šířky pásma sítě interní nebo externí.
- Azure Stack data transakcí v metrikách storage neobsahuje virtuální počítač přístup k připojené disky.


## <a name="api-versions"></a>Verze rozhraní API
Služba Azure Managed Disks zásobníku podporuje následující verze rozhraní API:
- 2017-03-30

## <a name="known-issues"></a>Známé problémy
Po použití 1809 aktualizace, může dojít k následujícím problémům při nasazování virtuálních počítačů se spravovanými disky:

   - Pokud předplatné bylo vytvořeno před aktualizací. 1808, nasazení virtuálního počítače se spravovanými disky může selhat s interní chybovou zprávu. Chcete chybu vyřešit, postupujte podle těchto kroků pro každé předplatné:
      1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Klikněte na tlačítko **poskytovatelů prostředků**, klikněte na **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
      2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
   - Pokud jste nakonfigurovali prostředí s více tenanty, nasazování virtuálních počítačů v rámci služby předplacené asociovaná s adresářem hosta může selhat s interní chybovou zprávu. Pokud chcete chybu vyřešit, postupujte podle kroků v [v tomto článku](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) změna konfigurace všech vašich adresářů hosta.


## <a name="next-steps"></a>Další postup
[Další informace o virtuálních počítačích Azure Stack](azure-stack-compute-overview.md)

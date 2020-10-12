---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72391598"
---
Změny provedené ve sdílené složce Azure pomocí Azure Portal nebo SMB se hned nedetekuje a replikují jako změny koncového bodu serveru. Soubory Azure ještě nemají oznámení o změnách ani deníky, takže neexistuje způsob, jak automaticky iniciovat relaci synchronizace při změně souborů. V systému Windows Server Azure File Sync používá [Deník USN systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) k automatickému zahájení relace synchronizace při změně souborů.

Pokud chcete zjistit změny sdílené složky Azure, Azure File Sync má naplánovanou úlohu s názvem *úloha detekce změn*. Úloha detekce změn vytvoří výčet všech souborů ve sdílené složce a pak ji porovná s verzí synchronizace pro daný soubor. Když úloha zjišťování změn zjistí, že se soubory změnily, Azure File Sync zahájí relaci synchronizace. Úloha zjišťování změn je zahájena každých 24 hodin. Vzhledem k tomu, že úloha zjišťování změn funguje při vytváření výčtu všech souborů ve sdílené složce Azure, zjišťování změn trvá déle než v menších oborech názvů. Pro velké obory názvů může trvat déle než jednou za 24 hodin, abyste zjistili, které soubory se změnily.

K okamžité synchronizaci souborů, které se změnily ve sdílené složce Azure, se dá použít rutina PowerShellu **Invoke-AzStorageSyncChangeDetection** k ručnímu spuštění rozpoznávání změn ve sdílené složce Azure. Tato rutina je určená pro scénáře, kdy nějaký typ automatizovaného procesu provádí změny ve sdílené složce Azure nebo že tyto změny provádí správce (například přesunutí souborů a adresářů do sdílené složky). V případě změn koncového uživatele doporučujeme nainstalovat agenta Azure File Sync do virtuálního počítače IaaS a nechat koncové uživatele přistupovat ke sdílené složce prostřednictvím virtuálního počítače IaaS. Tímto způsobem budou všechny změny rychle synchronizovány s ostatními agenty bez nutnosti používat rutinu Invoke-AzStorageSyncChangeDetection. Další informace najdete v dokumentaci k [vyvolání metody Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .

>[!NOTE]
>Změny provedené ve sdílené složce Azure pomocí REST neaktualizují čas poslední změny protokolu SMB a při synchronizaci se neprojeví jako změny.

Zkoumáme přidání zjišťování změn pro sdílenou složku Azure podobně jako u svazků na Windows serveru. Nám pomůžou nastavit prioritu této funkce pro budoucí vývoj, a to hlasováním v [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

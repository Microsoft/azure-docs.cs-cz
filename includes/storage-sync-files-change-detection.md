---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391598"
---
Změny provedené ve sdílené složce Azure pomocí portálu Azure nebo SMB nejsou okamžitě zjištěny a replikovány jako změny koncového bodu serveru. Soubory Azure ještě nemá oznámení o změně nebo ukládání do deníku, takže neexistuje žádný způsob, jak automaticky zahájit relaci synchronizace při změně souborů. Na Windows Serveru azure file sync používá [ukládání do deníku Windows USN](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) k automatickému zahájení relace synchronizace při změně souborů.

Chcete-li zjistit změny ve sdílené složce Azure, Azure File Sync má naplánovanou úlohu nazvanou *úloha zjišťování změn*. Úloha zjišťování změn vyjmenovává každý soubor ve sdílené složce a poté ji porovná s verzí synchronizace pro tento soubor. Když úloha zjišťování změn zjistí, že došlo ke změně souborů, Azure File Sync zahájí synchronizaci synchronizace. Úloha zjišťování změn je spuštěna každých 24 hodin. Vzhledem k tomu, že úloha zjišťování změn funguje tak, že vypočtu každého souboru ve sdílené složce Azure, detekce změn trvá déle ve větších oborech názvů než v menších oborech názvů. U velkých oborů názvů může trvat déle než jednou za 24 hodin, než se zjistí, které soubory byly změněny.

Chcete-li okamžitě synchronizovat soubory, které se změní ve sdílené složce Azure, můžete použít rutinu **Invoke-AzStorageSyncChangeDetection** PowerShell k ručnímu spuštění zjišťování změn ve sdílené složce Azure. Tato rutina je určena pro scénáře, kde nějaký typ automatizovaného procesu provádí změny ve sdílené složce Azure nebo změny provádí správce (jako je přesunutí souborů a adresářů do sdílené složky). Pro změny koncových uživatelů doporučujeme nainstalovat agenta Azure File Sync do virtuálního počítače IaaS a mít koncovým uživatelům přístup ke sdílené složce prostřednictvím virtuálního počítače IaaS. Tímto způsobem budou všechny změny rychle synchronizovány s jinými agenty bez nutnosti použití rutiny Invoke-AzStorageSyncChangeDetection. Další informace naleznete v dokumentaci [invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)

>[!NOTE]
>Změny provedené ve sdílené složce Azure pomocí REST neaktualizuje čas poslední změny SMB a nebude považován za změnu synchronizací.

Zkoumáme přidávání zjišťování změn pro sdílenou složku Azure podobnou USN pro svazky na Windows Serveru. Pomozte nám upřednostnit tuto funkci pro budoucí vývoj hlasováním na [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

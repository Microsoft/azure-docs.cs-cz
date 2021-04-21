---
title: Seznámení s Synchronizace souborů Azure | Microsoft Docs
description: Přehled Synchronizace souborů Azure, službu, která umožňuje vytvářet a používat sdílené složky souborů sítě v cloudu s využitím standardního standardního protokolu SMB.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797657"
---
# <a name="what-is-azure-file-sync"></a>Co je Synchronizace souborů Azure?
Synchronizace souborů Azure umožňuje centralizovat sdílené složky vaší organizace ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu souborového serveru s Windows. I když se někteří uživatelé můžou nechat uchovávat úplnou kopii svých dat místně, Synchronizace souborů Azure taky možnost transformovat Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru, včetně SMB, NFS a FTPS. Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.   

## <a name="videos"></a>Videa
| Představujeme Synchronizace souborů Azure | Soubory Azure se synchronizací (Ignite 2019)  |
|-|-|
| [![Záznam dění na indikátoru obrazovky Představujeme Synchronizace souborů Azure video – kliknutím zahrajete.](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Záznam dění na záznamovém pracovním souboru Azure se synchronizací prezentace – Kliknutím spustíte přehrávání.](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Výhody Synchronizace souborů Azure

### <a name="cloud-tiering"></a>Vrstvení cloudu
Díky povoleným vrstvám cloudu se nejčastěji používané soubory ukládají do mezipaměti na místním serveru a soubory s nejméně často používanými soubory jsou vrstveny do cloudu. Můžete určit, kolik místa na místním disku se pro ukládání do mezipaměti používá. Vrstvené soubory je možné rychle odvolat na vyžádání, což zajistí plynulé zprovoznění a umožní vám snížit náklady, protože potřebujete jenom ukládat zlomek vašich dat v místním prostředí. Další informace o vrstvení cloudu najdete v tématu [Přehled vrstvení cloudu](file-sync-cloud-tiering-overview.md). 

### <a name="multi-site-access-and-sync"></a>Přístup k více lokalitám a jejich synchronizace
Synchronizace souborů Azure je ideální pro scénáře distribuovaného přístupu. Pro každou z vašich poboček můžete zřídit místní Windows Server jako součást nasazení Synchronizace souborů Azure. Změny provedené na serveru v jedné kanceláři se automaticky synchronizují se servery ve všech ostatních kancelářích.

### <a name="business-continuity-and-disaster-recovery"></a>Provozní kontinuita a zotavení po havárii
Synchronizace souborů Azure se zálohují pomocí služby soubory Azure, která nabízí několik možností redundance pro úložiště s vysokou dostupností. Vzhledem k tomu, že Azure obsahuje odolné kopie vašich dat, váš místní server se stal zařízením pro ukládání do mezipaměti a obnovování z neúspěšného serveru se dá udělat přidáním nového serveru do nasazení Synchronizace souborů Azure. Místo obnovení z místní zálohy zřizujete jiný server Windows, nainstalujete na něj agenta Synchronizace souborů Azure a pak ho přidáte do nasazení Synchronizace souborů Azure. Synchronizace souborů Azure před stažením dat stahuje obor názvů souboru, takže váš server bude možné v co nejbližší době spustit. Pro ještě rychlejší obnovení můžete mít jako součást nasazení teplého serveru, nebo můžete použít Synchronizace souborů Azure s clustering Windows.

## <a name="cloud-side-backup"></a>Zálohování na straně cloudu
Využijte Azure Backup k omezení vašich místních záloh v cloudu pomocí. Sdílené složky SMB souborů Azure mají nativní možnosti snímků a proces je možné automatizovat pomocí Azure Backup naplánovat zálohování a spravovat jejich uchovávání. Azure Backup se taky integruje s vašimi místními servery, takže při obnovení do cloudu se tyto změny automaticky stáhnou na servery Windows.  

## <a name="next-steps"></a>Další kroky
* [Plánování nasazení Synchronizace souborů Azure](file-sync-planning.md)
* [Přehled vrstvení cloudu](file-sync-cloud-tiering-overview.md)
* [Sledování služby Synchronizace souborů Azure](file-sync-monitoring.md)
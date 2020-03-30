---
title: Přidání nebo odebrání koncového bodu serveru Azure File Sync | Dokumenty společnosti Microsoft
description: Přečtěte si, co je třeba vzít v úvahu při plánování nasazení souborů Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 684b30a24e049722cb531cbc84e3a2cd90912ec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255103"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Přidání nebo odebrání koncového bodu serveru Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Je to tím, že transformuje vaše servery Windows do rychlé mezipaměti sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

*Koncový bod serveru* představuje určité umístění na *registrovaném serveru*, například složku na svazku serveru nebo kořenový adresář svazku. Na stejném svazku může existovat více koncových bodů serveru, pokud se jejich obory názvů nepřekrývají (například F:\sync1 a F:\sync2). Zásady vrstvení cloudu můžete konfigurovat individuálně pro každý koncový bod serveru. Pokud přidáte umístění serveru s existující sadou souborů jako koncový bod serveru do skupiny synchronizace, budou tyto soubory sloučeny s jinými soubory, které jsou již v jiných koncových bodech ve skupině synchronizace.

Informace [o tom,](storage-sync-files-deployment-guide.md) jak nasadit azure file sync, najdete v článku Jak nasadit azure file sync od konce do konce.

## <a name="prerequisites"></a>Požadavky
Chcete-li vytvořit koncový bod serveru, musíte nejprve zajistit splnění následujících kritérií: 
- Server má nainstalovaného agenta Azure File Sync a byl zaregistrován. Pokyny pro instalaci agenta synchronizace souborů Azure najdete v [článku Register/Unregister server with Azure File Sync.](storage-sync-files-server-registration.md) 
- Ujistěte se, že byla nasazena služba synchronizace úložiště. Podrobnosti o nasazení služby Synchronizace úložiště najdete v článku [Jak nasadit Azure File Sync.](storage-sync-files-deployment-guide.md) 
- Ujistěte se, že byla nasazena skupina synchronizace. Přečtěte si, jak [vytvořit skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Ujistěte se, že server je připojený k internetu a že Azure je přístupný. Port 443 používáme pro veškerou komunikaci mezi serverem a naší službou.

## <a name="add-a-server-endpoint"></a>Přidání koncového bodu serveru
Chcete-li přidat koncový bod serveru, přejděte na požadovanou skupinu synchronizace a vyberte "Přidat koncový bod serveru".

![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

V části Přidat **koncový bod serveru**jsou vyžadovány následující informace :

- **Registrovaný server**: Název serveru nebo clusteru, na který má být koncový bod serveru.
- **Cesta:** Cesta na serveru Windows Server, která má být synchronizována jako součást skupiny synchronizace.
- **Vrstvení cloudu:** Přepínač pro povolení nebo zakázání vrstvení cloudu. Pokud je povoleno, vrstvení cloudu bude *vrstvení* souborů do sdílených složek Azure. Tím se převedou místní sdílené složky na mezipaměť, nikoli na úplnou kopii datové sady, což vám pomůže spravovat efektivitu prostoru na serveru.
- **Volné místo na svazku:** velikost volného místa k rezervaci na svazku, který je umístěn v koncovém bodu serveru. Pokud je například volné místo na svazku nastaveno na 50 % na svazku s jedním koncovým bodem serveru, bude zhruba polovina dat vrstvena na soubory Azure. Bez ohledu na to, jestli je povoleno vrstvení cloudu, vaše sdílená složka Azure má vždy úplnou kopii dat ve skupině synchronizace.

Výběrem **možnosti Vytvořit** přidejte koncový bod serveru. Soubory v oboru názvů skupiny synchronizace budou nyní synchronizovány. 

## <a name="remove-a-server-endpoint"></a>Odebrání koncového bodu serveru
Pokud chcete přestat používat Azure File Sync pro daný koncový bod serveru, můžete odebrat koncový bod serveru. 

> [!Warning]  
> Nepokoušejte se řešit problémy se synchronizací, vrstvením cloudu nebo jakýmkoli jiným aspektem synchronizace souborů Azure odebráním a opětovným vytvořením koncového bodu serveru, pokud to není výslovně uvedeno tak technikem společnosti Microsoft. Odebrání koncového bodu serveru je destruktivní operace a vrstvené soubory v rámci koncového bodu serveru nebude "znovu připojen" k jejich umístění ve sdílené složce Azure po opětovném vytvoření koncového bodu serveru, což bude mít za následek chyby synchronizace. Všimněte si také, vrstvené soubory, které existují mimo obor názvů koncového bodu serveru může být trvale ztracena. Vrstvené soubory mohou existovat v rámci koncového bodu serveru i v případě, že vrstvení cloudu nebylo nikdy povoleno.

Chcete-li zajistit, aby byly všechny vrstvené soubory odvolány před odebráním koncového bodu serveru, zakažte vrstvení cloudu v koncovém bodě serveru a pak spusťte následující rutinu prostředí PowerShell, abyste si vzpomněli na všechny vrstvené soubory v oboru názvů koncových bodů serveru:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Zadání `-Order CloudTieringPolicy` nejprve vyvolá naposledy upravené soubory.
Další volitelné, ale užitečné parametry, které je třeba zvážit, jsou:
* `-ThreadCount`určuje, kolik souborů lze souběžně svolat.
* `-PerFileRetryCount`určuje, jak často se bude o odvolání souboru, který je aktuálně blokován, pokusit.
* `-PerFileRetryDelaySeconds`určuje čas v sekundách mezi opakováním pokusů o odvolání a měl by být vždy použit v kombinaci s předchozím parametrem.

> [!Note]  
> Pokud místní svazek hostující server nemá dostatek volného místa pro vyvolání všech vrstvených dat, `Invoke-StorageSyncFileRecall` rutina se nezdaří.  

Odebrání koncového bodu serveru:

1. Přejděte do služby Synchronizace úložiště, kde je váš server registrován.
2. Přejděte na požadovanou skupinu synchronizace.
3. Odeberte koncový bod serveru, který si přejete, ve skupině synchronizace ve službě Synchronizace úložiště. Toho lze dosáhnout kliknutím pravým tlačítkem myši na příslušný koncový bod serveru v podokně skupiny synchronizace.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Další kroky
- [Registrace nebo zrušení registrace serveru pomocí Azure File Sync](storage-sync-files-server-registration.md)
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)

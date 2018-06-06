---
title: Přidat nebo odebrat server koncový bod synchronizace souboru Azure (preview) | Microsoft Docs
description: Zjistěte, co je potřeba zvážit při plánování nasazení služby Azure Files.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: wgries
ms.openlocfilehash: 93331dd936a6d7b30ca18743d2079900421b2620
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738475"
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Přidat nebo odebrat server koncový bod synchronizace souboru Azure (preview)
Azure File Sync (Preview) umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to pomocí transformace serverech s Windows do rychlé mezipaměti Azure sdílené složky. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

A *koncový bod serveru* představuje určitého umístění na *zaregistrovaný server*, například do složky na svazku serveru nebo v kořenovém adresáři svazku. Víc koncových bodů serveru může existovat na stejném svazku, pokud nejsou jejich obory názvů překrývání (například F:\sync1 a F:\sync2). Můžete nakonfigurovat zásady vrstvení cloud jednotlivě pro každý koncový bod serveru. Pokud přidáte umístění na serveru s existující sadou souborů jako koncový bod serveru do skupiny synchronizace, budou tyto soubory sloučit s další soubory na ostatní koncové body ve skupině synchronizace.

V tématu [nasazení synchronizace souboru Azure (preview)](storage-sync-files-deployment-guide.md) informace o tom, jak nasadit synchronizace souboru Azure klient server.

## <a name="prerequisites"></a>Požadavky
Před vytvořením koncový bod serveru, musíte napřed zajistit, že se splní následující kritéria: 
- Server je nainstalovaný agent synchronizace souboru Azure a byl zaregistrován. Pokyny pro instalaci agenta synchronizace souboru Azure lze nalézt v [registrace a zrušení registrace a server se synchronizací souboru Azure (preview)](storage-sync-files-server-registration.md) článku. 
- Ujistěte se, že byla nasazena synchronizační služby úložiště. V tématu [nasazení synchronizace souboru Azure (preview)](storage-sync-files-deployment-guide.md) podrobnosti o tom, jak nasadit synchronizační služby úložiště. 
- Ujistěte se, nasazený synchronizace skupiny. Zjistěte, jak [vytvořte skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a-sync-group).
- Ujistěte se, že je server připojen k Internetu a zda je přístupný Azure. Používáme port 443 pro veškerou komunikaci mezi serverem a naši službu.

## <a name="add-a-server-endpoint"></a>Přidat koncový bod serveru
Chcete-li přidat koncový bod serveru, přejděte do požadovaného synchronizace skupiny a vyberte "Přidat koncový bod serveru".

![Přidat nový koncový bod serveru v podokně skupiny synchronizace](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Následující informace jsou požadovány podle **přidat koncový bod serveru**:

- **Zaregistrovaný server**: název serveru nebo clusteru vytvořit na serveru koncového bodu.
- **Cesta**: cesta na serveru Windows k synchronizaci jako součást skupiny synchronizace.
- **Cloud Tiering**: přepínač k povolení nebo zakázání cloudu vrstev. Když je povolené, cloudu vrstvení bude *vrstvy* soubory do Azure sdílené složky. To převede místní sdílené složky do mezipaměti, nikoli úplnou kopii datovou sadu, které vám pomohou spravovat efektivitu místa na vašem serveru.
- **Volné místo na svazku**: množství volného místa vyhradit na svazku, který se nachází na serveru koncového bodu. Například pokud volné místo na svazku nastavená na 50 % na svazku s koncový bod jeden server, zhruba poloviční množství dat bude být rozvrstvena k Azure Files. Bez ohledu na tom, jestli cloud vrstvení je povoleno, Azure sdílené složky má vždy úplnou kopii dat ve skupině synchronizace.

Vyberte **vytvořit** k přidání koncového bodu serveru. Soubory v oboru názvů skupiny synchronizace bude nyní sesynchronizovávat. 

## <a name="remove-a-server-endpoint"></a>Odstranit koncový bod serveru
Pokud je chcete přestat pomocí synchronizace souboru Azure pro koncový bod daný server, můžete odebrat serveru koncového bodu. 

> [!Warning]  
> Nepokoušejte se vyřešit problémy s synchronizace, cloudu vrstvení nebo jiných aspektů synchronizace souboru Azure odebráním a znovu vytvořit koncový bod serveru, pokud není výslovně pokyn k pracovníkem společnosti Microsoft. Odebrání serveru koncového bodu je destruktivní operace a vrstvené souborů v rámci serveru koncového bodu nebude "zopakovat" na jejich umístění na sdílenou složkou Azure po koncový bod serveru znovu vytvořena, což způsobí synchronizované chyby. Všimněte si také, vrstvené souborů, které existují mimo obor názvů koncový bod serveru může být trvale ztratí. Vrstvený soubory mohou existovat i když váš server koncový bod cloudu vrstvení nebylo nikdy povoleno.

Aby se zajistilo, že jsou všechny soubory vrstvené připomenout před odebráním serveru koncového bodu, zakažte cloudu vrstvení na koncový bod serveru a potom spusťte následující rutiny prostředí PowerShell všechny vrstvené soubory v rámci vašeho oboru názvů koncový bod serveru:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Pokud místní svazek, který je hostitelem serveru nemá dostatek volného místa pro navrácení všech vrstvené souborů `Invoke-StorageSyncFileRecall` rutina selže.  

Odebrání serveru koncového bodu:

1. Přejděte do služby Sync úložiště, kde je server zaregistrován.
2. Přejděte do požadovaného synchronizace skupiny.
3. Odebere koncový bod serveru, které očekáváte skupiny synchronizace v úložišti služby synchronizace. To můžete provést kliknutím pravým tlačítkem na příslušný server koncového bodu v podokně synchronizace skupiny.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Další postup
- [Registrace a zrušení registrace serveru se synchronizací souboru Azure (preview)](storage-sync-files-server-registration.md)
- [Plánování nasazení Azure souboru Sync](storage-sync-files-planning.md)
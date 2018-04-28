---
title: Přidat nebo odebrat server koncový bod synchronizace souboru Azure (preview) | Microsoft Docs
description: Zjistěte, co je potřeba zvážit při plánování nasazení služby Azure Files.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 26e4af814bad988da02d4e0cf36f17e1beec872e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Přidat nebo odebrat server koncový bod synchronizace souboru Azure (preview)
Azure File Sync (Preview) umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to pomocí transformace serverů Windows na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

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
- **Cloud Tiering**: přepínač k povolení nebo zakázání cloudu vrstev, které umožňuje zřídka používají nebo získat přístup k souborům na být rozvrstvena k Azure Files.
- **Volné místo na svazku**: množství volného místa vyhradit na svazku, který se nachází na serveru koncového bodu. Například pokud volné místo na svazku nastavená na 50 % na svazku s koncový bod jeden server, zhruba poloviční množství dat bude být rozvrstvena k Azure Files. Bez ohledu na tom, jestli cloud vrstvení je povoleno, Azure sdílené složky má vždy úplnou kopii dat ve skupině synchronizace.

Vyberte **vytvořit** k přidání koncového bodu serveru. Soubory v oboru názvů skupiny synchronizace bude nyní sesynchronizovávat. 

## <a name="remove-a-server-endpoint"></a>Odstranit koncový bod serveru
Když je povolené pro koncový bod serveru, cloudové vrstvení bude *vrstvy* soubory do vaší sdílené složky Azure File. To umožňuje místní sdílené složky tak, aby fungoval jako mezipaměť, nikoli úplnou kopii datovou sadu, aby efektivní využití místa na souborovém serveru. Ale **serveru koncového bodu se odebere při vrstvené soubory stále místně na serveru,-li tyto soubory budou nepřístupné**. Proto v případě, že se požaduje přístup k souborům ve sdílených složkách na místě, je nutné odvolat všechny vrstvené soubory ze souborů Azure než budete pokračovat v odstraňování serveru koncového bodu. 

To můžete provést pomocí rutiny prostředí PowerShell, jak je uvedeno níže:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Pokud místní svazek, který je hostitelem serveru nemá dostatek volného místa pro navrácení všech vrstvené souborů `Invoke-StorageSyncFileRecall` rutina selže.  

Odebrání serveru koncového bodu:

1. Přejděte do služby Sync úložiště, kde je server zaregistrován.
2. Přejděte do požadovaného synchronizace skupiny.
3. Odebere koncový bod serveru, které očekáváte skupiny synchronizace v úložišti služby synchronizace. To můžete provést kliknutím pravým tlačítkem na příslušný server koncového bodu v podokně synchronizace skupiny.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Další postup
- [Registrace a zrušení registrace serveru se synchronizací souboru Azure (preview)](storage-sync-files-server-registration.md)
- [Plánování nasazení Azure souboru Sync](storage-sync-files-planning.md)

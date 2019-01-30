---
title: Přidání nebo odebrání koncového bodu serveru Azure File Sync | Dokumentace Microsoftu
description: Zjistěte, co vzít v úvahu při plánování nasazení služby soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 118ecc08604cbe05181dd3ba943fd73a34fd47f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212473"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Přidání nebo odebrání koncového bodu serveru Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to pomocí transformace serverů Windows na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

A *koncový bod serveru* představuje konkrétní umístění na *registrovaný server*, jako je například složka na svazku serveru nebo kořenový svazek. Více koncových bodů serveru může existovat v jednom svazku, pokud nejsou jejich obory názvů překrývají (například F:\sync1 a F:\sync2). Můžete nakonfigurovat zásady vrstvení cloudu jednotlivě pro každý koncový bod serveru. Pokud chcete přidat umístění na serveru s existující sadou souborů jako koncový bod serveru ke skupině synchronizace, tyto soubory se sloučí s jinými soubory na ostatní koncové body ve skupině synchronizace.

Zobrazit [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md) informace o tom, jak nasadit Azure File Sync end-to-end.

## <a name="prerequisites"></a>Požadavky
Pokud chcete vytvořit koncový bod serveru, je nutné nejdříve zkontrolovat, že se splní následující kritéria: 
- Server je nainstalovaný agent Azure File Sync a byl zaregistrován. Pokyny k instalaci agenta Azure File Sync najdete v [registrace nebo zrušení registrace a serveru pomocí služby Azure File Sync](storage-sync-files-server-registration.md) článku. 
- Ujistěte se, že byla nasazena služba synchronizace úložiště. Zobrazit [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md) podrobnosti o tom, jak nasadit služby synchronizace úložiště. 
- Ujistěte se, že byla nasazena skupině synchronizace. Zjistěte, jak [vytvořit skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Ujistěte se, že je server připojen k Internetu a že Azure je dostupný. Používáme port 443 pro veškerou komunikaci mezi serverem a naše služby.

## <a name="add-a-server-endpoint"></a>Přidání koncového bodu serveru
Chcete-li přidat koncový bod serveru, přejděte do požadovaného synchronizace skupiny a vyberte "Přidat koncový bod serveru".

![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Tyto informace se vyžaduje pod **přidat koncový bod serveru**:

- **Registrovaný server**: Název serveru nebo clusteru vytvořte na koncový bod serveru.
- **Cesta**: Cesta v systému Windows Server se dá provést synchronizace jako součást skupiny synchronizace.
- **Vrstvení cloudu**: Přepnout na povolit nebo zakázat cloudu ovládání datových vrstev. Při povolení cloudové vrstvení bude *úroveň* soubory do sdílených složek Azure. Tím převedete místní sdílené složky do mezipaměti, nikoli úplnou kopii datovou sadu, které vám pomohou spravovat efektivity místa na vašem serveru.
- **Volné místo na svazku**: množství volného místa vyhradit na svazku, který se nachází koncový bod serveru. Například pokud volné místo svazku je nastavena na 50 % na svazku s koncovým bodem jeden server, přibližně polovina objem dat se vrstvený do služby soubory Azure. Bez ohledu na to, jestli cloud ovládání datových vrstev je povoleno, sdílené složky Azure vždy obsahuje úplnou kopii dat ve skupině synchronizace.

Vyberte **vytvořit** k přidání koncového bodu serveru. Soubory v rámci oboru názvů skupiny synchronizace bude nyní udržovat synchronizované. 

## <a name="remove-a-server-endpoint"></a>Odebrat koncový bod serveru
Pokud chcete ukončit používání Azure File Sync pro koncový bod daného serveru, můžete odebrat koncový bod serveru. 

> [!Warning]  
> Nepokoušejte se řešení potíží s synchronizace, vrstvení cloudu nebo jiného aspektu Azure File Sync ve odebrat a znovu vytvořit koncový bod serveru neurčí explicitně do Microsoftu. Odebírá se koncový bod serveru je destruktivní operace a vrstvené soubory v rámci koncový bod serveru nebude je "připojit" k jejich umístění na sdílené složky Azure po koncový bod serveru je, což způsobí chyby synchronizace. Všimněte si také, vrstvené soubory, které existují mimo obor názvů koncový bod serveru může být trvale ztratí. Můžou existovat vrstvené soubory v rámci vašeho serveru i když koncový bod cloudu ovládání datových vrstev na nikdy nepovolilo.

Aby bylo zajištěno, že jsou všechny vrstvené soubory připomenout před odebráním koncového bodu serveru, zakažte na koncovém bodu serveru vrstvení cloudu a pak spusťte následující rutinu prostředí PowerShell mají svolat všechny vrstvené soubory v rámci vašeho oboru názvů koncový bod serveru:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Pokud místní svazek, který je hostitelem serveru nemá dostatek volného místa pro odvolání všechna Vrstvená data `Invoke-StorageSyncFileRecall` rutina selže.  

Chcete-li odebrat koncový bod serveru:

1. Přejděte do služby synchronizace úložiště, ve kterém je váš server zaregistrovaný.
2. Přejděte do požadovaného synchronizace skupiny.
3. Ve skupině synchronizace ve službě synchronizace úložiště odstraňte koncový bod serveru, které očekáváte. To můžete provést kliknutím pravým tlačítkem myši na příslušný server koncového bodu v podokně skupiny synchronizace.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Další postup
- [Registrace nebo zrušení registrace serveru pomocí služby Azure File Sync](storage-sync-files-server-registration.md)
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Monitorování Azure File Sync](storage-sync-files-monitoring.md)

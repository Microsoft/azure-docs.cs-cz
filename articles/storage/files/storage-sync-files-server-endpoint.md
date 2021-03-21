---
title: Přidat nebo odebrat koncový bod serveru Synchronizace souborů Azure | Microsoft Docs
description: Naučte se přidat nebo odebrat koncový bod serveru pomocí Synchronizace souborů Azure. Koncový bod serveru je určité umístění na zaregistrovaném serveru, jako je třeba složka na svazku na serveru.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013934"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Přidat nebo odebrat koncový bod serveru Synchronizace souborů Azure
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to tak, že transformuje servery Windows na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

*Koncový bod serveru* představuje konkrétní umístění na *zaregistrovaném serveru*, jako je například složka na svazku serveru nebo kořen svazku. Pokud jejich obory názvů nejsou překrývající se (například F:\sync1 a F:\sync2), můžou na stejném svazku existovat víc koncových bodů serveru, přičemž každý koncový bod se synchronizuje s jedinečnou skupinou synchronizace. Zásady cloudových vrstev můžete nakonfigurovat individuálně pro každý koncový bod serveru. Pokud přidáte umístění serveru s existující sadou souborů jako koncový bod serveru do skupiny synchronizace, budou tyto soubory sloučeny s dalšími soubory, které již jsou v jiných koncových bodech ve skupině synchronizace.

Informace o tom, jak nasadit Synchronizace souborů Azure kompletní, najdete v tématu [nasazení synchronizace souborů Azure](storage-sync-files-deployment-guide.md) .

## <a name="prerequisites"></a>Předpoklady
Pokud chcete vytvořit koncový bod serveru, musíte nejdřív zkontrolovat, jestli jsou splněné následující podmínky: 
- Server má nainstalovaného agenta Synchronizace souborů Azure a byl zaregistrován. Pokyny pro instalaci agenta Synchronizace souborů Azure najdete v článku [registrace nebo zrušení registrace serveru s synchronizace souborů azurem](storage-sync-files-server-registration.md) článkem. 
- Ujistěte se, že je nasazená služba synchronizace úložiště. Podrobnosti o tom, jak nasadit službu synchronizace úložiště, najdete v tématu [postup nasazení synchronizace souborů Azure](storage-sync-files-deployment-guide.md) . 
- Ujistěte se, že byla nasazena skupina synchronizace. Přečtěte si, jak [vytvořit skupinu synchronizace](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Ujistěte se, že je server připojený k Internetu a že je k dispozici Azure. Pro veškerou komunikaci mezi serverem a naší službou používáme port 443.

## <a name="add-a-server-endpoint"></a>Přidání koncového bodu serveru
Pokud chcete přidat koncový bod serveru, přejděte do požadované skupiny synchronizace a vyberte přidat koncový bod serveru.

![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

V části **přidat koncový bod serveru** se vyžadují tyto informace:

- **Registrovaný Server**: název serveru nebo clusteru, na kterém má být vytvořen koncový bod serveru.
- **Cesta**: cesta na serveru Windows Server, který se má synchronizovat jako součást skupiny synchronizace.
- **Vrstvení cloudu**: přepínač pro povolení nebo zakázání vrstvení cloudu. Když se povolí, vrstvení cloudu bude *vrstvy* souborů na sdílené složky Azure. Tato možnost převede místní sdílené složky do mezipaměti místo úplné kopie datové sady, která vám umožní spravovat efektivitu místa na serveru.
- **Volné místo na svazku**: množství volného místa, které se má rezervovat na svazku, který je umístěn na koncovém bodu serveru. Pokud je například volné místo svazku nastaveno na 50% na svazku s jedním koncovým bodem serveru, přibližně polovina množství dat bude vrstveno na soubory Azure. Bez ohledu na to, jestli je povolená vrstva cloudu, bude mít vaše sdílená složka Azure vždycky úplnou kopii dat ve skupině synchronizace.

Vyberte **vytvořit** a přidejte koncový bod serveru. Soubory v rámci oboru názvů skupiny synchronizace budou nyní udržovány v synchronizaci. 

## <a name="remove-a-server-endpoint"></a>Odebrání koncového bodu serveru
Pokud si přejete ukončit používání Synchronizace souborů Azure pro daný koncový bod serveru, můžete koncový bod serveru odebrat. 

> [!Warning]  
> Nepokoušejte se řešit potíže se synchronizací, vrstvením cloudu nebo jakýmkoli jiným aspektem Synchronizace souborů Azure tím, že odeberete a znovu vytvoříte koncový bod serveru, pokud ho nevýslovně nevydá pracovník společnosti Microsoft. Odebrání koncového bodu serveru je destruktivní operace a vrstvený soubor v rámci koncového bodu serveru nebude znovu připojen do jejich umístění ve sdílené složce Azure po opětovném vytvoření koncového bodu serveru, což bude mít za následek chyby synchronizace. Všimněte si také, že vrstvené soubory, které existují mimo obor názvů koncového bodu serveru, mohou být trvale ztraceny. Vrstvené soubory můžou existovat v rámci koncového bodu serveru i v případě, že se vrstva cloudu nikdy nepovolila.

Pokud chcete zajistit, aby se všechny vrstvené soubory před odebráním koncového bodu serveru znovu navolaly, zakažte na koncovém bodu serveru vrstvení cloudu a potom spusťte následující rutinu prostředí PowerShell, která odvolá všechny vrstvené soubory v rámci oboru názvů koncového bodu serveru:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
`-Order CloudTieringPolicy`Při zadání se nejdřív odeberou poslední změněné soubory.
Další volitelné, ale užitečné parametry, které je potřeba vzít v úvahu:
* `-ThreadCount` Určuje počet souborů, které mohou být vyvolány paralelně.
* `-PerFileRetryCount`Určuje, jak často se bude opakovat pokus o odvolání souboru, který je aktuálně blokován.
* `-PerFileRetryDelaySeconds`Určuje dobu v sekundách mezi opakovanými pokusy o odvolání a měla by být vždy použita v kombinaci s předchozím parametrem.

> [!Note]  
> Pokud místní svazek, který hostuje server, nemá dost volného místa pro odvolání všech vrstvených dat, `Invoke-StorageSyncFileRecall` rutina se nezdařila.  

Odebrání koncového bodu serveru:

1. Přejděte do služby synchronizace úložiště, kde je váš server zaregistrovaný.
2. Přejděte do požadované skupiny synchronizace.
3. Odeberte koncový bod serveru, který si přejete ve skupině synchronizace ve službě synchronizace úložiště. To lze provést kliknutím pravým tlačítkem myši na příslušný koncový bod serveru v podokně synchronizovat skupinu.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Další kroky
- [Registrace nebo zrušení registrace serveru s Synchronizace souborů Azure](storage-sync-files-server-registration.md)
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Sledování služby Synchronizace souborů Azure](storage-sync-files-monitoring.md)

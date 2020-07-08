---
title: Nakonfigurujte výchozí doménu NFSv 4.1 pro Azure NetApp Files | Microsoft Docs
description: V této části najdete popis postupu konfigurace klienta NFS pro použití NFSv 4.1 s Azure NetApp Files.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: dda911add42568e76160e4233502a1f4f550520d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483716"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Konfigurace výchozí domény NFSv4.1 pro Azure NetApp Files

Názvů NFSv4 zavádí koncept domény ověřování. Azure NetApp Files v současné době podporuje mapování uživatele jenom na kořenové úrovni ze služby na klienta NFS. Chcete-li používat funkci NFSv 4.1 s Azure NetApp Files, je nutné aktualizovat klienta NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Výchozí chování mapování uživatele nebo skupiny

Výchozí mapování je nastaveno na hodnotu `nobody` uživatel, protože doména názvů NFSv4 je nastavena na hodnotu `localdomain` . Když připojíte Azure NetApp Files svazek NFSv 4.1 jako kořenový adresář, zobrazí se oprávnění k souboru následujícím způsobem:  

![Výchozí chování mapování uživatele/skupiny pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Jak ukazuje výše uvedený příklad, uživatel `file1` by měl mít `root` hodnotu, ale `nobody` ve výchozím nastavení je namapován.  V tomto článku se dozvíte, jak nastavit `file1` uživatele na `root` .  

## <a name="steps"></a>Kroky 

1. Upravte `/etc/idmapd.conf` soubor v klientovi NFS.   
    Odkomentujte řádek `#Domain` (to znamená, odeberte `#` z řádku) a změňte hodnotu `localdomain` na `defaultv4iddomain.com` . 

    Počáteční konfigurace: 
    
    ![Počáteční konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Aktualizovaná konfigurace:
    
    ![Aktualizovaná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Odpojte všechny aktuálně připojené svazky systému souborů NFS.
3. Aktualizujte `/etc/idmapd.conf` soubor.
4. Restartujte `rpcbind` službu na hostiteli ( `service rpcbind restart` ), nebo jednoduše restartujte hostitele.
5. Připojte svazky NFS podle potřeby.   

    Viz [připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Následující příklad ukazuje výslednou změnu uživatele nebo skupiny: 

![Výsledná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Jak ukazuje příklad, uživatel nebo skupina se teď změnil z `nobody` na `root` .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Chování jiných uživatelů a skupin (bez kořenového uživatele)

Azure NetApp Files podporuje místní uživatele (uživatele vytvořené místně na hostiteli), kteří mají oprávnění spojená se soubory nebo složkami ve svazcích NFSv 4.1. Služba ale v současné době nepodporuje mapování uživatelů nebo skupin napříč více uzly. Proto se uživatelé, kteří vytvořili na jednom hostiteli, nebudou ve výchozím nastavení mapovat na uživatele vytvořené na jiném hostiteli. 

V následujícím příkladu `Host1` má tři existující zkušební uživatelské účty ( `testuser01` , `testuser02` , `testuser03` ): 

![Výsledná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2`Všimněte si, že jste nevytvořili účty testovacích uživatelů, ale stejný svazek je připojen na oba hostitele:

![Výsledná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Další krok 

[Připojení nebo odpojení svazku pro virtuální počítače s Windows nebo Linuxem](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)


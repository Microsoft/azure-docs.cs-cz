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
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906283"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Nakonfigurujte výchozí doménu NFSv 4.1 pro Azure NetApp Files

Názvů NFSv4 zavádí koncept domény ověřování. Azure NetApp Files v současné době podporuje mapování uživatele jenom na kořenové úrovni ze služby na klienta NFS. Chcete-li používat funkci NFSv 4.1 s Azure NetApp Files, je nutné aktualizovat klienta NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Výchozí chování mapování uživatele nebo skupiny

Výchozí mapování je nastaveno na hodnotu `nobody` uživatel, protože doména názvů NFSv4 je nastavena na hodnotu `localdomain`. Když připojíte Azure NetApp Files svazek NFSv 4.1 jako kořenový adresář, zobrazí se oprávnění k souboru následujícím způsobem:  

![Výchozí chování mapování uživatele/skupiny pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Jak ukazuje výše uvedený příklad, uživatel pro `file1` by měl být `root`, ale ve výchozím nastavení se mapuje na `nobody`.  V tomto článku se dozvíte, jak nastavit `file1` uživatele na `root`.  

## <a name="steps"></a>Kroky 

1. Upravte soubor `/etc/idmapd.conf` v klientovi NFS.   
    Odkomentujte `#Domain` čáry (to znamená, odeberte `#` z řádku) a změňte hodnotu `localdomain` na `defaultv4iddomain.com`. 

    Počáteční konfigurace: 
    
    ![Počáteční konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Aktualizovaná konfigurace:
    
    ![Aktualizovaná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Odpojte všechny aktuálně připojené svazky systému souborů NFS.
3. Aktualizujte soubor `/etc/idmapd.conf`.
4. Restartujte službu `rpcbind` na hostiteli (`service rpcbind restart`) nebo jednoduše restartujte hostitele.
5. Připojte svazky NFS podle potřeby.   

    Viz [připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Následující příklad ukazuje výslednou změnu uživatele nebo skupiny: 

![Výsledná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Jak ukazuje příklad, uživatel nebo skupina se teď změnila z `nobody` na `root`.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Chování jiných uživatelů a skupin (bez kořenového uživatele)

Azure NetApp Files podporuje místní uživatele (uživatele vytvořené místně na hostiteli), kteří mají oprávnění spojená se soubory nebo složkami ve svazcích NFSv 4.1. Služba ale v současné době nepodporuje mapování uživatelů nebo skupin napříč více uzly. Proto se uživatelé, kteří vytvořili na jednom hostiteli, nebudou ve výchozím nastavení mapovat na uživatele vytvořené na jiném hostiteli. 

V následujícím příkladu má `Host1` tři existující testovací uživatelské účty (`testuser01`, `testuser02`, `testuser03`): 

![Výsledná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Na `Host2`Pamatujte na to, že účty testovacích uživatelů nebyly vytvořeny, ale stejný svazek je připojen na oba hostitele:

![Výsledná konfigurace pro NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Další krok 

[Připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)


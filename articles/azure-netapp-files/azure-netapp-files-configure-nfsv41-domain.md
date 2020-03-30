---
title: Konfigurace výchozí domény NFSv4.1 pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje, jak nakonfigurovat klienta systému souborů NFS pro použití nfsv4.1 se soubory Azure NetApp.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906283"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Konfigurace výchozí domény NFSv4.1 pro Azure NetApp Files

NFSv4 zavádí koncept domény ověřování. Soubory Azure NetApp aktuálně podporuje mapování pouze pro root ze služby do klienta systému souborů NFS. Chcete-li používat funkci NFSv4.1 se soubory Azure NetApp, je třeba aktualizovat klienta systému souborů NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Výchozí chování mapování uživatele/skupiny

Kořenové mapování je `nobody` výchozí pro uživatele, protože doména `localdomain`NFSv4 je nastavena na . Když připojíte svazek NFSv4.1 azure netapp jako root, zobrazí se oprávnění k souborům následujícím způsobem:  

![Výchozí chování mapování uživatele/skupiny pro nfsv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Jak ukazuje výše uvedený příklad, uživatel pro `file1` by měl být `root`, ale ve výchozím nastavení se mapuje. `nobody`  Tento článek ukazuje, jak `file1` nastavit `root`uživatele na .  

## <a name="steps"></a>Kroky 

1. Upravte `/etc/idmapd.conf` soubor v klientovi systému souborů NFS.   
    Odkomentujte `#Domain` řádek (tj. `#` odeberte z řádku) a změňte hodnotu `localdomain` na `defaultv4iddomain.com`. 

    Počáteční konfigurace: 
    
    ![Počáteční konfigurace pro NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Aktualizovaná konfigurace:
    
    ![Aktualizovaná konfigurace pro nfsv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Odpojte všechny aktuálně připojené svazky nfs.
3. Aktualizujte `/etc/idmapd.conf` soubor.
4. Restartujte `rpcbind` službu na`service rpcbind restart`hostiteli ( ), nebo jednoduše restartujte hostitele.
5. Podle potřeby připojte svazky nfs.   

    Viz [Připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Následující příklad ukazuje výslednou změnu uživatele/skupiny: 

![Výsledná konfigurace pro NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Jak ukazuje příklad, uživatel/skupina se `nobody` nyní `root`změnila z na .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Chování ostatních (nekořenových) uživatelů a skupin

Soubory Azure NetApp podporují místní uživatele (uživatele vytvořené místně na hostiteli), kteří mají oprávnění přidružená k souborům nebo složkám ve svazcích NFSv4.1. Služba však aktuálně nepodporuje mapování uživatelů nebo skupin napříč více uzly. Proto uživatelé vytvořená na jednom hostiteli nemapují ve výchozím nastavení na uživatele vytvořené na jiném hostiteli. 

`Host1` V následujícím příkladu má tři existující`testuser01` `testuser02`testovací `testuser03`uživatelské účty ( , , ): 

![Výsledná konfigurace pro NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Zapnuto `Host2`, všimněte si, že testovací uživatelské účty nebyly vytvořeny, ale stejný svazek je připojen na obou hostitelích:

![Výsledná konfigurace pro NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Další krok 

[Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)


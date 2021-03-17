---
title: Konfigurace zásad exportu pro svazek NFS – Azure NetApp Files
description: Popisuje postup konfigurace zásad exportu pro řízení přístupu ke svazku systému souborů NFS pomocí Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: d3f268a4ce2660350055367770e987a06828e2d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740115"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurace zásad exportu pro svazek NFS

Můžete nakonfigurovat zásady exportu pro řízení přístupu ke svazku Azure NetApp Files. Zásada exportu Azure NetApp Files podporuje svazky, které používají protokol NFS (NFSv3 a NFSv 4.1), a duální protokol (NFSv3 a SMB). 

Můžete vytvořit až pět pravidel zásad pro export.

## <a name="configure-the-policy"></a>Konfigurace zásad 

1.  Na stránce **svazky** vyberte svazek, pro který chcete nakonfigurovat zásadu exportu, a pak vyberte **Exportovat zásadu**. Při vytváření svazku můžete také nakonfigurovat zásady exportu.

2.  Chcete-li vytvořit pravidlo zásad exportu, zadejte následující informace:   
    * **Index**: zadejte číslo indexu pro pravidlo.  
      
      Zásady exportu můžou mít maximálně pět pravidel. Pravidla se vyhodnocují podle jejich pořadí v seznamu čísel indexu. Pravidla s nižším číslem indexu se vyhodnocují jako první. Například pravidlo s číslem indexu 1 je vyhodnoceno před pravidlem s číslem indexu 2. 

    * **Povolení klienti**: zadejte hodnotu v jednom z následujících formátů:  
      * Adresa IPv4. Příklad: `10.1.12.24`
      * Adresa IPv4 s maskou podsítě vyjádřená počtem bitů Příklad: `10.1.12.10/4`
      * IP adresy oddělené čárkou. Můžete zadat více IP adres hostitele v jednom pravidle tak, že je oddělíte čárkami. Příklad: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Přístup**: vyberte jeden z následujících typů přístupu:  
      * Zakázaný přístup 
      * Čtení a zápis
      * Jen pro čtení

    * Jen **pro čtení** a **čtení a zápis**: Pokud používáte šifrování protokolem Kerberos s nfsv 4.1, postupujte podle pokynů v části [Konfigurace šifrování protokolu Kerberos nfsv 4.1](configure-kerberos-encryption.md).  Dopad na výkon protokolu Kerberos najdete v části [dopad protokolu Kerberos na svazky nfsv 4.1](performance-impact-kerberos.md). 

      ![Možnosti zabezpečení protokolu Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Root Access**: Určete, jestli má `root` účet přístup ke svazku.  Ve výchozím nastavení je přístup root nastavený na **zapnuto** a `root` účet má přístup ke svazku.

      ![Zásada exportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Další kroky 
* [Připojování nebo odpojování svazku pro virtuální počítače](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Správa snímků](azure-netapp-files-manage-snapshots.md)

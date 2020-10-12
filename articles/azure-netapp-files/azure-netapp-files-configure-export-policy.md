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
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533192"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurace zásad exportu pro svazek NFS

Můžete nakonfigurovat zásady exportu pro řízení přístupu ke svazku Azure NetApp Files. Zásada exportu Azure NetApp Files podporuje svazky, které používají protokol NFS (NFSv3 a NFSv 4.1), a duální protokol (NFSv3 a SMB). 

Můžete vytvořit až pět pravidel zásad pro export.

## <a name="steps"></a>Postup 

1.  Na stránce svazky vyberte svazek, pro který chcete nakonfigurovat zásadu exportu, a klikněte na **Exportovat zásadu**. 

    Při vytváření svazku můžete také nakonfigurovat zásady exportu.

2.  Zadejte do následujících polí informace pro vytvoření pravidla zásad exportu:   
    *  **Indexovacím**   
        Určete číslo indexu pro pravidlo.  
        Zásady exportu můžou mít maximálně pět pravidel. Pravidla se vyhodnocují podle jejich pořadí v seznamu čísel indexu. Pravidla s nižším číslem indexu se vyhodnocují jako první. Například pravidlo s číslem indexu 1 je vyhodnoceno před pravidlem s číslem indexu 2. 

    * **Povolení klienti**   
        Zadejte hodnotu v jednom z následujících formátů:  
        * IPv4 adresa, například `10.1.12.24` 
        * IPv4 adresa s maskou podsítě, vyjádřená jako počet bitů, například `10.1.12.10/4`

    * **Přístup**  
        Vyberte jeden z následujících typů přístupu:  
        * Zakázaný přístup 
        * Čtení a zápis
        * Jen pro čtení

    * Jen **pro čtení** a **čtení a zápis**  
        Pokud používáte šifrování protokolem Kerberos s NFSv 4.1, postupujte podle pokynů v části [Konfigurace šifrování protokolu Kerberos v nfsv 4.1](configure-kerberos-encryption.md).  Dopad na výkon protokolu Kerberos najdete v tématu [dopad na výkon protokolu Kerberos v nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance). 

        ![Možnosti zabezpečení protokolu Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Přístup ke kořenu**  
        Určete, jestli má `root` účet přístup ke svazku.  Ve výchozím nastavení je přístup root nastavený na **zapnuto**a `root` účet má přístup ke svazku.

![Zásada exportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Další kroky 
* [Připojování nebo odpojování svazku pro virtuální počítače](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Správa snímků](azure-netapp-files-manage-snapshots.md)

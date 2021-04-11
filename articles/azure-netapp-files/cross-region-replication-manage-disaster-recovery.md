---
title: Správa zotavení po havárii pomocí replikace Azure NetApp Files mezi oblastmi | Microsoft Docs
description: Popisuje, jak spravovat zotavení po havárii pomocí Azure NetApp Files replikace mezi oblastmi.
services: azure-netapp-files
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 5b1c1a5216b7a1ad5b23167e776f2b0bbb0a578f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590989"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Správa zotavení po havárii pomocí replikace mezi oblastmi 

Probíhající replikace mezi zdrojovým a cílovým svazkem (viz [vytvoření replikace svazků](cross-region-replication-create-peering.md)) připraví na událost zotavení po havárii. 

Pokud k takové události dojde, můžete převzít služby při [selhání cílového svazku](#fail-over-to-destination-volume), což klientovi umožní číst a zapisovat do cílového svazku. 

Po zotavení po havárii můžete provést operaci opětovné [synchronizace](#resync-replication) pro navrácení služeb po obnovení do zdrojového svazku. Pak znovu [vytvoříte replikaci zdrojového do cílového umístění](#reestablish-source-to-destination-replication) a znovu připojíte zdrojový svazek, ke kterému má klient přístup. 

Podrobnosti jsou popsány níže. 

## <a name="fail-over-to-destination-volume"></a>Převzetí služeb při selhání cílovým svazkem

Pokud potřebujete aktivovat cílový svazek (například když chcete převzít služby při selhání do cílové oblasti), musíte přerušit partnerský vztah replikace a pak cílový svazek připojit.  

1. Chcete-li přerušit partnerský vztah replikace, vyberte cílový svazek. Klikněte na **replikace** v části služba úložiště.  

2.  Než budete pokračovat, ověřte následující pole:  
    * Ujistěte se, že stav zrcadlení zobrazuje ***zrcadlený svazek***.   
        Nepokoušejte se přerušit partnerský vztah replikace, pokud stav zrcadlení ukazuje *uninicializovaný*.
    * Ujistěte se, že stav vztahu zobrazuje ***nečinné***.   
        Neprovádějte pokus o přerušení replikace partnerských vztahů, pokud stav relace zobrazuje *přenos*.   

    Podívejte se [na téma zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md). 

3.  Klikněte na **přerušit partnerský vztah**.  

4.  Po zobrazení výzvy zadejte **Ano** a klikněte na tlačítko **Break (zrušit** ). 

    ![Přerušit partnerský vztah replikace](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Připojte cílový svazek podle postupu v části [připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Tento krok umožňuje klientovi přístup k cílovému svazku.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>Po zotavení po havárii znovu synchronizovat svazky

Po zotavení po havárii můžete zdrojový svazek znovu aktivovat provedením operace opětovné synchronizace.  Operace opětovné synchronizace stornuje proces replikace a synchronizuje data z cílového svazku do zdrojového svazku.  

> [!IMPORTANT] 
> Operace opětovné synchronizace přepíše data zdrojového svazku daty cílového svazku.  Uživatelské rozhraní vás upozorní na potenciální ztrátu dat. Před zahájením operace se zobrazí výzva k potvrzení akce opětovné synchronizace.

1. Pokud chcete replikaci znovu synchronizovat, vyberte *zdrojový* svazek. Klikněte na **replikace** v části služba úložiště. Pak klikněte na znovu **synchronizovat**.  

2. Po zobrazení výzvy zadejte **Ano** a klikněte na tlačítko znovu **synchronizovat**. 
 
    ![Znovu synchronizovat replikaci](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Pomocí následujících kroků v [části zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)Sledujte stav zdrojového svazku.   
    Pokud stav zdrojového svazku zobrazuje následující hodnoty, operace opětovné synchronizace je dokončena a změny provedené na cílovém svazku jsou nyní zachyceny ve zdrojovém svazku:   

    * Zrcadlený stav: *zrcadlený*  
    * Stav přenosu: *nečinné*  

## <a name="reestablish-source-to-destination-replication"></a>Obnovení replikace mezi zdroji a cílovým umístěním

Po dokončení operace opětovné synchronizace z cílového umístění do zdroje budete muset znovu narušit partnerský vztah replikace a znovu vytvořit replikaci mezi zdroji a cílovým umístěním. Zdrojový svazek byste měli také znovu připojit, aby k němu mohl přistupovat klient.  

1. Přerušit partnerský vztah replikace:  
    a. Vyberte *cílový* svazek. Klikněte na **replikace** v části služba úložiště.  
    b. Než budete pokračovat, ověřte následující pole:   
    * Ujistěte se, že stav zrcadlení zobrazuje ***zrcadlený svazek***.   
    Nepokoušejte se přerušit partnerský vztah replikace, pokud stav zrcadlení ukazuje *uninicializovaný*.  
    * Ujistěte se, že stav vztahu zobrazuje ***nečinné***.   
    Neprovádějte pokus o přerušení replikace partnerských vztahů, pokud stav relace zobrazuje *přenos*.    

        Podívejte se [na téma zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md). 

    c. Klikněte na **přerušit partnerský vztah**.   
    d. Po zobrazení výzvy zadejte **Ano** a klikněte na tlačítko **Break (zrušit** ).  

2. Znovu synchronizovat zdrojový svazek s cílovým svazkem:  
    a. Vyberte *cílový* svazek. Klikněte na **replikace** v části služba úložiště. Pak klikněte na znovu **synchronizovat**.   
    b. Po zobrazení výzvy zadejte **Ano** a klikněte na tlačítko **Opětovná synchronizace** .

3. Znovu připojte zdrojový svazek podle postupu v části [připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Tento krok umožňuje klientovi přístup ke zdrojovému svazku.

## <a name="next-steps"></a>Další kroky  

* [Replikace mezi oblastmi](cross-region-replication-introduction.md)
* [Požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md)
* [Zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)
* [Změna velikosti cílového svazku replikace mezi oblastmi](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Metriky replikace svazků](azure-netapp-files-metrics.md#replication)
* [Odstranění svazků nebo replikací svazků](cross-region-replication-delete.md)
* [Řešení potíží s replikací mezi oblastmi](troubleshoot-cross-region-replication.md)


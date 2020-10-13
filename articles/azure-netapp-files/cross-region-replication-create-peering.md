---
title: Vytvořit partnerský vztah replikace pro Azure NetApp Files | Microsoft Docs
description: Popisuje, jak vytvořit partnerský vztah replikace svazků pro Azure NetApp Files nastavení replikace mezi oblastmi.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: df70f8a37a7223119068afd323583ea6126ca542
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708663"
---
# <a name="create-replication-peering-for-azure-netapp-files"></a>Vytvoření partnerského vztahu replikace pro Azure NetApp Files

> [!IMPORTANT]
> Funkce replikace mezi oblastmi je aktuálně ve verzi Public Preview. Pro přístup k této funkci musíte odeslat žádost pořadníku Azure NetApp Files prostřednictvím stránky pro [odeslání pořadníku replikace mezi oblastmi](https://aka.ms/anfcrrpreviewsignup). Než použijete funkci replikace mezi oblastmi, počkejte na oficiální e-mail s potvrzením od Azure NetApp Files týmu.

V tomto článku se dozvíte, jak nastavit replikaci mezi oblastmi vytvořením partnerského vztahu replikace. 

Nastavení partnerského vztahu replikace umožňuje asynchronně replikovat data z Azure NetApp Filesho svazku (zdroje) do jiného svazku Azure NetApp Files (cíle). Zdrojový svazek a cílový svazek musí být nasazeny v samostatných oblastech. Úroveň služby pro cílový fond kapacity se může shodovat s úrovní zdrojové kapacity fondu nebo můžete vybrat jinou úroveň služby.   

Replikace Azure NetApp Files v současné době nepodporuje více předplatných; všechny replikace se musí provádět v rámci jednoho předplatného.

Než začnete, ujistěte se, že jste zkontrolovali [požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md).  

## <a name="locate-the-source-volume-resource-id"></a>Vyhledejte ID prostředku zdrojového svazku.  

Potřebujete získat ID prostředku zdrojového svazku, který chcete replikovat. 

1. Otevřete zdrojový svazek a v části nastavení vyberte **vlastnosti** . zobrazí se ID prostředku zdrojového svazku.   
    ![Najít ID prostředku zdrojového svazku](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Zkopírujte ID prostředku do schránky.  Budete ho potřebovat později.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Vytvoření svazku replikace dat (cílový svazek)

Musíte vytvořit cílový svazek, do kterého chcete replikovat data ze zdrojového svazku.  Než budete moct vytvořit cílový svazek, musíte mít účet NetApp a fond kapacit v cílové oblasti. 

1. Cílový účet musí být v jiné oblasti než v oblasti zdrojového svazku. V případě potřeby vytvořte účet NetApp v oblasti Azure, který se bude používat pro replikaci, podle kroků v části [Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md).   
Můžete také vybrat existující účet NetApp v jiné oblasti.  

2. V případě potřeby vytvořte v nově vytvořeném účtu NetApp fond kapacit podle kroků v části [nastavení fondu kapacit](azure-netapp-files-set-up-capacity-pool.md).   

    Můžete také vybrat existující fond kapacit pro hostování cílového svazku replikace.  

    Úroveň služby pro cílový fond kapacity se může shodovat s úrovní zdrojové kapacity fondu nebo můžete vybrat jinou úroveň služby.

3. Pověřit podsíť v oblasti, která se má použít pro replikaci, podle kroků v části [delegování podsítě na Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

4. Vytvořte svazek replikace dat tak, že v části služba úložiště v cílovém účtu NetApp vyberete **svazky** . Pak klikněte na tlačítko **+ Přidat replikaci dat** .  

    ![Přidat replikaci dat](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. Na zobrazené stránce vytvořit svazek vyplňte následující pole na kartě **základy** :
    * Název svazku
    * Fond kapacit
    * Kvóta svazku
        > [!NOTE] 
        > Doporučuje se zrcadlit velikost kvóty svazku zdrojového svazku.
    * Virtuální síť 
    * Podsíť

    Podrobnosti o polích najdete v tématu [vytvoření svazku NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume). 

6. Na kartě **protokol** vyberte stejný protokol jako zdrojový svazek.  
V případě protokolu NFS zajistěte, aby pravidla zásad exportu splňovala požadavky všech hostitelů ve vzdálené síti, které budou mít přístup k exportu.  

7. Na kartě **značky** vytvořte páry klíč/hodnota podle potřeby.  

8. Na kartě **replikace** vložte do pole ID prostředku zdrojového svazku, které jste získali v části [Najděte ID prostředku zdrojového svazku](#locate-the-source-volume-resource-id)a potom vyberte požadovaný plán replikace. Mezi možnosti pro plán replikace patří: každých 10 minut, každou hodinu, denně, týdně a měsíčně.  

    ![Vytvoření replikace svazků](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Klikněte na tlačítko **zkontrolovat + vytvořit**a potom kliknutím na tlačítko **vytvořit** vytvořte svazek replikace dat.   

    ![Kontrola a vytváření replikace](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Autorizovat replikaci ze zdrojového svazku  

K autorizaci replikace musíte získat ID prostředku cílového svazku replikace a vložit ho do pole autorizovat zdrojového svazku replikace. 

1. V Azure Portal přejděte na Azure NetApp Files.

2. Přejít do cílového účtu NetApp a cílového fondu kapacity, kde se nachází cílový svazek pro replikaci.

3. Vyberte svazek cíle replikace, přejděte na **vlastnosti** v části nastavení a vyhledejte **ID prostředku** cílového svazku. Zkopírujte ID prostředku cílového svazku do schránky.

    ![ID prostředku vlastností](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. V Azure NetApp Files přejdete na zdrojový účet replikace a zdrojový fond kapacity. 

5. Vyhledejte zdrojový svazek replikace a vyberte jej. Přejděte na **replikace** v části služba úložiště a klikněte na **autorizovat**.

    ![Autorizovat replikaci](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. Do pole autorizovat Vložte ID prostředku cílového replikačního svazku, který jste získali v kroku 3, a pak klikněte na **OK**.

## <a name="next-steps"></a>Další kroky  

* [Replikace mezi oblastmi](cross-region-replication-introduction.md)
* [Požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md)
* [Zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)
* [Metriky replikace svazků](azure-netapp-files-metrics.md#replication)
* [Správa zotavení po havárii](cross-region-replication-manage-disaster-recovery.md)
* [Řešení potíží při replikaci mezi oblastmi](troubleshoot-cross-region-replication.md)


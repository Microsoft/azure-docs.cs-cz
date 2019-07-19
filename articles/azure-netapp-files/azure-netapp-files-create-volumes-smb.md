---
title: Vytvoření svazku SMB pro Azure NetApp Files | Microsoft Docs
description: Popisuje postup vytvoření svazku SMB pro Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 6e425eba3159f8840e1a7960f6a6c3171b1ba163
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850415"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Vytvoření svazku SMB pro Azure NetApp Files

Azure NetApp Files podporuje svazky NFS a SMBv3. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. V tomto článku se dozvíte, jak vytvořit svazek SMBv3. Pokud chcete vytvořit svazek NFS, přečtěte si téma [vytvoření svazku NFS pro Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Před zahájením 
Musíte mít už nastavený fond kapacity.   
[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)   
Podsíť musí být delegovaná na Azure NetApp Files.  
[Delegování podsítě na Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Požadavky na připojení služby Active Directory

 Před vytvořením svazku SMB musíte vytvořit připojení ke službě Active Directory. Požadavky na připojení služby Active Directory jsou následující: 

* Účet správce, který použijete, musí být schopný vytvářet účty počítačů v cestě organizační jednotky (OU), kterou zadáte.  

* V příslušném serveru služby Windows Active Directory (AD) musí být otevřeny správné porty.  
    Požadované porty jsou následující: 

    |     Služba           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    Webové služby AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Není k dispozici       |    Odpověď na ozvěnu    |
    |    Sdílené           |    464       |    TCP           |
    |    Sdílené           |    464       |    UDP           |
    |    Sdílené           |    88        |    TCP           |
    |    Sdílené           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Název pro rozhraní NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    Protokol Secure LDAP        |    636       |    TCP           |
    |    Protokol Secure LDAP        |    3269      |    TCP           |
    |    W32Time            |    123       |    UDP           |

## <a name="create-an-active-directory-connection"></a>Vytvoření připojení ke službě Active Directory

1. Z účtu NetApp klikněte na **připojení služby Active Directory**a pak klikněte na **připojit**.  

    ![Připojení ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. V okně připojit se ke službě Active Directory zadejte následující informace:

    * **Primární DNS**  
        Toto je služba DNS potřebná pro operace připojení k doméně služby Active Directory a ověřování SMB. 
    * **Sekundární DNS**   
        Toto je sekundární server DNS pro zajištění redundantních názvových služeb. 
    * **Domény**  
        Toto je název domény vašeho Active Directory Domain Services, ke které se chcete připojit.
    * **Předpona serveru SMB (účet počítače)**  
        Toto je předpona názvů pro účet počítače ve službě Active Directory, kterou Azure NetApp Files použít pro vytváření nových účtů.

        Pokud například standardní názvový server, který vaše organizace používá pro souborové servery, je NAS-01, NAS-02..., NAS-045, zadejte jako předponu "NAS". 

        Služba vytvoří ve službě Active Directory další účty počítačů podle potřeby.

    * **Cesta organizační jednotky**  
        Jedná se o cestu protokolu LDAP pro organizační jednotku (OU), kde budou vytvořeny účty počítačů serveru SMB. To znamená OU = druhá úroveň, OU = First Level. 
    * Přihlašovací údaje, včetně **uživatelského jména** a **hesla**

    ![Připojit ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klikněte na **Připojit**.  

    Zobrazí se připojení služby Active Directory, které jste vytvořili.

    ![Připojení ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Přidat svazek SMB

1. V okně fondy kapacit klikněte na okno **svazky** . 

    ![Přejít na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno vytvořit svazek.

3. V okně vytvořit svazek klikněte na **vytvořit** a zadejte informace pro následující pole:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název svazku musí být v rámci každého fondu kapacity jedinečný. Musí mít aspoň tři znaky dlouhé. Můžete použít jakékoli alfanumerické znaky.

    * **Fond kapacit**  
        Zadejte fond kapacit, ve kterém chcete vytvořit svazek.

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  

        Virtuální síť, kterou zadáte, musí mít přidělenou podsíť Azure NetApp Files. Služba Azure NetApp Files se dá použít jenom ze stejné virtuální sítě nebo z virtuální sítě, která se nachází ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuálních sítí. Ke svazku z místní sítě se můžete dostat i přes Express Route.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro svazek.  
        Podsíť, kterou zadáte, musí být delegovaná na Azure NetApp Files. 
        
        Pokud jste nedelegovanou podsíť, můžete na stránce vytvořit svazek kliknout na **vytvořit novou** . Pak na stránce vytvořit podsíť zadejte informace o podsíti a vyberte možnost **Microsoft. NetApp/** Volumes pro delegování podsítě pro Azure NetApp Files. V každé virtuální síti je možné delegovat jenom jednu podsíť na Azure NetApp Files.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klikněte na **protokol** a vyplňte následující informace:  
    * Jako typ protokolu pro svazek vyberte **SMB** . 
    * V rozevíracím seznamu vyberte připojení ke **službě Active Directory** .
    * Zadejte název sdíleného svazku do pole **název sdílené složky**.

    ![Zadat protokol SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte podrobnosti o svazku.  Pak kliknutím na **vytvořit** vytvořte svazek SMB.

    Svazek, který jste vytvořili, se zobrazí na stránce svazky. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="next-steps"></a>Další postup  

* [Připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informace o integraci virtuální sítě pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

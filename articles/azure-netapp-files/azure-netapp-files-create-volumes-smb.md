---
title: Vytvoření svazku SMB pro Azure NetApp Files | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit SMB3 svazek v Azure NetApp Files. Seznamte se s požadavky na připojení služby Active Directory a doménové služby.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: 28fc465627032522afb9da8f6ec0fad704834d09
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813699"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Vytvoření svazku SMB pro Azure NetApp Files

Azure NetApp Files podporuje vytváření svazků pomocí systému souborů NFS (NFSv3 a NFSv 4.1), SMB3 nebo duálního protokolu (NFSv3 a SMB). Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. 

V tomto článku se dozvíte, jak vytvořit svazek SMB3. Informace o svazcích NFS najdete v tématu [vytvoření svazku NFS](azure-netapp-files-create-volumes.md). Svazky s duálním protokolem najdete v tématu [vytvoření svazku se dvěma protokoly](create-volumes-dual-protocol.md).

## <a name="before-you-begin"></a>Než začnete 

* Musíte mít už nastavený fond kapacity. Viz [nastavení fondu kapacit](azure-netapp-files-set-up-capacity-pool.md).     
* Podsíť musí být delegovaná na Azure NetApp Files. Viz [delegování podsítě na Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Konfigurace připojení ke službě Active Directory 

Před vytvořením svazku SMB je potřeba vytvořit připojení ke službě Active Directory. Pokud jste nenakonfigurovali připojení služby Active Directory pro soubory Azure NetApp, postupujte podle pokynů uvedených v tématu [Vytvoření a Správa připojení ke službě Active Directory](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Přidat svazek SMB

1. V okně fondy kapacit klikněte na okno **svazky** . 

    ![Přejít na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno vytvořit svazek.

3. V okně vytvořit svazek klikněte na **vytvořit** a zadejte informace pro následující pole na kartě základy:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název svazku musí být v rámci každého fondu kapacity jedinečný. Musí mít aspoň tři znaky dlouhé. Můžete použít jakékoli alfanumerické znaky.   

        Nemůžete použít `default` nebo `bin` jako název svazku.

    * **Fond kapacit**  
        Zadejte fond kapacit, ve kterém chcete vytvořit svazek.

    * **Přidělení**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Propustnost (MiB/S)**   
        Pokud je svazek vytvořený v manuálním fondu kapacity QoS, určete propustnost, kterou pro svazek požadujete.   

        Pokud se svazek vytvoří ve fondu kapacity auto QoS, hodnota zobrazená v tomto poli je (propustnost × úroveň služby).   

    * **Virtuální síť**  
        Zadejte službu Azure Virtual Network (VNet), ze které chcete získat přístup ke svazku.  

        Virtuální síť, kterou zadáte, musí mít přidělenou podsíť Azure NetApp Files. Služba Azure NetApp Files se dá použít jenom ze stejné virtuální sítě nebo z virtuální sítě, která se nachází ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuálních sítí. Ke svazku z místní sítě se můžete dostat i přes Express Route.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro svazek.  
        Podsíť, kterou zadáte, musí být delegovaná na Azure NetApp Files. 
        
        Pokud jste nedelegovanou podsíť, můžete na stránce vytvořit svazek kliknout na **vytvořit novou** . Pak na stránce vytvořit podsíť zadejte informace o podsíti a vyberte možnost **Microsoft. NetApp/** Volumes pro delegování podsítě pro Azure NetApp Files. V každé virtuální síti je možné delegovat jenom jednu podsíť na Azure NetApp Files.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Pokud chcete pro svazek použít existující zásadu snímku, rozbalte ji kliknutím na **Zobrazit Upřesnit oddíl** , určete, jestli chcete cestu k snímku skrýt, a v rozevírací nabídce vyberte zásadu snímku. 

        Informace o vytváření zásad snímku najdete v tématu [Správa zásad snímků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Zobrazit rozšířený výběr](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klikněte na **protokol** a vyplňte následující informace:  
    * Jako typ protokolu pro svazek vyberte **SMB** . 
    * V rozevíracím seznamu vyberte připojení ke **službě Active Directory** .
    * Zadejte název sdíleného svazku do pole  **název sdílené složky**.
    * Pokud chcete povolit šifrování pro SMB3, vyberte **Povolit šifrování protokolu SMB3**.   
        Tato funkce umožňuje šifrování pro SMB3 data v letovém řádu. Klienti SMB, kteří nepoužívají šifrování SMB3, nebudou mít k tomuto svazku přístup.  Data v klidovém stavu jsou šifrována bez ohledu na toto nastavení.  
        Další informace najdete v tématu [Nejčastější dotazy týkající se šifrování protokolu SMB](azure-netapp-files-faqs.md#smb-encryption-faqs) . 

        Funkce **šifrování protokolu SMB3** je aktuálně ve verzi Preview. Pokud tuto funkci používáte poprvé, zaregistrujte funkci před jejím použitím: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Ověřte stav registrace funkce: 

        > [!NOTE]
        > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte na stav `Registered` .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Můžete také použít [příkazy rozhraní příkazového řádku Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace.  
    * Pokud chcete povolit nepřetržitou dostupnost pro svazek SMB, vyberte možnost **Povolit nepřetržitou dostupnost**.    

        > [!IMPORTANT]   
        > Funkce nepřetržité dostupnosti SMB je aktuálně ve verzi Public Preview. Pro přístup k této funkci musíte odeslat žádost pořadníku pomocí **[sdílených složek Azure NETAPP Files SMB nepřetržité dostupnosti Public Preview stránky pro odeslání pořadníku](https://aka.ms/anfsmbcasharespreviewsignup)**. Před použitím funkce nepřetržité dostupnosti počkejte na oficiální e-mail s potvrzením od Azure NetApp Files týmu.   
        > 
        > Měli byste povolit nepřetržitou dostupnost jenom pro SQL Server a [FsLogix kontejnery profilů uživatelů](../virtual-desktop/create-fslogix-profile-container.md). Použití sdílených složek SMB pro jiné úlohy než SQL Server a FsLogix kontejnerů profilů *uživatelů se nepodporuje* . Tato funkce je v současnosti podporovaná ve Windows SQL Server. Linux SQL Server se momentálně nepodporuje. Pokud k instalaci SQL Server používáte účet bez oprávnění správce (doména), ujistěte se, že má účet přiřazená požadovaná oprávnění zabezpečení. Pokud účet domény nemá požadovaná oprávnění zabezpečení ( `SeSecurityPrivilege` ) a oprávnění nelze nastavit na úrovni domény, můžete oprávnění účtu udělit pomocí pole **uživatelé oprávnění zabezpečení** připojení služby Active Directory. Viz téma [vytvoření připojení ke službě Active Directory](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Snímek obrazovky s popisem karty protokolu pro vytvoření svazku protokolu SMB.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte podrobnosti o svazku.  Pak kliknutím na **vytvořit** vytvořte svazek SMB.

    Svazek, který jste vytvořili, se zobrazí na stránce svazky. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="control-access-to-an-smb-volume"></a>Řízení přístupu ke svazku SMB  

Přístup ke svazku SMB je spravovaný prostřednictvím oprávnění.  

### <a name="share-permissions"></a>Oprávnění ke sdílení  

Ve výchozím nastavení má nový svazek oprávnění ke sdílení **všech uživatelů a úplné řízení** . Členové skupiny Domain Admins můžou oprávnění ke sdílení změnit takto:  

1. Namapujte sdílenou složku na jednotku.  
2. Pravým tlačítkem myši klikněte na jednotku, vyberte **vlastnosti** a pak přejděte na kartu **zabezpečení** .

[![Nastavení oprávnění ke sdílení](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>Oprávnění k souborům a složkám NTFS  

Oprávnění pro soubor nebo složku můžete nastavit pomocí karty **zabezpečení** vlastností objektu v klientovi Windows SMB.
 
![Nastavení oprávnění k souborům a složkám](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Další kroky  

* [Připojení nebo odpojení svazku pro virtuální počítače s Windows nebo Linuxem](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Nejčastější dotazy k protokolu SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Řešení potíží se svazky SMB nebo Dual-Protocol](troubleshoot-dual-protocol-volumes.md)
* [Informace o integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instalace nové doménové struktury služby Active Directory pomocí Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)

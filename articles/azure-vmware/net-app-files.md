---
title: Soubory NetApp pro řešení Azure VMware
description: Pomocí Azure NetApp Files s virtuálními počítači Azure VMware můžete migrovat a synchronizovat data napříč místními servery, virtuálními počítači řešení Azure VMware a cloudovou infrastrukturou.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 0adf137db358d3561365a3a1ed2def338eeeb158
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708711"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>Soubory NetApp pro řešení Azure VMware

V tomto článku Vás provedeme kroky integrace Azure NetApp Files s využitím úloh Azure VMware pro řešení. [Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) je služba Azure pro migraci a spouštění podnikových souborových úloh v cloudu beze změn kódu. Umožňuje snadnou migraci dat napříč místními i cloudovou infrastrukturou. Rychlá synchronizace dat s vylepšeným zabezpečením zjednodušuje scénáře migrace a DevOps díky funkcím, jako je okamžitý snímek, obnovení a integrace služby Active Directory.

## <a name="topology"></a>Topologie

V tomto scénáři otestujete a ověříte sdílenou složku Azure NetApp Files fondu Azure NetApp Files je nakonfigurovaná v Azure s fondem kapacit, fondem svazků a podsítí. Používali jsme protokol NFS. Řešení Azure NetApp Files i Azure VMware se vytvářejí ve stejné oblasti Azure Východní USA. Připojení k řešení Azure VMware je prostřednictvím Azure ExpressRoute.

![NetApp soubory pro topologii řešení Azure VMware.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Požadavky 

> [!div class="checklist"]
> * Předplatné Azure s Azure NetApp Files
> * Podsíť pro soubor Azure NetApp
> * Virtuální počítač se systémem Linux v řešení VMware Azure
> * Virtuální počítač s Windows v řešení VMware Azure

## <a name="verify-configuration-of-azure-netapp-files"></a>Ověřit konfiguraci Azure NetApp Files 

Nejdřív ověřte konfiguraci Azure NetApp Files vytvořenou v Azure na disku Premium.

1. V Azure Portal v části **úložiště**vyberte **Azure NetApp Files**. Zobrazí se seznam nakonfigurovaných Azure NetApp Files.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Seznam Azure NetApp Files."::: 

2. Pokud vyberete účet NetApp, můžete zobrazit různá nastavení. Pokud například vyberete **Contoso-anf2**, zobrazí se jeho nastavení. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Zobrazí nastavení účtu NetApp."::: 

3. Vyberte **fondy kapacit** pro ověření nakonfigurovaného fondu. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Ověřte nakonfigurovaný fond.":::

    Můžete vidět, že fond úložiště je nakonfigurovaný jako 4 TiB s diskem Premium.

4. Vyberte **svazky** (viz snímek obrazovky z kroku 2), chcete-li zobrazit svazky vytvořené v rámci fondu kapacit.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Azure NetApp Files svazek.":::

5. Vyberte svazek, pro který chcete zobrazit jeho konfiguraci.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Podrobnosti o konfiguraci svazku.":::

    Můžete vidět, že fond svazků anfpool s 200 GiB byl vytvořen jako sdílená složka NetApp Files. Pro Azure NetApp Files se vytvořila privátní virtuální síť IP, která umožňuje připojení k systému souborů NFS na virtuálních počítačích řešení Azure VMware. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Protokoly podporované nástrojem Azure NetApp Files  

Azure NetApp Files podporuje mapování sdílených složek protokolu SMB (Server Message Block) a systému souborů NFS (Network File System). 

- **Sdílená složka SMB**: Pokud chcete nasadit svazek SMB, musíte nejdřív vytvořit připojení ke službě Active Directory. Zadané řadiče domény musí být přístupné delegované podsíti Azure NetApp Files pro úspěšné připojení. Po nakonfigurování služby Active Directory v rámci Azure NetApp Files účtu se při vytváření svazků SMB zobrazí jako položka, která je k možnost výběru. 

- **Sdílená složka NFS**: Azure NetApp Files přispívá k vytváření svazků pomocí systému souborů NFS (NFSv3 a nfsv 4.1), SMBv3 nebo duálního protokolu (NFSV3 a SMB). Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. Systém souborů NFS lze připojit k serveru Linux pomocí příkazového řádku.

## <a name="create-azure-netapp-files"></a>Vytvořit Azure NetApp Files 

1. Přihlaste se k [Azure Portal](https://rc.portal.azure.com/#home). V části služby Azure vyberte **Azure NetApp Files**. 

2. Vyberte **+ Přidat** a vytvořte nový svazek Azure NetApp Files. 

3. Vyplňte požadovaná pole (název, předplatné, skupina prostředků a umístění) a vyberte **vytvořit**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Přidat fondy kapacit do Azure NetApp Files 

1. V Azure Portal vyberte **Azure NetApp Files**, vyberte **fondy kapacit** a **+ Přidat fond**. 

2. Zadejte požadované podrobnosti pro název fondu svazků, úroveň služby a velikost disku (FQDN) nebo IP a váhu. Vyberte **Přidat**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Přidejte fond kapacit k NetApp souborů.":::

3. Pokud chcete vytvořit svazky v rámci fondu kapacit, vyberte **svazky** v podokně hledání a vyberte **+ Přidat svazek**. 
 
4. Vyplňte požadovaná pole, jak je znázorněno na následujícím snímku obrazovky.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Přidejte svazky do fondu kapacit.":::

5. Na další stránce vyberte typ protokolu sdílení. Vyberte verze, pokud se jedná o sdílenou složku NFS, a vyberte doménu služby Active Directory, pokud se jedná o sdílenou složku SMB.  

6. Pokud se jedná o sdílenou složku systému souborů NFS, přidejte zdrojovou IP adresu, ze které bude k dispozici sdílená položka typu protokolu. Vyberte **Zkontrolovat a vytvořit**. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Vyberte podrobnosti o svazku.":::
 
    V části Azure NetApp Files v Azure Portal je vaše sdílená složka systému souborů NFS připravená k použití.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Sdílená složka NFS je připravená.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Připojení sdílené složky NFS na virtuálních počítačích řešení Azure VMware

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Připojení sdílené složky NFS na virtuálním počítači s Windows Azure VMware řešení

- Otevřete příkazový řádek a spusťte příkaz pro mapování sdílené složky systému souborů NFS. Následující snímky obrazovky ukazují jednotku Share namapovanou na virtuálním počítači s Windows v řešení Azure VMware.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Sdílejte jednotku namapovanou na virtuálním počítači s Windows v řešení Azure VMware.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Sdílejte jednotku namapovanou na virtuálním počítači s Windows.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Připojení sdílené složky systému souborů NFS na virtuálním počítači řešení Azure VMware pro Linux

#### <a name="setting-up-your-azure-instance"></a>Nastavení instance Azure

1. V Azure Portal přidružte instanci Azure k podsíti definované ve stejné virtuální síti jako svazek.

    > [!NOTE]
    > Podsíť potřebuje pravidlo skupiny zabezpečení sítě, které umožňuje provoz na portech NFS (2049, 111), UDP a TCP.

2. Otevřete klienta SSH a připojte se k instanci Azure. Další informace najdete v tématu [použití klíčů ssh s Windows v Azure](../virtual-machines/linux/ssh-from-windows.md).

3. Nainstalujte do instance Azure klienta NFS:
   - V Red Hat Enterprise Linux nebo SUSE Linux instance: `sudo yum install -y nfs-utils`
   - V instanci Ubuntu nebo Debian: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Připojení systému souborů

1. Vytvořte nový adresář v instanci Azure: `sudo mkdir ANFPOOL`

2. Vyberte cílovou IP adresu připojení.

3. Připojte systém souborů: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Kořenový test.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Vytvoření připojení služby Active Directory pro sdílenou složku SMB

1. V Azure Portal vyberte účet NetApp.

2. V části Azure NetApp Files vyberte **připojení ke službě Active Directory**.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Připojení služby Active Directory."::: 

3. Vyberte **připojit** a připojte se ke sdílené složce SMB ke službě Active Directory. Následující snímek obrazovky ukazuje podrobnosti o doméně sdílené složky SMB.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Podrobnosti o doméně sdílené složky SMB"::: 

    Sdílená složka SMB Azure je připravená k použití.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Sdílení souborů SMB je připravené."::: 

4. Namapujte sdílenou složku SMB na virtuální počítač řešení Windows Azure VMware. Použijte cestu pro připojení SMB, jak je znázorněno na předchozím snímku obrazovky. Další informace najdete v tématu [mapování síťové jednotky ve Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive) .

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [hierarchii úložiště Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Viz [Správa životního cyklu virtuálních počítačů řešení Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md) .
- Viz [integrace řešení Azure VMware v architektuře hub a paprsků](concepts-avs-hub-and-spoke-integration.md) .

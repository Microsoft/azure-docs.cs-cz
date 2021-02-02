---
title: Azure NetApp Files s využitím řešení Azure VMware
description: Pomocí Azure NetApp Files s virtuálními počítači řešení VMware Azure můžete migrovat a synchronizovat data na místních serverech, virtuálních počítačích řešení Azure VMware a cloudových infrastrukturách.
ms.topic: how-to
ms.date: 01/20/2021
ms.openlocfilehash: b4ffea23606e1829fbf713488f08bc5618180072
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430058"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files s využitím řešení Azure VMware

V tomto článku Vás provedeme kroky integrace Azure NetApp Files s využitím úloh Azure VMware pro řešení. Hostovaný operační systém se spustí v rámci virtuálních počítačů, které přistupují k Azure NetApp Files svazků. 

## <a name="azure-netapp-files-overview"></a>Přehled Azure NetApp Files

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) je služba Azure First stran pro migraci a spouštění nejnáročnějších podnikových souborových úloh v cloudu, včetně databází, SAP a vysoce výkonných výpočetních aplikací bez jakýchkoli změn kódu.

### <a name="features"></a>Funkce
(Služby, kde se používají Azure NetApp Files.)

- **Připojení ke službě Active Directory**: Azure NetApp Files podporuje [Active Directory Domain Services a Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Protokol sdílení**: Azure NetApp Files podporuje protokoly SMB (Server Message Block) a NFS (Network File System). Tato podpora znamená, že svazky lze připojit na klienta se systémem Linux a lze je namapovat na klienta systému Windows.

- **Řešení Azure VMware**: sdílené složky Azure NetApp Files můžete připojit z virtuálních počítačů, které jsou vytvořené v prostředí řešení Azure VMware.

Azure NetApp Files je k dispozici v mnoha oblastech Azure a podporuje replikaci mezi jednotlivými oblastmi. Informace o metodách konfigurace Azure NetApp Files najdete v tématu věnovaném [hierarchii úložiště Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Referenční architektura

Následující diagram znázorňuje připojení prostřednictvím Azure ExpressRoute k privátnímu cloudu řešení Azure VMware. Zobrazuje využití Azure NetApp Files sdílené složky připojená k virtuálním počítačům řešení VMware Azure, ke kterým se přistupovalo v prostředí řešení Azure VMware.

![Diagram znázorňující NetApp soubory pro architekturu řešení Azure VMware.](media/net-app-files/net-app-files-topology.png)

Tento článek obsahuje pokyny pro nastavení, testování a ověření Azure NetApp Filesho svazku jako sdílené složky pro virtuální počítače řešení Azure VMware. V tomto scénáři jsme použili protokol NFS. Řešení Azure NetApp Files a Azure VMware se vytvářejí ve stejné oblasti Azure.

## <a name="prerequisites"></a>Požadavky 

> [!div class="checklist"]
> * Předplatné Azure s povoleným Azure NetApp Files
> * Podsíť pro Azure NetApp Files
> * Virtuální počítač se systémem Linux v řešení VMware Azure
> * Virtuální počítače s Windows v řešení VMware Azure

## <a name="regions-supported"></a>Podporované oblasti

Seznam podporovaných oblastí najdete v části [produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Ověřit předem nakonfigurované Azure NetApp Files 

Postupujte podle podrobných pokynů v následujících článcích a vytvořte a připojte Azure NetApp Files svazky do virtuálních počítačů řešení Azure VMware.

- [Vytvoření účtu NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Nastavení fondu kapacity](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Vytvoření svazku SMB pro službu Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Vytvoření svazku NFS pro službu Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegování podsítě na službu Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Následující kroky zahrnují ověření předem nakonfigurovaných Azure NetApp Files vytvořených v Azure na úrovni služby Azure NetApp Files Premium.

1. V Azure Portal v části **úložiště** vyberte **Azure NetApp Files**. Zobrazí se seznam nakonfigurovaných Azure NetApp Files. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Snímek obrazovky se seznamem předem nakonfigurovaných Azure NetApp Files"::: 

2. Vyberte nakonfigurovaný účet souborů NetApp a zobrazte jeho nastavení. Vyberte například **Contoso-anf2**. 

3. Vyberte **fondy kapacit** pro ověření nakonfigurovaného fondu. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Snímek obrazovky znázorňující možnosti zobrazení fondů kapacity a svazků nakonfigurovaných NetApp souborů.":::

    Otevře se stránka fondy kapacit znázorňující kapacitu a úroveň služeb. V tomto příkladu je fond úložiště nakonfigurovaný jako 4 TiB s úrovní Premium Service.

4. Výběrem možnosti **svazky** zobrazíte svazky vytvořené v rámci fondu kapacit. (Viz předchozí snímek obrazovky.)

5. Vyberte svazek, pro který chcete zobrazit jeho konfiguraci.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Snímek obrazovky zobrazující svazky vytvořené pod fondem kapacit":::

    Otevře se okno se zobrazenými podrobnostmi o konfiguraci svazku.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Snímek obrazovky zobrazující podrobnosti o konfiguraci svazku.":::

    Můžete vidět, že svazek anfvolume s velikostí 200 GiB byl vytvořen ve fondu kapacity anfpool1 a exportován jako sdílená složka systému souborů NFS prostřednictvím 10.22.3.4:/ANFVOLUME. Pro Azure NetApp Files se vytvořila jedna privátní IP adresa z Azure Virtual Network (VNet) a cesta k systému souborů NFS, která se má na virtuálním počítači připojit. Informace o Azure NetApp Files výkon svazku vzhledem k velikosti ("kvóta") najdete v tématu věnovaném [důležitým informacím o výkonu pro Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Ověřit předkonfigurované mapování sdílené složky virtuálních počítačů řešení Azure VMware

Než předvádí přístupnost Azure NetApp Files sdílet s virtuálním počítačem řešení Azure VMware, je důležité pochopit mapování sdílených složek SMB a NFS. Až budete moct nakonfigurovat svazky SMB nebo NFS, je možné je připojit, jak je popsáno zde.

- Sdílená složka SMB: před nasazením svazku SMB vytvořte připojení ke službě Active Directory. Zadané řadiče domény musí být přístupné delegované podsíti Azure NetApp Files pro úspěšné připojení. Po nakonfigurování služby Active Directory v rámci Azure NetApp Files účtu se při vytváření svazků SMB zobrazí jako vybranější položka.

- Sdílená složka NFS: Azure NetApp Files přispívá k vytváření svazků pomocí systému souborů NFS nebo duálního protokolu (NFS a SMB). Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. NFS se dá připojit k serveru Linux pomocí příkazových řádků nebo položek/etc/fstab.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Použití případů Azure NetApp Files s řešením Azure VMware

Následuje jenom několik přesvědčivých Azure NetApp Files případů použití. 
- Správa profilů horizontu
- Správa profilů Citrix
- Správa profilů služby Vzdálená plocha
- Sdílené složky v řešení VMware Azure

## <a name="next-steps"></a>Další kroky

Po integraci Azure NetApp Files s využitím úloh řešení Azure VMware můžete chtít získat další informace v těchto případech:

- [Omezení prostředků pro Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- [Pokyny pro Azure NetApp Files plánování sítě](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- [Replikace Azure NetApp Files svazků mezi oblastmi](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Nejčastější dotazy týkající se Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md).

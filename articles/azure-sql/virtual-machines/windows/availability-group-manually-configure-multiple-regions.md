---
title: Konfigurace skupiny dostupnosti Always On SQL Server v různých oblastech
description: Tento článek vysvětluje, jak ve virtuálních počítačích Azure nakonfigurovat skupinu dostupnosti Always On SQL Server s replikou v jiné oblasti.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: f312b690ac7743b1574dbbec9d408b3fafbb0194
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263177"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>Konfigurace skupiny dostupnosti Always On SQL Server napříč různými oblastmi Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek vysvětluje, jak na virtuálních počítačích Azure nakonfigurovat repliku skupiny dostupnosti Always On SQL Server ve vzdáleném umístění Azure. Tuto konfiguraci použijte k podpoře zotavení po havárii.

Tento článek se týká Azure Virtual Machines v režimu Správce prostředků.

Na následujícím obrázku vidíte běžné nasazení skupiny dostupnosti na virtuálních počítačích Azure:

   ![Skupina dostupnosti](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

V tomto nasazení jsou všechny virtuální počítače v jedné oblasti Azure. Repliky skupin dostupnosti můžou mít synchronní potvrzení s automatickým převzetím služeb při selhání na SQL-1 a SQL-2. Pokud chcete vytvořit tuto architekturu, přečtěte si téma [Šablona skupiny dostupnosti nebo kurz](availability-group-overview.md).

Tato architektura je zranitelná v případě výpadku, pokud je oblast Azure nepřístupná. Pokud chcete tuto chybu zabezpečení překonat, přidejte repliku do jiné oblasti Azure. Následující diagram znázorňuje, jak by nová architektura vypadala:

   ![Skupina dostupnosti DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

Předchozí diagram znázorňuje nový virtuální počítač s názvem SQL-3. SQL-3 je v jiné oblasti Azure. SQL-3 se přidá do clusteru s podporou převzetí služeb při selhání se systémem Windows Server. SQL-3 může hostovat repliku skupiny dostupnosti. Nakonec si všimněte, že oblast Azure pro SQL-3 má nový nástroj pro vyrovnávání zatížení Azure.

>[!NOTE]
> Pokud je víc než jeden virtuální počítač ve stejné oblasti, vyžaduje se skupina dostupnosti Azure. Pokud je v této oblasti jenom jeden virtuální počítač, Skupina dostupnosti se nevyžaduje. Virtuální počítač můžete umístit pouze do skupiny dostupnosti v okamžiku vytvoření. Pokud je virtuální počítač již ve skupině dostupnosti, můžete později přidat virtuální počítač pro další repliku.

V této architektuře je replika ve vzdálené oblasti obvykle nakonfigurovaná s režimem dostupnosti s asynchronním potvrzováním a režimem ručního převzetí služeb při selhání.

Když jsou repliky skupin dostupnosti na virtuálních počítačích Azure v různých oblastech Azure, každá oblast vyžaduje:

* Brána virtuální sítě
* Připojení brány virtuální sítě

Následující diagram znázorňuje, jak sítě komunikují mezi datovými centry.

   ![Skupina dostupnosti](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>Tato architektura nenese poplatky za odchozí data pro data replikovaná mezi oblastmi Azure. Podívejte se na téma [ceny šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Vytvořit vzdálenou repliku

Pokud chcete vytvořit repliku ve vzdáleném datovém centru, proveďte následující kroky:

1. [Vytvořte virtuální síť v nové oblasti](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Nakonfigurujte připojení typu VNet-to-VNet pomocí Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >V některých případech může být nutné použít PowerShell k vytvoření připojení VNet-to-VNet. Pokud například použijete různé účty Azure, nemůžete konfigurovat připojení na portálu. V tomto případě najdete informace v tématu [Konfigurace připojení typu VNet-to-VNet pomocí Azure Portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [V nové oblasti vytvořte řadič domény](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Tento řadič domény zajišťuje ověřování, pokud řadič domény v primární lokalitě není k dispozici.

1. [Vytvořte SQL Server virtuální počítač v nové oblasti](create-sql-vm-portal.md).

1. [Vytvořte Nástroj pro vyrovnávání zatížení Azure v síti v nové oblasti](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

   Tento nástroj pro vyrovnávání zatížení musí:

   - Být ve stejné síti a podsíti jako nový virtuální počítač.
   - Mít statickou IP adresu pro naslouchací proces skupiny dostupnosti.
   - Zahrňte back-end fond tvořený pouze virtuálními počítači ve stejné oblasti jako nástroj pro vyrovnávání zatížení.
   - Použijte test portu TCP specifický pro IP adresu.
   - Musí mít pravidlo vyrovnávání zatížení specifické pro SQL Server ve stejné oblasti.  
   - Pokud virtuální počítače ve fondu back-end nejsou součástí jedné skupiny dostupnosti nebo sady škálování virtuálních počítačů, je Standard Load Balancer. Další informace najdete v článku [přehled Azure Load Balancer úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Přidejte funkci clusteringu s podporou převzetí služeb při selhání do nového SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Připojte se k novému SQL Server k doméně](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

1. [Nastavte nový účet služby SQL Server na používání doménového účtu](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount).

1. [Přidejte novou SQL Server do clusteru s podporou převzetí služeb při selhání se systémem Windows Server](availability-group-manually-configure-tutorial.md#addNode).

1. Přidejte prostředek IP adresy do clusteru.

   Prostředek IP adresy můžete vytvořit v Správce clusteru s podporou převzetí služeb při selhání. Vyberte název clusteru, klikněte pravým tlačítkem na název clusteru v části **základní prostředky clusteru** a vyberte **vlastnosti**: 

   ![Vlastnosti clusteru](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   V dialogovém okně **vlastnosti** vyberte v části **IP adresa**možnost **Přidat** a potom přidejte IP adresu názvu clusteru z oblasti vzdálené sítě. V dialogovém okně **IP adresa** vyberte **OK** a potom v dialogovém okně **Vlastnosti clusteru** vyberte znovu **OK** . tím uložíte novou IP adresu. 

   ![Přidat IP adresu clusteru](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. Přidejte IP adresu jako závislost pro základní název clusteru.

   Otevřete znovu vlastnosti clusteru a vyberte kartu **závislosti** . Nakonfigurujte závislost nebo pro tyto dvě IP adresy: 

   ![Vlastnosti clusteru](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. Přidejte prostředek IP adresy do role skupiny dostupnosti v clusteru. 

   Klikněte pravým tlačítkem na roli skupiny dostupnosti v Správce clusteru s podporou převzetí služeb při selhání, zvolte **Přidat prostředek**, **Další prostředky**a vyberte **IP adresa**.

   ![Vytvořit IP adresu](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   Tuto IP adresu nakonfigurujte následujícím způsobem:

   - Použijte síť ze vzdáleného datového centra.
   - Přiřaďte IP adresu z nového nástroje pro vyrovnávání zatížení Azure. 

1. Přidejte prostředek IP adresy jako závislost pro cluster naslouchacího bodu přístupu klienta (síťový název).

   Následující snímek obrazovky ukazuje správně nakonfigurovaný prostředek clusteru IP adres:

   ![Skupina dostupnosti](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Skupina prostředků clusteru zahrnuje jak IP adresy. Obě tyto IP adresy jsou závislosti pro naslouchací bod klienta pro naslouchací proces. V konfiguraci závislostí clusteru použijte operátor **or** .

1. [Nastavte parametry clusteru v prostředí PowerShell](availability-group-manually-configure-tutorial.md#setparam).

   Spusťte skript prostředí PowerShell s názvem sítě clusteru, IP adresou a portem testu, který jste nakonfigurovali v nástroji pro vyrovnávání zatížení v nové oblasti.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Na novém SQL Server v SQL Server Configuration Manager [Povolit skupiny dostupnosti Always On](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Otevřete porty brány firewall na novém SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

   Čísla portů, která je třeba otevřít, závisí na vašem prostředí. Otevřete porty pro koncový bod zrcadlení a test stavu služby Azure Load Balancer.


1. [Přidejte repliku do skupiny dostupnosti na novém SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Pro repliku ve vzdálené oblasti Azure ji nastavte pro asynchronní replikaci s ručním převzetím služeb při selhání.  

## <a name="set-connection-for-multiple-subnets"></a>Nastavení připojení pro více podsítí

Replika ve vzdáleném datovém centru je součástí skupiny dostupnosti, ale je v jiné podsíti. Pokud se tato replika stává primární replikou, může dojít k vypršení časového limitu připojení k aplikacím. Toto chování je stejné jako místní skupina dostupnosti v nasazení s více podsítěmi. Chcete-li v klientských aplikacích umožňovat připojení, buď aktualizujte připojení klienta, nebo nakonfigurujte ukládání názvů překladu IP adres do prostředku s názvem sítě v clusteru.

V případě potřeby aktualizujte připojovací řetězce klienta na hodnotu nastavit `MultiSubnetFailover=Yes` . Viz téma [připojení pomocí MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Pokud připojovací řetězce upravovat nemůžete, můžete nakonfigurovat ukládání názvů do mezipaměti. Podívejte se [na téma chyba vypršení časového limitu a nemůžete se připojit k naslouchacího procesu skupiny dostupnosti AlwaysOn SQL Server 2012 v prostředí s více podsítěmi](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Převzetí služeb při selhání do vzdálené oblasti

Pokud chcete otestovat připojení naslouchacího procesu ke vzdálené oblasti, můžete převzít služby repliky do vzdálené oblasti. I když je replika asynchronní, převzetí služeb při selhání je ohroženo potenciální ztrátou dat. Pokud chcete převzít služby při selhání bez ztráty dat, změňte režim dostupnosti na synchronní a nastavte režim převzetí služeb při selhání na automaticky. Použijte k tomu následující postup:

1. V **Průzkumník objektů**se připojte k instanci SQL Server, která je hostitelem primární repliky.
1. V části **skupiny dostupnosti AlwaysOn**, **skupiny dostupnosti**klikněte pravým tlačítkem na vaši skupinu dostupnosti a vyberte **vlastnosti**.
1. Na stránce **Obecné** v části **repliky dostupnosti**nastavte sekundární repliku na webu Dr tak, aby používala režim **synchronního potvrzování** a režim **automatického** převzetí služeb při selhání.
1. Pokud máte sekundární repliku ve stejné lokalitě jako primární replika pro zajištění vysoké dostupnosti, nastavte tuto repliku na **asynchronní potvrzení** a **Ruční**.
1. Vyberte OK.
1. V **Průzkumník objektů**klikněte pravým tlačítkem na skupinu dostupnosti a vyberte **zobrazit řídicí panel**.
1. Na řídicím panelu ověřte, zda je replika na webu DR synchronizovaná.
1. V **Průzkumník objektů**klikněte pravým tlačítkem na skupinu dostupnosti a vyberte **převzetí služeb při selhání...**. Studia Management SQL Server otevře Průvodce pro převzetí služeb při selhání SQL Server.  
1. Vyberte **Další**a na webu Dr vyberte instanci SQL Server. Znovu vyberte **Další** .
1. Připojte se k instanci SQL Server na webu DR a vyberte **Další**.
1. Na stránce **Souhrn** ověřte nastavení a vyberte **Dokončit**.

Po otestování připojení přesuňte primární repliku zpátky do svého primárního datového centra a nastavte režim dostupnosti zpět na normální provozní nastavení. Následující tabulka ukazuje normální provozní nastavení pro architekturu popsanou v tomto dokumentu:

| Umístění | Instance serveru | Role | Režim dostupnosti | Režim převzetí služeb při selhání
| ----- | ----- | ----- | ----- | -----
| Primární datové centrum | SQL-1 | Primární | Synchronní | Automaticky
| Primární datové centrum | SQL-2 | Sekundární | Synchronní | Automaticky
| Sekundární nebo vzdálené datové centrum | SQL-3 | Sekundární | Asynchronní | Ruční


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Další informace o plánovaném a vynuceném ručním převzetí služeb při selhání

Další informace najdete v následujících tématech:

- [Provedení plánovaného ručního převzetí služeb při selhání skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Provedení vynuceného ručního převzetí služeb při selhání skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="next-steps"></a>Další kroky

* [Skupiny dostupnosti AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Nástroje pro vyrovnávání zatížení Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Skupiny dostupnosti Azure](../../../virtual-machines/linux/manage-availability.md)

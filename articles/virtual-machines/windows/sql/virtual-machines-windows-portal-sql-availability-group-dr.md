---
title: "Skupiny dostupnosti SQL serveru – virtuální počítače Azure - zotavení po havárii | Microsoft Docs"
description: "Tento článek vysvětluje, jak konfigurovat skupinu dostupnosti systému SQL Server na virtuálních počítačích Azure s replikou v jiné oblasti."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 84fa2e051c46e178e3e72709886babc8c3db7b9d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Konfigurace skupiny dostupnosti Always On na virtuálních počítačích, které jsou v různých oblastech Azure

Tento článek vysvětluje postup konfigurace repliky dostupnosti skupiny SQL serveru Always On na virtuálních počítačích Azure ve vzdáleném umístění Azure. Tuto konfiguraci můžete použijte k podpoře zotavení po havárii.

Tento článek se týká na virtuálních počítačích Azure v režimu Resource Manager.

Následující obrázek znázorňuje běžné nasazení skupiny dostupnosti na virtuálních počítačích Azure:

   ![Skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

V tomto nasazení jsou všechny virtuální počítače v jedné oblasti Azure. Repliky skupin dostupnosti může mít synchronním potvrzováním s automatické převzetí služeb při selhání na SQL-1 a SQL 2. K vytvoření této architektury, najdete v části [skupiny dostupnosti šablony nebo kurzu](virtual-machines-windows-portal-sql-availability-group-overview.md).

Tato architektura je zranitelný vůči výpadek oblasti Azure přestane být nedostupné. Abyste vyřešili toto ohrožení zabezpečení, přidejte repliku v jiné oblasti Azure. Následující diagram znázorňuje, jak by vypadal nové architektury:

   ![Zotavení po Havárii skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Na předchozím obrázku uvádí nové virtuálního počítač s názvem SQL-3. SQL 3 je v jiné oblasti Azure. SQL 3 se přidá do clusteru Windows Server Failover Cluster. SQL 3 můžete hostovat repliku skupiny dostupnosti. Všimněte si, že oblast Azure SQL 3 má nové nástroje pro vyrovnávání zatížení Azure.

>[!NOTE]
> Nastavení Azure dostupnosti je potřeba při více než jeden virtuální počítač je ve stejné oblasti. Pokud jenom jeden virtuální počítač je v oblasti, pak skupinu dostupnosti se nevyžaduje. Virtuální počítač můžete umístit pouze ve skupině dostupnosti nastavena v okamžiku vytvoření. Pokud virtuální počítač je již v nastavení dostupnosti, můžete přidat virtuální počítač pro repliku další později.

V této architektuře repliky v oblasti vzdálené obvykle nakonfiguruje s asynchronního potvrzování dostupnosti režim a režim ručního převzetí služeb při selhání.

Pokud replik skupin dostupnosti jsou na virtuálních počítačích Azure v různých oblastech Azure, vyžaduje každá oblast:

* Brána virtuální sítě
* Připojení brány virtuální sítě

Následující diagram znázorňuje, jak sítě komunikaci mezi datovými centry.

   ![Skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Tato architektura způsobuje poplatky za odchozí data pro data replikovat mezi oblastmi Azure. V tématu [šířky pásma ceny](http://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Vytvoření vzdálené repliky

Chcete-li vytvořit repliku v centru vzdálených dat, proveďte následující kroky:

1. [Vytvořit virtuální síť v oblasti nové](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurace připojení typu VNet-to-VNet pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >V některých případech může mít jak pomocí prostředí PowerShell k vytvoření připojení VNet-to-VNet. Například pokud použijete různé účty Azure nemůžete nakonfigurovat připojení na portálu. V takovém případě najdete [konfigurace připojení typu VNet-to-VNet pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Vytvoření řadiče domény v nové oblasti](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Tento řadič domény poskytuje ověřování, pokud není dostupný řadič domény v primární lokalitě.

1. [Vytvoření virtuálního počítače s SQL serverem v oblasti nové](virtual-machines-windows-portal-sql-server-provision.md).

1. [Vytvoření pro vyrovnávání zatížení Azure v síti na nový oblast](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Tento nástroj pro vyrovnávání zatížení musí:

   - Být ve stejné síti a podsítě jako nový virtuální počítač.
   - Máte statickou IP adresu pro naslouchací proces skupiny dostupnosti.
   - Zahrnout back-endový fond, který se skládá z jenom virtuální počítače ve stejné oblasti jako nástroj pro vyrovnávání zatížení.
   - Použijte sondou TCP port konkrétní IP adresu.
   - Máte pravidlo, které jsou specifické pro SQL Server ve stejné oblasti vyrovnávání zatížení.  

1. [Přidejte funkci Clustering převzetí služeb při selhání na nový server SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Připojení k doméně nový Server SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Nastavit nový účet služby SQL Server na používání účtu domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Přidat nový Server SQL do clusteru Windows Server Failover Cluster](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Vytvořte prostředek IP adresy v clusteru.

   Ve Správci clusteru převzetí služeb při selhání můžete vytvořit prostředek IP adresy. Klikněte pravým tlačítkem na roli skupiny dostupnosti, klikněte na tlačítko **přidat prostředek**, **více prostředků**a klikněte na tlačítko **IP adresu**.

   ![Vytvoření IP adresy](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Tato IP adresa nakonfigurujte následujícím způsobem:

   - Použijte síti ze vzdáleného datového centra.
   - Přiřaďte IP adresu z nové nástroje pro vyrovnávání zatížení Azure. 

1. Na novém serveru SQL v SQL Server Configuration Manager [povolte skupiny dostupnosti Always On](http://msdn.microsoft.com/library/ff878259.aspx).

1. [Otevřete porty brány firewall na nový Server SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Čísla portů, které je třeba otevřít závisí na vašem prostředí. Otevřené porty pro zrcadlení koncový bod a Azure načíst sondu nástroje pro vyrovnávání stavu.

1. [Přidejte repliku do skupiny dostupnosti na nový Server SQL](http://msdn.microsoft.com/library/hh213239.aspx).

   Pro repliku ve vzdálené oblast Azure nastavte ji pro asynchronní replikaci s ruční převzetí služeb při selhání.  

1. Přidáte prostředek IP adresy jako závislost pro naslouchací proces klienta přístup k bodu (síťový název) clusteru.

   Následující snímek obrazovky ukazuje prostředek clusteru správně nakonfigurovaná adresa IP:

   ![Skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Skupinu prostředků clusteru zahrnuje obě IP adresy. Obě IP adresy jsou závislosti pro naslouchací proces klientský přístupový bod. Použití **nebo** operátor v konfiguraci clusteru závislostí.

1. [Nastavení parametrů clusteru v prostředí PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Spusťte skript prostředí PowerShell s název sítě s clustery, IP adresa a port testu, který jste nakonfigurovali v nástroji pro vyrovnávání zatížení v nové oblasti.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Sada připojení pro více podsítí

Replika v centru vzdálených dat je součástí skupiny dostupnosti, ale je v jiné podsíti. Pokud tato replika se stane primární replikou, může dojít k vypršení časových limitů připojení aplikace. Toto chování je stejné jako místní skupiny dostupnosti v nasazení více podsítí. Povolit připojení z klienta aplikace, aktualizujte připojení klienta nebo nakonfigurovat překlad ukládání do mezipaměti na prostředku názvu sítě clusteru.

Pokud možno aktualizovat připojovací řetězce klienta nastavit `MultiSubnetFailover=Yes`. V tématu [propojení s MultiSubnetFailover](http://msdn.microsoft.com/library/gg471494#Anchor_0).

Pokud nelze upravit připojovací řetězce, můžete nakonfigurovat název řešení ukládání do mezipaměti. V tématu [časových limitů připojení ve skupině dostupnosti více podsítí](http://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Převzetí služeb při selhání vzdáleného oblast

K testování připojení naslouchacího procesu vzdálené oblasti, může převzít repliky na vzdálené oblast. Replika je asynchronní, je zranitelný vůči potenciální ztrátě dat převzetí služeb při selhání. Chcete-li převzetí služeb při selhání bez ztráty dat, změňte režim dostupnosti na synchronní a nastavte režim převzetí služeb při selhání na automaticky. Použijte k tomu následující postup:

1. V **Průzkumník objektů**, připojte se k instanci systému SQL Server, který je hostitelem primární repliky.
1. V části **skupiny dostupnosti AlwaysOn**, **skupiny dostupnosti**, klikněte pravým tlačítkem na vaší skupiny dostupnosti a klikněte na **vlastnosti**.
1. Na **Obecné** v části **replik dostupnosti**, nastavit sekundární replika v lokalitě zotavení po Havárii používat **synchronní potvrdit** režim dostupnosti a **automatické** režim převzetí služeb při selhání.
1. Pokud máte sekundární repliku ve stejné lokalitě jako váš primární repliky pro vysokou dostupnost, nastavit této repliky **asynchronní potvrdit** a **ruční**.
1. Klikněte na tlačítko OK.
1. V **Průzkumník objektů**, klikněte pravým tlačítkem na skupinu dostupnosti a klikněte na **zobrazit řídicí panel**.
1. Na řídicím panelu ověřte, že je replika na webu zotavení po Havárii synchronizovány.
1. V **Průzkumník objektů**, klikněte pravým tlačítkem na skupinu dostupnosti a klikněte na tlačítko **převzetí služeb při selhání...** . SQL Server Management studia otevře se průvodce k převzetí služeb systému SQL Server.  
1. Klikněte na tlačítko **Další**a vyberte instance systému SQL Server v lokalitě zotavení po Havárii. Klikněte na tlačítko **Další** znovu.
1. Připojte se k instanci systému SQL Server v lokalitě zotavení po Havárii a klikněte na tlačítko **Další**.
1. Na **Souhrn** , ověřte nastavení a klikněte na tlačítko **Dokončit**.

Po otestování připojení, přesunutí primární repliku zpátky na primární datové centrum a nastavte režim dostupnosti zpátky na jejich normální provozní nastavení. Následující tabulka uvádí nastavení normální provozní pro architektury popsané v tomto dokumentu:

| Umístění | Instance serveru | Role | Režim dostupnosti | Režim převzetí služeb při selhání
| ----- | ----- | ----- | ----- | -----
| Primární datové centrum | SQL-1 | Primární | Synchronní | Automaticky
| Primární datové centrum | SQL-2 | Sekundární | Synchronní | Automaticky
| Sekundární nebo vzdáleného datového centra | SQL-3 | Sekundární | Asynchronní | Ručně


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Další informace o plánované a vynucené ruční převzetí služeb při selhání

Další informace najdete v následujících tématech:

- [Provedení plánované ruční převzetí služeb při selhání skupiny dostupnosti (SQL Server)](http://msdn.microsoft.com/library/hh231018.aspx)
- [Provedení vynucené ruční převzetí služeb při selhání skupiny dostupnosti (SQL Server)](http://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Další odkazy

* [Always On skupiny dostupnosti](http://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](http://docs.microsoft.com/azure/virtual-machines/windows/)
* [Nástroje pro vyrovnávání zatížení Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Skupiny dostupnosti Azure](../manage-availability.md)

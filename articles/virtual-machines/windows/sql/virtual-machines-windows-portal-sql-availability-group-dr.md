---
title: Konfigurace skupiny dostupnosti v různých oblastech
description: Tento článek vysvětluje, jak nakonfigurovat skupinu dostupnosti serveru SQL Server na virtuálních počítačích Azure s replikou v jiné oblasti.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8ca871a6f525d4e68ce70060e6faddbcfc8e1f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060118"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Konfigurace skupiny dostupnosti na virtuálních počítačích Azure SQL Server v různých oblastech

Tento článek vysvětluje, jak nakonfigurovat repliku skupiny dostupnosti SQL Server always on na virtuálních počítačích Azure ve vzdáleném umístění Azure. Tato konfigurace slouží k podpoře zotavení po havárii.

Tento článek se vztahuje na virtuální počítače Azure v režimu Správce prostředků.

Následující obrázek ukazuje společné nasazení skupiny dostupnosti na virtuálních počítačích Azure:

   ![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

V tomto nasazení jsou všechny virtuální počítače v jedné oblasti Azure. Repliky skupiny dostupnosti mohou mít synchronní potvrzení s automatickým převzetím služeb při selhání na SQL-1 a SQL-2. Chcete-li vytvořit tuto architekturu, naleznete [v tématu šablona skupiny dostupnosti nebo kurz](virtual-machines-windows-portal-sql-availability-group-overview.md).

Tato architektura je náchylná k prostojům, pokud se oblast Azure stane nepřístupnou. Chcete-li tuto chybu zabezpečení překonat, přidejte repliku v jiné oblasti Azure. Následující diagram znázorňuje, jak by nová architektura vypadala:

   ![Skupina dostupnosti DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Předchozí diagram znázorňuje nový virtuální počítač s názvem SQL-3. SQL-3 je v jiné oblasti Azure. SQL-3 je přidán do clusteru windows server převzetí služeb při selhání. SQL-3 může hostit repliku skupiny dostupnosti. Nakonec si všimněte, že oblast Azure pro SQL-3 má nový azure nástroje pro vyrovnávání zatížení.

>[!NOTE]
> Azure dostupnost sada je vyžadována, když více než jeden virtuální počítač je ve stejné oblasti. Pokud je v oblasti pouze jeden virtuální počítač, není vyžadována sada dostupnosti. Virtuální počítač můžete umístit pouze do sady dostupnosti v době vytvoření. Pokud je virtuální počítač již v sadě dostupnosti, můžete přidat virtuální počítač pro další repliku později.

V této architektuře je replika ve vzdálené oblasti obvykle konfigurována s režimem dostupnosti asynchronního potvrzení a ručním režimem převzetí služeb při selhání.

Když jsou repliky skupiny dostupnosti na virtuálních počítačích Azure v různých oblastech Azure, každá oblast vyžaduje:

* Brána virtuální sítě
* Připojení brány virtuální sítě

Následující diagram znázorňuje komunikaci sítí mezi datovými centry.

   ![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Tato architektura účtuje poplatky za odchozí data pro data replikovaná mezi oblastmi Azure. Viz [Ceny šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Vytvoření vzdálené repliky

Chcete-li vytvořit repliku ve vzdáleném datovém centru, postupujte takto:

1. [Vytvořte virtuální síť v nové oblasti](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Nakonfigurujte připojení virtuální sítě k virtuální síti pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >V některých případech bude pravděpodobně muset použít Prostředí PowerShell k vytvoření připojení virtuální sítě k virtuální síti. Například pokud používáte různé účty Azure, nemůžete nakonfigurovat připojení na portálu. V tomto případě [nakonfigurujte připojení virtuální sítě k virtuální síti pomocí portálu Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Vytvořte řadič domény v nové oblasti](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Tento řadič domény poskytuje ověřování, pokud řadič domény v primární lokalitě není k dispozici.

1. [Vytvořte virtuální počítač SQL Server v nové oblasti](virtual-machines-windows-portal-sql-server-provision.md).

1. [Vytvořte azure balancer v síti v nové oblasti](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Tento vykladač zatížení musí:

   - Buďte ve stejné síti a podsíti jako nový virtuální počítač.
   - Mít statickou ADRESU IP pro naslouchací proces skupiny dostupnosti.
   - Zahrnout back-endový fond skládající se pouze z virtuálních počítačů ve stejné oblasti jako nástroj pro vyrovnávání zatížení.
   - Použijte sondu portu TCP specifickou pro adresu IP.
   - Mají pravidlo vyrovnávání zatížení specifické pro SQL Server ve stejné oblasti.  
   - Nástroj pro vyrovnávání zatížení se bude řídit standardním nástrojem pro vyrovnávání zatížení, pokud virtuální počítače v back-endovém fondu nejsou součástí jedné skupiny dostupnosti ani škálovací sady virtuálních počítačů. Další informace najdete [v přehledu služby Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Přidejte funkci clustering u převzetí služeb při selhání do nového serveru SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Připojte nový SQL Server k doméně](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Nastavte nový účet služby SQL Server tak, aby používal účet domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Přidejte nový sql server do clusteru s podporou převzetí služeb při selhání systému Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Přidejte do clusteru prostředek adresy IP.

   Prostředek ip adresy můžete vytvořit ve Správci clusterů s podporou převzetí služeb při selhání. Vyberte název clusteru, potom klikněte pravým tlačítkem myši na název clusteru v části **Základní prostředky clusteru** a vyberte **Vlastnosti**: 

   ![Vlastnosti clusteru](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-name-properties.png)

   V dialogovém okně **Vlastnosti** vyberte **přidat** v části **Adresa IP**a pak přidejte adresu IP názvu clusteru z oblasti vzdálené sítě. V dialogovém okně **Adresa IP** vyberte **OK** a pak znovu v dialogovém okně **Vlastnosti clusteru** vyberte **OK,** chcete-li novou adresu IP uložit.. 

   ![Přidání adresy IP clusteru](./media/virtual-machines-windows-portal-sql-availability-group-dr/add-cluster-ip-address.png)


1. Přidejte adresu IP jako závislost pro název hlavního clusteru.

   Otevřete vlastnosti clusteru ještě jednou a vyberte kartu **Závislosti.** 

   ![Vlastnosti clusteru](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-ip-dependencies.png)

1. Přidejte prostředek adresy IP do role skupiny dostupnosti v clusteru. 

   Klepněte pravým tlačítkem myši na roli skupiny dostupnosti ve Správci clusteru s podporou převzetí služeb při selhání, vyberte **možnost Přidat prostředek**, Další **prostředky**a vyberte **adresu IP**.

   ![Vytvořit adresu IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Tuto adresu IP nakonfigurujte následujícím způsobem:

   - Použijte síť ze vzdáleného datového centra.
   - Přiřaďte IP adresu z nového azure balancer. 

1. Přidejte prostředek adresy IP jako závislost pro cluster klientského přístupového bodu (síťového názvu) naslouchací proces.

   Následující snímek obrazovky ukazuje správně nakonfigurovaný prostředek clusteru adres IP:

   ![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Skupina prostředků clusteru zahrnuje obě adresy IP. Obě adresy IP jsou závislosti pro přístupový bod klienta naslouchací proces. V konfiguraci závislosti clusteru použijte operátor **OR.**

1. [Nastavte parametry clusteru v prostředí PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Spusťte skript prostředí PowerShell s názvem sítě clusteru, ip adresou a portem sondy, který jste nakonfigurovali v zařízení pro vyrovnávání zatížení v nové oblasti.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Na novém serveru SQL Server ve Správci konfigurace serveru SQL Server [povolte vždy zapnuté skupiny dostupnosti](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Otevřete porty brány firewall na novém serveru SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Čísla portů, která je třeba otevřít, závisí na vašem prostředí. Otevřete porty pro zrcadlení koncového bodu a sondu stavu azure vyrovnávání zatížení.


1. [Přidejte repliku do skupiny dostupnosti na novém serveru SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Pro repliku ve vzdálené oblasti Azure nastavte ji pro asynchronní replikaci s ručním převzetím služeb při selhání.  

## <a name="set-connection-for-multiple-subnets"></a>Nastavení připojení pro více podsítí

Replika ve vzdáleném datovém centru je součástí skupiny dostupnosti, ale je v jiné podsíti. Pokud se tato replika stane primární replikou, může dojít k časovým limitům připojení aplikace. Toto chování je stejné jako místní skupiny dostupnosti v nasazení více podsítí. Chcete-li povolit připojení z klientských aplikací, aktualizujte připojení klienta nebo nakonfigurujte ukládání názvů do mezipaměti prostředku síťového názvu clusteru.

Pokud možno aktualizujte připojovací `MultiSubnetFailover=Yes`řetězce klienta a nastavte je . Viz [Připojení s multiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Pokud nelze změnit připojovací řetězce, můžete nakonfigurovat ukládání názvů do mezipaměti. Viz [Chyba časového opovězení a nemůžete se připojit k naslouchací procesu skupiny dostupnosti serveru SQL Server 2012 AlwaysOn v prostředí s více podsítěmi](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Převzetí služeb při selhání do vzdálené oblasti

Chcete-li otestovat připojení posluchače ke vzdálené oblasti, můžete přepojit repliku do vzdálené oblasti. Zatímco replika je asynchronní, převzetí služeb při selhání je náchylné k potenciální ztrátě dat. Chcete-li převzetí služeb při selhání bez ztráty dat, změňte režim dostupnosti na synchronní a nastavte režim převzetí služeb při selhání na automatický. Použijte k tomu následující postup:

1. V **Průzkumníkovi objektů**se připojte k instanci serveru SQL Server, který je hostitelem primární repliky.
1. V části **Skupiny dostupnosti AlwaysOn** **klikněte**pravým tlačítkem myši na skupinu dostupnosti a klikněte na **příkaz Vlastnosti**.
1. Na stránce **Obecné** **nastavte**sekundární repliku v lokalitě zotavení po Havárii tak, aby používala režim **dostupnosti synchronního potvrzení** a režim **automatického** převzetí služeb při selhání.
1. Pokud máte sekundární repliku ve stejné lokalitě jako primární replika pro vysokou dostupnost, nastavte tuto repliku na **asynchronní potvrzení** a **ruční**.
1. Klikněte na tlačítko OK.
1. V **Průzkumníkovi objektů**klikněte pravým tlačítkem myši na skupinu dostupnosti a klikněte na **příkaz Zobrazit řídicí panel**.
1. Na řídicím panelu ověřte, zda je replika na webu zotavení po Havárii synchronizována.
1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na skupinu dostupnosti a klikněte na **položku Převzetí služeb při selhání...**. SQL Server Management Studios otevře průvodce převzetí služeb při selhání sql server.  
1. Klepněte na tlačítko **Další**a vyberte instanci serveru SQL Server v lokalitě zotavení po Havárii. Znovu klikněte na **Další**.
1. Připojte se k instanci serveru SQL Server v lokalitě zotavení po Havárii a klepněte na tlačítko **Další**.
1. Na stránce **Souhrn** ověřte nastavení a klepněte na tlačítko **Dokončit**.

Po testování připojení přesuňte primární repliku zpět do primárního datového centra a nastavte režim dostupnosti zpět na normální provozní nastavení. V následující tabulce je uvedeno normální provozní nastavení pro architekturu popsanou v tomto dokumentu:

| Umístění | Instance serveru | Role | Režim dostupnosti | Režim převzetí služeb při selhání
| ----- | ----- | ----- | ----- | -----
| Primární datové centrum | SQL-1 | Primární | Synchronní | Automaticky
| Primární datové centrum | SQL-2 | Sekundární | Synchronní | Automaticky
| Sekundární nebo vzdálené datové centrum | SQL-3 | Sekundární | Asynchronní | Ruční


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Další informace o plánovaném a vynuceném ručním převzetí služeb při selhání

Další informace najdete v následujících tématech:

- [Provedení plánovanéruční převzetí služeb při selhání skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Provedení vynuceného ručního převzetí služeb při selhání skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Další odkazy

* [Vždy na skupinách dostupnosti](https://msdn.microsoft.com/library/hh510230.aspx)
* [Virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure Load Balancers](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Azure Dostupnost sady](../manage-availability.md)

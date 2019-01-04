---
title: Skupiny dostupnosti pro SQL Server – Azure Virtual Machines – zotavení po havárii | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak nakonfigurovat skupinu dostupnosti SQL Server na virtuálních počítačích Azure s replikou v jiné oblasti.
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
ms.openlocfilehash: d64c55857cda0aa64dc010566490e1696fffdea0
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972375"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Konfigurace skupiny dostupnosti AlwaysOn na virtuálních počítačích, které jsou v různých oblastech Azure

Tento článek vysvětluje, jak nakonfigurovat repliky dostupnosti skupin AlwaysOn SQL serveru na virtuálních počítačích Azure ve vzdáleném umístění Azure. Pomocí této konfigurace k podpoře zotavení po havárii.

Tento článek se týká k Azure Virtual Machines v režimu Resource Manageru.

Následující obrázek ukazuje běžné nasazení skupiny dostupnosti na virtuálních počítačích Azure:

   ![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

V tomto nasazení jsou všechny virtuální počítače v jedné oblasti Azure. Repliky skupin dostupnosti může mít synchronní potvrzení změn se automatické převzetí služeb při selhání na SQL-1 a SQL 2. K vytvoření této architektury, najdete v článku [skupiny dostupnosti šablony nebo kurzu](virtual-machines-windows-portal-sql-availability-group-overview.md).

Tato architektura je zranitelný vůči prostojům, pokud oblast stane nedostupnou. Abyste vyřešili toto ohrožení zabezpečení, přidejte repliku v jiné oblasti Azure. Následující diagram znázorňuje, jak by vypadal novou architekturu:

   ![Zotavení po Havárii skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Předchozí diagram znázorňuje nový virtuální počítač s názvem SQL-3. SQL-3 je v jiné oblasti Azure. SQL-3 se přidá do clusteru Windows Server Failover Cluster. Replika skupiny dostupnosti může být hostitelem SQL-3. Konečně si všimněte, že oblast Azure pro SQL 3 má nový nástroj pro vyrovnávání zatížení Azure.

>[!NOTE]
> Skupině dostupnosti Azure je potřeba po více než jeden virtuální počítač ve stejné oblasti. Pokud pouze jeden virtuální počítač je v oblasti, pak skupinu dostupnosti se nevyžaduje. Můžete pouze umístit virtuální počítač ve skupině dostupnosti v okamžiku vytvoření. Pokud virtuální počítač je už ve skupině dostupnosti, můžete přidat virtuální počítač pro další repliky později.

V této architektuře repliky ve vzdálené oblasti obvykle nastaven režim dostupnosti asynchronního potvrzování a režim ručního převzetí služeb při selhání.

Když replik skupin dostupnosti jsou na virtuálních počítačích Azure v různých oblastech Azure, vyžaduje každá oblast:

* Bránu virtuální sítě
* Připojení brány virtuální sítě

Následující diagram znázorňuje, jak sítích komunikovat mezi datovými centry.

   ![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Tato architektura se neúčtují poplatky za odchozí data pro data replikovat mezi oblastmi Azure. Zobrazit [cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Vytvoření vzdálené repliky

Chcete-li vytvořit repliku ve vzdáleném datovém centru, proveďte následující kroky:

1. [Vytvoření virtuální sítě v nové oblasti](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurace připojení typu VNet-to-VNet pomocí webu Azure portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >V některých případech může mít použití Powershellu k vytvoření připojení VNet-to-VNet. Například pokud používáte různé účty Azure nejde nakonfigurovat připojení na portálu. V tomto případě zobrazí [konfigurace připojení typu VNet-to-VNet pomocí webu Azure portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Vytvoření řadiče domény v nové oblasti](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Tento řadič domény poskytuje ověřování, pokud není dostupný řadič domény v primární lokalitě.

1. [Vytvoření virtuálního počítače s SQL serverem v nové oblasti](virtual-machines-windows-portal-sql-server-provision.md).

1. [Vytvoření služby Azure load balancer v síti na novém regionu](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Tento nástroj pro vyrovnávání zatížení musí:

   - Být ve stejné síti a podsíti jako nový virtuální počítač.
   - Máte statickou IP adresu pro naslouchací proces skupiny dostupnosti.
   - Zahrnout back-endový fond, který se skládá z virtuálních počítačů ve stejné oblasti jako nástroj pro vyrovnávání zatížení.
   - Použijte sondu protokolu TCP port specifické pro IP adresu.
   - Mají pravidla specifická pro SQL Server ve stejné oblasti vyrovnávání zatížení.  
   - Být standardní nástroj pro vyrovnávání zatížení, pokud virtuální počítače v back-endového fondu nejsou součástí jedné skupiny dostupnosti nebo škálovací sady virtuálních počítačů. Pro další informace najdete [Přehled služby Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Přidejte funkci Clustering převzetí služeb při selhání na nový server SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Připojte nový SQL Server k doméně](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Nastavit nový účet služby systému SQL Server na používání účtu domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Přidat nový SQL Server do clusteru Windows Server Failover Cluster](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Vytvořte prostředek IP adresy v clusteru.

   V modulu Správce clusteru převzetí služeb při selhání můžete vytvořit prostředek IP adresy. Klikněte pravým tlačítkem na roli skupiny dostupnosti, klikněte na tlačítko **přidat prostředek**, **více prostředků**a klikněte na tlačítko **IP adresu**.

   ![Vytvoření IP adresy](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Tuto IP adresu nakonfigurujte následujícím způsobem:

   - Použijte síti ze vzdáleného datového centra.
   - Přiřadíte IP adresu z nový nástroj pro vyrovnávání zatížení Azure. 

1. Na novém serveru SQL v SQL Server Configuration Manager [povolte skupiny dostupnosti Always On](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Otevřete porty brány firewall na nový Server SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Čísla portů, které budete muset otevřít závisí na vašem prostředí. Otevřené porty pro koncový bod zrcadlení a Azure načíst sondy stavu nástroje pro vyrovnávání.

1. [Přidání repliky do skupiny dostupnosti na novém serveru SQL](https://msdn.microsoft.com/library/hh213239.aspx).

   V případě repliky ve vzdálené oblasti Azure nastavte ji pro asynchronní replikaci s ruční převzetí služeb při selhání.  

1. Přidáte prostředek IP adresy jako závislost pro naslouchací proces klienta přístup k bodu (síťový název) clusteru.

   Následující snímek obrazovky ukazuje prostředek clusteru správně nakonfigurovaná adresa IP:

   ![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Skupina prostředků clusteru zahrnuje obě IP adresy. Obě IP adresy jsou závislosti pro klientský přístupový bod naslouchacího procesu. Použití **nebo** operátor v konfiguraci clusteru závislostí.

1. [Nastavení parametrů clusteru v prostředí PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Spusťte skript prostředí PowerShell se název sítě s clustery, IP adresy a portu sondy, které jste nakonfigurovali v nástroji pro vyrovnávání zatížení v nové oblasti.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Nastavit připojení pro více podsítí

Replik ve vzdáleném datovém centru je součástí skupiny dostupnosti, ale je v jiné podsíti. Pokud tato replika se stane primární replikou, může dojít k překročení časového limitu připojení aplikace. Toto chování je stejné jako místní skupiny dostupnosti v nasazení více podsítí. Povolit připojení z klienta aplikace, aktualizujte připojení klienta nebo nakonfigurovat překlad názvů ukládání do mezipaměti na prostředku názvu sítě clusteru.

Pokud možno aktualizovat připojovací řetězce klienta pro nastavení `MultiSubnetFailover=Yes`. Zobrazit [propojení s MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Pokud nelze upravit připojovací řetězce, můžete nakonfigurovat název řešení ukládání do mezipaměti. Zobrazit [časové limity připojení ve skupině dostupnosti více podsítí](https://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Převzetí služeb při selhání do vzdálené oblasti

K otestování připojení naslouchacího procesu do vzdálené oblasti, můžete převzetí služeb při selhání do vzdálené oblasti repliky. Replika je asynchronní, je zranitelný vůči potenciální ztrátě dat převzetí služeb při selhání. Převzít služby při selhání bez ztráty dat, změňte režim dostupnosti na synchronní a nastavte režim převzetí služeb při selhání na hodnotu automaticky. Použijte k tomu následující postup:

1. V **Průzkumník objektů**, připojte se k instanci systému SQL Server, který je hostitelem primární repliky.
1. V části **skupin dostupnosti AlwaysOn**, **skupin dostupnosti**, klikněte pravým tlačítkem na vaší skupiny dostupnosti a klikněte na tlačítko **vlastnosti**.
1. Na **Obecné** stránce v části **replik dostupnosti**, nastavte sekundární replika v lokalitě zotavení po Havárii použít **synchronní potvrzení** režim dostupnosti a  **Automatické** režim převzetí služeb při selhání.
1. Pokud máte ve stejné lokalitě jako váš primární repliky pro zajištění vysoké dostupnosti na sekundární repliku, nastavte na tuto repliku **asynchronní potvrzení** a **ruční**.
1. Klikněte na tlačítko OK.
1. V **Průzkumník objektů**, klikněte pravým tlačítkem na skupinu dostupnosti a klikněte na tlačítko **zobrazit řídicí panel**.
1. Na řídicím panelu ověřte, že se synchronizují repliky v lokalitě zotavení po Havárii.
1. V **Průzkumník objektů**, klikněte pravým tlačítkem na skupinu dostupnosti a klikněte na tlačítko **převzetí služeb při selhání...** . SQL Server Management studia spustí průvodce, převzít služby při selhání systému SQL Server.  
1. Klikněte na tlačítko **Další**a vyberte instanci SQL serveru v lokalitě zotavení po Havárii. Klikněte na tlačítko **Další** znovu.
1. Připojte se k instanci systému SQL Server v lokalitě zotavení po Havárii a klikněte na tlačítko **Další**.
1. Na **Souhrn** stránky, zkontrolujte nastavení a klikněte na tlačítko **Dokončit**.

Po otestování připojení k primární replice přejděte zpět do vašeho primárního datového centra a nastavte režim dostupnosti jejich normální provozní nastavení. V následující tabulce jsou uvedeny běžné provozní nastavení pro architekturu popsanou v tomto dokumentu:

| Umístění | Instance serveru | Role | Režim dostupnosti | Režim převzetí služeb při selhání
| ----- | ----- | ----- | ----- | -----
| Primární datové centrum | SQL-1 | Primární | Synchronní | Automaticky
| Primární datové centrum | SQL-2 | Sekundární | Synchronní | Automaticky
| Sekundární nebo vzdálené datové centrum | SQL-3 | Sekundární | Asynchronní | Ručně


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Další informace o plánovaných a vynucené ruční převzetí služeb při selhání

Další informace najdete v následujících tématech:

- [Provést plánované ruční převzetí služeb skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Provést vynucené ruční převzetí služeb skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Další odkazy

* [Skupiny dostupnosti AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Nástroje pro vyrovnávání zatížení Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Skupinu dostupnosti Azure](../manage-availability.md)

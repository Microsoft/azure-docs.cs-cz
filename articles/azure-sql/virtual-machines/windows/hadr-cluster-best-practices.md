---
title: Osvědčené postupy konfigurace clusteru
description: Přečtěte si o podporovaných konfiguracích clusteru, když konfigurujete vysokou dostupnost a zotavení po havárii (HADR) pro SQL Server v Azure Virtual Machines, jako jsou například podporované kvora nebo možnosti směrování připojení.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 5a2540aeb36cfcb2048ec994bbb486badc8a68d1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358805"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Osvědčené postupy pro konfiguraci clusteru (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cluster se používá pro vysokou dostupnost a zotavení po havárii (HADR) s SQL Server v Azure Virtual Machines (VM). 

Tento článek popisuje osvědčené postupy konfigurace clusteru pro [instance clusterů s podporou převzetí služeb při selhání (FCIs)](failover-cluster-instance-overview.md) i [skupiny dostupnosti](availability-group-overview.md) , pokud je používáte s SQL Server na virtuálních počítačích Azure. 


## <a name="networking"></a>Sítě

Použijte jednu síťovou kartu na server (uzel clusteru) a jednu podsíť. Sítě Azure mají fyzickou redundanci, která v hostovaném clusteru virtuálních počítačů Azure nepotřebuje další síťové adaptéry a podsítě. Sestava ověření clusteru vás upozorní, že uzly jsou dosažitelné jenom v jedné síti. Toto upozornění můžete ignorovat u clusterů s podporou převzetí služeb při selhání hosta virtuálního počítače Azure.

### <a name="tuning-failover-cluster-network-thresholds"></a>Ladění prahových hodnot sítě s clustery

Při spuštění uzlů clusteru s podporou převzetí služeb při selhání Windows na virtuálních počítačích Azure s SQL Server AlwaysOn se doporučuje změnit nastavení clusteru na odlehčený stav monitorování.  Díky tomu bude cluster mnohem stabilnější a spolehlivý.  Podrobnosti najdete v tématu [IaaS with SQL AlwaysOn – vyladění prahových hodnot sítě clusteru s podporou převzetí služeb při selhání](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="quorum"></a>Umožněn

I když cluster se dvěma uzly bude fungovat bez [prostředku kvora](/windows-server/storage/storage-spaces/understand-quorum), je nutné, aby zákazníci používali k podpoře produkčního prostředku prostředek kvora. Ověření clusteru neprojde žádný cluster bez prostředku kvora. 

Technicky, cluster se třemi uzly může předržet jednu ztrátu uzlu (na dvou uzlech) bez prostředku kvora. Ale po výpadku clusteru se dvěma uzly existuje riziko, že clusterované prostředky budou v případě ztráty uzlu v režimu offline nebo selhání komunikace, aby se zabránilo scénáři děleného mozku.

Konfigurace prostředku kvora umožní, aby cluster pokračoval online s pouze jedním uzlem online.

V následující tabulce jsou uvedené možnosti kvora, které jsou k dispozici v pořadí doporučeném pro použití s virtuálním počítačem Azure, přičemž určující disk má upřednostňovanou volbu: 


||[Disk s kopií clusteru](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Disk s kopií cloudu](/windows-server/failover-clustering/deploy-cloud-witness)  |[Určující sdílená složka](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Podporovaný operační systém**| Vše |Windows Server 2016 +| Vše|


### <a name="disk-witness"></a>Disk s kopií clusteru

Disk s kopií clusteru je malý clusterovaný disk ve skupině úložišť dostupných v clusteru. Tento disk je vysoce dostupný a může převzít služby při selhání mezi uzly. Obsahuje kopii clusterové databáze s výchozí velikostí, která je obvykle menší než 1 GB. Disk s kopií clusteru je upřednostňovanou možností kvora pro libovolný cluster, který používá sdílené disky Azure (nebo jakékoli řešení sdíleného disku, jako je například sdílené rozhraní SCSI, iSCSI nebo síť SAN s technologií Fibre Channel).  Sdílený svazek clusteru nelze použít jako určující disk.

Nakonfigurujte sdílený disk Azure jako určující disk. 

Informace o tom, jak začít, najdete v tématu [Konfigurace určujícího disku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Podporovaný operační systém**: vše   


### <a name="cloud-witness"></a>Disk s kopií cloudu

Určující Cloud je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání, který používá Microsoft Azure k poskytnutí hlasu kvora clusteru. Výchozí velikost je přibližně 1 MB a obsahuje pouze časové razítko. Disk s kopií cloudu je ideální pro nasazení ve více lokalitách, několika zónách a několika oblastech.

Informace o tom, jak začít, najdete v tématu [Konfigurace určujícího cloudu](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**Podporovaný operační systém**: Windows Server 2016 a novější   


### <a name="file-share-witness"></a>Určující sdílená složka

Určující sdílená složka je sdílená složka SMB, která je obvykle nakonfigurovaná na souborovém serveru se systémem Windows Server. Udržuje informace o clusteringu v souboru. log, ale neukládá kopii databáze clusteru. V Azure můžete [sdílenou složku Azure](../../../storage/files/storage-how-to-create-file-share.md) nakonfigurovat tak, aby se používala jako určující sdílená složka, nebo můžete použít sdílenou složku na samostatném virtuálním počítači.

Pokud budete používat sdílenou složku Azure, můžete ji připojit ke stejnému procesu, který jste použili k [připojení sdílené složky Premium](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Informace o tom, jak začít, najdete v tématu [Konfigurace určující sdílené složky](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Podporovaný operační systém**: Windows Server 2012 a novější   

## <a name="connectivity"></a>Připojení

V tradičních místních síťových prostředích se SQL Server instance clusteru s podporou převzetí služeb při selhání jeví jako jediná instance SQL Server spuštěná v jednom počítači. Vzhledem k tomu, že instance clusteru s podporou převzetí služeb při selhání převezme služby při selhání z uzlu na uzel, název virtuální sítě (VNN) pro instanci poskytuje jednotný spojovací bod a umožňuje aplikacím připojit se k instanci SQL Server bez vědomí, který uzel je aktuálně aktivní. Když dojde k převzetí služeb při selhání, název virtuální sítě se zaregistruje do nového aktivního uzlu po jeho spuštění. Tento proces je transparentní pro klienta nebo aplikaci, které se připojují k SQL Server. tím se minimalizuje prostoje, ke kterým klient nebo aplikace při selhání dojde. Naslouchací proces skupiny dostupnosti také používá VNN ke směrování provozu do příslušné repliky. 

Použijte VNN s Azure Load Balancer nebo název distribuované sítě (DNN) ke směrování provozu do VNN instance clusteru s podporou převzetí služeb při selhání s SQL Server na virtuálních počítačích Azure nebo pro nahrazení stávajícího naslouchacího procesu VNN ve skupině dostupnosti. 


Následující tabulka porovnává podporu připojení HADR: 

| |**Název Virtual Network (VNN)**  |**Název distribuované sítě (DNN)**  |
|---------|---------|---------|
|**Minimální verze operačního systému**| Vše | Windows Server 2016 |
|**Minimální verze SQL Server** |Vše |SQL Server 2019 CU2 (pro FCI)<br/> SQL Server 2019 CU8 (pro AG)|
|**Podporované řešení HADR** | Instance clusteru s podporou převzetí služeb při selhání <br/> Skupina dostupnosti | Instance clusteru s podporou převzetí služeb při selhání <br/> Skupina dostupnosti|


### <a name="virtual-network-name-vnn"></a>Název virtuální sítě (VNN)

Vzhledem k tomu, že přístupový bod virtuální IP adresy funguje v Azure jinak, je nutné nakonfigurovat [Azure Load Balancer](../../../load-balancer/index.yml) ke směrování provozu na IP adresu uzlů FCI nebo naslouchacího procesu skupiny dostupnosti. Nástroj pro vyrovnávání zatížení ve virtuálních počítačích Azure obsahuje IP adresu VNN, na které se spoléhají Clusterové SQL Server prostředky. Nástroj pro vyrovnávání zatížení distribuuje příchozí toky, které dorazí na front-end, a potom směruje provoz do instancí definovaných back-end fondem. Tok přenosů se konfiguruje pomocí pravidel vyrovnávání zatížení a sond stavu. U SQL Server FCI jsou instance fondu back-endu virtuálními počítači Azure, na kterých běží SQL Server. 

Při použití nástroje pro vyrovnávání zatížení existuje mírné zpoždění při převzetí služeb při selhání, protože sonda stavu je ve výchozím nastavení každých 10 sekund. 

Pokud chcete začít, přečtěte si, jak nakonfigurovat Azure Load Balancer pro [instanci clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-vnn-azure-load-balancer-configure.md) nebo [skupinu dostupnosti](availability-group-vnn-azure-load-balancer-configure.md) .

**Podporovaný operační systém**: vše   
**Podporovaná verze SQL**: vše   
**Podporované řešení hadr**: instance clusteru s podporou převzetí služeb při selhání a skupina dostupnosti   


### <a name="distributed-network-name-dnn"></a>Název distribuované sítě (DNN)

Název distribuované sítě je nová funkce Azure pro SQL Server 2019. DNN nabízí alternativní způsob, jak se klienti SQL Server připojit k instanci clusteru s podporou převzetí služeb při selhání SQL Server nebo skupině dostupnosti bez použití nástroje pro vyrovnávání zatížení. 

Při vytvoření prostředku DNN se cluster připojí k názvu DNS s IP adresami všech uzlů v clusteru. Klient SQL se pokusí připojit ke každé IP adrese v tomto seznamu a vyhledat tak prostředek, ke kterému se má připojit.  Tento proces lze urychlit zadáním `MultiSubnetFailover=True` v připojovacím řetězci. Toto nastavení oznamuje poskytovateli, aby vyzkoušel všechny IP adresy paralelně, takže se klient může okamžitě připojit k FCI nebo naslouchacímu procesu. 

Pokud je to možné, doporučuje se název distribuované sítě přes Nástroj pro vyrovnávání zatížení, protože: 
- Kompletní řešení je robustnější, protože už nemusíte spravovat prostředek nástroje pro vyrovnávání zatížení. 
- Odstraněním sond nástroje pro vyrovnávání zatížení minimalizujete dobu trvání převzetí služeb při selhání. 
- DNN zjednodušuje zřizování a správu instancí clusteru s podporou převzetí služeb při selhání nebo naslouchacího procesu skupiny dostupnosti s SQL Server na virtuálních počítačích Azure. 

Většina funkcí SQL Server při použití DNN transparentně spolupracuje s FCI a skupinami dostupnosti, ale některé funkce můžou vyžadovat zvláštní pozornost. Další informace najdete v tématech [interoperabilita FCI and DNN](failover-cluster-instance-dnn-interoperability.md) a [interoperabilita AG a DNN](availability-group-dnn-interoperability.md) . 

Začněte tím, že se seznámíte s konfigurací prostředku názvu distribuované sítě pro [instanci clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-distributed-network-name-dnn-configure.md) nebo [skupinu dostupnosti](availability-group-distributed-network-name-dnn-listener-configure.md) .

**Podporovaný operační systém**: Windows Server 2016 a novější   
**Podporovaná verze SQL**: SQL Server 2019 CU2 (FCI) a SQL Server 2019 CU8 (AG)   
**Podporované řešení hadr**: instance clusteru s podporou převzetí služeb při selhání a skupina dostupnosti   


## <a name="limitations"></a>Omezení

Při práci s FCI nebo skupinami dostupnosti a SQL Server v Azure Virtual Machines Vezměte v úvahu následující omezení. 

### <a name="msdtc"></a>NÁSTROJE 

Azure Virtual Machines podporuje Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) na Windows serveru 2019 s úložištěm na sdílených svazcích clusteru (CSV) a [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) nebo na SQL Server virtuálních počítačích, které používají sdílené disky Azure. 

V Azure Virtual Machines není služba MSDTC podporovaná pro Windows Server 2016 nebo starší se sdílenými svazky clusteru, protože:

- Clusterový prostředek MSDTC nejde nakonfigurovat tak, aby používal sdílené úložiště. Pokud v systému Windows Server 2016 vytvoříte prostředek MSDTC, nezobrazí se žádné sdílené úložiště dostupné pro použití, a to i v případě, že je úložiště k dispozici. Tento problém byl opravený v systému Windows Server 2019.
- Nástroj pro vyrovnávání zatížení úrovně Basic nezpracovává porty RPC.


## <a name="next-steps"></a>Další kroky

Až zjistíte vhodné osvědčené postupy pro vaše řešení, začněte tím, že [připravíte SQL Server virtuální počítač pro FCI](failover-cluster-instance-prepare-vm.md) nebo vytvoříte skupinu dostupnosti pomocí [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShellu](./availability-group-az-commandline-configure.md)nebo [šablon Azure pro rychlý Start](availability-group-quickstart-template-configure.md).
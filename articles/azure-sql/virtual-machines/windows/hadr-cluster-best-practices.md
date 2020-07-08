---
title: Osvědčené postupy konfigurace clusteru
description: Přečtěte si o podporovaných konfiguracích clusteru, když konfigurujete vysokou dostupnost a zotavení po havárii (HADR) pro SQL Server v Azure Virtual Machines, jako jsou například podporované kvora nebo možnosti směrování připojení.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: d20ac5964ef70618d4d7dc2d4a7fe7d7d01284ce
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965533"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Osvědčené postupy konfigurace clusteru (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cluster se používá pro vysokou dostupnost a zotavení po havárii (HADR) s SQL Server v Azure Virtual Machines (VM). 

Tento článek popisuje osvědčené postupy konfigurace clusteru pro [instance clusterů s podporou převzetí služeb při selhání (FCIs)](failover-cluster-instance-overview.md) i [skupiny dostupnosti](availability-group-overview.md) , pokud je používáte s SQL Server na virtuálních počítačích Azure. 


## <a name="networking"></a>Sítě

Použijte jednu síťovou kartu na server (uzel clusteru) a jednu podsíť. Sítě Azure mají fyzickou redundanci, která v hostovaném clusteru virtuálních počítačů Azure nepotřebuje další síťové adaptéry a podsítě. Sestava ověření clusteru vás upozorní, že uzly jsou dosažitelné jenom v jedné síti. Toto upozornění můžete ignorovat u clusterů s podporou převzetí služeb při selhání hosta virtuálního počítače Azure.

## <a name="quorum"></a>Umožněn

I když cluster se dvěma uzly bude fungovat bez [prostředku kvora](/windows-server/storage/storage-spaces/understand-quorum), je nutné, aby zákazníci používali k podpoře produkčního prostředku prostředek kvora. Ověření clusteru neprojde žádný cluster bez prostředku kvora. 

Technicky, cluster se třemi uzly může předržet jednu ztrátu uzlu (na dvou uzlech) bez prostředku kvora. I když je cluster nefunkční na dvou uzlech, existuje riziko, že je možné spustit: 

- **Oddíl v prostoru** (dělené mozek): uzly clusteru se v síti rozdělují, protože se jedná o problém serveru, síťové karty nebo přepínače. 
- **Oddíl v čase** (Amnesia): uzel se připojí nebo znovu připojí ke clusteru a pokusí se o to, aby vyžádat vlastnictví skupiny clusteru nebo role clusteru nevhodně. 

Prostředek kvora chrání cluster proti jednomu z těchto problémů. 

Ke konfiguraci prostředku kvora s SQL Server na virtuálních počítačích Azure můžete použít tyto typy kopií: 


||[Disk s kopií clusteru](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Disk s kopií cloudu](/windows-server/failover-clustering/deploy-cloud-witness)  |[Určující sdílená složka](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Podporovaný operační systém**| Vše |Windows Server 2016 +| Windows Server 2012 +|
|**Podporovaná verze SQL Server**|SQL Server 2019|SQL Server 2016 +|SQL Server 2016 +|



### <a name="disk-witness"></a>Disk s kopií clusteru

Disk s kopií clusteru je malý clusterovaný disk ve skupině úložišť dostupných v clusteru. Tento disk je vysoce dostupný a může převzít služby při selhání mezi uzly. Obsahuje kopii clusterové databáze s výchozí velikostí, která je obvykle menší než 1 GB. 

Nakonfigurujte sdílený disk Azure jako určující disk. 

Informace o tom, jak začít, najdete v tématu [Konfigurace určujícího disku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Podporovaný operační systém**: vše    
**Podporovaná verze SQL**: SQL Server 2019   


### <a name="cloud-witness"></a>Disk s kopií cloudu

Určující Cloud je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání, který používá Microsoft Azure k poskytnutí hlasu kvora clusteru. Výchozí velikost je přibližně 1 MB a obsahuje pouze časové razítko. Disk s kopií cloudu je ideální pro nasazení ve více lokalitách, několika zónách a několika oblastech.

Informace o tom, jak začít, najdete v tématu [Konfigurace určujícího cloudu](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**Podporovaný operační systém**: Windows Server 2016 a novější   
**Podporovaná verze SQL**: SQL Server 2016 a novější     


### <a name="file-share-witness"></a>Určující sdílená složka

Určující sdílená složka je sdílená složka SMB, která je obvykle nakonfigurovaná na souborovém serveru se systémem Windows Server. Udržuje informace o clusteringu v souboru. log, ale neukládá kopii databáze clusteru. V Azure můžete [sdílenou složku Azure](../../../storage/files/storage-how-to-create-file-share.md) nakonfigurovat tak, aby se používala jako určující sdílená složka, nebo můžete použít sdílenou složku na samostatném virtuálním počítači.

Pokud budete používat jinou sdílenou složku Azure, můžete ji připojit se stejným procesem, který slouží k [připojení sdílené složky Premium](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Informace o tom, jak začít, najdete v tématu [Konfigurace určující sdílené složky](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Podporovaný operační systém**: Windows Server 2012 a novější   
**Podporovaná verze SQL**: SQL Server 2016 a novější   


## <a name="connectivity"></a>Připojení

V tradičních místních síťových prostředích se SQL Server instance clusteru s podporou převzetí služeb při selhání jeví jako jediná instance SQL Server spuštěná v jednom počítači. Vzhledem k tomu, že instance clusteru s podporou převzetí služeb při selhání převezme služby při selhání z uzlu na uzel, název virtuální sítě (VNN) pro instanci poskytuje jednotný spojovací bod a umožňuje aplikacím připojit se k instanci SQL Server bez vědomí, který uzel je aktuálně aktivní. Když dojde k převzetí služeb při selhání, název virtuální sítě se zaregistruje do nového aktivního uzlu po jeho spuštění. Tento proces je transparentní pro klienta nebo aplikaci, které se připojují k SQL Server. tím se minimalizuje prostoje, ke kterým klient nebo aplikace při selhání dojde. 

K směrování provozu do VNN instance clusteru s podporou převzetí služeb při selhání s SQL Server na virtuálních počítačích Azure použijte VNN s Azure Load Balancer nebo název distribuované sítě (DNN). Funkce DNN je aktuálně dostupná jenom pro SQL Server 2019 CU2 a novější na virtuálním počítači s Windows serverem 2016 (nebo novějším). 

Následující tabulka porovnává podporu připojení HADR: 

| |**Název virtuální sítě (VNN)**  |**Název distribuované sítě (DNN)**  |
|---------|---------|---------|
|**Minimální verze operačního systému**| Windows Server 2012 | Windows Server 2016|
|**Minimální verze SQL Server** |SQL Server 2012 |SQL Server 2019 CU2|
|**Podporované řešení HADR** | Instance clusteru s podporou převzetí služeb při selhání <br/> Skupina dostupnosti | Instance clusteru s podporou převzetí služeb při selhání|


### <a name="virtual-network-name-vnn"></a>Název virtuální sítě (VNN)

Vzhledem k tomu, že přístupový bod virtuální IP adresy funguje v Azure jinak, je nutné nakonfigurovat [Azure Load Balancer](../../../load-balancer/index.yml) ke směrování provozu na IP adresu uzlů FCI. Nástroj pro vyrovnávání zatížení ve virtuálních počítačích Azure obsahuje IP adresu VNN, na které se spoléhají Clusterové SQL Server prostředky. Nástroj pro vyrovnávání zatížení distribuuje příchozí toky, které dorazí na front-end, a potom směruje provoz do instancí definovaných back-end fondem. Tok přenosů se konfiguruje pomocí pravidel vyrovnávání zatížení a sond stavu. U SQL Server FCI jsou instance fondu back-endu virtuálními počítači Azure, na kterých běží SQL Server. 

Při použití nástroje pro vyrovnávání zatížení existuje mírné zpoždění při převzetí služeb při selhání, protože sonda stavu je ve výchozím nastavení každých 10 sekund. 

Pokud chcete začít, přečtěte si, jak [nakonfigurovat Azure Load Balancer pro FCI](hadr-vnn-azure-load-balancer-configure.md). 

**Podporovaný operační systém**: Windows Server 2012 a novější   
**Podporovaná verze SQL**: SQL Server 2012 a novější   
**Podporované řešení hadr**: instance clusteru s podporou převzetí služeb při selhání a skupina dostupnosti 


### <a name="distributed-network-name-dnn"></a>Název distribuované sítě (DNN)

Název distribuované sítě je nová funkce Azure pro SQL Server 2019 CU2. DNN nabízí alternativní způsob, jak SQL Server klientům připojit se k instanci clusteru s podporou převzetí služeb při selhání SQL Server bez použití nástroje pro vyrovnávání zatížení. 

Při vytvoření prostředku DNN se cluster připojí k názvu DNS s IP adresami všech uzlů v clusteru. Klient SQL se pokusí připojit ke každé IP adrese v tomto seznamu, aby našel uzel, ve kterém je aktuálně spuštěná instance clusteru s podporou převzetí služeb při selhání. Tento proces lze urychlit zadáním `MultiSubnetFailover=True` v připojovacím řetězci. Toto nastavení oznamuje poskytovateli, aby vyzkoušel všechny IP adresy paralelně, takže se klient může k FCI okamžitě připojit. 

Pokud je to možné, doporučuje se název distribuované sítě přes Nástroj pro vyrovnávání zatížení, protože: 
- Kompletní řešení je robustnější, protože už nemusíte spravovat prostředek nástroje pro vyrovnávání zatížení. 
- Odstraněním sond nástroje pro vyrovnávání zatížení minimalizujete dobu trvání převzetí služeb při selhání. 
- DNN zjednodušuje zřizování a správu instance clusteru s podporou převzetí služeb při selhání SQL Server na virtuálních počítačích Azure. 

Většina funkcí SQL Server pracuje transparentně s FCI. V těchto případech můžete jednoduše nahradit existující název DNS VNN názvem DNS DNN nebo hodnotu DNN nastavit pomocí existujícího názvu DNS VNN. Některé komponenty na straně serveru ale vyžadují alias sítě, který mapuje VNN název na DNN název. Konkrétní případy můžou vyžadovat explicitní použití názvu DNS DNN, například při definování určitých adres URL v konfiguraci na straně serveru. 

Pokud chcete začít, přečtěte si, jak [nakonfigurovat prostředek DNN pro FCI](hadr-distributed-network-name-dnn-configure.md). 

**Podporovaný operační systém**: Windows Server 2016 a novější   
**Podporovaná verze SQL**: SQL Server 2019 a novější   
**Podporované řešení hadr**: jenom instance clusteru s podporou převzetí služeb při selhání


## <a name="limitations"></a>Omezení

Při práci s FCI nebo skupinami dostupnosti a SQL Server v Azure Virtual Machines Vezměte v úvahu následující omezení. 

### <a name="msdtc"></a>NÁSTROJE 
Azure Virtual Machines podporuje Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) na Windows serveru 2019 s úložištěm na sdílených svazcích clusteru (CSV) a v [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md).

V Azure Virtual Machines není služba MSDTC podporovaná pro Windows Server 2016 nebo starší, protože:

- Clusterový prostředek MSDTC nejde nakonfigurovat tak, aby používal sdílené úložiště. Pokud v systému Windows Server 2016 vytvoříte prostředek MSDTC, nezobrazí se žádné sdílené úložiště dostupné pro použití, a to i v případě, že je úložiště k dispozici. Tento problém byl opravený v systému Windows Server 2019.
- Nástroj pro vyrovnávání zatížení úrovně Basic nezpracovává porty RPC.


## <a name="next-steps"></a>Další kroky

Až zjistíte vhodné osvědčené postupy pro vaše řešení, začněte tím, že [připravíte SQL Server virtuální počítač pro FCI](failover-cluster-instance-prepare-vm.md). Skupinu dostupnosti můžete vytvořit také pomocí [Azure CLI](availability-group-az-cli-configure.md)nebo [šablon Azure pro rychlý Start](availability-group-quickstart-template-configure.md). 


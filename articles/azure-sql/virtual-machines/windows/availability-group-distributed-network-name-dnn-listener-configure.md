---
title: Konfigurace naslouchacího procesu DNN pro skupinu dostupnosti
description: Naučte se nakonfigurovat naslouchací proces DNN (Distributed Network Name), který nahradí naslouchací proces vaší virtuální sítě (VNN) a směruje provoz do skupiny dostupnosti Always On na SQL Server na VIRTUÁLNÍm počítači Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bf5c4c39ea8f5705cc9788fdcf2cddd01dcb4087
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034703"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Konfigurace naslouchacího procesu DNN pro skupinu dostupnosti
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Při SQL Server na virtuálních počítačích Azure směruje název distribuované sítě (DNN) provoz na příslušný clusterovaný prostředek. Poskytuje snazší způsob, jak se připojit ke skupině dostupnosti Always On (AG) než k naslouchacího procesu VNN (Virtual Network Name), a to bez nutnosti Azure Load Balancer.

Tento článek vás seznámí s konfigurací naslouchacího procesu DNN k nahrazení naslouchacího procesu VNN a směrování provozu do vaší skupiny dostupnosti pomocí SQL Server na virtuálních počítačích Azure pro zajištění vysoké dostupnosti a zotavení po havárii (HADR).

Funkce naslouchacího procesu DNN je momentálně dostupná jenom od verze SQL Server 2019 CU8 v systému Windows Server 2016 a novějším.

Pro možnost alternativního připojení zvažte [vnn naslouchací proces a](availability-group-vnn-azure-load-balancer-configure.md) místo toho Azure Load Balancer.

## <a name="overview"></a>Přehled

Naslouchací proces DNN (Distributed Network Name) nahrazuje klasický naslouchací proces skupiny dostupnosti virtuální sítě (VNN) při použití se [skupinami dostupnosti Always On na SQL Server virtuálních počítačích](availability-group-overview.md). To by tedy bylo nutnost, aby Azure Load Balancer směrovala provoz, zjednodušila nasazení, údržbu a vylepšuje převzetí služeb při selhání.

Použijte naslouchací proces DNN k nahrazení stávajícího naslouchacího procesu VNN, případně ho použijte ve spojení s existujícím naslouchací proces VNN, aby vaše skupina dostupnosti měla dva odlišné body připojení – jeden pomocí názvu naslouchacího procesu VNN (a port, pokud není výchozí), a druhý pomocí názvu a portu naslouchacího procesu DNN.

> [!CAUTION]
> Chování směrování při použití DNN se liší při použití VNN. Nepoužívejte port 1433. Další informace najdete v části [zvážení portu](#port-considerations) dále v tomto článku.

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku byste už měli mít:

- SQL Server 2019 v systému Windows Server 2016 a novějším na CU8 nebo novějším
- Bylo rozhodnuto, že název distribuované sítě je vhodnou [možností připojení pro vaše řešení hadr](hadr-cluster-best-practices.md#connectivity).
- Nakonfigurovali jste [skupinu dostupnosti Always On](availability-group-overview.md). 
- Nainstalovali jste nejnovější verzi [PowerShellu](/powershell/azure/install-az-ps). 
- Byl identifikován jedinečný port, který budete používat pro naslouchací proces DNN. Port, který se používá pro naslouchací proces DNN, musí být jedinečný ve všech replikách skupiny dostupnosti nebo instance clusteru s podporou převzetí služeb při selhání.  Žádné jiné připojení nemůže sdílet stejný port.



## <a name="create-script"></a>Vytvořit skript

Pomocí PowerShellu vytvořte prostředek distribuované sítě (DNN) a přidružte ho ke svojí skupině dostupnosti.

To můžete provést pomocí těchto kroků:

1. Spusťte textový editor, například Poznámkový blok.
1. Zkopírujte a vložte následující skript:

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Uložte skript jako `.ps1` soubor, například `add_dnn_listener.ps1` .

## <a name="execute-script"></a>Spustit skript

Chcete-li vytvořit naslouchací proces DNN, spusťte skript předávající parametry pro název skupiny dostupnosti, název naslouchacího procesu a port.

Za předpokladu, že jako název skupiny dostupnosti `ag1` , název naslouchacího procesu `dnnlsnr` a port naslouchacího procesu jako `6789` , použijte následující postup:

1. Otevřete nástroj rozhraní příkazového řádku, jako je třeba příkazový řádek nebo PowerShell.
1. Přejděte do umístění, do kterého jste `.ps1` skript uložili, například c:\Documents.
1. Spusťte skript: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Například:

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Ověřit naslouchací proces

K potvrzení úspěšného vytvoření naslouchacího procesu DNN použijte buď SQL Server Management Studio, nebo Transact-SQL.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Rozbalením naslouchacího procesu **skupiny dostupnosti** v [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) zobrazíte naslouchací proces DNN:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Zobrazení naslouchacího procesu DNN v rámci naslouchacího procesu skupiny dostupnosti v SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Pro zobrazení stavu naslouchacího procesu DNN použijte příkaz Transact-SQL:

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Hodnota `1` pro `is_distributed_network_name` znamená, že naslouchací proces je název distribuované sítě (DNN):

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Použijte sys.availability_group_listeners k identifikaci naslouchacího procesu DNN, který má hodnotu 1 v is_distributed_network_name":::

## <a name="update-connection-string"></a>Aktualizovat připojovací řetězec

Aktualizujte připojovací řetězce pro aplikace tak, aby se připojily ke službě DNN Listener. Připojovací řetězce pro naslouchací procesy DNN musí poskytovat číslo portu DNN. Pokud chcete zajistit rychlé připojení při převzetí služeb při selhání, přidejte `MultiSubnetFailover=True` do připojovacího řetězce, pokud ho klient SQL podporuje.

## <a name="test-failover"></a>Testovací převzetí služeb při selhání

Otestujte převzetí služeb při selhání skupiny dostupnosti, aby se zajistila funkčnost.

Při testování převzetí služeb při selhání postupujte takto:

1. Připojte se k naslouchacího procesu DNN nebo k některé z replik pomocí [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
1. Rozbalte **skupinu dostupnosti Always On** v **Průzkumník objektů**.
1. Klikněte pravým tlačítkem na skupinu dostupnosti a výběrem možnosti **převzetí služeb při** selhání otevřete **Průvodce převzetí služeb při selhání**.
1. Podle zobrazených výzev vyberte cíl převzetí služeb při selhání a skupinu dostupnosti napřed do sekundární repliky.
1. Potvrďte, že je databáze v synchronizovaném stavu na nové primární replice.
1. Volitelné Navrácení služeb po obnovení původní primární nebo jiné sekundární replice.

## <a name="test-connectivity"></a>Test připojení

Otestujte připojení ke svému naslouchacího procesu DNN pomocí těchto kroků:

1. Otevřete [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Připojte se k vašemu DNN naslouchacího procesu.
1. Otevřete nové okno dotazu a ověřte, ke které replice jste se připojili spuštěním příkazu `SELECT @@SERVERNAME` .
1. Skupinu dostupnosti můžete převzít do jiné repliky.
1. Po rozumné době spusťte příkaz `SELECT @@SERVERNAME` a potvrďte, že vaše skupina dostupnosti je teď hostovaná na jiné replice.

## <a name="limitations"></a>Omezení

- V současné době je naslouchací proces DNN pro skupinu dostupnosti podporován pouze pro SQL Server 2019 CU8 a novější v systému Windows Server 2016 a novějším. 
- Naslouchací procesy DNN **musí** být nakonfigurovány s jedinečným portem.  Port nelze sdílet s žádným jiným připojením v žádné replice.
- Pokud pracujete s jinými SQL Server funkcemi a skupinou dostupnosti s DNN, může docházet k dalším okolnostem. Další informace najdete v tématu [AG s interoperabilitou DNN](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Požadavky na porty

Naslouchací procesy DNN jsou navržené tak, aby naslouchaly na všech IP adresách, ale na konkrétním jedinečném portu. Položka DNS pro název naslouchacího procesu by se měla překládat na adresy všech replik ve skupině dostupnosti. To se provádí automaticky pomocí skriptu prostředí PowerShell, který je k dispozici v části [Vytvoření skriptu](#create-script) . Vzhledem k tomu, že naslouchací procesy DNN přijímají připojení na všech IP adresách, je důležité, aby port naslouchacího procesu byl jedinečný a nepoužíval se u žádné jiné repliky ve skupině dostupnosti. Vzhledem k tomu, že SQL Server vždy naslouchá na portu 1433, a to buď přímo, nebo prostřednictvím služby SQL Browser, port 1433 nelze použít pro žádný naslouchací proces DNN.

## <a name="next-steps"></a>Další kroky

Další informace o funkcích SQL Server HADR v Azure najdete v tématu [skupiny dostupnosti](availability-group-overview.md) a [instance clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-overview.md). Můžete si také přečíst [osvědčené postupy](hadr-cluster-best-practices.md) pro konfiguraci prostředí pro zajištění vysoké dostupnosti a zotavení po havárii. 

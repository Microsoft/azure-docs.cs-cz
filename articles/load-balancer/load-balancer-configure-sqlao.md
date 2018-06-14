---
title: Konfigurace pro vyrovnávání zatížení pro SQL Server vždy na | Microsoft Docs
description: Konfigurace vyrovnávání zatížení, fungovat s SQL serveru Always On a zjistěte, jak pomocí prostředí PowerShell vytvořit nástroj pro vyrovnávání zatížení pro implementaci serveru SQL
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: a0c2345b47b9103ac6a7ae998f13a12332e3907e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
ms.locfileid: "30262513"
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Konfigurace služby Vyrovnávání zatížení pro SQL serveru Always On



Skupiny dostupnosti SQL serveru Always On s nástrojem pro vyrovnávání zatížení pro vnitřní teď můžete spustit. Skupina dostupnosti je řešení nejdůležitějšího SQL serveru pro vysokou dostupnost a zotavení po havárii. Naslouchací proces skupiny dostupnosti, umožňuje klientským aplikacím bezproblémově připojit k primární replice, bez ohledu na počet replik v konfiguraci.

Název naslouchacího procesu (DNS) je namapována na IP adresu s vyrovnáváním zatížení. Azure nástroj pro vyrovnávání zatížení bude směrovat příchozí provoz jenom na primární server sady replik.

Podpora nástroje pro vyrovnávání zatížení interní můžete použít pro koncové body SQL serveru Always On (naslouchací proces). Nyní máte kontrolu nad usnadnění naslouchacího procesu. Můžete z konkrétní podsítě IP adresu s vyrovnáváním zatížení ve virtuální síti.

Pomocí interní vyrovnávání zátěže na naslouchací proces, koncový bod SQL Server (například Server = tcp:ListenerName, 1433; Database = DatabaseName) je přístupná jenom pro:

* Služby a virtuální počítače ve stejné virtuální síti.
* Služeb a virtuálních počítačů z připojených místní sítě.
* Služeb a virtuálních počítačů z vzájemně propojena virtuální sítě.

![Nástroj pro vyrovnávání zatížení pro vnitřní SQL serveru Always On](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Přidejte interní nástroj pro službu

1. V následujícím příkladu můžete nakonfigurovat virtuální síť, která obsahuje podsíť s názvem "Subnet-1.:

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Přidejte Vyrovnávání zatížení sítě koncové body pro interní nástroj pro každý virtuální počítač.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    V předchozím příkladu máte dva virtuální počítače nazvané "sqlsvc1" a "sqlsvc2" spuštěné v rámci cloudové služby "Sqlsvc". Po vytvoření Vyrovnávání zatížení pro vnitřní `DirectServerReturn` přepnout, přidáte do nástroje pro vyrovnávání zatížení pro vnitřní koncové body s vyrovnáváním zatížení. Koncové body s vyrovnáváním zatížení povolit systému SQL Server ke konfiguraci naslouchací procesy pro skupiny dostupnosti.

Další informace o SQL serveru Always On najdete v tématu [konfigurace interní nástroj pro skupiny dostupnosti Always On v Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Začněte konfiguraci služby Vyrovnávání zatížení veřejnou](load-balancer-get-started-internet-arm-ps.md)
* [Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-ps.md)
* [Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)

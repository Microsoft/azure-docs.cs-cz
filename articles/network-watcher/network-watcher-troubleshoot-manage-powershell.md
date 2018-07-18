---
title: Řešení potíží s brány virtuální sítě Azure a připojení – PowerShell | Dokumentace Microsoftu
description: 'Tato stránka vysvětluje, jak používat Azure Network Watcher řešení potíží s powershellu:'
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 67c34156d6a397cdeb4bb50c712b1bb651c2f257
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090424"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Řešení potíží s brány virtuální sítě a připojení pomocí Azure Powershellu sledovací proces sítě

> [!div class="op_single_selector"]
> - [Azure Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Sledovací proces sítě poskytuje mnoho funkcí, jako má vztah k pochopení síťovým prostředkům v Azure. Jeden z těchto funkcí je prostředek řešení potíží. Řešení potíží s prostředků lze volat na portálu, Powershellu, rozhraní příkazového řádku nebo rozhraní REST API. Při volání, Network Watcheru kontroluje stav brány virtuální sítě nebo připojení a vrátí jeho zjištění.

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher.

Seznam podporovaných brány najdete typy [typy brány nepodporuje](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s prostředků poskytuje možnost řešení problémů, které vznikají u brány virtuální sítě a připojení. Po odeslání žádosti do prostředků, řešení potíží s protokoly se dotazovat a prozkoumat. Po dokončení kontroly výsledky jsou vráceny. Řešení problémů s požadavky jsou spuštěna po dlouhou dobu požadavky na zdroje, což může trvat několik minut vrátit požadovaný výsledek. Protokoly z Poradce při potížích se ukládají v kontejneru v účtu úložiště, který je určen.

## <a name="retrieve-network-watcher"></a>Načíst Network Watcher

Prvním krokem je načtení instance Network Watcheru. `$networkWatcher` Proměnná je předána `Start-AzureRmNetworkWatcherResourceTroubleshooting` rutiny v kroku 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Načíst připojení brány virtuální sítě

V tomto příkladu řešení potíží s prostředků je právě spuštěný připojení. Můžete také předat ji bránu virtuální sítě.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Řešení potíží s prostředku vrátí data o stavu prostředku, také uloží protokoly do účtu úložiště, které se mají zkontrolovat. V tomto kroku vytvoříme účet úložiště, pokud existuje stávající účet úložiště můžete ho použít.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Spuštění Poradce při potížích prostředek Network Watcher

Řešení potíží s prostředky s `Start-AzureRmNetworkWatcherResourceTroubleshooting` rutiny. Objekt Network Watcher, Id připojení nebo brány virtuální sítě, id účtu úložiště a cestu k uložení výsledků předáme rutinu.

> [!NOTE]
> `Start-AzureRmNetworkWatcherResourceTroubleshooting` Je dlouho běžící rutiny a může trvat několik minut.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Po spuštění rutiny Network Watcheru kontroluje prostředek, který chcete ověřit stav. Vrací výsledky do prostředí a ukládá protokoly výsledků zadaný účet úložiště.

## <a name="understanding-the-results"></a>Principy výsledky

Text akce obsahuje obecné pokyny k vyřešení daného problému. Pokud mohou se akce pro tento problém, je k dispozici odkaz Další informace. V případě, pokud neexistuje žádné další doprovodné materiály, odpověď obsahuje adresu url pro otevření případu podpory.  Další informace o vlastnostech odpovědi a co je součástí [přehled řešení potíží s sledovací proces sítě](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účtů úložiště azure, najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který je možné je Průzkumníka služby Storage. Další informace o Průzkumníku služby Storage najdete tady na následující odkaz: [Průzkumníka služby Storage](http://storageexplorer.com/)

## <a name="next-steps"></a>Další postup

Pokud byly změněny nastavení této možnosti připojení sítě VPN stop, přečtěte si téma [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) vysledovat pravidla zabezpečení sítě skupiny a zabezpečení, které mohou být nejistá.

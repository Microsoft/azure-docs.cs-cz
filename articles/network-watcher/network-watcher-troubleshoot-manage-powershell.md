---
title: Řešení potíží s bránou virtuální sítě Azure a připojeními – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak používat rutinu PowerShellu pro řešení potíží s Network Watcher Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 1461f7f9811145553da6c200111d4db1f013060e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84738681"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Řešení potíží s Virtual Network bránou a připojením pomocí Azure Network Watcher PowerShellu

> [!div class="op_single_selector"]
> - [Azure Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher poskytuje řadu možností, které se týkají porozumění síťovým prostředkům v Azure. Jednou z těchto funkcí je řešení potíží s prostředky. Řešení potíží s prostředky je možné volat prostřednictvím portálu, PowerShellu, rozhraní příkazového řádku nebo REST API. Při volání Network Watcher zkontroluje stav Virtual Network brány nebo připojení a vrátí své závěry.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher.

Seznam podporovaných typů bran najdete v části [podporované typy bran](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s prostředky poskytuje možnost řešit problémy, které vznikají Virtual Network Branch a připojeních. Při odstraňování potíží s prostředky se protokoly dotazují a kontrolují. Po dokončení kontroly se vrátí výsledky. Požadavky na řešení potíží s prostředky jsou dlouho běžící požadavky, což může trvat několik minut, než se výsledek vrátí. Protokoly z řešení potíží se ukládají do kontejneru v účtu úložiště, který je určený.

## <a name="retrieve-network-watcher"></a>Načíst Network Watcher

Prvním krokem je načtení instance Network Watcher. `$networkWatcher`Proměnná je předána `Start-AzNetworkWatcherResourceTroubleshooting` rutině v kroku 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Načtení připojení Virtual Network brány

V tomto příkladu Probíhá odstraňování potíží s prostředky v připojení. Můžete ji také předat Virtual Network bránu.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Řešení potíží s prostředky vrátí data o stavu prostředku, ale také uloží protokoly do účtu úložiště, který se má zkontrolovat. V tomto kroku vytvoříme účet úložiště, pokud už existuje existující účet úložiště, který můžete použít.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Řešení potíží se spuštěním Network Watcher prostředků

Pomocí rutiny můžete řešit problémy s prostředky `Start-AzNetworkWatcherResourceTroubleshooting` . Rutinu předáte objekt Network Watcher, ID připojení nebo Virtual Network bránu, ID účtu úložiště a cestu k uložení výsledků.

> [!NOTE]
> `Start-AzNetworkWatcherResourceTroubleshooting`Rutina je dlouho spuštěná a dokončení může trvat několik minut.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Po spuštění rutiny Network Watcher zkontroluje prostředek a ověří stav. Vrátí výsledky do prostředí a uloží protokoly výsledků v zadaném účtu úložiště.

## <a name="understanding-the-results"></a>Porozumění výsledkům

Text akce poskytuje obecné pokyny k vyřešení tohoto problému. Pokud je možné provést akci pro daný problém, je k dispozici odkaz s dalšími pokyny. V případě, že nejsou k dispozici žádné další doprovodné materiály, odpověď poskytne adresu URL pro otevření případu podpory.  Další informace o vlastnostech odpovědi a o tom, co je zahrnuto, najdete v tématu [Network Watcher řešení potíží – přehled](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účtů Azure Storage najdete v tématu [Začínáme s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Průzkumník služby Storage se dá použít jiný nástroj. Další informace o Průzkumník služby Storage najdete na následujícím odkazu: [Průzkumník služby Storage](https://storageexplorer.com/)

## <a name="next-steps"></a>Další kroky

Pokud se změnila nastavení, která zastavují připojení k síti VPN, přečtěte si téma [Správa skupin zabezpečení](../virtual-network/manage-network-security-group.md) sítě a sledujte skupinu zabezpečení sítě a pravidla zabezpečení, která mohou být v dané otázce.

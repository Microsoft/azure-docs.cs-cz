---
title: Správa protokolů toku skupiny zabezpečení sítě pomocí sledovací proces sítě Azure - rozhraní příkazového řádku Azure | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupiny zabezpečení sítě v Azure sledovací proces sítě pomocí rozhraní příkazového řádku Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fa90d406313d756f3dd852305a6d61db29815192
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203405"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Skupina zabezpečení sítě toku protokoly jsou funkce sledovací proces sítě, která vám umožní zobrazit informace o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě. Tyto protokoly toku jsou zapsané ve formátu json a zobrazit příchozí a odchozí toky na základě pravidla na síťový adaptér tok se vztahuje na 5 řazené kolekce členů informace o toku (protokol IP zdroj nebo cíl, zdrojový nebo cílový Port), a pokud se povoluje nebo odepírá provoz.

Tento článek používá naší nové generace rozhraní příkazového řádku pro model nasazení prostředků management, Azure CLI 2.0, která je dostupná pro Windows, Mac a Linux.

Chcete-li provést kroky v tomto článku, je potřeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="register-insights-provider"></a>Registrace zprostředkovatele statistiky

V pořadí pro tok protokolování nemusí fungovat správně **Microsoft.Insights** zprostředkovatele musí být zaregistrován. Pokud si nejste jisti Pokud **Microsoft.Insights** zaregistrovat poskytovatele, spusťte následující skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Protokoly toku povolit skupinu zabezpečení sítě

Příkaz pro povolení protokolů toku je znázorněno v následujícím příkladu:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
```

Účet úložiště, který určíte, nemůže mít nakonfigurovaná pro něj pravidla sítě, které omezují přístup k síti pouze služby společnosti Microsoft nebo konkrétní virtuálních sítí. Účet úložiště může být ve stejné nebo jiné předplatné, než NSG, které umožňují tok protokolu pro. Pokud používáte různých předplatných, musí být obě přidruženy ke stejné klienta Azure Active Directory. Musí mít účet, který použijete pro každé předplatné [potřebná oprávnění](required-rbac-permissions.md).

## <a name="disable-network-security-group-flow-logs"></a>Protokoly toku zakázat skupinu zabezpečení sítě

Pomocí následujícího příkladu zakázat toku protokoly:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Stáhnout protokolu toku

Umístění úložiště toku protokolu se definuje při vytvoření. Je vhodné nástroj pro přístup k tyto protokoly toku uložit do účtu úložiště Microsoft Azure Storage Explorer, kterou můžete stáhnout tady:  http://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory zachytávání paketů ukládají na účet úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informace o struktuře protokolu najdete v článku [toku skupiny zabezpečení sítě protokolu – přehled](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [vizualizovat protokolů NSG toku pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Zjistěte, jak [vizualizovat toku protokolů NSG s otevřeným zdrojem nástroje](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

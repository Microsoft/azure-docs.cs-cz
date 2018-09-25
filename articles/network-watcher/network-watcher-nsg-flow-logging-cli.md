---
title: Správa protokolů toku skupiny zabezpečení sítě pomocí služby Azure Network Watcher – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupin zabezpečení sítě ve službě Azure Network Watcher pomocí Azure CLI
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
ms.openlocfilehash: e603ef749dbe66eda1c235b62c5155c4af6dc9db
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955142"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupin zabezpečení sítě jsou funkce služby Network Watcher, který vám umožní zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě. Tyto protokoly toku jsou napsané ve formátu json a zobrazení odchozí a příchozí toků na základě pravidel za NIC toku se vztahuje na 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový Port, protokol), a jestli byl povolený nebo zakázaný provoz.

K provedení kroků v tomto článku, budete muset [instalace rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby se tok protokolování nemusí fungovat správně **Microsoft.Insights** musí být zaregistrovaný poskytovatel. Pokud si nejste jisti Pokud **Microsoft.Insights** poskytovatel je zaregistrovaný, spusťte následující skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Povolit skupiny zabezpečení sítě protokolů toku

Příkaz povolení protokolů toku můžete vidět v následujícím příkladu:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
```

Účet úložiště, který zadáte, nemůže mít pravidel sítě nakonfigurována, které omezují přístup k síti jenom na služby nebo konkrétní virtuální sítě. Účet úložiště může být ve stejné nebo jiné předplatné Azure, než skupina zabezpečení sítě, která umožňují v protokolu toku. Pokud používáte různých předplatných, musí být obě přidružené ke stejnému tenantovi Azure Active Directory. Musíte mít účet, který používáte pro každé předplatné [potřebná oprávnění](required-rbac-permissions.md). 

Pokud účet úložiště je v jiné skupině prostředků nebo předplatného, než skupina zabezpečení sítě, zadejte úplné ID účtu úložiště, a ne její název. Například, pokud je účet úložiště ve skupině prostředků s názvem *RG úložiště*, místo určení *storageAccountName* v předchozím příkazu, zadali byste   */subscriptions / { SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Protokoly toku zakázat skupina zabezpečení sítě

Chcete-li zakázat protokolů toku použijte následující příklad:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Stáhnout protokol toku

Umístění úložiště protokolů toku je definován při vytvoření. Praktický nástroj pro přístup k těmto protokolů toku uložit do účtu úložiště je Microsoft Azure Storage Explorer, které je možné stáhnout tady:  http://storageexplorer.com/

Pokud je určen účet úložiště, jsou uloženy soubory zachytávání paketů do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informace o struktuře protokol [tok skupiny zabezpečení sítě protokolu – přehled](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [vizualizaci protokolů toku NSG s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Zjistěte, jak [vizualizovat vaše protokoly toků NSG s open source nástroje](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

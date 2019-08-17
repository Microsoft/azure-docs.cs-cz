---
title: Správa protokolů toku skupin zabezpečení sítě pomocí Azure Network Watcher – Azure CLI | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupin zabezpečení sítě v Azure Network Watcher pomocí Azure CLI.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 5e7c09c1a06a94a2ed64f3624ee38dc42606d7bc
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563488"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupin zabezpečení sítě jsou funkcí Network Watcher, která vám umožní zobrazit informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě. Tyto protokoly toků jsou napsané ve formátu JSON a zobrazují odchozí a příchozí toky na základě jednotlivých pravidel. síťové rozhraní, ke kterému se tok vztahuje, je 5 – informace o toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) a pokud byl provoz povolený nebo zakázaný.

K provedení kroků v tomto článku potřebujete [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby protokolování toků fungovalo úspěšně, musí být zaregistrovaný poskytovatel **Microsoft. Insights** . Pokud si nejste jistí, jestli je poskytovatel **Microsoft. Insights** zaregistrovaný, spusťte následující skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Povolit protokoly toku skupin zabezpečení sítě

V následujícím příkladu se zobrazí příkaz pro povolení protokolů toku:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Účet úložiště, který zadáte, nemůže mít pro něj nakonfigurovaná Síťová pravidla, která omezují přístup k síti jenom na služby Microsoftu nebo konkrétní virtuální sítě. Účet úložiště může být ve stejném nebo jiném předplatném Azure, než NSG, pro který povolíte protokol toku. Pokud používáte jiné odběry, musí být oba přidruženy ke stejnému Azure Active Directory tenantovi. Účet, který použijete pro každé předplatné, musí mít [potřebná oprávnění](required-rbac-permissions.md). 

Pokud je účet úložiště v jiné skupině prostředků nebo předplatném, než je skupina zabezpečení sítě, zadejte celé ID účtu úložiště, nikoli jeho název. Pokud je například účet úložiště ve skupině prostředků s názvem *RG-Storage*, místo určení *storageAccountName* v předchozím příkazu byste zadali */Subscriptions/{SubscriptionId}/resourceGroups/RG-Storage/Providers/ Microsoft. Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Zakázat protokoly toků skupin zabezpečení sítě

K zakázání protokolů toku použijte následující příklad:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Stažení protokolu toku

Umístění úložiště protokolu toku je definováno při vytvoření. Pohodlný nástroj pro přístup k těmto protokolům toků uloženým do účtu úložiště je Průzkumník služby Microsoft Azure Storage, který se dá stáhnout tady: https://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory protokolu Flow se uloží do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

> [!IMPORTANT]
> V současné době dochází k potížím s [protokolem toku NSG (Network Security Group)](network-watcher-nsg-flow-logging-overview.md) pro Network Watcher se z úložiště objektů BLOB automaticky neodstraňují na základě nastavení zásad uchovávání informací. Pokud máte existující nenulové zásady uchovávání informací, doporučujeme, abyste pravidelně odstranili objekty blob úložiště, které jsou po dobu jejich uchování, a nemuseli se jim účtovat poplatky. Další informace o tom, jak odstranit blog úložiště protokolu toku NSG, najdete v tématu [odstranění objektů BLOB úložiště protokolu toku NSG](network-watcher-delete-nsg-flow-log-blobs.md).

## <a name="next-steps"></a>Další kroky

Naučte [se vizualizovat protokoly toku NSG pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) .

Naučte se [vizualizovat protokoly toku NSG pomocí nástrojů Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) .

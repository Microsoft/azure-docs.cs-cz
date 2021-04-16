---
title: Správa protokolů toku NSG – Azure CLI
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupin zabezpečení sítě v Azure Network Watcher pomocí Azure CLI.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: a25d14660e5006aca2913053b17852c752c786d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535256"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupin zabezpečení sítě jsou funkcí Network Watcher, která vám umožní zobrazit informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě. Tyto protokoly toků jsou napsané ve formátu JSON a zobrazují odchozí a příchozí toky na základě jednotlivých pravidel. síťové rozhraní, ke kterému se tok vztahuje, je 5 – informace o toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) a pokud byl provoz povolený nebo zakázaný.

K provedení kroků v tomto článku potřebujete [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI)](/cli/azure/install-azure-cli). Podrobné specifikace všech příkazů protokolů toků najdete [tady](https://docs.microsoft.com/cli/azure/network/watcher/flow-log?view=azure-cli-latest) .

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby protokolování toků fungovalo úspěšně, musí být zaregistrovaný poskytovatel **Microsoft. Insights** . Pokud si nejste jistí, jestli je poskytovatel **Microsoft. Insights** zaregistrovaný, spusťte následující skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Povolit protokoly toku skupin zabezpečení sítě

V následujícím příkladu se zobrazí příkaz pro povolení protokolů toku:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Účet úložiště, který zadáte, nemůže mít pro něj nakonfigurovaná Síťová pravidla, která omezují přístup k síti jenom na služby Microsoftu nebo konkrétní virtuální sítě. Účet úložiště může být ve stejném nebo jiném předplatném Azure, než NSG, pro který povolíte protokol toku. Pokud používáte jiné odběry, musí být oba přidruženy ke stejnému Azure Active Directory tenantovi. Účet, který použijete pro každé předplatné, musí mít [potřebná oprávnění](required-rbac-permissions.md). 

Pokud je účet úložiště v jiné skupině prostředků nebo předplatném, než je skupina zabezpečení sítě, zadejte celé ID účtu úložiště, nikoli jeho název. Pokud je například účet úložiště ve skupině prostředků s názvem *RG-Storage*, místo určení *storageAccountName* v předchozím příkazu byste zadali */Subscriptions/{SubscriptionId}/resourceGroups/RG-Storage/Providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Zakázat protokoly toků skupin zabezpečení sítě

K zakázání protokolů toku použijte následující příklad:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Stažení protokolu toku

Umístění úložiště protokolu toku je definováno při vytvoření. Pohodlný nástroj pro přístup k těmto protokolům toků uloženým do účtu úložiště je Průzkumník služby Microsoft Azure Storage, který se dá stáhnout tady:  https://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory protokolu Flow se uloží do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Další kroky

Naučte [se vizualizovat protokoly toku NSG pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) .

Naučte se [vizualizovat protokoly toku NSG pomocí nástrojů Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) .

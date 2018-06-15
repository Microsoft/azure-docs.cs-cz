---
title: Všimněte si 1 vyřazení hostovaného operačního systému rodiny | Microsoft Docs
description: Poskytuje informace o při vyřazení Azure hostovaného operačního systému rodiny 1 stalo a jak určit, pokud se vás
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 3178a09dab1cb972a3460d54dc9908fb95cce68b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843130"
---
# <a name="guest-os-family-1-retirement-notice"></a>Všimněte si vyřazení hostovaného operačního systému rodiny 1
Vyřazení operačního systému rodiny 1 byla poprvé oznámeno na 1 červen 2013.

**2. září 2014** Azure hostovaný operační systém (hostovaného operačního systému) rodiny 1.x, která je založena na operační systém Windows Server 2008, byl oficiálně vyřazeno. Všechny pokusy o nasazení nové služby nebo upgradovat stávající služby pomocí řady 1 se nezdaří s chybovou zprávu oznamující, že byl vyřazen 1 hostovaného operačního systému rodiny.

**3. listopadu 2014** ukončení rozšířené podpory pro hostovaného operačního systému rodiny 1 a plně je vyřazeno. Bude mít dopad na všechny služby ještě na 1 rodiny. Můžeme kdykoli přestat těchto služeb. Neexistuje žádná záruka, které vaše služby bude nadále spouštět Pokud provedete ruční upgrade je sami.

Pokud máte další dotazy, navštivte [fóra služby Cloud](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) nebo [požádejte podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Je zasaženo?
Cloudové služby se týká, pokud platí kterákoli z následujících akcí:

1. Mít hodnotu "osFamily ="1"explicitně zadané v souboru ServiceConfiguration.cscfg soubor pro cloudové služby.
2. Nemáte hodnotu pro atribut osFamily explicitně zadané v souboru ServiceConfiguration.cscfg soubor pro cloudové služby. V současné době systém použije výchozí hodnotu 1"v takovém případě.
3. Portál Azure uvádí rodiny hodnota hostovaný operační systém jako "Windows Server 2008".

K vyhledání, která z cloudové služby běží které operačních systémů, můžete spustit následující skript v prostředí Azure PowerShell, i když je potřeba [nastavení prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) první. Další informace o skriptu najdete v tématu [Azure hostovaného operačního systému rodiny 1 End z životnosti: června 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Cloudové služby bude ovlivněná podle operačního systému rodiny 1 vyřazení sloupce osFamily ve výstupu skriptu je prázdný nebo obsahuje "1".

## <a name="recommendations-if-you-are-affected"></a>Doporučení, pokud se vás
Doporučujeme, abyste že migrovat role cloudové služby na jednu z podporovaných rodin OS hosta:

**Hostovaného operačního systému rodiny 4.x** -Windows Server 2012 R2 *(doporučeno)*

1. Ujistěte se, že vaše aplikace používá SDK 2.1 nebo vyšší v rozhraní .NET framework 4.0, 4.5 a 4.5.1.
2. Nastavte atribut osFamily, který se "4" v souboru ServiceConfiguration.cscfg souboru a znovu nasaďte cloudové služby.

**Hostovaného operačního systému rodiny 3.x** -Windows Server 2012

1. Ujistěte se, že vaše aplikace používá SDK 1,8 nebo novější s rozhraním .NET framework 4.0 nebo 4.5.
2. V souboru ServiceConfiguration.cscfg souboru nastavený atribut osFamily na "3" a znovu nasaďte cloudové služby.

**Hostovaného operačního systému rodiny 2.x** -Windows Server 2008 R2

1. Ujistěte se, že vaše aplikace používá SDK 1.3 a výše uvedený v rozhraní .NET framework 3.5 nebo 4.0.
2. V souboru ServiceConfiguration.cscfg souboru nastavený atribut osFamily na 2 a znovu nasaďte cloudové služby.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozšířené podpory pro hostovaného operačního systému rodiny 1 skončila 3 listopadu 2014
Cloudové služby na hostovaného operačního systému rodiny 1 již nejsou podporovány. Migrujte z rodiny 1 co nejdříve chcete zabránit přerušení služby.  

## <a name="next-steps"></a>Další kroky
Přečtěte si nejnovější [uvolní hostovaného operačního systému](cloud-services-guestos-update-matrix.md).

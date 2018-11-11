---
title: Oznámení o hostovaného operačního systému řady 1 vyřazení | Dokumentace Microsoftu
description: Poskytuje informace o při vyřazení Azure hostovaného operačního systému řady 1 se stalo a jak určit, pokud se vás týká
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
ms.openlocfilehash: d6429766b6aac547fd99279659acb1067298e77c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244674"
---
# <a name="guest-os-family-1-retirement-notice"></a>Oznámení o vyřazení hostovaného operačního systému řady 1
Vyřazení operačního systému řady 1 bylo poprvé oznámena v 1. června 2013.

**2. září 2014** The Azure hostovaný operační systém (Guest OS) řady 1.x, který je založený na operačním systému Windows Server 2008, oficiálně skončil. Všechny pokusy o nasazení nové služby nebo upgradovat existující služby pomocí řady 1 se nezdaří s chybovou zprávu informující, že byl vyřazen hostovaného operačního systému řady 1.

**3. listopadu 2014** ukončení rozšířené podpory pro hostovaného operačního systému řady 1 a plně je vyřazený. Všechny služby stále na řady 1 budou mít vliv. Tyto služby nám může kdykoli zastavit. Neexistuje žádná záruka, kterou vaše služby budou i nadále spouštět, pokud provedete ruční upgrade je sami.

Pokud máte další dotazy, navštivte [Cloud Services fóra](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) nebo [požádejte podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Se vás týká?
Cloud Services se týká, pokud platí některý z následujících akcí:

1. Mít hodnotu "osFamily ="1"explicitně zadané v souboru ServiceConfiguration.cscfg souboru pro Cloudovou službu.
2. Nemáte hodnotu pro atribut osFamily explicitně zadané v souboru ServiceConfiguration.cscfg souboru pro Cloudovou službu. V současné době systém použije výchozí hodnotu "1" v tomto případě.
3. Na webu Azure portal obsahuje hodnotu řady hostovaného operačního systému jako "Systém Windows Server 2008".

Pokud chcete zjistit, které z vašich cloudových služeb jsou spuštěny které řada operačního systému, můžete spustit následující skript v prostředí Azure PowerShell, i když je potřeba [nastavení prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) první. Další informace o skriptu najdete v tématu [Azure hostovaného operačního systému řady 1 koncový z života: červen 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Cloudové služby bude ovlivněná podle operačního systému řady 1 vyřazení osFamily sloupec ve výstupu skriptu je prázdný nebo obsahuje "1".

## <a name="recommendations-if-you-are-affected"></a>Doporučení, pokud jsou ovlivněny
Doporučujeme že migrovat role cloudové služby na jednu z podporovaných rodin OS hosta:

**Hostovaný operační systém řady 4.x** – Windows Server 2012 R2 *(doporučeno)*

1. Ujistěte se, že vaše aplikace používá SDK 2.1 nebo novější s rozhraním .NET framework 4.0, 4.5 a 4.5.1.
2. Nastavte atribut osFamily "4" v souboru ServiceConfiguration.cscfg souboru a znovu nasadit cloudovou službu.

**Hostovaný operační systém řady 3.x** – Windows Server 2012

1. Ujistěte se, že vaše aplikace je pomocí sady SDK 1.8 a novější rozhraní .NET framework 4.0 nebo 4.5.
2. Nastavte atribut osFamily "3" v souboru ServiceConfiguration.cscfg souboru a znovu nasadit cloudovou službu.

**Hostovaný operační systém řady 2.x** – Windows Server 2008 R2

1. Ujistěte se, že vaše aplikace používá SDK 1.3 a novější s rozhraním .NET framework 3.5 nebo 4.0.
2. Nastavte atribut osFamily na "2" v souboru ServiceConfiguration.cscfg souboru a znovu nasadit cloudovou službu.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozšířená podpora pro hostovaného operačního systému řady 1 ukončení 3. listopadu 2014
Cloudové služby na hostovaného operačního systému řady 1 již nejsou podporovány. Migrate z řady 1 co nejdříve chcete zabránit přerušení služby.  

## <a name="next-steps"></a>Další postup
Přečtěte si nejnovější [verzí hostovaných operačních systémů](cloud-services-guestos-update-matrix.md).

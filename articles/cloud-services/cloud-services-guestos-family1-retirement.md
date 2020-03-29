---
title: Host OS rodina 1 odchod do důchodu oznámení | Dokumenty společnosti Microsoft
description: Obsahuje informace o tom, kdy došlo k vyřazení služby Azure Guest OS Family 1 a jak zjistit, jestli se vás to týká.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359582"
---
# <a name="guest-os-family-1-retirement-notice"></a>Oznámení o vyřazení hostovaného operačního systému řady 1 z provozu
Odchod Do důchodu OS Family 1 byl poprvé oznámen v červnu 1, 2013.

**2. září 2014** Operační systém Azure Guest (Guest OS) Family 1.x, který je založen na operačním systému Windows Server 2008, byl oficiálně vyřazen. Všechny pokusy o nasazení nových služeb nebo upgrade stávajících služeb pomocí rodiny 1 se nezdaří s chybovou zprávou informující o tom, že rodina hostovaného operačního systému 1 byla vyřazena.

**3. listopadu 2014November 3, 2014** Rozšířená podpora pro Guest OS Family 1 skončila a je plně vyřazena. Všechny služby, které jsou stále v rodině 1, budou ovlivněny. Tyto služby můžeme kdykoli zastavit. Neexistuje žádná záruka, že vaše služby budou nadále spuštěny, pokud je ručně neupgradujete sami.

Pokud máte další dotazy, navštivte [fóra cloudových služeb](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) nebo [se obraťte na podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Jste ovlivněni?
Vaše cloudové služby jsou ovlivněny, pokud platí některá z následujících položek:

1. Máte hodnotu "osFamily = "1" explicitně uvedenou v souboru ServiceConfiguration.cscfg pro vaši cloudovou službu.
2. Nemáte hodnotu pro osFamily explicitně uvedenou v souboru ServiceConfiguration.cscfg pro cloudovou službu. V současné době systém používá výchozí hodnotu "1" v tomto případě.
3. Portál Azure uvádí hodnotu rodiny hostovaného operačního systému jako "Windows Server 2008".

Chcete-li zjistit, které z vašich cloudových služeb běží, které rodiny operačních služeb, můžete spustit následující skript v Azure PowerShell, i když je nutné [nastavit Azure PowerShell](/powershell/azureps-cmdlets-docs) jako první. Další informace o skriptu najdete v [tématu Azure Guest OS Family 1 Konec životnosti: červen 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Vaše cloudové služby budou ovlivněny vyřazením rodiny operačních systémů 1, pokud je sloupec osFamily ve výstupu skriptu prázdný nebo obsahuje "1".

## <a name="recommendations-if-you-are-affected"></a>Doporučení, pokud se vás to týká
Doporučujeme migrovat role cloudové služby do jedné z podporovaných rodin hostovaného operačního systému:

**Hostovče řady 4.x** - Windows Server 2012 R2 *(doporučeno)*

1. Ujistěte se, že vaše aplikace používá sdk 2.1 nebo novější s rozhraním .NET framework 4.0, 4.5 nebo 4.5.1.
2. Nastavte atribut osFamily na "4" v souboru ServiceConfiguration.cscfg a znovu nasaďte cloudovou službu.

**Hostované operační systémy řady 3.x** – Windows Server 2012

1. Ujistěte se, že vaše aplikace používá sdk 1.8 nebo novější s rozhraním .NET framework 4.0 nebo 4.5.
2. Nastavte atribut osFamily na "3" v souboru ServiceConfiguration.cscfg a znovu nasaďte cloudovou službu.

**Hostované operační systémy řady 2.x** - Windows Server 2008 R2

1. Ujistěte se, že vaše aplikace používá sDK 1.3 a vyšší s rozhraním .NET framework 3.5 nebo 4.0.
2. Nastavte atribut osFamily na "2" v souboru ServiceConfiguration.cscfg a znovu nasaďte cloudovou službu.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozšířená podpora pro hostovanéo OS Rodina 1 skončila 3.listopadu 2014
Cloudové služby v rodině hostovaného operačního systému 1 již nejsou podporovány. Migrujte z rodiny 1 co nejdříve, abyste se vyhnuli přerušení služby.  

## <a name="next-steps"></a>Další kroky
Projděte si nejnovější [verze hostovaného operačního systému](cloud-services-guestos-update-matrix.md).

---
title: Řada operačních systémů Host 1 – oznámení o vyřazení | Microsoft Docs
description: Obsahuje informace o tom, kdy došlo k vyřazení rodinného operačního systému Azure 1, a jak zjistit, jestli jste to ovlivnili.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 7f6d3feee95d4cce654b2cc1547b8bd7f4eb45d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743011"
---
# <a name="guest-os-family-1-retirement-notice"></a>Oznámení o vyřazení hostovaného operačního systému řady 1 z provozu

Vyřazení řady operačních systémů 1 bylo poprvé oznámeno 1. června 2013.

**Září 2, 2014** Hostovaný operační systém Azure (hostovaný operační systém) řady 1. x, který je založený na operačním systému Windows Server 2008, byl oficiálně vyřazený. Všechny pokusy o nasazení nových služeb nebo upgradování stávajících služeb pomocí řady 1 selžou s chybovou zprávou s oznámením, že řada hostovaného operačního systému 1 je vyřazená.

**3. listopadu 2014** Rozšířená podpora pro rodinu hostovaného operačního systému 1 skončila a je plně vyřazená. Budou to mít vliv na všechny služby, které jsou pořád v rodině 1. Tyto služby můžeme kdykoli zastavit. Není zaručena žádná záruka, že vaše služby budou nadále spuštěny, dokud je ručně NEUPGRADUJTE sami.

Pokud máte další otázky, přejděte na [stránku s dotazem na Microsoft Q&pro Cloud Services](/answers/topics/azure-cloud-services.html) nebo se [obraťte na podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Jste to ovlivnili?
Vaše Cloud Services jsou ovlivněny, pokud platí některá z následujících možností:

1. Máte hodnotu "osFamily =" 1 explicitně zadanou v souboru ServiceConfiguration. cscfg pro vaši cloudovou službu.
2. V souboru ServiceConfiguration. cscfg pro vaši cloudovou službu nemáte hodnotu pro osFamily explicitně zadanou. V současné době používá systém v tomto případě výchozí hodnotu "1".
3. Azure Portal uvádí hodnotu vaší řady hostovaných operačních systémů jako Windows Server 2008.

Pokud chcete zjistit, které z vašich cloudových služeb běží na kterých řadě operačních systémů, můžete spustit následující skript v Azure PowerShell, ale musíte nejdřív [nastavit Azure PowerShell](/powershell/azure/) . Další informace o skriptu najdete v tématu [řada hostovaných operačních systémů Azure 1 konec životnosti: červen 2014](/archive/blogs/ryberry/azure-guest-os-family-1-end-of-life-june-2014).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

V případě, že je sloupec osFamily ve výstupu skriptu prázdný nebo obsahuje znak "1", bude ovlivněna služba Cloud Services 1.

## <a name="recommendations-if-you-are-affected"></a>Doporučení, pokud jste to ovlivnili
Doporučujeme migrovat role cloudové služby na jednu z podporovaných rodin hostovaných operačních systémů:

**Řada operačních systémů Host 4. x** – Windows Server 2012 R2 *(doporučeno)*

1. Zajistěte, aby vaše aplikace používala sadu SDK 2,1 nebo novější s rozhraním .NET Framework 4,0, 4,5 nebo 4.5.1.
2. V souboru ServiceConfiguration. cscfg nastavte atribut osFamily na "4" a znovu nasaďte cloudovou službu.

**Řada operačních systémů Host 3. x** – Windows Server 2012

1. Zajistěte, aby vaše aplikace používala sadu SDK 1,8 nebo novější s rozhraním .NET Framework 4,0 nebo 4,5.
2. V souboru ServiceConfiguration. cscfg nastavte atribut osFamily na "3" a znovu nasaďte cloudovou službu.

**Řada hostovaného operačního systému 2. x** – Windows Server 2008 R2

1. Zajistěte, aby vaše aplikace používala sadu SDK 1,3 a novější s rozhraním .NET Framework 3,5 nebo 4,0.
2. V souboru ServiceConfiguration. cscfg nastavte atribut osFamily na "2" a znovu nasaďte cloudovou službu.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Rozšířená podpora pro rodinu hostovaného operačního systému 1 skončila 3. listopadu 2014
Služba Cloud Services v hostovaném operačním systému řady 1 už není podporovaná. Abyste se vyhnuli výpadkům služby, můžete co nejdříve migrovat rodinu 1.  

## <a name="next-steps"></a>Další kroky
Přečtěte si nejnovější [verze hostovaného operačního systému](cloud-services-guestos-update-matrix.md).
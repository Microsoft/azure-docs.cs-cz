---
title: Zobrazení relativních latencí v oblastech Azure z určitých umístění
description: Naučte se zobrazovat relativní latence napříč poskytovateli internetu do oblastí Azure z konkrétních umístění.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 938315dac80ae51984851083753a9cb8f77662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737899"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Zobrazení relativní latence připojení k oblastem Azure z konkrétních umístění

> [!WARNING]
> Tato funkce je aktuálně ve verzi Preview a stále se testuje na stabilitu.

V tomto kurzu se naučíte, jak používat službu Azure [Network Watcher](network-watcher-monitoring-overview.md) k tomu, abyste se rozhodli, jakou oblast Azure nasadit vaši aplikaci nebo službu v nástroji, na základě demografických údajů uživatele. Navíc ho můžete použít k vyhodnocení připojení poskytovatelů služeb k Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Vytvoření sledovacího procesu sítě

Pokud již máte sledovací proces sítě v alespoň jedné [oblasti](https://azure.microsoft.com/regions)Azure, můžete přeskočit úkoly v této části. Vytvořte skupinu prostředků pro sledovací proces sítě. V tomto příkladu se vytvoří skupina prostředků v Východní USA oblasti, ale můžete vytvořit skupinu prostředků v libovolné oblasti Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Vytvořte sledovací proces sítě. Je nutné, aby byl vytvořen sledovací proces sítě alespoň v jedné oblasti Azure. V tomto příkladu se v oblasti Východní USA Azure vytvoří sledovací proces sítě.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porovnání relativních latencí sítě s jednou oblastí Azure z konkrétního umístění

Vyhodnoťte poskytovatele služeb nebo vyřešte potíže s uživateli, který nahlásí problém, jako je například "Web byl pomalý", z konkrétního umístění do oblasti Azure, ve které je služba nasazená. Následující příkaz například vrátí průměrné relativní latence poskytovatele internetových služeb mezi stavem Washington v USA a Západní USA 2 oblasti Azure mezi 13-15. prosince 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Oblast, kterou zadáte v předchozím příkazu, se nemusí shodovat s oblastí, kterou jste zadali při načítání sledovacího procesu sítě. Předchozí příkaz jednoduše vyžaduje, abyste zadali existující sledovací proces sítě. Sledovací proces sítě může být v libovolné oblasti. Pokud zadáte hodnoty pro `-Country` a `-State` , musí být platné. V hodnotách se rozlišují malá a velká písmena. Data jsou k dispozici omezenému počtu zemí nebo oblastí, států a měst. Spusťte příkazy v části [Zobrazit dostupné země/oblasti, stavy, města a poskytovatele,](#view-available) abyste si zobrazili seznam dostupných zemí/oblastí, města a států pro použití s předchozím příkazem. 

> [!WARNING]
> Pro a je nutné zadat datum v posledních 30 dnech `-StartTime` `-EndTime` . Když zadáte předchozí datum, nebudou se vracet žádná data.

Výstup z předchozího příkazu je následující:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

Ve vráceném výstupu je hodnota pro **skore** relativní latenci napříč oblastmi a poskytovateli. Skóre 1 je nejhorší (nejvyšší) latence, zatímco 100 je nejnižší latence. Relativní latence jsou v průměru pro daný den. V předchozím příkladu je sice jasné, že latence byly stejné jako oba dny a že existuje malý rozdíl mezi latencí těchto dvou zprostředkovatelů, je také jasné, že latence u obou zprostředkovatelů je nízká a 1-100 škálování. V takovém případě se očekává, že stav Washington v USA je fyzicky blízko Západní USA 2 oblasti Azure, někdy ale výsledky neodpovídají očekávání. Čím větší je rozsah dat, který zadáte, tím více můžete průměrnou latenci v průběhu času.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porovnání relativních latencí sítě napříč oblastmi Azure z konkrétního umístění

Pokud místo určení relativních latencí mezi konkrétním umístěním a konkrétní oblastí Azure pomocí `-Location` jste chtěli určit relativní latenci pro všechny oblasti Azure z konkrétního fyzického umístění, můžete to udělat i vy. Například následující příkaz vám pomůže vyhodnotit, co oblast Azure nasadí, v případě, že vaši primární uživatelé Comcastu uživatele ve státě Washington:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Na rozdíl od toho, kdy zadáte jedno umístění, pokud nezadáte umístění nebo zadáte více umístění, například "Západ USA 2", "Západní USA", je při spuštění příkazu nutné zadat poskytovatele internetových služeb. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Zobrazit dostupné země/oblasti, státy, města a poskytovatele

Data jsou k dispozici pro konkrétní poskytovatele internetových služeb, země/oblasti, státy a města. Chcete-li zobrazit seznam všech dostupných poskytovatelů internetových služeb, zemí nebo oblastí, států a měst, pro které můžete zobrazit data, zadejte následující příkaz:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data jsou k dispozici pouze pro země, oblasti, stavy a města vrácené předchozím příkazem. Předchozí příkaz vyžaduje, abyste zadali existující sledovací proces sítě. V příkladu je zadaný *NetworkWatcher_eastus* sledovací proces sítě ve skupině prostředků s názvem *NetworkWatcherRG*, ale můžete zadat libovolný existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte ho provedením úkolů v tématu [Vytvoření sledovacího procesu sítě](#create-a-network-watcher). 

Po spuštění předchozího příkazu můžete vyfiltrovat vrácený výstup zadáním platných hodnot pro **zemi**, **stát** a **město**, pokud je to potřeba.  Pokud například chcete zobrazit seznam poskytovatelů internetových služeb dostupných v Seattle, Washington, zadejte v USA následující příkaz:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Hodnota zadaná pro **zemi** musí být velká a malá písmena. Hodnoty zadané pro **stav** a **město** musí být malé. Hodnoty musí být uvedeny ve výstupu vráceném po spuštění příkazu bez hodnot pro **zemi**, **stát** a **město**. Pokud zadáte nesprávné zadání velkých a malých písmen, nebo zadejte hodnotu pro **zemi**, **stát** nebo **město** , která není ve výstupu vracená po spuštění příkazu bez hodnot těchto vlastností, vrácený výstup je prázdný.

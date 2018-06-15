---
title: Zobrazit relativní latence k oblastem Azure z určitých umístění | Microsoft Docs
description: Zjistěte, jak chcete-li zobrazit relativní latenci mezi poskytovatelům internetových k oblastem Azure z určitých umístění.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2018
ms.locfileid: "27600954"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Zobrazení relativní latence k oblastem Azure z určitých umístění

V tomto kurzu, Naučte se používat Azure [sledovací proces sítě](network-watcher-monitoring-overview.md) služby vám pomohou rozhodnout, jaké oblast Azure k nasazení aplikace nebo služby v, na základě vaší demografické údaje uživatele. Kromě toho můžete použít k vyhodnocení poskytovatelé služeb připojení k Azure.  
        
## <a name="create-a-network-watcher"></a>Vytvoření sledovací proces sítě

Pokud už máte sledovací proces sítě v Azure alespoň jeden [oblast](https://azure.microsoft.com/regions), můžete přeskočit úlohy v této části. Vytvořte skupinu prostředků pro sledovací proces sítě. V tomto příkladu se vytvoří skupina prostředků v oblasti Východ USA, ale můžete vytvořit skupinu prostředků v libovolné oblasti Azure.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Vytvořte sledovací proces sítě. Musíte mít sledovací proces sítě vytvořené v alespoň jedné oblasti Azure. V tomto příkladu se vytvoří sledovací proces sítě v oblasti Azure USA – východ.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porovnání relativní síťovou latenci k jedné oblasti Azure z určitého umístění

Vyhodnocení poskytovatelé služeb nebo odstraňovat uživatele sestav problém, jako je "webu bylo pomalé," z konkrétní umístění, které oblasti azure, kde je nasazená služba. Například následující příkaz vrátí průměrnou latenci zprostředkovatele relativní služby Internet mezi státu Washington v USA a oblasti Azure 2 v západní USA mezi 13 až 15 2017 prosinec:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Oblast, kterou zadáte v předchozí příkaz nemusí být stejný jako oblast, kterou jste zadali při, který jste získali sledovací proces sítě. Předchozí příkaz jednoduše je potřeba zadat existující sledovací proces sítě. Sledovací proces sítě může být v libovolné oblasti. Pokud zadáte hodnoty pro `-Country` a `-State`, musí být platný. Hodnoty jsou malá a velká písmena. Data jsou k dispozici pro omezený počet zemích, stavy a měst. Spusťte příkazy [zobrazení k dispozici zemích, stavy, města a poskytovatelé](#view-available) zobrazíte seznam dostupných zemích, města a stavy pro použití s předchozí příkaz. 

> [!WARNING]
> Zadejte datum po 14 listopadu 2017 pro `-StartTime` a `-EndTime`. Určením data před 14 listopadu 2017 nevrátí žádná data. 

Výstup z předchozí příkaz takto:

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

Ve výstupu vrácená hodnota **skóre** je relativní latence napříč regiony a zprostředkovatele. Skóre 1 je nejhorší (nejvyšší) latenci, zatímco 100 je nejnižší latenci. Relativní latence zprůměrovány dne. V předchozím příkladu během je zřejmé, že latence byly stejné oba dny a že je malý rozdíl mezi latence dva poskytovatelé, ho má také zřejmé, že latence pro oba poskytovatelé jsou nízkou na rozsahu 1 až 100. Když toto chování se očekává, protože státu Washington v USA je fyzicky blízko oblasti Azure 2 v západní USA, někdy nejsou k dispozici výsledky podle očekávání. Větší rozsah dat zadáte, čím průměrnou latenci v čase.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porovnání relativní síťovou latenci mezi oblastmi Azure z určitého umístění

Pokud místo zadání relativní latenci mezi konkrétní umístění a konkrétní oblasti Azure pomocí `-Location`jste chtěli určit relativní latence pro všechny oblasti Azure z určitého fyzického umístění, můžete to udělat příliš. Například následující příkaz vám pomůže vyhodnotit jakou oblast azure k nasazení služby v, pokud jsou uživatelé společnosti Comcast umístěné ve státě Washington vaši primární uživatelé:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
Na rozdíl od při určit jedno umístění, pokud nechcete zadat umístění, nebo zadejte více umístění, jako například "Západní US2", "Západní USA", musíte zadat poskytovatele služeb Internetu při spuštění příkazu. 

## <a name="view-available"></a>Zobrazení k dispozici zemích, stavy, města a zprostředkovatelů

Data jsou k dispozici pro konkrétní poskytovatelům internetových služeb, zemích, stavy a měst. Chcete-li zobrazit seznam všech dostupných poskytovatelům internetových služeb, země, stavy a města, které můžete zobrazit data, zadejte následující příkaz:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data je dostupná jenom pro jednotlivé země, stavy a města vrácený předchozí příkaz. Předchozí příkaz vyžaduje, abyste zadejte existující sledovací proces sítě. Příklad zadaný *NetworkWatcher_eastus* sledovací proces sítě ve skupině prostředků s názvem *NetworkWatcherRG*, ale můžete zadat všechny existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte ji dokončení úkolů v [vytvořit sledovací proces sítě](#create-a-network-watcher). 

Po spuštění předchozí příkaz, můžete filtrovat výstup zadáním platné hodnoty pro **země**, **stavu**, a **města**, v případě potřeby.  Například pokud chcete zobrazit seznam dostupných v Praze, Washington, poskytovatelům internetových služeb ve Spojených státech amerických, zadejte následující příkaz:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Hodnota zadaná pro **země** musí být malá a velká písmena. Hodnoty zadané pro **stavu** a **města** musí být psaný malými písmeny. Hodnoty musí být uvedené v po spuštění příkazu bez hodnot pro výstup **země**, **stavu**, a **města**. Pokud zadáte nesprávný případ, nebo zadejte hodnotu pro **země**, **stavu**, nebo **města** který se nenachází v výstup po spuštění příkazu bez hodnot pro tyto vlastnosti, vrácený výstup je prázdný.

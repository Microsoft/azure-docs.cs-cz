---
title: Zobrazit relativní latence do oblastí Azure z konkrétních míst | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit relativní latence mezi poskytovatelů internetových služeb do oblastí Azure z konkrétních míst.
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
ms.openlocfilehash: 6ac37c3a53b0cc71bdab85fb86e0e85d998867aa
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300604"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Zobrazení relativní latence do oblastí Azure z konkrétních míst

V tomto kurzu se naučíte používat Azure [Network Watcher](network-watcher-monitoring-overview.md) služby vám pomohou rozhodnout, jaké oblasti Azure k nasazení aplikace nebo služby, založené na demografických uživatelů. Kromě toho můžete použít k vyhodnocení poskytovatelů služeb připojení k Azure.  
        
## <a name="create-a-network-watcher"></a>Vytvořit network watcher

Pokud už máte sledovací proces sítě v Azure alespoň jeden [oblasti](https://azure.microsoft.com/regions), můžete přeskočit úkolů v této části. Vytvořte skupinu prostředků pro sledovací proces sítě. V tomto příkladu se vytvoří skupina prostředků v oblasti východní USA, ale můžete vytvořit skupinu prostředků v libovolné oblasti Azure.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Vytvořte network watcher. Musí mít sledovací proces sítě vytvořené v nejméně jedné oblasti Azure. V tomto příkladu se vytvoří network watcheru v oblasti Azure USA – východ.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porovnat relativní síťovou latenci z určitého umístění na jedné oblasti Azure

Služba poskytovatele vyhodnotit nebo řešení potíží s uživateli, jako je "webu bylo pomalé v sítích," ohlášení problému z určitého umístění na základě oblasti azure, ve kterém je nasazená služba. Například následující příkaz vrátí průměrnou latenci poskytovatele relativní služby Internet mezi státu Washington ve Spojených státech amerických a v oblasti západní USA 2 platformě Azure mezi 13 15. prosince 2017:

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
> Oblast, kterou jste zadali v předchozím příkazu nemusí být stejná jako oblast, kterou jste zadali při, který jste získali sledovací proces sítě. Předchozí příkaz jednoduše potřeba zadat existující sledovací proces sítě. Sledovací proces sítě může být v libovolné oblasti. Pokud zadáte hodnoty pro `-Country` a `-State`, musí být platný. Hodnoty jsou malá a velká písmena. Data jsou k dispozici omezený počet země, státy a města. Spusťte příkazy [zobrazení k dispozici země, státy, města a poskytovatelé](#view-available) zobrazíte seznam dostupných země, města a stavu pro použití s předchozím příkazem. 

> [!WARNING]
> Je nutné zadat data za posledních 30 dnů pro `-StartTime` a `-EndTime`. Určení data před způsobí žádná data se vrací.

Následuje výstup z předchozího příkazu:

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

Ve výstupu vráceného, hodnotu pro **skóre** je relativní latence mezi oblastmi a poskytovatelů. Skóre 1 je nejhorší (nejvyšší) latenci, zatímco 100 je nejnižší latenci. Relativní latence zprůměrovány daného dne. V předchozím příkladu zatímco je zřejmé, že latence byly stejné dny a, že se jedním malým rozdílem mezi latencí dva poskytovatelé, ale také to je zřejmé, že latence pro oba poskytovatelé jsou nízké v rozsahu 1-100. Když tato situace může nastat, protože státu Washington ve Spojených státech je fyzicky blízko oblast západní USA 2 platformě Azure, někdy nejsou k dispozici výsledky podle očekávání. Větší rozsah dat zadáte, tím více Průměrná latence v čase.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porovnat relativní síťovou latenci v různých oblastech Azure z určitého umístění

Pokud místo určení relativní latence mezi na určité místo a konkrétní oblasti Azure pomocí `-Location`, kdybyste chtěli určení relativní latence pro všemi oblastmi Azure z určitého fyzického umístění, můžete to udělat příliš. Například následující příkaz vám pomůže vyhodnotit jaké oblasti azure do nasazení služby v, pokud jsou vaši primární uživatelé tak uživatelům umístěným ve státě Washington:

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
Na rozdíl od při zadávání na jednom místě, pokud nechcete zadat umístění nebo zadejte několika místy, jako je například "Západní USA 2", "Západní USA", je nutné zadat poskytovatele služeb Internetu při spuštění příkazu. 

## <a name="view-available"></a>Zobrazit dostupné země, státy, města a poskytovatelé

Data jsou k dispozici pro konkrétní poskytovatelů internetových služeb, země, státy a města. Chcete-li zobrazit seznam všech dostupných poskytovatelů internetových služeb, země, státy a města, které můžete zobrazit data, zadejte následující příkaz:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data dostupná jenom pro země, státy a města vrácené předchozím příkazem. Předchozí příkaz, musíte zadat existující sledovací proces sítě. V příkladu zadaný *NetworkWatcher_eastus* sledovací proces sítě ve skupině prostředků s názvem *NetworkWatcherRG*, ale můžete zadat všechny existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte ho dokončení úkolů v [vytvořit network watcher](#create-a-network-watcher). 

Po spuštění předchozího příkazu, můžete filtruje výstup vrácený zadáním platné hodnoty pro **země**, **stavu**, a **Město**, v případě potřeby.  Například chcete-li zobrazit seznam poskytovatelů služeb Internetu v Seattlu ve Washingtonu, k dispozici ve Spojených státech amerických, zadejte následující příkaz:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Hodnota zadaná pro omezení **země** musí být malá a velká písmena. Hodnoty zadané pro **stavu** a **Město** musí obsahovat malá písmena. Hodnoty musí být uvedené ve výstupu vráceného po spuštění příkazu s žádné hodnoty pro **země**, **stavu**, a **Město**. Pokud zadáte nesprávné velikosti písmen, nebo zadejte hodnotu pro **země**, **stavu**, nebo **Město** , který není ve výstupu vráceného po spuštění příkazu s žádné hodnoty pro tyto Vlastnosti vráceného výstup je prázdný.

---
title: Zobrazení relativních latencí do oblastí Azure z konkrétních umístění | Dokumenty společnosti Microsoft
description: Zjistěte, jak zobrazit relativní latence mezi poskytovateli internetu do oblastí Azure z konkrétních umístění.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: c3a85de3a201a89d6d9500e4f4b2df9e667e3537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840532"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Zobrazení relativní latence připojení k oblastem Azure z konkrétních umístění

> [!WARNING]
> Tato funkce je v současné době ve verzi preview a stále se testuje na stabilitu.

V tomto kurzu se dozvíte, jak pomocí služby Azure [Network Watcher,](network-watcher-monitoring-overview.md) které vám pomohou rozhodnout, do jaké oblasti Azure nasadit vaši aplikaci nebo službu, na základě demografického vývoje uživatele. Kromě toho ji můžete použít k vyhodnocení připojení poskytovatelů služeb k Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Vytvoření sledovacího procesu sítě

Pokud už máte sledovací proces sítě alespoň v jedné [oblasti](https://azure.microsoft.com/regions)Azure , můžete přeskočit úkoly v této části. Vytvořte skupinu prostředků pro sledovací proces sítě. V tomto příkladu se skupina prostředků vytvoří v oblasti USA – východ, ale můžete vytvořit skupinu prostředků v libovolné oblasti Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Vytvořte sledovací proces sítě. Musíte mít sledovací proces sítě vytvořený alespoň v jedné oblasti Azure. V tomto příkladu je vytvořen sledovací proces sítě v oblasti East US Azure.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porovnání relativních latencí sítě s jednou oblastí Azure z určitého umístění

Vyhodnoťte poskytovatele služeb nebo vyřešte potíže s uživatelem, který hlásí problém, jako je například "web byl pomalý", z konkrétního umístění do oblasti azure, kde je služba nasazena. Například následující příkaz vrátí průměrnou relativní latenci poskytovatele internetových služeb mezi státem Washington ve Spojených státech a oblastí Azure v západní ČÁSTI USA 2 mezi 13-15, 2017:

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
> Oblast zadaná v předchozím příkazu nemusí být stejná jako oblast, kterou jste zadali při načítání sledovacího procesu sítě. Předchozí příkaz jednoduše vyžaduje zadání existujícího sledovacího procesu sítě. Sledovací proces sítě může být v libovolné oblasti. Pokud zadáte hodnoty `-Country` `-State`pro a , musí být platné. Hodnoty rozlišují malá a velká písmena. Data jsou k dispozici pro omezený počet zemí nebo oblastí, států a měst. Spusťte příkazy v [zobrazení dostupných zemí nebo oblastí, států, měst a poskytovatelů a](#view-available) zobrazte seznam dostupných zemí nebo oblastí, měst a států, které chcete použít s předchozím příkazem. 

> [!WARNING]
> Je nutné zadat datum za posledních `-StartTime` 30 dní pro a `-EndTime`. Zadání předchozího data bude mít za následek vrácení žádných dat.

Výstup z předchozího příkazu následuje:

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

Ve vráceném výstupu je hodnota **skóre** relativní latence napříč oblastmi a poskytovateli. Skóre 1 je nejhorší (nejvyšší) latence, zatímco 100 je nejnižší latence. Relativní latence jsou zprůměrovány pro den. V předchozím příkladu, i když je jasné, že latence byly stejné oba dny a že je malý rozdíl mezi latencí dvou poskytovatelů, je také jasné, že latence pro oba zprostředkovatele jsou nízké na škále 1-100. I když se to očekává, protože stát Washington ve Spojených státech je fyzicky blízko oblasti Azure západní USA 2, někdy výsledky nejsou podle očekávání. Čím větší je rozsah dat, tím více můžete průměrnou latenci v průběhu času.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porovnání relativních latencí sítě v oblastech Azure z konkrétního umístění

Pokud místo určení relativní latence mezi konkrétní umístění a konkrétní `-Location`oblasti Azure pomocí , jste chtěli určit relativní latence pro všechny oblasti Azure z určitého fyzického umístění, můžete to udělat taky. Následující příkaz vám například pomůže vyhodnotit, v jaké oblasti Azure se má nasadit službu, pokud jsou vašimi primárními uživateli uživatelé comcastu, kteří se nacházejí ve státě Washington:

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
> Na rozdíl od zadání jednoho umístění, pokud nezadáte umístění nebo více umístění, například "Západní US2", "Západní USA", je nutné při spuštění příkazu zadat poskytovatele služeb Internetu. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Zobrazení dostupných zemí nebo oblastí, států, měst a poskytovatelů

Data jsou k dispozici pro konkrétní poskytovatele internetových služeb, země nebo oblasti, státy a města. Chcete-li zobrazit seznam všech dostupných poskytovatelů internetových služeb, zemí nebo oblastí, států a měst, pro které můžete zobrazit data, zadejte následující příkaz:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data jsou k dispozici pouze pro země nebo oblasti, státy a města vrácená předchozím příkazem. Předchozí příkaz vyžaduje zadání existujícího sledovacího procesu sítě. Příklad zadal *NetworkWatcher_eastus* sledovacího programu sítě ve skupině prostředků s názvem *NetworkWatcherRG*, ale můžete zadat libovolný existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte jej dokončením úkolů v části [Vytvořit sledovací proces sítě](#create-a-network-watcher). 

Po spuštění předchozího příkazu můžete v případě potřeby filtrovat vrácený výstup zadáním platných hodnot pro **země**, **stát**a **město**.  Chcete-li například zobrazit seznam poskytovatelů služeb Internetu dostupných v Seattlu ve Státě Washington ve Spojených státech, zadejte následující příkaz:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Hodnota zadaná pro **Zemi** musí být velká a malá písmena. Hodnoty zadané pro **stát** a **město** musí být malá písmena. Hodnoty musí být uvedeny ve výstupu vráceném po spuštění příkazu bez hodnot **Země**, **Stát**a **Město**. Pokud zadáte nesprávný případ nebo zadáte hodnotu **země**, **státu**nebo **města,** která není ve výstupu vrácena po spuštění příkazu bez hodnot pro tyto vlastnosti, vrácený výstup je prázdný.

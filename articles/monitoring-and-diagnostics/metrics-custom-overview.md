---
title: Vlastní metriky ve službě Azure Monitor
description: Další informace o vlastní metriky v Azure Monitor a jak jsou modelovány.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 1bdf1e1f5e58ecb0939d5876e0cef349e32de517
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344742"
---
# <a name="custom-metrics-in-azure-monitor"></a>Vlastní metriky ve službě Azure Monitor

Jakmile nasadíte prostředkům a aplikacím v Azure, budete chtít začal shromažďovat telemetrii a získejte přehled o jejich výkon a stav. Azure zpřístupňuje některé metriky, out-of-the-box během nasazování prostředků. Nazývají se metriky standard nebo platformu. Tyto metriky jsou však omezeny ze své podstaty. Můžete chtít shromažďovat některé vlastní ukazatelů nebo konkrétní obchodní metriky, které poskytují podrobnější přehledy.
Tyto metriky "vlastní" mohou být shromažďovány prostřednictvím telemetrie vaší aplikace, agenta spuštěného na prostředky Azure, nebo i mimo – v systému sledování a odeslat přímo do Azure monitoru. Po publikování do Azure monitoru, můžete procházet, dotazování a výstrahy na vlastní metriky pro vaše prostředky Azure a aplikace vedle sebe standardních metrik, protože ho vygeneroval Azure.

## <a name="send-custom-metrics"></a>Odeslat vlastní metriky
Vlastní metriky je odeslat do Azure monitoru prostřednictvím různých metod.
- Instrumentace vaší aplikace pomocí Application Insights SDK a odesílání vlastních telemetrických dat do Azure monitoru 
- Nainstalovat rozšíření diagnostiky Windows na vašich [virtuálního počítače Azure](metrics-store-custom-guestos-resource-manager-vm.md), [Škálovací sady virtuálních počítačů](metrics-store-custom-guestos-resource-manager-vmss.md), [klasický virtuální počítač](metrics-store-custom-guestos-classic-vm.md), nebo [Classic Cloudovou službu](metrics-store-custom-guestos-classic-cloud-service.md)a odeslat čítače výkonu do Azure monitoru 
- Nainstalujte [InfluxDB Telegraf agenta](metrics-store-custom-linux-telegraf.md) na počítači Azure s Linuxem a metriky odeslat pomocí modulu plug-in Azure Monitor výstupu
- Odeslat vlastní metriky [přímo k REST API služby Azure Monitor](metrics-store-custom-rest-api.md) https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

Při odesílání vlastních metrik do Azure monitoru, každý datový bod (nebo hodnotu) hlášené musí obsahovat následující informace:

### <a name="authentication"></a>Authentication
K odeslání vlastních metrik do Azure monitoru entity odesílání metriku musí být platný token Azure Active Directory "Nosiče" záhlaví žádosti. K získání tokenu nosiče platný několik podporované způsoby:
1. [Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) – poskytuje identitu do samotného prostředku Azure (například virtuální počítač). MSI je určená k získání prostředků oprávnění k provádění určitých operací – například povolení prostředek generovat metriky o sobě. Prostředek (nebo jeho MSI) můžete udělit oprávnění "Monitorování metrik vydavatele" na jiný prostředek, a tím umožnit MSI generovat metriky a další zdroje.
2. [Instanční objekt služby AAD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) – scénář v tomto poli je aplikace AAD (služba) je možné přiřadit oprávnění k emitování metriky o prostředku Azure.
Azure Monitor ověřit žádost, ověří token aplikace pomocí AAD veřejných klíčů. Existující role "Monitorování metrik vydavatele" už má toto oprávnění, která je k dispozici na webu Azure Portal. Instanční objekt služby, v závislosti na tom, jaké prostředky se generování vlastních metrik, je možné přidělit role "Monitorování metrik vydavatele" v oboru požadované (předplatné, skupinu prostředků nebo konkrétní prostředek).

> [!NOTE]
> Při požadování tokenu AAD ke generování vlastních metrik zajistit je cílová skupina nebo prostředek, který žádá token pro https://monitoring.azure.com/ (nezapomeňte zahrnout koncový znak "/")

### <a name="subject"></a>Subjekt
Tato vlastnost zaznamená, u kterého ID prostředku Azure vlastní metrika se použije v hlášení pro. Tyto informace budou kódovaný v adrese URL provádí volání rozhraní API. Každé rozhraní API, můžete zadat pouze hodnoty metrik pro jeden prostředek Azure.

> [!NOTE]
> Nelze generovat vlastní metrik pro ID prostředku skupiny prostředků nebo předplatného.
>
>

### <a name="region"></a>Oblast
Tato vlastnost zaznamená jaké oblasti Azure v nasazení na prostředek, který emitujete metriky pro. Metriky, musí se emitovat do stejné Azure Monitor místní koncový bod jako oblast prostředku je nasazena v. Například vlastní metriky pro virtuální počítač jsou nasazené v oblasti západní USA se musí odeslat na WestUS místní koncový bod Azure Monitor. Informace o oblasti je také kódovaný v adrese URL volání rozhraní API.

> [!NOTE]
> Ve verzi public preview vlastní metriky je k dispozici pouze v podmnožině oblastí s Azure. Seznam podporovaných oblastí najdete v další části tohoto článku.
>
>

### <a name="timestamp"></a>Časové razítko
Každý datový bod, který je odeslán do Azure monitoru musí být označen s časovým razítkem. Toto časové razítko zaznamená datum a čas, kdy byla hodnota metriky měří/shromážděných. Azure Monitor bude přijímat data metriky s časovými razítky až na hodnotu 20 minut v minulosti a až na hodnotu 5 minut do budoucna.

### <a name="namespace"></a>Obor názvů
Obory názvů jsou způsob, jak zařadit nebo seskupit podobné metriky. Obory názvů umožňují dosáhnout izolace mezi skupinami metriky, které může pokračovat ve shromažďování různými insights nebo ukazatele výkonu. Můžete mít například obor názvů s názvem *ContosoMemoryMetrics* , který je použité sledování paměti použijte metriky, které profilu vaší aplikace a jiný obor názvů s názvem *ContosoAppTransaction* , který sleduje vše metriky o uživatelské transakce ve vaší aplikaci.

### <a name="name"></a>Název
Název metriky, které hlásí. Název je obvykle dostatečně vám pomůže identifikovat, co měří popisný. Například metriky, které měří počet bajtů paměti využívané na daný virtuální počítač může mít název metriky, jako je "Paměti využití svěřených bajtů".

### <a name="dimension-keys"></a>Klíče dimenzí
Dimenze je dvojice klíč/hodnota, usnadňující popisují další charakteristiky o metriku shromažďují. Další charakteristiky Povolit shromažďování dalších informací o metriku, která umožní hloubkové informace. Metrika "Paměti využití svěřených bajtů" může mít například dimenze klíč s názvem "Procesu", který explicitně zaznamenává počet bajtů paměti spotřebovává každý proces na virtuálním počítači. To umožňuje filtrovat metriky, které chcete zjistit, kolik paměti specifické procesy jsou, nebo k identifikaci Top 5 procesů podle využití paměti.
Jednotlivé vlastní metriky můžete mít až 10 dimenze.

### <a name="dimension-values"></a>Hodnoty dimenzí
Při vytváření sestav metrik datapoint, pro každý klíč dimenze na metriku hlásí, neexistuje odpovídající hodnotu dimenze. Například Kdybyste chtěli sestavy paměť používanou ContosoApp na vašem virtuálním počítači:

* Název metriky by měl být *bajty paměti*
* Klíč dimenze by *procesu*
* Hodnotu dimenze by *ContosoApp.exe*

Při publikování hodnota metriky, můžete zadat pouze jednu hodnotu dimenze na klíče dimenzí. Pokud shromažďujete stejné využití paměti v aplikaci pro více procesů na virtuálním počítači, může hlásit více hodnoty metrik pro tohoto časového razítka. Každá hodnota metriky zadejte jinou dimenzi hodnotu pro klíč dimenze procesu.

### <a name="metric-values"></a>Hodnoty metrik
Azure Monitor ukládá všechny metriky v intervalech každou minutu. Chápeme, že možná muset odeberou více než jednou (např metriku. Využití procesoru) nebo měří pro mnoho diskrétní událostí (např.) podepsat transakce latence) během dané minuty. Chcete-li omezit počet nezpracované hodnoty, které je nutné vygenerovat a platit za ve službě Azure Monitor, můžete místně předem agregovat a generování hodnoty:

* Min: Minimální hodnotu ze všech ukázek/měření pozorováno za minutu
* Maximální počet: Maximum pozorované hodnotu ze všech ukázek/měření za minutu
* Součet: Souhrn všech zjištěných hodnot z všechna ukázková/měření za minutu
* Počet: Počet vzorků/měření pořízených za minutu

Například, pokud byly nějaké transakce 4 přihlášení do aplikace během zadanou minutu a výsledný do měřené latence pro každou byly:

|Transakce 1|Transakce 2|Transakce 3|Transakce 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Výsledný metriky publikování do Azure monitoru by byl:
* Min: 4
* Maximální počet: 16
* Součet: 40
* Počet: 4

Pokud aplikace nedokáže předem agregovat místně a posílat každý ukázkový diskrétní nebo události ihned po kolekce je potřeba, můžete posílat hodnoty nezpracovaná měření.
Například pokaždé, když transakce přihlášení došlo k ve vaší aplikaci že metriku bude publikována do Azure monitoru s pouze jedno měření. Přihlášení transakcí, která trvala 12 ms a metriky tedy publikace by byl:
* Min: 12
* Maximální počet: 12
* Součet: 12
* Počet: 1

Tento proces můžete generovat více hodnot pro stejné metriky + kombinace dimenzí během dané minuty. Azure Monitor se pak provést všechny nezpracované hodnoty pro danou chvíli a agregovat je společně.

### <a name="sample-custom-metric-publication"></a>Ukázka vlastních metrik publikace
V následujícím příkladu vytvoříte vlastní metriku s názvem "Paměti využití svěřených bajtů", v části metriky oboru názvů "Paměti profilu" pro virtuální počítač. Metrika má jedinou dimenzí nazývá "Proces". Pro dané časové razítko jsme se generování hodnoty metrik pro dva různé procesy:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]
> Application Insights, rozšíření Windows Azure Diagnostics a agent InfluxData Telegraf už nakonfigurované pro generování hodnoty metrik na správné místní koncový bod a budou mít všechny výše uvedené vlastnosti v každé emisí.
>
>

## <a name="custom-metric-definitions"></a>Vlastní definice metrik
Není nutné předem definovat vlastní metriky ve službě Azure Monitor, než je vygenerován. Protože metriky datových bodů publikování obsahuje obor názvů, název a informace o dimenzích, při prvním vlastní metrika je vygenerován pro Azure Monitor definice metriky se automaticky vytvoří. Tato definice metriky je pak zjistitelné na prostředky, které metrika generovalo proti prostřednictvím definice metrik.

> [!NOTE]
> Azure Monitor zatím nepodporuje, definování "Jednotky" vlastní metriky.

## <a name="using-custom-metrics"></a>Pomocí vlastních metrik
Po vlastní metriky se odešlou do Azure monitoru procházet přes Azure portal, dotazovat přes rozhraní REST API služby Azure Monitor nebo vytvořit oznámení na nich, můžete být upozorněni při splnění určitých podmínek.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procházet vaše vlastní metriky přes Azure portal
1.  Přejděte na web [Azure Portal](https://portal.azure.com).
2.  Vyberte v okně Monitor
3.  Klikněte na metriky
4.  Vyberte, které se mají generované vlastních metrik pro prostředek
5.  Vyberte obor názvů metriky pro vaše vlastní metrika
6.  Vyberte vlastní metrika

## <a name="supported-regions"></a>Podporované oblasti
Ve verzi public preview možnost publikovat vlastní metriky je k dispozici pouze v podmnožině oblastí Azure. To znamená, že se metriky lze publikovat pouze pro prostředky v jednom z podporovaných oblastí. Následující tabulka uvádí sadu podporovaných oblastech Azure pro vlastní metriky a odpovídající koncový bod, který by se měly zveřejňovat metriky pro prostředky v těchto oblastech k.

|Oblast Azure|Předpona místní koncový bod|
|---|---|
|USA – východ|https://eastus.monitoring.azure.com/|
|Střed USA – jih|https://southcentralus.monitoring.azure.com/|
|Západní střed USA|https://westcentralus.monitoring.azure.com/|
|USA – západ 2|https://westus2.monitoring.azure.com/|
|Jihovýchodní Asie|https://southeastasia.monitoring.azure.com/|
|Severní Evropa|https://northeurope.monitoring.azure.com/|
|Západní Evropa|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Kvóty a omezení
Azure Monitor má následující omezení použití u vlastních metrik.

|Kategorie|Omezení|
|---|---|
|Aktivní čas řady nebo předplatná/oblast|50,000|
|Klíče dimenzí na metriku|10|
|Délka řetězce pro metriky obory názvů, názvy metrik, klíče dimenzí a hodnoty dimenzí|256 znaků.|
Aktivní časové řady se definuje jako jakékoli jedinečnou kombinací metrik, klíče dimenzí, hodnotu dimenze, pro který byla hodnoty metrik, které jsou zveřejněné v posledních 12 hodin.

## <a name="next-steps"></a>Další postup
Použít vlastní metriky z různých služeb 
 - [Virtuální počítač](metrics-store-custom-guestos-resource-manager-vm.md)
 - [Virtuální počítač Škálovací sady](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [Virtuální počítač (klasický)](metrics-store-custom-guestos-classic-vm.md)
 - [Virtuální počítač s Linuxem pomocí Telegraf agenta](metrics-store-custom-linux-telegraf.md)
 - [REST API](metrics-store-custom-rest-api.md)
 - [Cloudová služba (klasická)](metrics-store-custom-guestos-classic-cloud-service.md)
 
---
title: Vlastní metriky ve Službě Azure Monitor
description: Přečtěte si o vlastnímetriky v Azure Monitoru a jak jsou modelovány.
author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 099ab150cde763551c2ad10a4e9159909ccff4dd
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270702"
---
# <a name="custom-metrics-in-azure-monitor"></a>Vlastní metriky ve Službě Azure Monitor

Při nasazování prostředků a aplikací v Azure budete chtít začít shromažďovat telemetrii, abyste získali přehled o jejich výkonu a stavu. Azure vám zpřístupní některé metriky ipo za balení. Tyto metriky se nazývají [standard nebo platforma](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported). Nicméně, oni jsou omezené povahy. Můžete chtít shromáždit některé vlastní ukazatele výkonu nebo metriky specifické pro podnikání, abyste poskytli hlubší přehledy.
Tyto **vlastní** metriky lze shromažďovat prostřednictvím telemetrie aplikace, agenta, který běží na vašich prostředcích Azure, nebo dokonce externího monitorovacího systému a odeslány přímo do Azure Monitoru. Po publikování na Azure Monitor, můžete procházet, dotazovat a výstrahy na vlastní metriky pro vaše prostředky Azure a aplikace vedle standardní metriky vyzařované Azure.

## <a name="methods-to-send-custom-metrics"></a>Metody odesílání vlastních metrik

Vlastní metriky lze odeslat do Azure Monitoru pomocí několika metod:
- Instrumentujte svou aplikaci pomocí sady Azure Application Insights SDK a odesílejte vlastní telemetrii do Azure Monitoru. 
- Nainstalujte si rozšíření Diagnostika Windows Azure (WAD) na [virtuální počítač Azure](collect-custom-metrics-guestos-resource-manager-vm.md), [škálovací sadu virtuálních počítačů](collect-custom-metrics-guestos-resource-manager-vmss.md), [klasický virtuální počítač](collect-custom-metrics-guestos-vm-classic.md)nebo [klasické cloudové služby](collect-custom-metrics-guestos-vm-cloud-service-classic.md) a odešlete čítače výkonu do Azure Monitoru. 
- Nainstalujte [agenta InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) do virtuálního počítače Azure Linux a odesílejte metriky pomocí výstupního modulu plug-in Azure Monitor.
- Odešlete vlastní metriky [přímo do rozhraní AZURE Monitor REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md). `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`

## <a name="pricing-model"></a>Cenový model

Neexistuje žádné náklady na ingestování standardní metriky (metriky platformy) do úložiště metrik Azure Monitoru. Vlastní metriky ingestované do úložiště metrik Azure Monitoru se budou účtovat za MByte s každou vlastní metrickou datovou pointou zapsanou za velikost 8 bajtů. Všechny požité metriky jsou zachovány po dobu 90 dnů.

Metriky dotazy budou účtovány na základě počtu volání standardní rozhraní API. Standardní volání rozhraní API je volání, které analyzuje 1 440 datových bodů (1 440 je také celkový počet datových bodů, které lze uložit na metriku za den). Pokud volání rozhraní API analyzuje více než 1 440 datových bodů, pak se bude počítat jako více standardních volání rozhraní API. Pokud volání rozhraní API analyzuje méně než 1 440 datových bodů, bude se počítat jako méně než jedno volání rozhraní API. Počet standardních volání rozhraní API se vypočítá každý den jako celkový počet datových bodů analyzovaných za den vydělený 1 440.

Konkrétní podrobnosti o ceně pro vlastní metriky a dotazy na metriky jsou k dispozici na [stránce s cenami Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/).

> [!NOTE]  
> Metriky odeslané do Azure Monitoru prostřednictvím sady Application Insights SDK se budou účtovat jako data protokolu a účtují se vám další poplatky za metriky pouze v případě, že byla vybrána funkce Přehledy aplikací [Povolit výstrahy na vlastní dimenze metriky.](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) Přečtěte si další informace o [cenovém modelu Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model) a [cenách ve vaší oblasti](https://azure.microsoft.com/pricing/details/monitor/).

> [!NOTE]  
> Podrobnosti o tom, kdy bude fakturace povolena pro vlastní metriky a dotazy na metriky, najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) Azure Monitoru. 

## <a name="how-to-send-custom-metrics"></a>Jak odesílat vlastní metriky

Když odesíláte vlastní metriky do Azure Monitoru, každý datový bod nebo hodnota, hlášené musí obsahovat následující informace.

### <a name="authentication"></a>Authentication
Chcete-li odeslat vlastní metriky do Azure Monitoru, entita, která odešle metriku, potřebuje platný token Azure Active Directory (Azure AD) v hlavičce **Doručitele** požadavku. Existuje několik podporovaných způsobů, jak získat platný žeton nosiče:
1. [Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Poskytuje identitu samotnému prostředku Azure, jako je například virtuální počítač. Identita spravované služby (MSI) je navržena tak, aby udělují oprávnění k provádění určitých operací prostředků. Příkladem je povolení prostředku vyzařovat metriky o sobě. Prostředek nebo jeho MSI, může být udělena **oprávnění vydavatele metriky monitorování** na jiný prostředek. S tímto oprávněním může MSI vyzařovat metriky i pro jiné prostředky.
2. [Azure AD Service Inismu](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). V tomto scénáři aplikace Azure AD nebo služby lze přiřadit oprávnění k emitování metriko v yomovaném prostředku Azure.
K ověření požadavku Azure Monitor ověří token aplikace pomocí veřejných klíčů Azure AD. Existující role **vydavatele metrik monitorování** již toto oprávnění má. Je k dispozici na webu Azure Portal. Instanční objekt, v závislosti na tom, jaké prostředky vydává vlastní metriky pro, může být uveden role **vydavatele metrikmonitorování metriky** v požadovaném oboru. Příklady jsou předplatné, skupina prostředků nebo konkrétní prostředek.

> [!TIP]  
> Když požádáte o token Azure AD k vyzařují vlastní metriky, ujistěte se, že cílovou skupinu nebo prostředek token je `https://monitoring.azure.com/`požadováno . Nezapomeňte uvést koncové '/'.

### <a name="subject"></a>Subjekt
Tato vlastnost zachycuje, které ID prostředku Azure vlastní metrika je hlášena pro. Tyto informace budou zakódovány v adrese URL volání rozhraní API. Každé rozhraní API může odesílat jenom hodnoty metrik pro jeden prostředek Azure.

> [!NOTE]  
> Vlastní metriky nelze vyzařovat podle ID prostředku skupiny prostředků nebo předplatného.


### <a name="region"></a>Region (Oblast)
Tato vlastnost zachycuje, v jaké oblasti Azure se nasadí prostředek, pro který vyzařujete metriky. Metriky musí být vyzařovány do stejného koncového bodu Azure Monitor jako oblast, ve které se prostředek nasazuje. Například vlastní metriky pro virtuální počítač nasazený v západní USA musí být odeslány do koncového bodu WestUS regional Azure Monitor. Informace o oblasti jsou také zakódovány v adrese URL volání rozhraní API.

> [!NOTE]  
> Během veřejné verze Preview jsou vlastní metriky dostupné jenom v podmnožině oblastí Azure. Seznam podporovaných oblastí je popsán v další části tohoto článku.
>
>

### <a name="timestamp"></a>Časové razítko
Každý datový bod odeslaný do Azure Monitoru musí být označen časovým razítkem. Toto časové razítko zachycuje DateTime, ve kterém je měřena nebo shromažďována hodnota metriky. Azure Monitor přijímá metrická data s časovými razítky až do 20 minut v minulosti a 5 minut v budoucnosti. Časové razítko musí být ve formátu ISO 8601.

### <a name="namespace"></a>Obor názvů
Obory názvů jsou způsob, jak kategorizovat nebo seskupit podobné metriky dohromady. Pomocí oborů názvů můžete dosáhnout izolace mezi skupinami metrik, které mohou shromažďovat různé přehledy nebo ukazatele výkonu. Můžete mít například obor názvů nazývaný **contosomemorymetrics,** který sleduje metriky využití paměti, které profilují vaši aplikaci. Jiný obor názvů nazývaný **contosoapptransaction** může sledovat všechny metriky o transakcích uživatelů ve vaší aplikaci.

### <a name="name"></a>Name (Název)
**Název** je název metriky, která se hlásí. Název je obvykle dostatečně popisný, aby pomohl určit, co se měří. Příkladem je metrika, která měří počet bajtů paměti používaných na daném virtuálním počítači. Může mít název metriky, jako jsou **paměťové bajty v provozu**.

### <a name="dimension-keys"></a>Klíče dimenze
Dimenze je pár klíčů nebo hodnot, který pomáhá popsat další charakteristiky o shromažďované metriky. Pomocí dalších charakteristik můžete shromažďovat další informace o metrike, která umožňuje hlubší přehledy. Například metrika Počet **využitých paměti** může mít klíč dimenze s názvem **Proces,** který zachycuje, kolik bajtů paměti každý proces na virtuálním počítači spotřebovává. Pomocí tohoto klíče můžete filtrovat metriku a zjistit, kolik procesů specifických pro paměť používá, nebo identifikovat prvních pět procesů podle využití paměti.
Dimenze jsou volitelné, ne všechny metriky mohou mít dimenze. Vlastní metrika může mít až 10 dimenzí.

### <a name="dimension-values"></a>Hodnoty dimenzí
Při vykazování datového bodu metriky je pro každý klíč dimenze v ynometrické metrikě odpovídající hodnota dimenze. Můžete například chtít nahlásit paměť používanou contosoapp na vašem virtuálním počítači:

* Název metriky by **paměti bajty v použití**.
* Klíč dimenze **by**proces .
* Hodnota dimenze by **contosoApp.exe**.

Při publikování hodnoty metriky můžete zadat pouze jednu hodnotu dimenze na klíč dimenze. Pokud shromažďujete stejné využití paměti pro více procesů na virtuálním počítači, můžete pro toto časové razítko nahlásit více hodnot metrik. Každá hodnota metriky by určila jinou hodnotu dimenze pro klíč dimenze **Proces.**
Dimenze jsou volitelné, ne všechny metriky mohou mít dimenze. Pokud metrický příspěvek definuje klíče dimenzí, odpovídající hodnoty dimenzí jsou povinné.

### <a name="metric-values"></a>Hodnoty metriky
Azure Monitor ukládá všechny metriky v intervalech rozlišovací schopnosti jednu minutu. Chápeme, že během dané minuty může být nutné několikrát vzorkovat metriku. Příkladem je využití procesoru. Nebo to může být nutné měřit pro mnoho diskrétních událostí. Příkladem je latence transakcí přihlášení. Chcete-li omezit počet nezpracovaných hodnot, které musíte vyzařovat a platit v Azure Monitoru, můžete místně předem agregovat a vyzařovat hodnoty:

* **Min**: Minimální pozorovaná hodnota ze všech vzorků a měření během minuty.
* **Max**: Maximální pozorovaná hodnota ze všech vzorků a měření během minuty.
* **Součet**: Součet všech pozorovaných hodnot ze všech vzorků a měření během minuty.
* **Počet**: Počet vzorků a měření odebraných během minuty.

Pokud například během dané minuty došlo ke čtyřem transakcím přihlášení do aplikace, může být výsledná naměřená čekací doba pro každou z nich následující:

|Transakce 1|Transakce 2|Transakce 3|Transakce 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Výsledná metrika publikace azure monitoru by pak být následující:
* Min.: 4.
* Max: 16
* Součet: 40
* Počet: 4

Pokud aplikace není schopna předem agregovat místně a potřebuje vyzařovat každý diskrétní vzorek nebo událost ihned po sběru, můžete vyzařovat hodnoty míry raw. Například pokaždé, když dojde k přihlašovací transakci ve vaší aplikaci, publikujete metriku do Azure Monitoru s jenom jedním měřením. Takže pro přihlašovací transakce, která trvala 12 ms, metrika publikace by být následující:
* Min. 12.
* Max: 12
* Součet: 12
* Počet: 1.

Pomocí tohoto procesu můžete během dané minuty vyzařovat více hodnot pro stejnou kombinaci metriky plus dimenze. Azure Monitor pak převezme všechny nezpracované hodnoty vyzařované pro danou minutu a agreguje je společně.

### <a name="sample-custom-metric-publication"></a>Ukázka vlastní metrické publikace
V následujícím příkladu vytvoříte vlastní metriku nazvanou **Paměti bajty v použití** v rámci profilu paměti oboru názvů **metriky** pro virtuální počítač. Metrika má jednu dimenzi nazvanou **Proces**. Pro dané časové razítko vyzařujeme metrické hodnoty pro dva různé procesy:

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
> Application Insights, rozšíření diagnostiky a agent AVTA Telegraf jsou již nakonfigurovány tak, aby vyzařovaly hodnoty metrik proti správnému místnímu koncovému bodu a nesly všechny předchozí vlastnosti v každé zem.
>
>

## <a name="custom-metric-definitions"></a>Vlastní definice metrik
Není třeba předdefinovat vlastní metriku ve Službě Azure Monitor před jeho emitem. Každý publikovaný datový bod metriky obsahuje obor názvů, název a informace o dimenzi. Takže při prvním vyzařováno vlastní metriky na Azure Monitor, definice metriky se automaticky vytvoří. Tato definice metriky je pak zjistitelné na všech prostředků metrika je vydáván prostřednictvím definice metriky.

> [!NOTE]  
> Azure Monitor ještě nepodporuje definování **jednotek** pro vlastní metriku.

## <a name="using-custom-metrics"></a>Použití vlastních metrik
Po odeslání vlastních metrik do Azure Monitoru je můžete procházet přes portál Azure a dotazovat se na ně prostřednictvím api azure monitoru REST. Můžete také vytvořit upozornění na ně upozornit, když jsou splněny určité podmínky.

> [!NOTE]
> Chcete-li zobrazit vlastní metriky, musíte být roli čtenáře nebo přispěvatele.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procházení vlastních metrik prostřednictvím portálu Azure
1.    Přejděte na [portál Azure](https://portal.azure.com).
2.    Vyberte podokno **Sledování.**
3.    Vyberte **Metriky**.
4.    Vyberte prostředek, který jste vypouštěli vlastní metriky proti.
5.    Vyberte obor názvů metrik pro vlastní metriku.
6.    Vyberte vlastní metriku.

## <a name="supported-regions"></a>Podporované oblasti
Během veřejné verze Preview je možnost publikovat vlastní metriky dostupná jenom v podmnožině oblastí Azure. Toto omezení znamená, že metriky lze publikovat pouze pro prostředky v jedné z podporovaných oblastí. V následující tabulce je uvedena sada podporovaných oblastí Azure pro vlastní metriky. Uvádí také odpovídající koncové body, které metriky pro zdroje v těchto oblastech by měly být zveřejněny na:

|Oblast Azure |Předpona místního koncového bodu|
|---|---|
| **USA a Kanada** | |
|USA – středozápad | https:\//westcentralus.monitoring.azure.com/ |
|USA – západ 2       | https:\//westus2.monitoring.azure.com/ |
|USA – středosever | https:\//northcentralus.monitoring.azure.com
|USA – středojih| https:\//southcentralus.monitoring.azure.com/ |
|USA – střed      | https:\//centralus.monitoring.azure.com |
|Střední Kanada | https:\//canadacentral.monitoring.azure.comc
|USA – východ| https:\//eastus.monitoring.azure.com/ |
| **Evropa** | |
|Severní Evropa    | https:\//northeurope.monitoring.azure.com/ |
|Západní Evropa     | https:\//westeurope.monitoring.azure.com/ |
|Spojené království – jih | https:\//uksouth.monitoring.azure.com
|Francie – střed | https:\//francecentral.monitoring.azure.com |
| **Afrika** | |
|Jižní Afrika – sever | https:\//southafricanorth.monitoring.azure.com
| **Asie** | |
|Indie – střed | https:\//centralindia.monitoring.azure.com
|Austrálie – východ | https:\//australiaeast.monitoring.azure.com
|Japonsko – východ | https:\//japaneast.monitoring.azure.com
|Jihovýchodní Asie  | https:\//southeastasia.monitoring.azure.com |
|Východní Asie | https:\//eastasia.monitoring.azure.com
|Jižní Korea – střed   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Kvóty a omezení
Azure Monitor ukládá následující omezení využití na vlastní metriky:

|Kategorie|Omezení|
|---|---|
|Aktivní časové řady/odběry/oblast|50 000|
|Klíče dimenze na metriku|10|
|Délka řetězce pro obory názvů metrik, názvy metrik, klíče dimenzí a hodnoty dimenzí|256 znaků|

Aktivní časové řady jsou definovány jako libovolná jedinečná kombinace metriky, klíče dimenze nebo hodnoty dimenze, u které byly v posledních 12 hodinách publikovány hodnoty metriky.

## <a name="next-steps"></a>Další kroky
Používejte vlastní metriky z různých služeb: 
 - [Virtuální počítače](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Škálovací sada virtuálních počítačů](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Virtuální počítače Azure (klasické)](collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuální stroj Linux pomocí agenta Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klasické cloudové služby](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
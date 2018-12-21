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
ms.openlocfilehash: 2b3e8877713fde088be2147bbfa8969e351a3f06
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326028"
---
# <a name="custom-metrics-in-azure-monitor"></a>Vlastní metriky ve službě Azure Monitor

Jakmile nasadíte prostředkům a aplikacím v Azure, budete chtít začal shromažďovat telemetrii a získejte přehled o jejich výkon a stav. Azure zpřístupňuje některé metriky pro vás připravené. Tyto metriky se nazývají standard nebo platformu. Nicméně jste omezeni ze své podstaty. Můžete chtít shromažďovat některé vlastní ukazatelů nebo konkrétní obchodní metriky, které poskytují podrobnější přehledy.
Tyto **vlastní** metriky se můžou shromažďovat prostřednictvím telemetrie vaší aplikace, agenta, který běží na vašich prostředků Azure, nebo dokonce i externí monitorovací systém a odeslat přímo do Azure monitoru. Po se publikují do Azure monitoru, můžete procházet dotaz a upozornit na vlastní metriky pro vaše prostředky Azure a aplikace vedle standardních metrik, protože ho vygeneroval Azure.

## <a name="send-custom-metrics"></a>Odeslat vlastní metriky
Vlastní metriky můžete odeslaná do Azure monitoru pomocí několika metod:
- Instrumentovat aplikaci pomocí Azure Application Insights SDK a odesílání vlastních telemetrických dat do Azure monitoru. 
- Nainstalovat rozšíření Windows Azure Diagnostics (WAD) na vaši [virtuálního počítače Azure](collect-custom-metrics-guestos-resource-manager-vm.md), [škálovací sadu virtuálních počítačů](collect-custom-metrics-guestos-resource-manager-vmss.md), [klasický virtuální počítač](collect-custom-metrics-guestos-vm-classic.md), nebo [classic Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) a odeslat do Azure monitoru čítače výkonu. 
- Nainstalujte [InfluxData Telegraf agenta](collect-custom-metrics-linux-telegraf.md) na počítači Azure s Linuxem a metriky odeslat pomocí Azure monitoru výstupu modulu plug-in.
- Odeslat vlastní metriky [přímo k REST API služby Azure Monitor](../../monitoring-and-diagnostics/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Při odesílání vlastních metrik Azure monitoru, každý datový bod, nebo hodnotu hlášenou musí obsahovat následující informace.

### <a name="authentication"></a>Authentication
K odeslání vlastních metrik do Azure monitoru, entity, která odešle metriku potřebuje platný token Azure Active Directory (Azure AD) v **nosiče** záhlaví požadavku. K získání tokenu nosiče platný několik podporované způsoby:
1. [Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Poskytuje identitu, která k prostředku Azure, jako je například virtuální počítač. Identita spravované služby (MSI) slouží k udělení oprávnění k provádění určitých operací prostředky. Příklad povoluje prostředek generovat metriky o sobě. Prostředek nebo jeho MSI lze udělit **monitorování metrik vydavatele** oprávnění na jiný prostředek. S tímto oprávněním může vysílat MSI metriky pro i další prostředky.
2. [Instanční objekt Azure AD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). V tomto scénáři, aplikaci Azure AD nebo služby je možné přiřadit oprávnění k emitování metriky o prostředku Azure.
Azure Monitor ověřit žádost, ověří token aplikace pomocí veřejných klíčů služby Azure AD. Existující **monitorování metrik vydavatele** role už má toto oprávnění. Je dostupná na webu Azure Portal. Instanční objekt služby, v závislosti na tom, jaké prostředky vydává vlastní metriky, mohou být zadány **monitorování metrik vydavatele** role v oboru požadované. Příklady jsou předplatné, skupinu prostředků nebo konkrétní prostředek.

> [!NOTE]  
> Pokud budete požadovat token Azure AD a vygenerovat vlastní metriky, ujistěte se, že cílové skupiny nebo token, který je požadovaný pro prostředek https://monitoring.azure.com/. Nezapomeňte zahrnout koncový znak "/".

### <a name="subject"></a>Subjekt
Tato vlastnost zaznamená, u kterého ID prostředku Azure vlastní metrika se použije v hlášení pro. Tyto informace budou kódovaný v adrese URL provádí volání rozhraní API. Každé rozhraní API, můžete zadat pouze hodnoty metrik pro jeden prostředek Azure.

> [!NOTE]  
> Nelze generovat vlastní metrik pro ID prostředku skupiny prostředků nebo předplatného.
>
>

### <a name="region"></a>Oblast
Tato vlastnost zaznamená jaké oblasti Azure, které jste generování metriky pro prostředek je nasazena v. Metriky, musí se emitovat do stejné Azure Monitor místní koncový bod jako oblast prostředku je nasazena v. Například vlastní metriky pro virtuální počítač nasazený v oblasti západní USA se musí odeslat na WestUS místní koncový bod Azure Monitor. Informace o oblasti je také kódovaný v adrese URL volání rozhraní API.

> [!NOTE]  
> Ve verzi public preview vlastní metriky jsou k dispozici pouze v podmnožině oblastí Azure. Seznam podporovaných oblastí najdete v další části tohoto článku.
>
>

### <a name="timestamp"></a>Timestamp
Každý datový bod, který je odeslán do Azure monitoru musí být označen s časovým razítkem. Toto časové razítko zaznamená datum a čas, kdy je hodnota metriky měří nebo shromažďovat. Azure Monitor přijímá data metriky s časovými razítky až na hodnotu 20 minut v minulosti a v budoucnu 5 minut.

### <a name="namespace"></a>Obor názvů
Obory názvů jsou způsob, jak zařadit nebo seskupit podobné metriky. Použití oboru názvů, můžete dosáhnout izolace mezi skupinami metriky, které by mohl shromažďovat různé přehledy nebo ukazatele výkonu. Například můžete mít obor názvů s názvem **ContosoMemoryMetrics** , který sleduje metriky využití paměti, která profilu vaší aplikace. Jiný obor názvů s názvem **ContosoAppTransaction** může sledovat všechny metriky o uživatelské transakce ve vaší aplikaci.

### <a name="name"></a>Název
**Název** je název metriky, které hlásí. Je obvykle dostatečně vám pomůže identifikovat, co se měří popisný název. Příkladem je metriku, která měří počet bajtů paměti na daný virtuální počítač. Může mít název metriky, jako je **využití svěřených bajtů paměti**.

### <a name="dimension-keys"></a>Klíče dimenzí
Dimenze je dvojice klíč nebo hodnota, která pomáhá popisují další charakteristiky o metriku shromažďují. Další vlastnosti můžete shromažďovat další informace o metriku, která umožní hloubkové informace. Například **využití svěřených bajtů paměti** metrika pravděpodobně dimenze klíč s názvem **procesu** , která zaznamenává počet bajtů paměti spotřebuje každý proces na virtuálním počítači. Pomocí tohoto klíče můžete filtrovat metriky, které chcete zobrazit množství paměti, použít konkrétní procesy nebo k identifikaci důležitých 5 procesů podle využití paměti.
Jednotlivé vlastní metriky můžete mít až 10 dimenze.

### <a name="dimension-values"></a>Hodnoty dimenzí
Při vytváření sestav metrik datový bod, pro každý klíč dimenze na metriku hlásí, neexistuje odpovídající hodnotu dimenze. Můžete například chtít sestavu paměť používanou ContosoApp na vašem virtuálním počítači:

* Název metriky by měl být **bajty paměti**.
* Klíč dimenze by **procesu**.
* Hodnotu dimenze by **ContosoApp.exe**.

Při publikování hodnota metriky, můžete zadat pouze jednu hodnotu dimenze na klíče dimenzí. Pokud shromažďujete stejné využití paměti v aplikaci pro více procesů na virtuálním počítači, může hlásit více hodnoty metrik pro tohoto časového razítka. Každá hodnota metriky byste zadat hodnotu různé dimenze **procesu** klíče dimenzí.

### <a name="metric-values"></a>Hodnoty metrik
Azure Monitor ukládá všechny metriky v intervalech každou minutu. Chápeme, že během dané minuty metriky možná muset být prováděno vzorkování několikrát. Příkladem je využití procesoru. Nebo může být nutné měřit diskrétní událostem. Příkladem je latence přihlášení transakce. Chcete-li omezit počet nezpracované hodnoty, které je nutné vygenerovat a platit za ve službě Azure Monitor, můžete místně předem agregovat a generování hodnoty:

* **Min**: Minimální zjištěnou hodnotu ze všech ukázek a měření za minutu.
* **Maximální počet**: Maximum pozorované z ukázky a měření za minutu.
* **Součet**: Souhrn všech zjištěných hodnot z ukázky a měření za minutu.
* **Počet**: Počet vzorků a měření pořízených za minutu.

Například, pokud byly nějaké transakce 4 přihlášení do aplikace během zadaný za minutu, výsledná měří latence pro každý může být následujícím způsobem:

|Transakce 1|Transakce 2|Transakce 3|Transakce 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Potom výsledný metriky publikování do Azure monitoru by měl vypadat takto:
* Min: 4
* Max: 16
* Součet: 40
* Počet: 4

Pokud aplikace nedokáže předem agregovat místně a posílat každý ukázkový diskrétní nebo události ihned po kolekce je potřeba, můžete posílat hodnoty nezpracovaná měření. Například pokaždé, když transakce přihlašování dojde k ve vaší aplikaci publikovat metriky do Azure monitoru s pouze jedno měření. Takže přihlášení transakcí, která trvala 12 ms, metriky publikace by měl vypadat takto:
* Min: 12
* Max: 12
* Součet: 12
* Počet: 1

S tímto procesem můžete generovat více hodnot pro stejné metriky a kombinace dimenzí během dané minuty. Azure Monitor pak přijímá všechny nezpracované hodnoty pro danou chvíli a agreguje je společně.

### <a name="sample-custom-metric-publication"></a>Ukázka vlastních metrik publikace
V následujícím příkladu vytvoříte vlastní metriku s názvem **bajty paměti** v rámci oboru názvů metriky **paměti profilu** pro virtuální počítač. Metrika má jedinou dimenzí volá **procesu**. Pro dané časové razítko jsme generování hodnoty metrik pro dva různé procesy:

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
> Application Insights, diagnostické rozšíření a agent InfluxData Telegraf jsou již nakonfigurována pro generování hodnoty metrik na správné místní koncový bod a budou mít všechny předchozí vlastnosti v každé emisí.
>
>

## <a name="custom-metric-definitions"></a>Vlastní definice metrik
Není nutné pro předdefinování vlastních metrik ve službě Azure Monitor, než je vygenerován. Metriky datových bodů publikování obsahuje obor názvů, název a informace o dimenzích. Proto první vlastní metrika je vygenerován pro monitorování Azure, automaticky se vytvoří definice metriky. Tato definice metriky je pak zjistitelné na prostředky, které metrika je vygenerován proti prostřednictvím definice metrik.

> [!NOTE]  
> Azure Monitor zatím nepodporuje definování **jednotky** vlastní metriky.

## <a name="using-custom-metrics"></a>Pomocí vlastních metrik
Po vlastní metriky se odešlou do Azure monitoru, můžete procházet přes Azure portal a jejich dotazování přes rozhraní REST API služby Azure Monitor. Můžete také vytvořit oznámení, aby vás upozorní, pokud jsou splněny určité podmínky.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procházet vaše vlastní metriky přes Azure portal
1.  Přejděte na [Azure Portal](https://portal.azure.com).
2.  Vyberte **monitorování** podokně.
3.  Vyberte **Metriky**.
4.  Vyberte, kterou jste generované vlastních metrik pro prostředek.
5.  Vyberte obor názvů metriky pro vaše vlastní metriky.
6.  Vyberte vlastní metriky.

## <a name="supported-regions"></a>Podporované oblasti
Ve verzi public preview je k dispozici pouze v podmnožině oblastí s Azure možnost publikovat vlastní metriky. Toto omezení znamená, že metriky lze publikovat pouze pro prostředky v jednom z podporovaných oblastí. V následující tabulce jsou uvedeny sadu podporovaných oblastech Azure pro vlastní metriky. Také uvádí odpovídající koncové body, které by měl být publikovány metriky pro prostředky v těchto oblastech:

|Oblast Azure|Předpona místní koncový bod|
|---|---|
|USA – východ|https://eastus.monitoring.azure.com/|
|Střed USA – jih|https://southcentralus.monitoring.azure.com/|
|Západní střed USA|https://westcentralus.monitoring.azure.com/|
|Západní USA 2|https://westus2.monitoring.azure.com/|
|Jihovýchodní Asie|https://southeastasia.monitoring.azure.com/|
|Severní Evropa|https://northeurope.monitoring.azure.com/|
|Západní Evropa|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Kvóty a omezení
Azure Monitor má následující omezení použití u vlastních metrik:

|Kategorie|Omezení|
|---|---|
|Aktivní čas řady nebo předplatné a oblast|50,000|
|Klíče dimenzí na metriku|10|
|Délka řetězce pro metriky obory názvů, názvy metrik, klíče dimenzí a hodnoty dimenzí|256 znaků.|
Aktivní časové řady se definuje jako jakékoli jedinečná kombinace metrika, klíč dimenze nebo dimenze hodnotu, která byla publikována za posledních 12 hodin hodnoty metrik.

## <a name="next-steps"></a>Další postup
Použijte vlastní metriky z různých služeb: 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Škálovací sada virtuálních počítačů](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Virtuální počítače Azure (klasické)](collect-custom-metrics-guestos-vm-classic.md)
 - [Použití Telegraf agenta virtuálního počítače s Linuxem](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../monitoring-and-diagnostics/metrics-store-custom-rest-api.md)
 - [Klasické cloudové služby](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
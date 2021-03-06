---
title: Vlastní metriky v Azure Monitor (Preview)
description: Seznamte se s vlastními metrikami v Azure Monitor a způsobu jejich modelování.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: bd7f19df5eed87f2fb02af4b5f2577340bcbfd60
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812097"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Vlastní metriky v Azure Monitor (Preview)

Při nasazení prostředků a aplikací v Azure budete chtít začít shromažďovat telemetrii, abyste získali přehled o jejich výkonu a stavu. Azure zpřístupňuje některé metriky, které jsou dostupné v poli. Tyto metriky se nazývají [Standard nebo Platform](./metrics-supported.md). Jsou však omezené. 

Je možné, že budete chtít shromáždit některé vlastní ukazatele výkonu nebo metriky specifické pro firmy, které poskytují hlubší přehledy. Tyto **vlastní** metriky je možné shromažďovat prostřednictvím telemetrie aplikací, agenta, který běží na vašich prostředcích Azure, nebo i mimo monitorovací systém a přímo odeslat Azure monitor. Po publikování do Azure Monitor můžete procházet, dotazovat a upozorňovat na vlastní metriky pro vaše prostředky a aplikace Azure vedle sebe se standardními metrikami, které vysílá Azure.

Azure Monitor vlastní metriky jsou aktuální ve verzi Public Preview. 

## <a name="methods-to-send-custom-metrics"></a>Metody pro posílání vlastních metrik

Vlastní metriky je možné odesílat Azure Monitor prostřednictvím několika metod:
- Instrumentujte svoji aplikaci pomocí sady Azure Application Insights SDK a odešlete vlastní telemetrii do Azure Monitor. 
- Nainstalujte agenta Azure Monitor (Preview) na [virtuální počítač se systémem Windows nebo Linux](../agents/azure-monitor-agent-overview.md) a pomocí [pravidla shromažďování dat](../agents/data-collection-rule-azure-monitor-agent.md) odešlete čítače výkonu pro Azure monitor metrik.
- Nainstalujte rozšíření Windows Azure Diagnostics (WAD) na virtuální počítač [Azure](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md), [sadu škálování virtuálního počítače](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), [klasický virtuální](../essentials/collect-custom-metrics-guestos-vm-classic.md)počítač nebo [klasický Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) a odešlete čítače výkonu do Azure monitor. 
- Nainstalujte na virtuální počítač Azure Linux [agenta InfluxData telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) a odešlete metriky pomocí modulu plug-in Azure monitor Output.
- Odešlete vlastní metriky [přímo do Azure Monitor REST API](./metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Cenový model a uchování

Podrobnosti o tom, kdy se má povolit pro dotazy na vlastní metriky a metriky, najdete na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) . Konkrétní podrobnosti o ceně pro všechny metriky, včetně vlastních metrik a dotazů metrik, jsou na této stránce k dispozici. V souhrnu se neúčtují žádné náklady na ingestování standardní metriky (metriky platforem) do úložiště metrik Azure Monitor, ale při zadávání obecné dostupnosti budou mít vlastní metriky náklady. V dotazech rozhraní API metriky se účtují náklady.

Vlastní metriky se uchovávají po [stejnou dobu jako metriky platforem](../essentials/data-platform-metrics.md#retention-of-metrics). 

> [!NOTE]  
> Metriky odesílané Azure Monitor prostřednictvím sady Application Insights SDK se účtují jako ingestovaná data protokolu. Účtují jenom další metriky jenom v případě, že je vybraná funkce Application Insights [Povolit upozorňování na vlastní dimenze metriky](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) . Toto zaškrtávací políčko odesílá data do databáze Azure Monitor metrik pomocí vlastních rozhraní API metrik, které umožňuje komplexnější výstrahy.  Přečtěte si další informace o [cenovém modelu Application Insights](../app/pricing.md#pricing-model) a [cenách ve vaší oblasti](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="how-to-send-custom-metrics"></a>Jak odesílat vlastní metriky

Když odesíláte vlastní metriky do Azure Monitor, musí každý datový bod nebo hodnota nahlášené obsahovat následující informace.

### <a name="authentication"></a>Authentication
Aby bylo možné odesílat vlastní metriky Azure Monitor, entita, která odesílá metriku, potřebuje platný token Azure Active Directory (Azure AD) v hlavičce **nosiče** žádosti. Existuje několik podporovaných způsobů získání platného nosných tokenů:
1. [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). Poskytuje identitu samotného prostředku Azure, jako je třeba virtuální počítač. Identita spravované služby (MSI) je navržena tak, aby poskytovala oprávnění k provádění určitých operací. Příklad povoluje prostředku generovat metriky o sobě samým. K prostředku nebo jeho MSI se dá udělit oprávnění pro **monitorování** pro ostatní prostředky. S tímto oprávněním může MSI vysílat metriky i pro jiné prostředky.
2. [Instanční objekt služby Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). V tomto scénáři může být aplikace nebo služba Azure AD přiřazená oprávnění k vygenerování metriky o prostředku Azure.
K ověření žádosti Azure Monitor ověří token aplikace pomocí veřejných klíčů Azure AD. Existující role **vydavatele metrik monitorování** již má toto oprávnění. Je k dispozici v Azure Portal. Instanční objekt v závislosti na tom, k jakým prostředkům vysílat vlastní metriky, je možné v požadovaném oboru udělit roli **vydavatele metrik monitorování** . Příkladem může být předplatné, skupina prostředků nebo konkrétní prostředek.

> [!TIP]  
> Když požádáte o token Azure AD, aby vygeneroval vlastní metriky, ujistěte se, že cílová skupina nebo prostředek, pro který je požadován token, jsou `https://monitoring.azure.com/` . Nezapomeňte zahrnout koncové lomítko (/).

### <a name="subject"></a>Předmět
Tato vlastnost zachycuje ID prostředku Azure, pro který je nahlášená vlastní metrika. Tyto informace se zakódují v adrese URL vytvořeného volání rozhraní API. Každé rozhraní API může odesílat jenom hodnoty metrik pro jeden prostředek Azure.

> [!NOTE]  
> Nemůžete vygenerovat vlastní metriky s ID prostředku skupiny prostředků nebo předplatného.


### <a name="region"></a>Oblast
Tato vlastnost zachycuje, co je to oblast Azure, pro kterou je prostředek, pro který vydáváte metriky, nasazený v. Metriky musí být vygenerovány do stejného Azure Monitor oblastní koncový bod jako oblast, ve které je prostředek nasazen. Například vlastní metriky pro virtuální počítač nasazený v Západní USA musí být odesílány do koncového bodu oblastní Azure Monitor WestUS. Informace o oblasti jsou také kódované v adrese URL volání rozhraní API.

> [!NOTE]  
> Během veřejné verze Preview jsou vlastní metriky dostupné jenom v podmnožině oblastí Azure. Seznam podporovaných oblastí je popsán v pozdější části tohoto článku.
>
>

### <a name="timestamp"></a>Timestamp
Každý datový bod odeslaný do Azure Monitor musí být označený pomocí časového razítka. Toto časové razítko zachycuje hodnotu DateTime, při které je hodnota metriky měřena nebo shromažďována. Azure Monitor přijímá data metriky s časovými razítky až do 20 minut v minulosti a 5 minut v budoucnosti. Časové razítko musí být ve formátu ISO 8601.

### <a name="namespace"></a>Obor názvů
Obory názvů představují způsob kategorizace nebo seskupení podobných metrik dohromady. Pomocí oborů názvů můžete dosáhnout izolace mezi skupinami metrik, které mohou shromažďovat různé přehledy nebo ukazatele výkonu. Například můžete mít obor názvů s názvem **contosomemorymetrics** , který sleduje metriky využití paměti, které profilují vaši aplikaci. Jiný obor názvů s názvem **contosoapptransaction** může sledovat všechny metriky o transakcích uživatelů ve vaší aplikaci.

### <a name="name"></a>Name
**Název** je název metriky, která je hlášena. Obvykle je název dostatečně popisný, aby mohl lépe identifikovat, co je měřené. Příkladem je metrika, která měří počet bajtů paměti použitých na daném virtuálním počítači. Může mít název metriky, například **používané paměťové bajty**.

### <a name="dimension-keys"></a>Klíče dimenzí
Dimenze je dvojice klíč-hodnota, která pomáhá popsat další charakteristiky shromažďované metriky. Pomocí dalších charakteristik můžete shromažďovat další informace o této metrikě, což umožňuje získat hlubší přehledy. Například metrika **paměti při použití** může mít klíč dimenze s názvem **proces** , který zachycuje počet bajtů paměti, které každý proces na virtuálním počítači spotřebovává. Pomocí tohoto klíče můžete vyfiltrovat metriku a zjistit, kolik procesů specifických pro paměť využívá nebo k identifikaci horních pěti procesů podle využití paměti.
Dimenze jsou volitelné, ne všechny metriky mohou mít rozměry. Vlastní metrika může mít až 10 dimenzí.

### <a name="dimension-values"></a>Hodnoty dimenzí
Při vytváření sestav datového bodu metriky pro každý klíč dimenze na hlášené metriky existuje odpovídající hodnota dimenze. Můžete například chtít ohlásit paměť využívané ContosoApp na vašem VIRTUÁLNÍm počítači:

* V názvu metriky by se **používaly paměťové bajty**.
* Bude **zpracován** klíč dimenze.
* Hodnota dimenze by byla **ContosoApp.exe**.

Při publikování hodnoty metriky můžete zadat pouze jednu hodnotu dimenze na klíč dimenze. Pokud shromažďujete stejné využití paměti pro více procesů na virtuálním počítači, můžete pro toto časové razítko vykázat více hodnot metriky. Každá hodnota metriky by určovala jinou hodnotu dimenze pro klíč dimenze **procesu** .
Dimenze jsou volitelné, ne všechny metriky mohou mít rozměry. Pokud příspěvek metriky definuje klíče dimenzí, jsou odpovídající hodnoty dimenze povinné.

### <a name="metric-values"></a>Hodnoty metriky
Azure Monitor ukládá všechny metriky v intervalech členitosti po minutách. Chápeme, že během dané minuty může být nutné vzorkovat určitou metriku několikrát. Například podle využití procesoru. Nebo může být nutné změřit mnoho diskrétních událostí. Příkladem jsou latence transakcí přihlášení. Pokud chcete omezit počet nezpracovaných hodnot, které musíte vygenerovat a zaplatit za Azure Monitor, můžete tyto hodnoty lokálně agregovat a generovat:

* **Min**: minimální zjištěná hodnota ze všech vzorků a měření během minuty.
* **Max**: maximální získaná hodnota ze všech vzorků a měření během minuty.
* **Sum**: suma všech pozorovaných hodnot ze všech vzorků a měření během minuty.
* **Count**(počet): počet vzorků a měření odebraných během minuty.

Pokud jste například během určité minuty do vaší aplikace používali čtyři transakce přihlášení, může být výsledkem měřené latence následující:

|Transakce 1|Transakce 2|Transakce 3|Transakce 4|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|

Výsledná Azure Monitorová publikace metriky pak bude následující:
* Minimum: 4
* Maximum: 16
* Suma: 40
* Počet: 4

Pokud se vaše aplikace nemůže předem agregovat místně a potřebuje k okamžitému vygenerování každého diskrétního vzorku nebo události na základě kolekce, můžete vygenerovat hodnoty nezpracovaných měr. Například pokaždé, když v aplikaci dojde k transakci přihlášení, publikujete metriku pro Azure Monitor jenom s jedním měřením. Takže pro transakci přihlášení, která trvala 12 MS, bude publikace metriky vypadat takto:
* Minimum: 12
* Maximum: 12
* Suma: 12
* Počet: 1

V tomto procesu můžete vygenerovat více hodnot pro stejnou kombinaci metrik a dimenzí během dané minuty. Azure Monitor pak převezme všechny nezpracované hodnoty emitované po určitou minutu a agreguje je dohromady.

### <a name="sample-custom-metric-publication"></a>Ukázka vlastní publikace metriky
V následujícím příkladu vytvoříte vlastní metriku nazvanou **paměťové bajty používané v** **profilu paměti** oboru názvů metriky pro virtuální počítač. Metrika má jedinou dimenzi nazývanou **proces**. Pro dané časové razítko vygenerujeme hodnoty metriky pro dva různé procesy:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
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
> Application Insights, rozšíření Diagnostika a Agent InfluxData telegraf jsou již nakonfigurovány tak, aby generovaly hodnoty metriky se správným oblastním koncovým bodem a převedly všechny předchozí vlastnosti v každé emisi.
>
>

## <a name="custom-metric-definitions"></a>Definice vlastních metrik
Před tím, než se vygeneruje, není nutné před tím, než bude vygenerována, definovat vlastní metriku v Azure Monitor. Každý publikovaný datový bod metriky obsahuje obor názvů, název a informace o dimenzích. Takže se při prvním vygenerování vlastní metriky Azure Monitor automaticky vytvoří definice metriky. Tato definice metriky je pak zjistitelná u všech prostředků, ke kterým se metrika vysílá přes definice metrik.

> [!NOTE]  
> Azure Monitor ještě nepodporují definování **jednotek** pro vlastní metriku.

## <a name="using-custom-metrics"></a>Použití vlastních metrik
Až se vlastní metriky odešlou do Azure Monitor, můžete je procházet pomocí Azure Portal a dotazovat je prostřednictvím rozhraní API REST Azure Monitor. Můžete také vytvořit výstrahy, které vám upozorní na splnění určitých podmínek.

> [!NOTE]
> Abyste mohli zobrazit vlastní metriky, musíte být čtenář nebo role Přispěvatel. Viz [monitorování pro monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procházejte vlastní metriky prostřednictvím Azure Portal
1.    Přejděte na [Azure Portal](https://portal.azure.com).
2.    Vyberte podokno **monitorování** .
3.    Vyberte **Metriky**.
4.    Vyberte prostředek, pro který jste vygenerovali vlastní metriky.
5.    Vyberte obor názvů metrik pro vlastní metriku.
6.    Vyberte vlastní metriku.

> [!NOTE]
> Další informace o zobrazení metrik v Azure Portal najdete v tématu [Začínáme s Azure Průzkumník metrik](./metrics-getting-started.md) .

## <a name="supported-regions"></a>Podporované oblasti
Ve verzi Public Preview je možnost publikovat vlastní metriky k dispozici pouze v podmnožině oblastí Azure. Toto omezení znamená, že metriky lze publikovat pouze pro prostředky v jedné z podporovaných oblastí. Další informace o oblastech Azure najdete v tématu geografické [grafy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) . Kód oblasti Azure, který se používá v níže uvedených koncových bodech, je jenom název oblasti s odebraným prázdným znakem. v následující tabulce jsou uvedené sady podporovaných oblastí Azure pro vlastní metriky. Obsahuje také seznam odpovídajících koncových bodů, na které by se měly metriky pro prostředky v těchto oblastech publikovat:

|Oblast Azure |Předpona regionálního koncového bodu|
|---|---|
| Všechny oblasti veřejného cloudu | https://<azure_region_code>. monitoring.azure.com |
| **Azure Government** | |
| USA (Gov) – Arizona | https: \/ /usgovarizona.Monitoring.Azure.us |
| **Čína** | |
| Čína – východ 2 | https: \/ /chinaeast2.Monitoring.Azure.cn |

## <a name="latency-and-storage-retention"></a>Latence a uchování úložiště

Přidání značky nová metrika nebo přidání nové dimenze do metriky může trvat až 2 až 3 minuty, než se objeví. V systému by se měla data zobrazit během méně než 30 sekund 99% času. 

Pokud odstraníte metriku nebo odeberete dimenzi, změna může trvat týden až měsíčně, než se odstraní ze systému.

## <a name="quotas-and-limits"></a>Kvóty a omezení
Azure Monitor ukládá následující limity použití pro vlastní metriky:

|Kategorie|Omezení|
|---|---|
|Aktivní časová řada/předplatná/oblast|50,000|
|Klíče dimenzí na metriku|10|
|Délka řetězce pro obory názvů metriky, názvy metrik, klíče dimenzí a hodnoty dimenzí|256 znaků|

Aktivní časová řada je definovaná jako jakákoli jedinečná kombinace metriky, klíče dimenze nebo hodnoty dimenze, u kterých se v posledních 12 hodinách publikovaly hodnoty metriky.

Pokud chcete pochopit limit časových řad 50 tis, vezměte v úvahu následující metriku:

*Doba odezvy serveru* s rozměry: *oblast*, *oddělení*, *KódZákazníka*

Tato metrika obsahuje 10 oblastí, 20 oddělení a 100 zákazníků, které vám poskytnou 10 × 20 x 100 = 2000 časových řad. 

Pokud máte 100 oblastí, 200 oddělení a 2000, zákazníci 100 x 200 x 2000 = 40 000 000 Time-Series, což je daleko nad limit jenom pro tuto metriku. 

Toto omezení není znovu pro jednotlivé metriky. Je to pro součet všech takových metrik v rámci předplatného a oblasti.  

## <a name="design-limitations-and-considerations"></a>Omezení návrhu a požadavky

**Nepoužívejte Application Insights pro účely auditování** – kanál telemetrie Application Insights je optimalizován pro minimalizaci dopadu na výkon a omezení síťového provozu z monitorování vaší aplikace. V takovém případě omezuje nebo vzorkuje (přebírá jenom procento z telemetrie a ignoruje zbytek), pokud počáteční datová sada bude moc velká. Z důvodu tohoto chování ji nelze použít pro účely auditování, protože některé záznamy budou pravděpodobně vyřazeny. 

**Metriky s proměnnou v názvu** – nepoužívejte proměnnou jako součást názvu metriky, místo toho použijte konstantu. Pokaždé, když proměnná změní svou hodnotu, Azure Monitor vygeneruje novou metriku a rychle zvýší počet metrik. Obecně platí, že pokud vývojáři chtějí do názvu metriky zahrnout proměnnou, chtějí si ve skutečnosti chtít sledovat více časové řady v rámci jedné metriky a používat dimenze místo názvů proměnných metriky. 

**Vysoká míra metriky mohutnosti** – metriky s příliš velkým počtem platných hodnot v dimenzi (vysoká mohutnost) jsou mnohem pravděpodobnější, že 50 tis limit. Obecně platí, že nikdy nepoužívejte trvale měnící hodnotu v dimenzi nebo názvu metriky. Časové razítko, například by nikdy nemělo být dimenze. Můžete použít server, zákazníka nebo ProductID, ale pouze v případě, že máte menší počet každého z těchto typů. V rámci testu si Položte dotaz, jestli byste někdy chtěli data v grafu.  Pokud máte 10 nebo možná i 100 serverů, může být užitečné, abyste je viděli v grafu pro účely porovnání. Pokud ale máte 1000, výsledný graf by nejspíš byl obtížné, pokud ho nebude možné přečíst. Osvědčeným postupem je udržovat méně až 100 platných hodnot. Až 300 je šedá oblast.  Pokud potřebujete přejít přes tuto částku, použijte místo toho Azure Monitor vlastní protokoly.   

Pokud máte proměnnou v názvu nebo vysoké dimenzi mohutnosti, může dojít k následujícím akcím:
- Metriky se stávají nespolehlivou kvůli omezování.
- Průzkumník metrik nefunguje
- Výstrahy a oznámení se stanou nepředvídatelnými.
- Náklady se můžou zvýšit neočekávaným způsobem – Microsoft není zpoplatněný, zatímco vlastní metriky s dimenzemi jsou ve verzi Public Preview. Jakmile se ale v budoucnu začnou účtovat poplatky, budou se vám účtovat neočekávané poplatky. Plán se účtuje za spotřebu metrik na základě počtu monitorovaných časových řad a počtu provedených volání rozhraní API.  

## <a name="next-steps"></a>Další kroky
Použijte vlastní metriky z různých služeb: 
 - [Virtual Machines](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Škálovací sada virtuálních počítačů](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Virtual Machines Azure (Classic)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuální počítač se systémem Linux pomocí agenta telegraf](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Klasický Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)

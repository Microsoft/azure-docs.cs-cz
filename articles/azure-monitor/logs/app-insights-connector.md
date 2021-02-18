---
title: Zobrazit data aplikace v Azure Application Insights | Microsoft Docs
description: Pomocí řešení Application Insights Connector můžete diagnostikovat problémy s výkonem a porozumět tomu, co uživatelé s vaší aplikací dělají při monitorování pomocí Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: 2e103bb3cce364aeb5c25dcc2b54bf78c6993ca0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611513"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Řešení pro správu Application Insights Connector (zastaralé)

![Symbol Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> S podporou [dotazů mezi prostředky](../logs/cross-workspace-query.md)už řešení pro správu Application Insights Connector není potřeba. Už se nepoužívá a odebírá se z Azure Marketplace společně s portálem OMS, který byl oficiálně zastaralý od 15. ledna 2019 pro komerční cloud Azure. Vyřadí se od 30. března 2019 pro státní správu Azure USA.
>
>Stávající připojení budou fungovat i do 30. června 2019.  V případě zastaralého portálu OMS neexistuje způsob, jak nakonfigurovat a odebrat existující připojení z portálu. Pokud chcete pomocí PowerShellu odebrat existující připojení, přečtěte si téma [Odebrání konektoru pomocí PowerShellu](#removing-the-connector-with-powershell) .
>
>Pokyny k dotazování na data protokolu Application Insights pro více aplikací najdete v tématu [sjednocení více Azure Monitorch Application Insightsch prostředků](../log-query/unify-app-resource-data.md). Další informace o zastaralých portálech OMS najdete v tématu [Přesun portálu OMS do Azure](../platform/oms-portal-transition.md).
>
> 

Řešení konektoru služby Application Insights pomáhá diagnostikovat problémy s výkonem a porozumět tomu, co uživatelé s vaší aplikací dělají při monitorování pomocí [Application Insights](../app/app-insights-overview.md). Zobrazení stejné telemetrie aplikace, kterou vývojáři uvidí v Application Insights, jsou k dispozici v Log Analytics. Když ale aplikace Application Insights integruje s Log Analytics, je lepší viditelnost vašich aplikací tím, že bude mít data o provozu a aplikacích na jednom místě. Stejná zobrazení vám umožní spolupracovat s vývojáři vaší aplikace. Běžné pohledy můžou zkrátit čas na detekci a řešení problémů s aplikacemi i platformou.

Při použití řešení můžete:

- Zobrazit všechny Application Insights aplikace na jednom místě, i když jsou v různých předplatných Azure
- Korelace dat infrastruktury s daty aplikací
- Vizualizace dat aplikací pomocí perspektiv v hledání v protokolu
- Překlopit data z Log Analytics do Application Insights aplikace v Azure Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Připojené zdroje

Na rozdíl od většiny ostatních Log Analytics řešení nejsou shromažďována data pro Application Insights Connector agenti. Všechna data, která řešení používá, přichází přímo z Azure.

| Připojený zdroj | Podporováno | Description |
| --- | --- | --- |
| [Agenti systému Windows](./../agents/agent-windows.md) | No | Řešení neshromažďuje informace od agentů systému Windows. |
| [Agenti systému Linux](../vm/quick-collect-linux-computer.md) | No | Řešení neshromažďuje informace od agentů systému Linux. |
| [Skupina pro správu SCOM](../agents/om-agents.md) | No | Řešení neshromažďuje informace od agentů v připojené skupině pro správu systému SCOM. |
| [Účet úložiště Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | Řešení neumožňuje shromažďování informací z Azure Storage. |

## <a name="prerequisites"></a>Požadavky

- Pokud chcete získat přístup k informacím o Application Insights Connector, musíte mít předplatné Azure.
- Musíte mít minimálně jeden nakonfigurovaný prostředek Application Insights.
- Musíte být vlastníkem nebo přispěvatelem prostředku Application Insights.

## <a name="configuration"></a>Konfigurace

1. Povolte řešení Azure Web Apps Analytics z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) nebo pomocí procesu popsaného v tématu [Přidání Log Analytics řešení z galerie řešení](../insights/solutions.md).
2. Přejděte na [Azure Portal](https://portal.azure.com). Pro otevření Application Insights vyberte **všechny služby** . Pak vyhledejte Application Insights. 
3. V části **předplatná** vyberte předplatné, které má Application Insights prostředky, a pak v části **název** vyberte jednu nebo více aplikací.
4. Klikněte na **Uložit**.

Během přibližně 30 minut budou data k dispozici a Application Insights dlaždice se aktualizují daty, podobně jako na následujícím obrázku:

![Dlaždice Application Insights](./media/app-insights-connector/app-insights-tile.png)

Další body, které je potřeba vzít v úvahu:

- Application Insights aplikace můžete propojit pouze s jedním Log Analytics pracovním prostorem.
- K Log Analytics můžete propojit jenom [základní a podnikové Application Insights prostředky](https://azure.microsoft.com/pricing/details/application-insights) . Můžete ale použít bezplatnou úroveň Log Analytics.

## <a name="management-packs"></a>Sady Management Pack

Toto řešení neinstaluje žádné sady Management Pack v připojených skupinách pro správu.

## <a name="use-the-solution"></a>Použití řešení

Následující části popisují, jak můžete pomocí oken zobrazených na řídicím panelu Application Insights zobrazit data z aplikací a pracovat s nimi.

### <a name="view-application-insights-connector-information"></a>Zobrazit Application Insights Connector informace

Kliknutím na dlaždici **Application Insights** otevřete řídicí panel **Application Insights** zobrazíte následující okna.

![Snímek obrazovky řídicího panelu Application Insights znázorňující okna pro aplikace, objem dat a dostupnost.](./media/app-insights-connector/app-insights-dash01.png)

![Snímek obrazovky řídicího panelu Application Insights znázorňující okna požadavků serveru, selhání a výjimky.](./media/app-insights-connector/app-insights-dash02.png)

Řídicí panel obsahuje okna zobrazená v tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Můžete spustit hledání v protokolu, které vrátí všechny záznamy, když kliknete na **Zobrazit vše** ve spodní části okna nebo po kliknutí na záhlaví okna.


| **Sloupec** | **Popis** |
| --- | --- |
| Aplikace – počet aplikací | Zobrazuje počet aplikací v aplikačních prostředcích. Uvádí také seznam názvů aplikací a pro každý z nich počet záznamů aplikací. Kliknutím na číslo spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kliknutím na název aplikace spustíte prohledávání protokolu pro aplikaci, která zobrazuje záznamy aplikací na hostitele, záznamy podle typu telemetrie a všechna data podle typu (na základě posledního dne). |
| Objem dat – hostitelé odesílající data | Zobrazuje počet hostitelů počítačů odesílajících data. Také uvádí seznam hostitelů počítačů a počtu záznamů pro každého hostitele. Kliknutím na číslo spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klikněte na název počítače a spusťte hledání protokolu pro hostitele, který zobrazuje záznamy aplikací na hostitele, záznamy podle typu telemetrie a všechna data podle typu (na základě posledního dne). |
| Dostupnost – výsledky webového testu | Zobrazuje prstencový graf pro výsledky webového testu, který označuje úspěch nebo neúspěch. Kliknutím na graf spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Výsledky zobrazují počet průchodů a selhání pro všechny testy. Zobrazuje všechny Web Apps s přenosy za poslední minutu. Kliknutím na název aplikace zobrazíte prohledávání protokolu s podrobnostmi o neúspěšných webových testech. |
| Požadavky na server – požadavky za hodinu | Zobrazuje spojnicový graf požadavků serveru za hodinu pro různé aplikace. Najeďte myší na čáru v grafu, abyste viděli horní 3 aplikace, které přijímají požadavky na určitý bod v čase. Zobrazuje také seznam aplikací, které přijímají požadavky a počet požadavků pro vybrané období. <br><br>Kliknutím na graf spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , které zobrazuje podrobnější spojnicový graf požadavků serveru za hodinu pro různé aplikace. <br><br> Kliknutím na aplikaci v seznamu spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , které zobrazuje seznam požadavků, grafy požadavků v průběhu času a trvání žádosti a seznam kódů odpovědí na žádosti.   |
| Selhání – neúspěšné žádosti za hodinu | Zobrazuje spojnicový graf neúspěšných požadavků aplikace za hodinu. Najeďte myší na graf, abyste viděli horní 3 aplikace s neúspěšnými požadavky pro určitý bod v čase. Zobrazuje také seznam aplikací s počtem neúspěšných žádostí pro každý z nich. Kliknutím na graf spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , které obsahuje podrobnější spojnicový graf neúspěšných požadavků aplikace. <br><br>Kliknutím na položku v seznamu spustíte hledání v protokolu <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>  , které zobrazuje neúspěšné žádosti, grafy pro neúspěšné žádosti v čase a dobu trvání žádosti a seznam kódů odpovědí na neúspěšné žádosti. |
| Výjimky – výjimky za hodinu | Zobrazuje spojnicový graf výjimek za hodinu. Najeďte myší na graf, abyste viděli horní 3 aplikace s výjimkami pro určitý bod v čase. Zobrazuje také seznam aplikací s počtem výjimek pro každý z nich. Kliknutím na graf spustíte hledání v protokolu <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , které obsahuje podrobnější odkazový diagram výjimek. <br><br>Kliknutím na položku v seznamu spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> , které zobrazuje seznam výjimek, grafy výjimek v čase a neúspěšné žádosti a seznam typů výjimek.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Zobrazení Application Insights perspektivy pomocí hledání v protokolu

Když kliknete na libovolnou položku na řídicím panelu, zobrazí se Application Insights perspektiva uvedená v hledání. Perspektiva poskytuje rozšířenou vizualizaci na základě typu telemetrie, který jste vybrali. Proto se změny obsahu vizualizace pro různé typy telemetrie.

Po kliknutí na libovolné místo v okně aplikace se zobrazí perspektiva výchozích **aplikací** .

![Perspektiva Application Insightsch aplikací](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektiva zobrazuje přehled vybrané aplikace.

V okně **dostupnost** se zobrazí jiné zobrazení Perspektiva, kde můžete zobrazit výsledky webového testu a související neúspěšné žádosti.

![Perspektiva dostupnosti Application Insights](./media/app-insights-connector/availability-blade-drill-search.png)

Po kliknutí na libovolné místo v okně žádosti nebo **chyby** **serveru** se změní komponenty perspektivy, aby vám poskytovala vizualizaci, která souvisí s požadavky.

![Okno chyby Application Insights](./media/app-insights-connector/server-requests-failures-drill-search.png)

Když kliknete kamkoli v okně **výjimky** , zobrazí se vizualizace, která je přizpůsobená výjimkám.

![Okno výjimek Application Insights](./media/app-insights-connector/exceptions-blade-drill-search.png)

Bez ohledu na to, zda kliknete na některý z **Application Insights Connector** řídicího panelu, na samotné stránce pro **hledání** , bude v každém dotazu, který vrací Application Insights data, zobrazená perspektiva Application Insights. Pokud například zobrazujete Application Insights data, **&#42;** dotaz také zobrazuje kartu perspektiva jako na následujícím obrázku:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Komponenty perspektivy jsou aktualizovány v závislosti na vyhledávacím dotazu. To znamená, že výsledky můžete filtrovat pomocí libovolného vyhledávacího pole, které vám umožní zobrazit data z těchto možností:

- Všechny vaše aplikace
- Jedna vybraná aplikace
- Skupina aplikací

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Překlopení do aplikace v Azure Portal

Okna Application Insights Connector jsou navržená tak, aby vám *při použití Azure Portal* mohli na vybranou Application Insights aplikaci Překlopit. Řešení můžete použít jako platformu pro monitorování vysoké úrovně, která pomáhá řešit problémy s aplikací. Když se v některé z vašich připojených aplikací zobrazí potenciální problém, můžete k nim přejít v Log Analytics hledání nebo můžete přímo překlopit přímo do Application Insights aplikace.

Chcete-li vytvořit kontingenční tabulku, klikněte na tlačítko se třemi tečkami (**...**), které se zobrazí na konci každého řádku, a vyberte možnost **otevřít v Application Insights**.

>[!NOTE]
>**Otevření v Application Insights** není k dispozici v Azure Portal.

![Otevřít v Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Ukázka – opravená data

Application Insights poskytuje *[opravu vzorkování](../app/sampling.md)* , která pomůže snížit provoz telemetrie. Když povolíte vzorkování v aplikaci Application Insights, získáte omezený počet záznamů v Application Insights i v Log Analytics. I když se konzistence dat zachová na **Application Insights Connector** stránce a perspektivách, měli byste ručně opravit ukázková data pro vlastní dotazy.

Tady je příklad opravy vzorkování v dotazu prohledávání protokolu:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Pole s **počtem vzorků** je k dispozici ve všech položkách a zobrazuje počet datových bodů, které položka představuje. Pokud pro Application Insights aplikaci zapnete vzorkování, je **počet vzorků** větší než 1. Chcete-li spočítat skutečný počet položek, které vaše aplikace vygenerovala, sečtěte pole **počet vzorků** .

Vzorkování ovlivní pouze celkový počet položek, které aplikace generuje. Nemusíte opravovat vzorkování pro pole metriky, jako je **RequestDuration** nebo **AvailabilityDuration**  , protože tato pole zobrazují průměr reprezentovaných položek.

## <a name="input-data"></a>Vstupní data

Řešení přijímá následující typy telemetrie dat z vašich připojených aplikací Application Insights:

- Dostupnost
- Výjimky
- Žádosti
- Zobrazení stránek – pro váš pracovní prostor, abyste mohli přijímat zobrazení stránek, musíte nakonfigurovat aplikace, aby tyto informace shromáždily. Další informace najdete v tématu [PageViews](../app/api-custom-events-metrics.md#page-views).
- Vlastní události – chcete-li, aby váš pracovní prostor přijímal vlastní události, je nutné aplikace nakonfigurovat tak, aby tyto informace shromáždily. Další informace najdete v tématu [TrackEvent](../app/api-custom-events-metrics.md#trackevent).

Data jsou přijímána Log Analytics z Application Insights, jakmile budou k dispozici.

## <a name="output-data"></a>Výstupní data

Záznam s *typem* *ApplicationInsights* se vytvoří pro každý typ vstupních dat. Záznamy ApplicationInsights mají vlastnosti, které jsou uvedeny v následujících částech:

### <a name="generic-fields"></a>Obecná pole

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| IP adresa klienta |   |
| TimeGenerated | Čas záznamu |
| ApplicationId | Klíč instrumentace aplikace Application Insights |
| ApplicationName | Název aplikace Application Insights |
| RoleInstance | ID hostitelského serveru |
| DeviceType | Klientské zařízení |
| ScreenResolution |   |
| Kontinent | Kontinent, kde žádost pochází |
| Země | Země nebo oblast, kde žádost pochází |
| Oblasti | Okres, stát nebo národní prostředí, kde žádost pochází |
| City (Město) | Město nebo města, kde pochází požadavek |
| Syntetické | Určuje, zda byla žádost vytvořena uživatelem nebo automatizovanou metodou. True = automatizovaná metoda nebo false = generovaná uživatelem |
| SamplingRate | Procento telemetrie vygenerované sadou SDK, která je odeslána na portál. Rozsah 0,0 – 100,0. |
| SampledCount | 100/(SamplingRate). Například 4 = &gt; 25% |
| Ověřování typu | True nebo false |
| OperationID | Položky, které mají stejné ID operace, se zobrazují jako související položky na portálu. Obvykle ID žádosti |
| ParentOperationID | ID nadřazené operace |
| OperationName |   |
| SessionId | Identifikátor GUID, který jednoznačně identifikuje relaci, ve které se vytvořila žádost |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Pole specifická pro dostupnost

| Vlastnost | Popis |
| --- | --- |
| TelemetryType | Dostupnost |
| AvailabilityTestName | Název webového testu |
| AvailabilityRunLocation | Geografický zdroj požadavku HTTP |
| AvailabilityResult | Indikuje výsledek úspěšnosti webového testu. |
| AvailabilityMessage | Zpráva připojená k webovému testu |
| AvailabilityCount | 100/(vzorkovací frekvence). Například 4 = &gt; 25% |
| DataSizeMetricValue | 1,0 nebo 0,0 |
| DataSizeMetricCount | 100/(vzorkovací frekvence). Například 4 = &gt; 25% |
| AvailabilityDuration | Doba trvání webového testu v milisekundách |
| AvailabilityDurationCount | 100/(vzorkovací frekvence). Například 4 = &gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Jedinečný identifikátor GUID pro webový test |
| AvailabilityTimestamp | Přesné časové razítko testu dostupnosti |
| AvailabilityDurationMin | U ukázkových záznamů v tomto poli se zobrazuje minimální doba trvání webového testu (milisekundy) pro reprezentované datové body. |
| AvailabilityDurationMax | U ukázkových záznamů v tomto poli se zobrazuje maximální doba trvání webového testu (milisekundy) pro reprezentované datové body. |
| AvailabilityDurationStdDev | U ukázkových záznamů v tomto poli se zobrazuje směrodatná odchylka mezi všemi dobami trvání webových testů (milisekund) pro reprezentované datové body. |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Pole specifická pro výjimku

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Výjimka |
| Typvýjimky | Typ výjimky |
| ExceptionMethod | Metoda, která vytvoří výjimku |
| ExceptionAssembly | Sestavení zahrnuje architekturu a verzi a token veřejného klíče. |
| Výjimka | Typ výjimky |
| ExceptionHandledAt | Označuje úroveň, která zpracovává výjimku. |
| ExceptionCount | 100/(vzorkovací frekvence). Například 4 = &gt; 25% |
| ExceptionMessage | Zpráva o výjimce |
| ExceptionStack | Úplný zásobník výjimky |
| ExceptionHasStack | True, pokud má výjimka zásobník |



### <a name="request-specific-fields"></a>Pole specifická pro požadavek

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Žádost |
| ResponseCode | Odpověď HTTP se odeslala klientovi. |
| RequestSuccess | Označuje úspěch nebo neúspěch. True nebo false |
| ID požadavku | ID pro jednoznačnou identifikaci žádosti |
| Žádost o zadání | Základ pro GET/POST + URL |
| RequestDuration | Doba trvání žádosti v sekundách |
| URL | Adresa URL požadavku, který nezahrnuje hostitele |
| Hostitel | Hostitel webového serveru |
| URLBase | Úplná adresa URL požadavku |
| ApplicationProtocol | Typ protokolu používaného aplikací |
| RequestCount | 100/(vzorkovací frekvence). Například 4 = &gt; 25% |
| RequestDurationCount | 100/(vzorkovací frekvence). Například 4 = &gt; 25% |
| RequestDurationMin | U ukázkových záznamů v tomto poli se zobrazuje minimální doba trvání požadavku (milisekundy) pro reprezentované datové body. |
| RequestDurationMax | U ukázkových záznamů v tomto poli se zobrazuje maximální doba trvání požadavku (milisekundy) pro reprezentované datové body. |
| RequestDurationStdDev | U ukázkových záznamů v tomto poli se zobrazuje směrodatná odchylka mezi všemi dobami trvání žádosti (milisekundy) pro reprezentované datové body. |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Toto řešení neobsahuje sadu vzorových hledání protokolu zobrazených na řídicím panelu. Ukázky dotazů na prohledávání protokolu s popisy se ale zobrazují v části [zobrazení Application Insights Connector informace](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>Odebrání konektoru pomocí PowerShellu
V případě zastaralého portálu OMS neexistuje způsob, jak nakonfigurovat a odebrat existující připojení z portálu. Existující připojení můžete odebrat pomocí následujícího skriptu PowerShellu. Tuto operaci lze provést pouze v případě, že jste vlastníkem nebo přispěvatelem pracovního prostoru a čtecího modulu Application Insightsho prostředku.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Seznam aplikací můžete načíst pomocí následujícího skriptu prostředí PowerShell, který vyvolá volání REST API. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Tento skript vyžaduje ověřovací token nosiče pro ověřování proti Azure Active Directory. Jedním ze způsobů, jak tento token načíst, je použití článku na [webu dokumentace REST API](/rest/api/loganalytics/datasources/createorupdate). Klikněte na **vyzkoušet** a přihlaste se k předplatnému Azure. Z **náhledu žádosti** můžete zkopírovat nosný token, jak je znázorněno na následujícím obrázku.


![Nosný token](media/app-insights-connector/bearer-token.png)


Seznam aplikací můžete také načíst pomocí dotazu protokolu:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Další kroky

- K zobrazení podrobných informací o aplikacích Application Insights použijte [hledání v protokolu](../log-query/log-query-overview.md) .


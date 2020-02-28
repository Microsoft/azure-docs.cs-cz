---
title: Zobrazení dat aplikací Azure Application Insights | Dokumentace Microsoftu
description: Řešení Application Insights Connector můžete použít k diagnostice problémů s výkonem a pochopit, co uživatelé dělají s vaší aplikací při monitorovat pomocí Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665149"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Řešení pro správu Application Insights Connector (zastaralé)

![Application Insights symbol](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> S podporou [dotazů mezi prostředky](../../azure-monitor/log-query/cross-workspace-query.md)už řešení pro správu Application Insights Connector není potřeba. Už se nepoužívá a odebírá se z Azure Marketplace společně s portálem OMS, který byl oficiálně zastaralý od 15. ledna 2019 pro komerční cloud Azure. Vyřadí se od 30. března 2019 pro státní správu Azure USA.
>
>Stávající připojení budou fungovat i do 30. června 2019.  V případě zastaralého portálu OMS neexistuje způsob, jak nakonfigurovat a odebrat existující připojení z portálu. Pokud chcete pomocí PowerShellu odebrat existující připojení, přečtěte si téma [Odebrání konektoru pomocí PowerShellu](#removing-the-connector-with-powershell) .
>
>Pokyny k dotazování na data protokolu Application Insights pro více aplikací najdete v tématu [sjednocení více Azure Monitorch Application Insightsch prostředků](../log-query/unify-app-resource-data.md). Další informace o zastaralých portálech OMS najdete v tématu [Přesun portálu OMS do Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Řešení konektoru služby Application Insights pomáhá diagnostikovat problémy s výkonem a porozumět tomu, co uživatelé s vaší aplikací dělají při monitorování pomocí [Application Insights](../../azure-monitor/app/app-insights-overview.md). Zobrazení stejné aplikace telemetrická data, která vývojářům zobrazit ve službě Application Insights jsou k dispozici ve službě Log Analytics. Však při integraci vašich aplikací služby Application Insights s Log Analytics se zvýší viditelnost vaší aplikace tím, že data o operacích a aplikacích na jednom místě. S stejného zobrazení vám umožňuje spolupracovat s vývojáři vaší aplikace. Obecná zobrazení může pomoct snížit čas a vyřešte aplikace od problémů platformy.

Při použití řešení můžete:

- Zobrazit všechny vaše aplikace Application Insights na jednom místě, i když se nachází v různých předplatných Azure
- Korelujte data infrastruktury se data aplikací
- Vizualizace dat aplikace pomocí perspektiv v prohledávání protokolu
- Otáčení z dat Log Analytics do vaší aplikace Application Insights na webu Azure Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Připojené zdroje

Na rozdíl od většina jiných řešení Log Analytics data nejsou shromažďována pro Application Insights Connector agenty. Všechna data, která používá řešení pochází přímo z Azure.

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| [Agenti systému Windows](../../azure-monitor/platform/agent-windows.md) | Ne | Řešení neshromažďuje informace z agentů Windows. |
| [Agenti systému Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace z agentů Linuxu. |
| [Skupina pro správu SCOM](../../azure-monitor/platform/om-agents.md) | Ne | Řešení neshromažďuje informace z agentů v připojené skupině pro správu nástroje SCOM. |
| [Účet služby Azure Storage](collect-azure-metrics-logs.md) | Ne | Toto řešení dělá není shromažďování informací ze služby Azure storage. |

## <a name="prerequisites"></a>Požadavky

- Přístup k informacím Application Insights Connector, musíte mít předplatné Azure
- Musí mít aspoň jeden nakonfigurované prostředku Application Insights.
- Musíte být vlastníkem nebo přispěvatelem prostředek služby Application Insights.

## <a name="configuration"></a>Konfigurace

1. Povolte řešení Azure Web Apps Analytics z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) nebo pomocí procesu popsaného v tématu [Přidání Log Analytics řešení z galerie řešení](../../azure-monitor/insights/solutions.md).
2. Přejděte na web [Azure Portal](https://portal.azure.com). Pro otevření Application Insights vyberte **všechny služby** . Vyhledejte Application Insights. 
3. V části **předplatná**vyberte předplatné, které má Application Insights prostředky, a pak v části **název**vyberte jednu nebo více aplikací.
4. Klikněte na možnost **Uložit**.

Během přibližně 30 minut budou data k dispozici a na dlaždici služby Application Insights se aktualizuje s daty, jako na následujícím obrázku:

![Dlaždici služby Application Insights](./media/app-insights-connector/app-insights-tile.png)

Ostatní body brát v úvahu:

- Aplikace Application Insights můžete propojit jenom k jednomu pracovnímu prostoru Log Analytics.
- K Log Analytics můžete propojit jenom [základní a podnikové Application Insights prostředky](https://azure.microsoft.com/pricing/details/application-insights) . Můžete však použít úroveň Free služby Log Analytics.

## <a name="management-packs"></a>Sady Management Pack

Toto řešení není možné nainstalovat všechny sady management Pack v připojených skupin pro správu.

## <a name="use-the-solution"></a>Použití řešení

Následující části popisují, jak můžete okna zobrazené v řídicím panelu služby Application Insights k zobrazení a interakci s daty z vašich aplikací.

### <a name="view-application-insights-connector-information"></a>Zobrazit informace o Application Insights Connector

Kliknutím na dlaždici **Application Insights** otevřete řídicí panel **Application Insights** zobrazíte následující okna.

![Řídicí panel Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Řídicí panel Application Insights](./media/app-insights-connector/app-insights-dash02.png)

Řídicí panel obsahuje listy v tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Můžete spustit hledání v protokolu, které vrátí všechny záznamy, když kliknete na **Zobrazit vše** ve spodní části okna nebo po kliknutí na záhlaví okna.


| **Kolo** | **Popis** |
| --- | --- |
| Aplikace – počet aplikací | Zobrazuje počet aplikací v prostředků aplikace. Také obsahuje seznam názvů aplikací a pro každou počet záznamů, aplikace. Kliknutím na číslo spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klikněte na název aplikace spustíte hledání v protokolu aplikace, který zobrazuje záznamy aplikace na hostitele, záznamy podle typu telemetrie a všechna data podle typu (založené na poslední den). |
| Objem dat – hostitele, kteří odesílají data | Zobrazuje počet počítač hostitele, kteří odesílají data. Obsahuje také seznam počítač hostuje a počet záznamů pro každého hostitele. Kliknutím na číslo spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klikněte na název počítače spustíte hledání v protokolu pro hostitele, který zobrazuje záznamy aplikace na hostitele, záznamy podle typu telemetrie a všechna data podle typu (založené na poslední den). |
| Dostupnost – výsledky webového testu | Ukazuje prstencového grafu pro výsledků testu webu, která dokončeno nebo chyba. Kliknutím na graf spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Výsledky zobrazit počet průchodů a neúspěchů pro všemi testy. Zobrazuje všechny webové aplikace se provoz za poslední minutu. Klikněte na název aplikace zobrazení hledání v protokolu s podrobnostmi o neúspěšných webové testy. |
| Požadavky serveru – žádosti za hodinu | Zobrazuje spojnicový graf požadavky serveru za hodinu pro různé aplikace. Najeďte myší na řádek v grafu pro zobrazení 3 nejpoužívanější aplikace přijímat žádosti do bodu v čase. Také zobrazuje seznam aplikací, které přijímají požadavky a počet požadavků pro vybrané období. <br><br>Kliknutím na graf spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, které zobrazuje podrobnější spojnicový graf požadavků serveru za hodinu pro různé aplikace. <br><br> Kliknutím na aplikaci v seznamu spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>, které zobrazuje seznam požadavků, grafy požadavků v čase a dobu trvání žádosti a seznam kódů odpovědí na žádosti.   |
| Chyby – neúspěšné požadavky za hodinu | Zobrazuje spojnicový graf žádosti o selhání aplikace za hodinu. Najeďte myší na graf zobrazíte 3 nejpoužívanější aplikace s neúspěšné požadavky na bod v čase. Také zobrazuje seznam aplikací s počtem neúspěšných žádostí pro každý. Kliknutím na graf spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, které zobrazuje podrobnější spojnicový graf neúspěšných požadavků aplikace. <br><br>Kliknutím na položku v seznamu spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>, které zobrazuje neúspěšné žádosti, grafy pro neúspěšné žádosti v čase a dobu trvání žádosti a seznam kódů odpovědí na neúspěšné žádosti. |
| Výjimky – výjimky za hodinu | Zobrazuje spojnicový graf výjimek za hodinu. Najeďte myší na graf zobrazíte 3 nejpoužívanější aplikace s výjimkami bodu v čase. Také zobrazuje seznam aplikací s počtem výjimky pro každý. Kliknutím na graf spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, které zobrazuje podrobnější odkazový diagram výjimek. <br><br>Kliknutím na položku v seznamu spustíte prohledávání protokolu pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code>, které zobrazuje seznam výjimek, grafy výjimek v průběhu času a neúspěšné žádosti a seznam typů výjimek.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Zobrazení perspektivy Application Insights s prohledáváním protokolů

Po kliknutí na libovolnou položku na řídicím panelu zobrazí perspektivy Application Insights zobrazí v hledání. Perspektiva poskytuje rozšířené vizualizace, podle typu telemetrie, které vybrali. Ano, vizualizace změny obsahu pro typy jiné telemetrie.

Po kliknutí na libovolné místo v okně aplikace se zobrazí perspektiva výchozích **aplikací** .

![Application Insights aplikace perspektivy](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektivy ukazuje přehled aplikace, kterou jste vybrali.

V okně **dostupnost** se zobrazí jiné zobrazení Perspektiva, kde můžete zobrazit výsledky webového testu a související neúspěšné žádosti.

![Application Insights dostupnost perspektivy](./media/app-insights-connector/availability-blade-drill-search.png)

Po kliknutí na libovolné místo v okně žádosti nebo **chyby** **serveru** se změní komponenty perspektivy, aby vám poskytovala vizualizaci, která souvisí s požadavky.

![Application Insights selhání okno](./media/app-insights-connector/server-requests-failures-drill-search.png)

Když kliknete kamkoli v okně **výjimky** , zobrazí se vizualizace, která je přizpůsobená výjimkám.

![Okno Application Insights výjimky](./media/app-insights-connector/exceptions-blade-drill-search.png)

Bez ohledu na to, zda kliknete na některý z **Application Insights Connector** řídicího panelu, na samotné stránce pro **hledání** , bude v každém dotazu, který vrací Application Insights data, zobrazená perspektiva Application Insights. Pokud například zobrazujete Application Insights data, **&#42;** dotaz také zobrazuje kartu perspektiva jako na následujícím obrázku:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Součástí perspektivy jsou aktualizovány v závislosti na vyhledávací dotaz. To znamená, že výsledky můžete filtrovat pomocí vyhledávacího pole, která vám dává možnost vidět data z:

- Všechny aplikace
- Jeden vybrané aplikace
- Skupiny aplikací

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Uveďte do aplikace na webu Azure Portal

Okna Application Insights Connector jsou navržená tak, aby vám *při použití Azure Portal*mohli na vybranou Application Insights aplikaci Překlopit. Řešení můžete použít jako základní monitorovací platformě, která pomáhá při řešení problémů aplikace. Až uvidíte potenciální problém v některé z připojených aplikací, můžete buď přejít k podrobnostem ho ve službě Log Analytics search nebo můžete vytvořit kontingenční přímo do aplikace Application Insights.

Chcete-li vytvořit kontingenční tabulku, klikněte na tlačítko se třemi tečkami ( **...** ), které se zobrazí na konci každého řádku, a vyberte možnost **otevřít v Application Insights**.

>[!NOTE]
>**Otevření v Application Insights** není k dispozici v Azure Portal.

![Otevřít v Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Opravit ukázková data

Application Insights poskytuje *[opravu vzorkování](../../azure-monitor/app/sampling.md)* , která pomůže snížit provoz telemetrie. Když povolíte vzorkování na své aplikaci Application Insights, získat menší počet položek uložených ve službě Application Insights a Log Analytics. I když se konzistence dat zachová na **Application Insights Connector** stránce a perspektivách, měli byste ručně opravit ukázková data pro vlastní dotazy.

Tady je příklad opravy vzorkování vyhledávacího dotazu protokolu:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Pole s **počtem vzorků** je k dispozici ve všech položkách a zobrazuje počet datových bodů, které položka představuje. Pokud pro Application Insights aplikaci zapnete vzorkování, je **počet vzorků** větší než 1. Chcete-li spočítat skutečný počet položek, které vaše aplikace vygenerovala, sečtěte pole **počet vzorků** .

Vzorkování ovlivňuje jenom celkový počet záznamů, které vaše aplikace generuje. Nemusíte opravovat vzorkování pro pole metriky, jako je **RequestDuration** nebo **AvailabilityDuration** , protože tato pole zobrazují průměr reprezentovaných položek.

## <a name="input-data"></a>Vstupní data

Řešení přijímá následující typy dat telemetrie z připojených aplikací služby Application Insights:

- Dostupnost
- Výjimky
- Požadavky
- Zobrazení stránek – pro váš pracovní prostor pro příjem zobrazení stránek, je nutné nakonfigurovat aplikace ke shromažďování těchto informací. Další informace najdete v tématu [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Vlastní události – pro vašeho pracovního prostoru pro příjem vlastní události, je nutné nakonfigurovat aplikace ke shromažďování těchto informací. Další informace najdete v tématu [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Log Analytics ze služby Application Insights přijme data, jakmile je k dispozici.

## <a name="output-data"></a>Výstupní data

Záznam s *typem* *ApplicationInsights* se vytvoří pro každý typ vstupních dat. ApplicationInsights záznamy mají vlastnosti, které jsou uvedené v následujících částech:

### <a name="generic-fields"></a>Obecné pole

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Čas záznamu |
| ApplicationId | Instrumentační klíč Application Insights aplikace |
| ApplicationName | Název služby Application Insights aplikaci |
| RoleInstance | ID hostitelského serveru |
| DeviceType | Klientské zařízení |
| ScreenResolution |   |
| Kontinent | Kontinent původu žádosti |
| Země | Země nebo oblast, kde žádost pochází |
| Kraj | Provincie, stavu nebo národní prostředí původu žádosti |
| Město | Město nebo obec původu žádosti |
| isSynthetic | Určuje, zda byla vytvořena uživatelem nebo automatizované metodou. True = automatizovaná metoda nebo false = generovaná uživatelem |
| SamplingRate | Procento telemetrii generovanou sady SDK, která je odeslána na portál. V rozsahu od 0,0 100.0. |
| SampledCount | 100/(SamplingRate). Například 4 =&gt; 25% |
| Ověření identity | True nebo False |
| OperationID | Položky, které mají stejnou operaci ID se zobrazují jako související položky na portálu. Obvykle ID požadavku |
| ParentOperationID | ID nadřazené operace |
| OperationName |   |
| ID relace | Identifikátor GUID k jednoznačné identifikaci relace, ve kterém byla vytvořena |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Pole založené podle rámce dostupnosti

| Vlastnost | Popis |
| --- | --- |
| TelemetryType | Dostupnost |
| AvailabilityTestName | Název webového testu |
| AvailabilityRunLocation | Geografické zdroj požadavku http |
| AvailabilityResult | Označuje úspěšný výsledek webového testu |
| AvailabilityMessage | Zpráva připojené do webového testu |
| AvailabilityCount | 100 /(Sampling Rate). Například 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 nebo 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Například 4 =&gt; 25% |
| AvailabilityDuration | Doba v milisekundách, trvání webového testu |
| AvailabilityDurationCount | 100 /(Sampling Rate). Například 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Jedinečný identifikátor GUID pro webový test |
| AvailabilityTimestamp | Přesné časové razítko testu dostupnosti |
| AvailabilityDurationMin | Vzorky záznamů toto pole ukazuje minimální webového testu doba trvání (milisekundy) reprezentována datových bodů |
| AvailabilityDurationMax | Vzorky záznamů toto pole ukazuje maximální webového testu doba trvání (milisekundy) reprezentována datových bodů |
| AvailabilityDurationStdDev | Vzorky záznamů toto pole ukazuje směrodatnou odchylku mezi všechny webové dob trvání testů (milisekundy) na základě zastoupené datových bodů |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Pole specifické pro výjimky

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Výjimka |
| ExceptionType | Typ výjimky |
| ExceptionMethod | Metoda, která se vytvoří výjimka |
| ExceptionAssembly | Sestavení obsahuje rozhraní framework a verzi a token veřejného klíče |
| ExceptionGroup | Typ výjimky |
| ExceptionHandledAt | Označuje úroveň, která zpracovává výjimku |
| ExceptionCount | 100 /(Sampling Rate). Například 4 =&gt; 25% |
| ExceptionMessage | Zpráva o výjimce |
| Zásobník výjimky | Plnohodnotných výjimky |
| ExceptionHasStack | Hodnota TRUE, pokud výjimka obsahuje zásobník |



### <a name="request-specific-fields"></a>Pole specifické pro žádost

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Žádost |
| ResponseCode | Odpovědi HTTP odeslané do klienta |
| RequestSuccess | Indikuje úspěch nebo neúspěch. True nebo false. |
| RequestID | ID k jednoznačné identifikaci žádosti |
| RequestName | GET/POST + základ adresy URL |
| RequestDuration | Čas v sekundách, doba trvání žádosti |
| URL | Adresa URL požadavku nezahrnuje hostitele |
| Hostitel | Hostitele webového serveru |
| URLBase musí | Úplná adresa URL požadavku |
| ApplicationProtocol | Typ protokolu používaný aplikace |
| RequestCount | 100 /(Sampling Rate). Například 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Například 4 =&gt; 25% |
| RequestDurationMin | Vzorky záznamů toto pole ukazuje minimální požadavek doba trvání (MS) pro zastoupené datových bodů. |
| RequestDurationMax | Vzorky záznamů toto pole ukazuje maximální doba trvání požadavku (milisekundy) na základě zastoupené datových bodů |
| RequestDurationStdDev | Vzorky záznamů toto pole ukazuje směrodatnou odchylku mezi všech dob trvání požadavku (milisekundy) na základě zastoupené datových bodů |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Toto řešení nemá sadu ukázky hledání v protokolech zobrazeny na řídicím panelu. Ukázky dotazů na prohledávání protokolu s popisy se ale zobrazují v části [zobrazení Application Insights Connector informace](#view-application-insights-connector-information) .

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
Tento skript vyžaduje ověřovací token nosiče pro ověřování proti Azure Active Directory. Jedním ze způsobů, jak tento token načíst, je použití článku na [webu dokumentace REST API](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Klikněte na **vyzkoušet** a přihlaste se k předplatnému Azure. Z **náhledu žádosti** můžete zkopírovat nosný token, jak je znázorněno na následujícím obrázku.


![Nosný token](media/app-insights-connector/bearer-token.png)


Seznam aplikací můžete také načíst pomocí dotazu protokolu:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Další kroky

- K zobrazení podrobných informací o aplikacích Application Insights použijte [hledání v protokolu](../../azure-monitor/log-query/log-query-overview.md) .

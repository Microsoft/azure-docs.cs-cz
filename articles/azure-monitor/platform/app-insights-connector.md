---
title: Zobrazení dat aplikace Azure Application Insights | Dokumenty společnosti Microsoft
description: Pomocí řešení Application Insights Connector můžete diagnostikovat problémy s výkonem a pochopit, co uživatelé dělají s vaší aplikací, když jsou sledováni pomocí Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665149"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Řešení pro správu konektorů Application Insights (zastaralé)

![Symbol Přehledy aplikací](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> S podporou [dotazů napříč prostředky](../../azure-monitor/log-query/cross-workspace-query.md)již není potřeba řešení pro správu konektoru Application Insights. Byl zastaralá a odebrána z Azure Marketplace, spolu s portálem OMS, který byl oficiálně zastaralé v lednu 15, 2019 pro komerční cloud Azure. 30. března 2019 se vypoučí pro cloud Azure US Government.
>
>Stávající připojení budou fungovat až do 30.6.2019.  S oms portálu vyřazení neexistuje žádný způsob, jak nakonfigurovat a odebrat existující připojení z portálu. Skript o použití PowerShellu k odebrání existujících připojení najdete v tématu [Odebrání konektoru pomocí PowerShellu](#removing-the-connector-with-powershell) níže.
>
>Pokyny k dotazování dat protokolu Application Insights pro více aplikací najdete v [tématu Unify více prostředků Azure Monitor Application Insights](../log-query/unify-app-resource-data.md). Další informace o vyřazení portálu OMS najdete v tématu [oms portál u přechodu na Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Řešení Applications Insights Connector vám pomůže diagnostikovat problémy s výkonem a pochopit, co uživatelé dělají s vaší aplikací, když je monitorována pomocí [Application Insights](../../azure-monitor/app/app-insights-overview.md). Zobrazení stejné telemetrie aplikace, které vývojáři vidí v Application Insights jsou k dispozici v Log Analytics. Když ale integrujete aplikace Application Insights s Log Analytics, viditelnost vašich aplikací se zvýší tím, že máte provozní a aplikační data na jednom místě. Stejná zobrazení vám pomohou spolupracovat s vývojáři aplikací. Společná zobrazení mohou zkrátit dobu zjišťování a řešení problémů s aplikací i platformou.

Při použití řešení můžete:

- Zobrazení všech aplikací Application Insights na jednom místě, i když jsou v různých předplatných Azure
- Korelovat data infrastruktury s daty aplikací
- Vizualizace dat aplikací s perspektivami při hledání v protokolu
- Přetočte se z dat Log Analytics do aplikace Application Insights na webu Azure Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Připojené zdroje

Na rozdíl od většiny ostatních řešení Log Analytics se data neshromažďují pro konektor Application Insights agenty. Všechna data používaná řešením pocházejí přímo z Azure.

| Připojený zdroj | Podporuje se | Popis |
| --- | --- | --- |
| [Agenti systému Windows](../../azure-monitor/platform/agent-windows.md) | Ne | Řešení neshromažďuje informace od agentů systému Windows. |
| [Agenti systému Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace od agentů Linuxu. |
| [Skupina pro správu SCOM](../../azure-monitor/platform/om-agents.md) | Ne | Řešení neshromažďuje informace od agentů v připojené skupině pro správu SCOM. |
| [Účet služby Azure Storage](collect-azure-metrics-logs.md) | Ne | Řešení nezkresluje informace z úložiště Azure. |

## <a name="prerequisites"></a>Požadavky

- Chcete-li získat přístup k informacím o konektoru Application Insights, musíte mít předplatné Azure
- Musíte mít alespoň jeden nakonfigurovaný prostředek Application Insights.
- Musíte být vlastníkem nebo přispěvatelem prostředku Application Insights.

## <a name="configuration"></a>Konfigurace

1. Povolte řešení Azure Web Apps Analytics z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) nebo pomocí procesu popsaného v [řešeních Add Log Analytics z Galerie řešení](../../azure-monitor/insights/solutions.md).
2. Přejděte na [portál Azure](https://portal.azure.com). Vyberte **Všechny služby,** chcete-li otevřít Application Insights. Potom vyhledejte Application Insights. 
3. V **části Předplatná**vyberte předplatné, které má prostředky Application Insights, a potom v části **Název**vyberte jednu nebo více aplikací.
4. Klikněte na **Uložit**.

Přibližně za 30 minut budou data dostupná a dlaždice Přehledy aplikací se aktualizují o data, například na následujícím obrázku:

![Dlaždice Přehledy aplikací](./media/app-insights-connector/app-insights-tile.png)

Další body, které je třeba mít na paměti:

- Aplikace Application Insights můžete propojit jenom s jedním pracovním prostorem Log Analytics.
- Můžete propojit pouze [základní nebo podnikové aplikace Insights prostředky](https://azure.microsoft.com/pricing/details/application-insights) Log Analytics. Můžete však použít úroveň Free log analytics.

## <a name="management-packs"></a>Sady Management Pack

Toto řešení neinstaluje žádné sady Management Pack v připojených skupinách pro správu.

## <a name="use-the-solution"></a>Použijte řešení

Následující části popisují, jak můžete pomocí listů zobrazených na řídicím panelu Application Insights zobrazit data z vašich aplikací a pracovat s nimi.

### <a name="view-application-insights-connector-information"></a>Zobrazit informace o konektoru Application Insights

Kliknutím na dlaždici **Application Insights** otevřete řídicí panel **Application Insights** a podívejte se na následující okna.

![Řídicí panel Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Řídicí panel Application Insights](./media/app-insights-connector/app-insights-dash02.png)

Přístrojová deska obsahuje čepele zobrazené v tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Můžete spustit hledání protokolu, který vrátí všechny záznamy po klepnutí na tlačítko **Zobrazit vše** v dolní části okna nebo po klepnutí na záhlaví okna.


| **Sloupec** | **Popis** |
| --- | --- |
| Aplikace - Počet žádostí | Zobrazuje počet aplikací v aplikačních prostředcích. Také uvádí názvy aplikací a pro každý počet záznamů aplikací. Kliknutím na číslo spustíte hledání protokolu.<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kliknutím na název aplikace spustíte hledání protokolu pro aplikaci, která zobrazuje záznamy aplikací na hostitele, záznamy podle typu telemetrie a všechna data podle typu (na základě posledního dne). |
| Objem dat – hostitelé odesílající data | Zobrazuje počet hostitelů počítačů, kteří odesílají data. Také uvádí hostitele počítače a počet záznamů pro každého hostitele. Kliknutím na číslo spustíte hledání protokolu.<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kliknutím na název počítače spustíte hledání protokolu hostitele, který zobrazuje záznamy aplikací na hostitele, záznamy podle typu telemetrie a všechna data podle typu (na základě posledního dne). |
| Dostupnost – výsledky webtestu | Zobrazuje prstencový graf pro výsledky webových testů, který označuje průchod nebo selhání. Kliknutím na graf spustíte hledání protokolu.<code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Výsledky ukazují počet průchodů a selhání pro všechny testy. Zobrazuje všechny webové aplikace s provozem na poslední chvíli. Kliknutím na název aplikace zobrazíte hledání protokolu zobrazující podrobnosti neúspěšných webových testů. |
| Požadavky serveru – požadavky za hodinu | Zobrazuje spojnicový graf požadavků serveru za hodinu pro různé aplikace. Najeďte přes čáru v grafu a podívejte se na 3 nejlepší aplikace, které přijímají požadavky na bod v čase. Zobrazí také seznam žádostí přijímajících žádosti a počet žádostí pro vybrané období. <br><br>Klepnutím na graf spustíte <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> hledání protokolu, které zobrazuje podrobnější spojnicový graf požadavků serveru za hodinu pro různé aplikace. <br><br> Klepnutím na aplikaci v seznamu spustíte hledání <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> protokolu, které zobrazuje seznam požadavků, grafy pro požadavky v průběhu času a dobu trvání požadavku a seznam kódů odpovědí na požadavky.   |
| Selhání – neúspěšné požadavky za hodinu | Zobrazuje spojnicový graf neúspěšných požadavků aplikací za hodinu. Najeďte přes graf a podívejte se na 3 nejlepší aplikace s neúspěšnými požadavky na bod v čase. Zobrazí také seznam aplikací s počtem neúspěšných požadavků pro každý. Klepnutím na graf spustíte <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> hledání protokolu, které zobrazuje podrobnější spojnicový graf neúspěšných požadavků aplikací. <br><br>Klepnutím na položku v seznamu spustíte vyhledávání <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> protokolů, které zobrazuje neúspěšné požadavky, grafy neúspěšných požadavků v průběhu času a dobu trvání požadavku a seznam kódů odpovědí na neúspěšné požadavky. |
| Výjimky – výjimky za hodinu | Zobrazuje spojnicový graf výjimek za hodinu. Najeďte přes graf a podívejte se na 3 nejlepší aplikace s výjimkami pro bod v čase. Zobrazí také seznam aplikací s počtem výjimek pro každou z nich. Klepnutím na graf spustíte <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> hledání protokolu, které zobrazuje podrobnější graf výjimek. <br><br>Klepnutím na položku v seznamu spustíte hledání <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> protokolu, které zobrazuje seznam výjimek, grafy výjimek v průběhu času a neúspěšných požadavků a seznam typů výjimek.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Zobrazení perspektivy Přehledy aplikací pomocí hledání protokolu

Když kliknete na libovolnou položku v řídicím panelu, zobrazí se ve vyhledávání perspektiva Application Insights. Perspektiva poskytuje rozšířenou vizualizaci založenou na vybraném typu telemetrie. Takže změny obsahu vizualizace pro různé typy telemetrie.

Když klepnete na libovolné místo v okně Aplikace, zobrazí se výchozí **perspektiva Aplikace.**

![Perspektiva aplikací Přehledy aplikací](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektiva zobrazuje přehled vybrané aplikace.

Okno **Dostupnost** zobrazuje jiné perspektivní zobrazení, kde můžete zobrazit výsledky webových testů a související neúspěšné požadavky.

![Perspektiva dostupnosti přehledů aplikací](./media/app-insights-connector/availability-blade-drill-search.png)

Když klepnete na libovolné místo v okně **Požadavky na server** nebo **selhání,** perspektivní součásti se změní, aby vám poskytly vizualizaci související s požadavky.

![Okno Selhání přehledů aplikací](./media/app-insights-connector/server-requests-failures-drill-search.png)

Když kliknete na libovolné místo v okně **Výjimky,** zobrazí se vizualizace, která je přizpůsobená výjimkám.

![Okno Výjimky přehledů aplikací](./media/app-insights-connector/exceptions-blade-drill-search.png)

Bez ohledu na to, zda klepnete na něco z jednoho panelu **Application Insights Connector,** v rámci samotné stránky **Hledání,** všechny dotazy vracející data Application Insights zobrazí perspektivu Application Insights. Pokud například prohlížíte data Application Insights, zobrazí **se**&#42;dotaz také kartu perspektivy, jako je následující obrázek:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Komponenty perspektivy jsou aktualizovány v závislosti na vyhledávacím dotazu. To znamená, že výsledky můžete filtrovat pomocí libovolného vyhledávacího pole, které umožňuje zobrazit data z:

- Všechny vaše aplikace
- Jedna vybraná aplikace
- Skupina žádostí

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Přeplužte k aplikaci na webu Azure Portal

Blades Konektor Application Insights jsou navrženy tak, aby vám umožnily přetoč na vybranou aplikaci Application Insights *při použití portálu Azure*. Řešení můžete použít jako platformu monitorování na vysoké úrovni, která vám pomůže řešit potíže s aplikací. Když se v některé z připojených aplikací zobrazí potenciální problém, můžete do něj přejít k podrobnostem ve vyhledávání Log Analytics nebo se můžete obrátit přímo na aplikaci Application Insights.

Chcete-li se otočit, klepněte na tři tečky (**...**), které se zobrazí na konci každého řádku, a vyberte **Otevřít v přehledech aplikací**.

>[!NOTE]
>**Open in Application Insights** není na webu Azure Portal k dispozici.

![Otevřít v přehledech aplikací](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Ukázková data opravená

Application Insights poskytuje *[korekci vzorkování,](../../azure-monitor/app/sampling.md)* která pomáhá snížit přenos telemetrie. Když povolíte vzorkování v aplikaci Application Insights, získáte snížený počet položek uložených v Application Insights a v Log Analytics. Zatímco konzistence dat je zachována na stránce **Application Insights Connector** a perspektivy, měli byste ručně opravit vzorkovaná data pro vlastní dotazy.

Zde je příklad korekce vzorkování ve vyhledávacím dotazu protokolu:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Pole **Vzorek počet** je k dispozici ve všech položkách a zobrazuje počet datových bodů, které položka představuje. Pokud zapnete vzorkování pro aplikaci Application Insights, **ukázkový počet** je větší než 1. Chcete-li spočítat skutečný počet položek, které aplikace generuje, sečtete pole **Vzorkovaný počet.**

Vzorkování ovlivní pouze celkový počet položek, které aplikace generuje. Není nutné opravit vzorkování pro metrická pole, jako **je RequestDuration** nebo **AvailabilityDuration,** protože tato pole zobrazují průměr pro reprezentované položky.

## <a name="input-data"></a>Vstupní data

Řešení přijímá následující typy telemetrických dat z připojených aplikací Application Insights:

- Dostupnost
- Výjimky
- Žádosti
- Zobrazení stránek – Aby váš pracovní prostor mohl přijímat zobrazení stránek, je nutné nakonfigurovat aplikace tak, aby tyto informace shromažďovaly. Další informace naleznete v tématu [Zobrazení stránek](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Vlastní události – Aby váš pracovní prostor mohl přijímat vlastní události, musíte nakonfigurovat aplikace tak, aby tyto informace shromažďovaly. Další informace naleznete v tématu [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Data jsou přijímána log analytics z Application Insights, jakmile bude k dispozici.

## <a name="output-data"></a>Výstupní data

Pro každý typ vstupních dat je vytvořen záznam s *typem* *ApplicationInsights.* Záznamy ApplicationInsights mají vlastnosti zobrazené v následujících částech:

### <a name="generic-fields"></a>Obecná pole

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Čas záznamu |
| ApplicationId | Klíč instrumentace aplikace Application Insights |
| ApplicationName | Název aplikace Application Insights |
| Instance role | ID hostitele serveru |
| DeviceType | Klientské zařízení |
| Rozlišení obrazovky |   |
| Kontinent | Kontinent, kde žádost vznikla |
| Země | Země nebo oblast, odkud žádost vznikla |
| Provincii | Provincie, stát nebo národní prostředí, odkud žádost pochází |
| Město | Město nebo město, odkud žádost vznikla |
| isSyntetická | Označuje, zda byl požadavek vytvořen uživatelem nebo automatickou metodou. True = automatizovaná metoda nebo false = vygenerované uživatelem |
| Vzorkovací míra | Procento telemetrie generované sadou SDK, která je odeslána na portál. Rozsah 0,0-100,0. |
| SampledCount | 100/(Vzorkovací frekvence). Například 4&gt; = 25 % |
| IsAuthenticated | Pravda nebo nepravda |
| ID operace | Položky, které mají stejné ID operace jsou zobrazeny jako související položky na portálu. Obvykle ID žádosti |
| Id nadřazené operace | ID nadřazené operace |
| OperationName |   |
| SessionId | IDENTIFIKÁTOR GUID pro jednoznačnou identifikaci relace, kde byl požadavek vytvořen. |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Pole specifická pro dostupnost

| Vlastnost | Popis |
| --- | --- |
| TelemetrieTyp | Dostupnost |
| AvailabilityTestName | Název webového testu |
| AvailabilityRunLocation | Geografický zdroj požadavku http |
| Výsledek dostupnosti | Označuje výsledek úspěchu webového testu. |
| Zpráva o dostupnosti | Zpráva připojená k webovému testu |
| Počet dostupností | 100/(vzorkovací frekvence). Například 4&gt; = 25 % |
| Hodnota DataSizeMetric | 1,0 nebo 0,0 |
| Počet metrik DataSizeMetricCount | 100/(vzorkovací frekvence). Například 4&gt; = 25 % |
| DostupnostDoba trvání | Doba trvání webového testu v milisekundách |
| Počet dostupností | 100/(vzorkovací frekvence). Například 4&gt; = 25 % |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Jedinečný identifikátor GUID pro webový test |
| AvailabilityTimerazítko | Přesné časové razítko testu dostupnosti |
| Doba dostupnostim min | U vzorkovaných záznamů toto pole zobrazuje minimální dobu trvání webového testu (milisekund) pro reprezentované datové body. |
| DostupnostDobaMax | U vzorkovaných záznamů toto pole zobrazuje maximální dobu trvání webového testu (milisekund) pro reprezentované datové body. |
| DostupnostDobaStdDev | U vzorkovaných záznamů toto pole zobrazuje směrodatnou odchylku mezi všemi dobami trvání webového testu (milisekundy) pro reprezentované datové body. |
| DostupnostMin |   |
| DostupnostMax |   |
| DostupnostStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Pole specifická pro výjimku

| Typ | ApplicationInsights |
| --- | --- |
| TelemetrieTyp | Výjimka |
| Exceptiontype | Typ výjimky |
| ExceptionMethod | Metoda, která vytvoří výjimku |
| Sestava výjimek | Sestavení obsahuje rámec a verzi, stejně jako token veřejného klíče |
| Skupina výjimek | Typ výjimky |
| ExceptionHandledAt | Označuje úroveň, která zpracovala výjimku. |
| ExceptionCount | 100/(vzorkovací frekvence). Například 4&gt; = 25 % |
| Zpráva o výsaze | Zpráva o výjimce |
| Zásobník výjimek | Plný stoh výjimky |
| ExceptionHasStack | True, pokud má výjimka zásobník |



### <a name="request-specific-fields"></a>Pole specifická pro požadavek

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetrieTyp | Žádost |
| ResponseCode | Odpověď HTTP odeslaná klientovi |
| RequestSuccess | Označuje úspěch nebo neúspěch. True nebo false |
| ID požadavku | ID pro jednoznačnou identifikaci žádosti |
| Název_požadavku | GET/POST + URL základna |
| Doba trvání_požadavku | Doba trvání požadavku v sekundách |
| zprostředkovatele identity | ADRESA URL požadavku bez hostitele |
| Hostitel | Hostitel webového serveru |
| Základna adres URL | Úplná adresa URL žádosti |
| ApplicationProtocol | Typ protokolu používaného aplikací |
| RequestCount | 100/(vzorkovací frekvence). Například 4&gt; = 25 % |
| RequestDurationCount | 100/(vzorkovací frekvence). Například 4&gt; = 25 % |
| RequestDurationMin | U vzorkovaných záznamů toto pole zobrazuje minimální dobu trvání požadavku (milisekund) pro reprezentované datové body. |
| RequestDurationMax | U vzorkovaných záznamů toto pole zobrazuje maximální dobu trvání požadavku (milisekund) pro reprezentované datové body. |
| RequestDurationStdDev | U vzorkovaných záznamů toto pole zobrazuje směrodatnou odchylku mezi všemi dobami trvání požadavku (milisekundy) pro reprezentované datové body. |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Toto řešení nemá sadu hledání ukázkového protokolu zobrazené na řídicím panelu. Ukázkové vyhledávací dotazy protokolu s popisy jsou však zobrazeny v části [Zobrazit informace o konektoru přehledy aplikací.](#view-application-insights-connector-information)

## <a name="removing-the-connector-with-powershell"></a>Odebrání konektoru pomocí prostředí PowerShell
S oms portálu vyřazení neexistuje žádný způsob, jak nakonfigurovat a odebrat existující připojení z portálu. Existující připojení můžete odebrat pomocí následujícího skriptu prostředí PowerShell. Chcete-li provést tuto operaci, musíte být vlastníkem nebo přispěvatelem pracovního prostoru a čtenářem prostředku Application Insights.

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

Seznam aplikací můžete načíst pomocí následujícího skriptu prostředí PowerShell, který vyvolá volání rozhraní REST API. 

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
Tento skript vyžaduje ověřovací token nosiče pro ověřování ve službě Azure Active Directory. Jedním ze způsobů, jak načíst tento token, je použití článku v [konzumním webu rozhraní REST API](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Klikněte na **Vyzkoušet** a přihlaste se k předplatnému Azure. Token nosiče můžete zkopírovat z **náhledu požadavku,** jak je znázorněno na následujícím obrázku.


![Žeton na doručitele](media/app-insights-connector/bearer-token.png)


Můžete také načíst seznam aplikací pomocí dotazu protokolu:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Další kroky

- Pomocí [funkce Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) můžete zobrazit podrobné informace o aplikacích Application Insights.

---
title: Zobrazení dat aplikací Azure Application Insights | Dokumentace Microsoftu
description: Řešení Application Insights Connector můžete použít k diagnostice problémů s výkonem a pochopit, co uživatelé dělají s vaší aplikací při monitorovat pomocí Application Insights.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 4157b812fbb17b524a21814f41e0e4ec43e39e70
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042083"
---
# <a name="application-insights-connector-management-solution-preview"></a>Řešení správy Application Insights Connector (Preview)

![Application Insights symbol](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Díky podpoře [dotazy napříč prostředky](log-analytics-cross-workspace-search.md), řešení pro správu konektoru služby Application Insights se už nevyžaduje a přestanou používat. Od července se nebudou moct propojit nové prostředky Application Insights do pracovních prostorů Log Analytics. Existující odkazy a řídicí panely, budou nadále fungovat až do listopadu 2018. Další informace najdete v tématu [portálu OMS do Azure](log-analytics-oms-portal-transition.md).

Aplikace Insights Connector řešení vám pomůže diagnostikovat problémy s výkonem a pochopit, co uživatelé dělají s vaší aplikací při se monitoruje s [Application Insights](../application-insights/app-insights-overview.md). Zobrazení stejné aplikace telemetrická data, která vývojářům zobrazit ve službě Application Insights jsou k dispozici ve službě Log Analytics. Však při integraci vašich aplikací služby Application Insights s Log Analytics se zvýší viditelnost vaší aplikace tím, že data o operacích a aplikacích na jednom místě. S stejného zobrazení vám umožňuje spolupracovat s vývojáři vaší aplikace. Obecná zobrazení může pomoct snížit čas a vyřešte aplikace od problémů platformy.

Při použití řešení můžete:

- Zobrazit všechny vaše aplikace Application Insights na jednom místě, i když se nachází v různých předplatných Azure
- Korelujte data infrastruktury se data aplikací
- Vizualizace dat aplikace pomocí perspektiv v prohledávání protokolu
- Otáčení z dat Log Analytics do vaší aplikace Application Insights na webu Azure Portal

## <a name="connected-sources"></a>Připojené zdroje

Na rozdíl od většina jiných řešení Log Analytics data nejsou shromažďována pro Application Insights Connector agenty. Všechna data, která používá řešení pochází přímo z Azure.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne | Řešení neshromažďuje informace z agentů Windows. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Řešení neshromažďuje informace z agentů Linuxu. |
| [Skupiny pro správu SCOM](log-analytics-om-agents.md) | Ne | Řešení neshromažďuje informace z agentů v připojené skupině pro správu nástroje SCOM. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Toto řešení dělá není shromažďování informací ze služby Azure storage. |

## <a name="prerequisites"></a>Požadavky

- Přístup k informacím Application Insights Connector, musíte mít předplatné Azure
- Musí mít aspoň jeden nakonfigurované prostředku Application Insights.
- Musíte být vlastníkem nebo přispěvatelem prostředek služby Application Insights.

## <a name="configuration"></a>Konfigurace

1. Povolení řešení Azure Web Apps Analytics z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) nebo pomocí procesu popsaného v [přidání řešení Log Analytics z Galerie řešení](log-analytics-add-solutions.md).
2. Na portálu OMS klikněte na tlačítko **nastavení** &gt; **Data** &gt; **Application Insights**.
3. V části **vybrat odběr, který**, vyberte předplatné, které má prostředky Application Insights a pak v části **název_aplikace**, vyberte jednu nebo více aplikací.
4. Klikněte na **Uložit**.

Během přibližně 30 minut budou data k dispozici a na dlaždici služby Application Insights se aktualizuje s daty, jako na následujícím obrázku:

![Dlaždici služby Application Insights](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Ostatní body brát v úvahu:

- Aplikace Application Insights můžete propojit jenom k jednomu pracovnímu prostoru Log Analytics.
- Lze propojit pouze [Basic nebo Enterprise Application Insights prostředky](https://azure.microsoft.com/pricing/details/application-insights) ke službě Log Analytics. Můžete však použít úroveň Free služby Log Analytics.

## <a name="management-packs"></a>Sady Management Pack

Toto řešení není možné nainstalovat všechny sady management Pack v připojených skupin pro správu.

## <a name="use-the-solution"></a>Použití řešení

Následující části popisují, jak můžete okna zobrazené v řídicím panelu služby Application Insights k zobrazení a interakci s daty z vašich aplikací.

### <a name="view-application-insights-connector-information"></a>Zobrazit informace o Application Insights Connector

Klikněte na tlačítko **Application Insights** otevřete dlaždici **Application Insights** řídicího panelu zobrazíte následující okna.

![Řídicí panel Application Insights](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Řídicí panel Application Insights](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

Řídicí panel obsahuje listy v tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Můžete spustit prohledávání protokolu, který vrátí všechny záznamy, po kliknutí na **zobrazit všechny** v dolní části okna, nebo když kliknete na záhlaví okna.


| **Sloupec** | **Popis** |
| --- | --- |
| Aplikace – počet aplikací | Zobrazuje počet aplikací v prostředků aplikace. Také obsahuje seznam názvů aplikací a pro každou počet záznamů, aplikace. Kliknutím na příslušné číslo spustíte hledání v protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klikněte na název aplikace spustíte hledání v protokolu aplikace, který zobrazuje záznamy aplikace na hostitele, záznamy podle typu telemetrie a všechna data podle typu (založené na poslední den). |
| Objem dat – hostitele, kteří odesílají data | Zobrazuje počet počítač hostitele, kteří odesílají data. Obsahuje také seznam počítač hostuje a počet záznamů pro každého hostitele. Kliknutím na příslušné číslo spustíte hledání v protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klikněte na název počítače spustíte hledání v protokolu pro hostitele, který zobrazuje záznamy aplikace na hostitele, záznamy podle typu telemetrie a všechna data podle typu (založené na poslední den). |
| Dostupnost – výsledky webového testu | Ukazuje prstencového grafu pro výsledků testu webu, která dokončeno nebo chyba. Klikněte na graf na spustíte hledání v protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Výsledky zobrazit počet průchodů a neúspěchů pro všemi testy. Zobrazuje všechny webové aplikace se provoz za poslední minutu. Klikněte na název aplikace zobrazení hledání v protokolu s podrobnostmi o neúspěšných webové testy. |
| Požadavky serveru – žádosti za hodinu | Zobrazuje spojnicový graf požadavky serveru za hodinu pro různé aplikace. Najeďte myší na řádek v grafu pro zobrazení 3 nejpoužívanější aplikace přijímat žádosti do bodu v čase. Také zobrazuje seznam aplikací, které přijímají požadavky a počet požadavků pro vybrané období. <br><br>Klikněte na graf tak, aby spustíte hledání v protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , který se zobrazí podrobnější spojnicový graf požadavky serveru za hodinu pro různé aplikace. <br><br> Klikněte na tlačítko spustíte hledání v protokolu pro aplikaci v seznamu <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , která zobrazuje seznam žádostí, grafy pro žádostí v průběhu času a žádost o dobu trvání a seznam požadavek kódy odpovědí.   |
| Chyby – neúspěšné požadavky za hodinu | Zobrazuje spojnicový graf žádosti o selhání aplikace za hodinu. Najeďte myší na graf zobrazíte 3 nejpoužívanější aplikace s neúspěšné požadavky na bod v čase. Také zobrazuje seznam aplikací s počtem neúspěšných žádostí pro každý. Klikněte na graf na spustíte hledání v protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , který se zobrazí podrobnější spojnicový graf žádosti o aplikace se nezdařilo. <br><br>Klikněte na položku v seznamu a spustíte hledání v protokolu pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , ukazuje neúspěšné požadavky, grafy pro převzetí služeb při selhání žádosti o času a žádost o době trvání a seznam kódů odpovědí chybných požadavků. |
| Výjimky – výjimky za hodinu | Zobrazuje spojnicový graf výjimek za hodinu. Najeďte myší na graf zobrazíte 3 nejpoužívanější aplikace s výjimkami bodu v čase. Také zobrazuje seznam aplikací s počtem výjimky pro každý. Klikněte na graf na spustíte hledání v protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , která zobrazuje podrobnější odkaz grafu výjimek. <br><br>Klikněte na položku v seznamu a spustíte hledání v protokolu pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> , která zobrazuje seznam výjimek, grafy pro výjimky v průběhu času i neúspěšné požadavky a seznam typů výjimek.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Zobrazení perspektivy Application Insights s prohledáváním protokolů

Po kliknutí na libovolnou položku na řídicím panelu zobrazí perspektivy Application Insights zobrazí v hledání. Perspektiva poskytuje rozšířené vizualizace, podle typu telemetrie, které vybrali. Ano, vizualizace změny obsahu pro typy jiné telemetrie.

Po kliknutí na libovolné místo v okně aplikace, se zobrazí výchozí **aplikací** perspektivy.

![Application Insights aplikace perspektivy](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

Perspektivy ukazuje přehled aplikace, kterou jste vybrali.

**Dostupnosti** okno se teď zobrazují různé perspektivy ve kterém uvidíte výsledků testu webu a související neúspěšných žádostí.

![Application Insights dostupnost perspektivy](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

Po kliknutí na libovolné místo v **požadavky serveru** nebo **selhání** oken, součástí perspektivy změna umožňují vizualizaci, která související s požadavky.

![Application Insights selhání okno](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

Po kliknutí na libovolné místo v **výjimky** okně zobrazí vizualizaci, která je vytvořený na míru k výjimkám.

![Okno Application Insights výjimky](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Bez ohledu na to, zda klepnutí na něco **Application Insights Connector** řídicího panelu, v rámci **hledání** stránce samostatně, jakýkoli dotaz vrací data Application Insights zobrazí aplikace Insights perspektivy. Například, pokud se vám zobrazuje data Application Insights **&#42;** dotaz také zobrazí na kartě perspektivy jako na následujícím obrázku:

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Součástí perspektivy jsou aktualizovány v závislosti na vyhledávací dotaz. To znamená, že výsledky můžete filtrovat pomocí vyhledávacího pole, která vám dává možnost vidět data z:

- Všechny aplikace
- Jeden vybrané aplikace
- Skupiny aplikací

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Uveďte do aplikace na webu Azure Portal

Application Insights Connector okna jsou určené k usnadnění a přesouvat na vybranou aplikaci Application Insights se tak *při použití na portálu OMS*. Řešení můžete použít jako základní monitorovací platformě, která pomáhá při řešení problémů aplikace. Až uvidíte potenciální problém v některé z připojených aplikací, můžete buď přejít k podrobnostem ho ve službě Log Analytics search nebo můžete vytvořit kontingenční přímo do aplikace Application Insights.

Pro otáčení, klikněte na symbol tří teček (**...** ), který se zobrazí na konci každého řádku a vyberte **otevřít ve službě Application Insights**.

>[!NOTE]
>**Otevřít ve službě Application Insights** není k dispozici na webu Azure Portal.

![Otevřít v Application Insights](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Opravit ukázková data

Application Insights poskytuje *[vzorkování opravy](../application-insights/app-insights-sampling.md)* ke snížení provozu telemetrie. Když povolíte vzorkování na své aplikaci Application Insights, získat menší počet položek uložených ve službě Application Insights a Log Analytics. Zatímco se zachovají konzistenci dat. v **Application Insights Connector** stránky a perspektivy, by měl ručně opravit jen Vzorkovaná data pro své vlastní dotazy.

Tady je příklad opravy vzorkování vyhledávacího dotazu protokolu:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**Vzorkovány počet** pole je k dispozici ve všech položek a zobrazuje počet datových bodů, které představuje položku. Pokud aplikace Application Insights, zapnout vzorkování **Vzorkovány počet** je větší než 1. Zjistit skutečný počet položek, které vaše aplikace generuje součet **Vzorkovány počet** pole.

Vzorkování ovlivňuje jenom celkový počet záznamů, které vaše aplikace generuje. Není nutné opravit vzorkování pro metriku pole, jako jsou **RequestDuration** nebo **AvailabilityDuration** vzhledem k tomu, že tato pole Zobrazit průměr pro zastoupené položky.

## <a name="input-data"></a>Vstupní data

Řešení přijímá následující typy dat telemetrie z připojených aplikací služby Application Insights:

- Dostupnost
- Výjimky
- Požadavky
- Zobrazení stránek – pro váš pracovní prostor pro příjem zobrazení stránek, je nutné nakonfigurovat aplikace ke shromažďování těchto informací. Další informace najdete v článku [zobrazení stránky](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Vlastní události – pro vašeho pracovního prostoru pro příjem vlastní události, je nutné nakonfigurovat aplikace ke shromažďování těchto informací. Další informace najdete v článku [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Log Analytics ze služby Application Insights přijme data, jakmile je k dispozici.

## <a name="output-data"></a>Výstupní data

Záznam s *typ* z *ApplicationInsights* se vytvoří pro každý typ vstupní data. ApplicationInsights záznamy mají vlastnosti, které jsou uvedené v následujících částech:

### <a name="generic-fields"></a>Obecné pole

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| Když |   |
| TimeGenerated | Čas záznamu |
| ApplicationId | Instrumentační klíč Application Insights aplikace |
| ApplicationName | Název služby Application Insights aplikaci |
| Instance role | ID hostitelského serveru |
| deviceType | Klientské zařízení |
| ScreenResolution |   |
| Kontinent | Kontinent původu žádosti |
| Země | Země původu žádosti |
| Kraj | Provincie, stavu nebo národní prostředí původu žádosti |
| Město | Město nebo obec původu žádosti |
| isSynthetic | Určuje, zda byla vytvořena uživatelem nebo automatizované metodou. True = generovaným uživateli nebo false = automatizované – metoda |
| SamplingRate | Procento telemetrii generovanou sady SDK, která je odeslána na portál. V rozsahu od 0,0 100.0. |
| SampledCount | 100/(SamplingRate). Například, 4 =&gt; 25 % |
| Ověření identity | True nebo False |
| ID operace | Položky, které mají stejnou operaci ID se zobrazují jako související položky na portálu. Obvykle ID požadavku |
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
| AvailabilityCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| DataSizeMetricValue | 1.0 nebo 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| AvailabilityDuration | Doba v milisekundách, trvání webového testu |
| AvailabilityDurationCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
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
| ExceptionCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
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
| ID žádosti | ID k jednoznačné identifikaci žádosti |
| RequestName | GET/POST + základ adresy URL |
| RequestDuration | Čas v sekundách, doba trvání žádosti |
| zprostředkovatele identity | Adresa URL požadavku nezahrnuje hostitele |
| Hostitel | Hostitele webového serveru |
| URLBase musí | Úplná adresa URL požadavku |
| ApplicationProtocol | Typ protokolu používaný aplikace |
| RequestCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| RequestDurationCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| RequestDurationMin | Vzorky záznamů toto pole ukazuje minimální požadavek doba trvání (MS) pro zastoupené datových bodů. |
| RequestDurationMax | Vzorky záznamů toto pole ukazuje maximální doba trvání požadavku (milisekundy) na základě zastoupené datových bodů |
| RequestDurationStdDev | Vzorky záznamů toto pole ukazuje směrodatnou odchylku mezi všech dob trvání požadavku (milisekundy) na základě zastoupené datových bodů |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Toto řešení nemá sadu ukázky hledání v protokolech zobrazeny na řídicím panelu. Ale ukázkové dotazy prohledávání protokolu s popisy jsou uvedeny v [zobrazení Application Insights Connector informace](#view-application-insights-connector-information) oddílu.

## <a name="next-steps"></a>Další postup

- Použití [prohledávání protokolů](log-analytics-log-searches.md) k zobrazení podrobných informací pro vaše aplikace Application Insights.

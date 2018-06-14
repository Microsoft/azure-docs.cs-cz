---
title: Zobrazení dat aplikací Azure Application Insights | Microsoft Docs
description: Řešení Application Insights konektor slouží k diagnostice problémů s výkonem a pochopit, co uživatelé dělají s vaší aplikací, pokud monitorované pomocí Application Insights.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: magoedte
ms.openlocfilehash: 854ec70c897b6a561fdec056228f82ccec3ae16c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30186229"
---
# <a name="application-insights-connector-management-solution-preview"></a>Řešení správy konektor služby Statistika aplikací (Preview)

![Application Insights symbol](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

Konektor služby Statistika aplikací řešení umožňuje diagnostikovat problémy s výkonem a pochopit, co uživatelé dělají s vaší aplikací když je monitorovaný s [Application Insights](../application-insights/app-insights-overview.md). Zobrazení stejná telemetrická data aplikací, které vývojáři zobrazit ve službě Application Insights jsou k dispozici v analýzy protokolů. Při integraci aplikace Application Insights s analýzy protokolů, zda se aplikace zvýšit tak, že data operace a aplikace na jednom místě. Má stejné zobrazení umožňuje spolupracovat s vývojáři vaší aplikace. Obecná zobrazení může pomoci zkrátit čas a vyřešte aplikace a problém s platformou.

Pokud použijete řešení, můžete:

- Zobrazit všechny aplikace služby Application Insights na jednom místě, i když se nachází v různých předplatných Azure
- Korelaci dat infrastruktury se data aplikací
- Vizualizovat data aplikací s perspektivami v hledání protokolů
- Otáčení z analýzy protokolů dat do aplikace Application Insights na portálu Azure

## <a name="connected-sources"></a>Připojené zdroje

Na rozdíl od většině ostatních řešení pro analýzu protokolu není data shromážděná pro konektor služby Statistika aplikace pomocí agentů. Všechna data, která používá řešení pochází přímo z Azure.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne | Řešení neshromažďuje informace z agentů v systému Windows. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Řešení neshromažďuje informace od agentů systému Linux. |
| [Skupiny správy nástroje SCOM](log-analytics-om-agents.md) | Ne | Řešení neshromažďuje informace od agentů v připojené skupině pro správu SCOM. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Řešení nemá shromažďování informací z úložiště Azure. |

## <a name="prerequisites"></a>Požadavky

- Pro přístup k informacím konektor služby Statistika aplikace, musíte mít předplatné Azure
- Musí mít alespoň jeden nakonfigurované prostředek Application Insights.
- Musí být vlastníka nebo přispěvatele daného prostředku Application Insights.

## <a name="configuration"></a>Konfigurace

1. Povolit řešení Azure Web Apps Analytics z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).
2. Na portálu OMS, klikněte na tlačítko **nastavení** &gt; **Data** &gt; **Application Insights**.
3. V části **vybrat odběr, který**, vyberte odběr, který má prostředky Application Insights a pak v části **název aplikace**, vyberte jednu nebo více aplikací.
4. Klikněte na **Uložit**.

V přibližně 30 minut bude k dispozici data a dlaždici Application Insights se aktualizuje s daty, jako na následujícím obrázku:

![Dlaždice Application Insights](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Ostatní body mějte na paměti:

- Aplikace služby Application Insights můžete propojit jenom jeden pracovní prostor analýzy protokolů.
- Můžete propojit pouze [Basic nebo Enterprise Application Insights prostředků](https://azure.microsoft.com/pricing/details/application-insights) k analýze protokolů. Můžete však použít úroveň Free Log Analytics.

## <a name="management-packs"></a>Sady Management Pack

Toto řešení nenainstaluje žádné sady management Pack v připojených skupin pro správu.

## <a name="use-the-solution"></a>Použít řešení

Následující části popisují, jak můžete okna na řídicím panelu Application Insights k zobrazení a interakci s daty z vašich aplikací.

### <a name="view-application-insights-connector-information"></a>Zobrazení informací o konektor služby Statistika aplikace

Klikněte **Application Insights** dlaždici otevřete **Application Insights** řídicí panel zobrazíte následující okna.

![Přehledný řídicí panel aplikací](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Přehledný řídicí panel aplikací](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

Řídicí panel obsahuje okna uvedené v tabulce. Každý okno uvádí až 10 položky odpovídající kritériím tohoto okna pro zadaný obor a časový rozsah. Můžete spustit hledání protokolů, která vrací všechny záznamy, po kliknutí na tlačítko **zobrazit všechny** v dolní části okna, nebo když kliknete na záhlaví okna.

[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **Sloupec** | **Popis** |
| --- | --- |
| Aplikace – počet aplikací | Zobrazuje počet aplikací v prostředky aplikace. Také uvádí názvy aplikací a pro každou počet záznamů aplikace. Klikněte na číslo ke spuštění vyhledávání protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klikněte na název aplikace ke spuštění vyhledávání protokolu pro aplikaci, která zobrazuje aplikace záznamů na hostitele, záznamy podle typu telemetrie a všechna data podle typu (na základě poslední den). |
| Datový svazek – hostitelů odeslání dat | Zobrazuje počet počítač hostitele, kteří odesílají data. Také uvádí hostitele počítače a počet záznamů pro každého hostitele. Klikněte na číslo ke spuštění vyhledávání protokolu pro <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klikněte na název počítače, spusťte hledání protokolů pro hostitele, který zobrazuje aplikace záznamů na hostitele, záznamy podle typu telemetrie a všechna data podle typu (na základě poslední den). |
| Dostupnost – výsledky testu webu | Zobrazí prstencový graf pro web výsledky testů, označující průchodu nebo selhání. Klikněte na graf ke spuštění vyhledávání protokolu pro <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Výsledky zobrazit počet průchodů a selhání pro všechny testy. Zobrazuje všechny webové aplikace s přenosy dat za poslední minutu. Klikněte na název aplikace zobrazíte vyhledávání protokolu s podrobnostmi o testy webu se nezdařilo. |
| Požadavky serveru – počet požadavků za hodinu | Zobrazí graf řádku požadavků serveru za hodinu pro různé aplikace. Najeďte myší na řádek v grafu pro zobrazení 3 hlavních aplikace přijímá požadavky na bod v čase. Také ukazuje seznam aplikací, které přijímá požadavky a počet požadavků pro vybrané období. <br><br>Klikněte na graf tak, aby spuštění protokolu vyhledejte <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> který ukazuje podrobnější spojnicový graf požadavků serveru za hodinu pro různé aplikace. <br><br> Klikněte na tlačítko Spustit hledání protokolů pro aplikaci v seznamu <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , obsahuje seznam požadavků, grafy pro požadavky na čas a žádost o dobu trvání a seznam požadavek kódů odpovědi.   |
| Chyby – neúspěšné požadavky za hodinu | Zobrazí graf řádku žádostí o selhání aplikace za hodinu. Najeďte na graf zobrazíte hlavních 3 aplikací s neúspěšných požadavků pro bod v čase. Také ukazuje seznam aplikací s počtem neúspěšných požadavků pro každou. Klikněte na graf ke spuštění protokolu vyhledejte <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> který ukazuje podrobnější spojnicový graf požadavků na aplikaci, která selhala. <br><br>Klikněte na položku v seznamu a spusťte hledání protokolů pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , ukazuje neúspěšné požadavky, grafy pro neúspěšné požadavky přes čas a žádost o doba trvání a seznam kódů odpovědi chybných požadavků. |
| Výjimky – výjimky za hodinu | Zobrazí graf řádku výjimek za hodinu. Najeďte na graf zobrazíte hlavních 3 aplikací s výjimky pro bod v čase. Také ukazuje seznam aplikací s počet výjimek pro každou. Klikněte na graf ke spuštění protokolu vyhledejte <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> , zobrazí podrobnější graf odkaz výjimek. <br><br>Klikněte na položku v seznamu a spusťte hledání protokolů pro <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> , zobrazí se seznam výjimek, grafy pro výjimky přes čas a k selhání požadavků a seznam typů výjimek.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Zobrazení perspektivy Application Insights s hledání protokolů

Když kliknete na libovolnou položku v řídicím panelu, zobrazí Application Insights perspektivy uvedené v hledání. Perspektivy poskytuje rozšířené vizualizace, v závislosti na typu telemetrie, které vybrali. Ano, vizualizace změny obsahu pro jiné telemetrie typy.

Když v okně aplikace kliknete na libovolné místo, se zobrazí výchozí **aplikace** perspektivy.

![Application Insights aplikace perspektivy](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

Perspektivy ukazuje přehled aplikace, která jste vybrali.

**Dostupnosti** okno ukazuje různé perspektivy zobrazení, kde můžete zobrazit výsledky testů webových a související neúspěšných požadavků.

![Perspektiva statistice dostupnosti aplikace](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

Když kliknete na libovolné místo v **požadavků serveru** nebo **selhání** oken, komponenty perspektivy změnit tak, abyste získali vizualizace, který týkajících se žádostí o.

![Okno statistiky selhání aplikace](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

Když kliknete na libovolné místo v **výjimky** okně uvidíte vizualizace, přesně podle výjimky.

![Okna Statistika výjimky aplikací](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Bez ohledu na to, jestli klepnutí na něco **konektor služby Statistika aplikace** řídicího panelu, v **vyhledávání** samostatně, stránka jakýkoli dotaz vrací Application Insights data zobrazují perspektivy Application Insights. Pokud se nacházíte Application Insights data, například **&#42;** dotaz také zobrazí na kartě perspektivy jako na následujícím obrázku:

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Perspektivy součásti jsou aktualizovány v závislosti na vyhledávací dotaz. To znamená, že výsledky můžete filtrovat pomocí jakékoli vyhledávací pole, které vám umožní zobrazit data z:

- Všechny aplikace
- Jediné vybrané aplikace
- Skupinu aplikací

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Otáčení do aplikace v portálu Azure

Konektor služby Statistika oken aplikace jsou navržené tak, abyste otáčení k vybrané aplikace služby Application Insights *při použití portálu OMS*. Řešení můžete použít jako základní monitorování platformu, která vám pomůže vyřešit aplikace. Když se na potenciální problém v některé z vašich připojených aplikací, můžete buď přejít k podrobnostem je v Log Analytics vyhledávání nebo můžete vytvořit kontingenční přímo do aplikace Application Insights.

Chcete-li otáčení, klikněte na symbol tří teček (**...** ), zobrazí se na konci každého řádku a vyberte **otevřete ve službě Application Insights**.

>[!NOTE]
>**Otevřete ve službě Application Insights** není k dispozici na portálu Azure.

![Otevřete ve službě Application Insights](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Opravě ukázková data

Poskytuje služby Application Insights *[vzorkování oprava](../application-insights/app-insights-sampling.md)* ke snížení telemetrie přenosů. Když povolíte vzorkování ve vaší aplikaci Application Insights, zobrazí menší počet položek, které jsou uložené ve službě Application Insights a v analýzy protokolů. Při konzistenci dat se zachová, i v **konektor služby Statistika aplikace** stránky a perspektivy, je nutno ručně opravit jen Vzorkovaná data pro své vlastní dotazy.

Tady je příklad vzorkování oprava do protokolu vyhledávacího dotazu:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**Počet vzorků** pole je k dispozici ve všech položek a zobrazuje počet datových bodů, které představuje položku. Pokud zapnete vzorkování pro vaši aplikaci Application Insights **počet vzorků** je větší než 1. Zjistit skutečný počet položek, které vaše aplikace generuje součet **počet vzorků** pole.

Vzorkování ovlivňuje pouze celkový počet záznamů, které vaše aplikace generuje. Nemusíte opravte vzorkování pro metriky pole **RequestDuration** nebo **AvailabilityDuration** vzhledem k tomu, že tato pole Zobrazit průměr reprezentována záznamy.

## <a name="input-data"></a>Vstupní data

Řešení přijímá následující typy telemetrická data z připojených aplikací Application Insights:

- Dostupnost
- Výjimky
- Požadavky
- Stránka zobrazení – pro pracovní prostor pro příjem zobrazení stránky, je nutné nakonfigurovat aplikace ke shromažďování těchto informací. Další informace naleznete v tématu [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Vlastní události – vašeho pracovního prostoru pro příjem vlastních událostí, je nutné nakonfigurovat aplikace ke shromažďování těchto informací. Další informace naleznete v tématu [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Data je přijatých analýzy protokolů z Application Insights, jakmile je k dispozici.

## <a name="output-data"></a>Výstupní data

Záznam s *typ* z *ApplicationInsights* se vytvoří pro každý typ vstupní data. ApplicationInsights záznamy mají vlastnosti zobrazené v následující části:

### <a name="generic-fields"></a>Obecné pole

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| Když |   |
| TimeGenerated | Čas záznamu |
| ApplicationId | Klíč instrumentace Application Insights aplikace |
| ApplicationName | Název služby Application Insights aplikace |
| RoleInstance | ID hostitelského serveru |
| DeviceType | Klientské zařízení |
| ScreenResolution |   |
| Kontinent | Kontinentě, kde tato žádost pochází |
| Země | Zemi, kde tato žádost pochází |
| Kraj | Okres, stavu nebo národní prostředí, kde tato žádost pochází |
| Město | Města nebo lokality, kde tato žádost pochází města |
| isSynthetic | Určuje, zda požadavek nebyl vytvořen uživatelem nebo automatizované metodou. Hodnotu true = uživatelem generovaný nebo = false automatizované – metoda |
| SamplingRate | Procento telemetrii vygenerovanou sadou SDK, která je odeslána na portál. V rozsahu 0,0 100.0. |
| SampledCount | 100/(SamplingRate). Například, 4 =&gt; 25 % |
| IsAuthenticated | True nebo False |
| OperationID | Položky, které mají stejné operace ID se zobrazují jako související položky v portálu. Obvykle ID požadavku |
| ParentOperationID | ID nadřazené operace |
| OperationName |   |
| ID relace | Identifikátor GUID k jednoznačné identifikaci relace, kde byla vytvořena |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Dostupnost konkrétních polí

| Vlastnost | Popis |
| --- | --- |
| TelemetryType | Dostupnost |
| AvailabilityTestName | Název webového testu |
| AvailabilityRunLocation | Zeměpisná zdroj požadavku http |
| AvailabilityResult | Označuje úspěšný výsledek webového testu |
| AvailabilityMessage | Zpráva připojené k testu webu |
| AvailabilityCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| DataSizeMetricValue | 1.0 nebo 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| AvailabilityDuration | Doba v milisekundách, doby trvání webového testu |
| AvailabilityDurationCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Jedinečný identifikátor GUID pro test webu |
| AvailabilityTimestamp | Přesné časové razítko test dostupnosti |
| AvailabilityDurationMin | Pro jen Vzorkovaná záznamy toto pole ukazuje minimální webového testu dobu trvání (v milisekundách) pro reprezentována datových bodů |
| AvailabilityDurationMax | Pro jen Vzorkovaná záznamy toto pole ukazuje maximální webového testu dobu trvání (v milisekundách) pro reprezentována datových bodů |
| AvailabilityDurationStdDev | Pro jen Vzorkovaná záznamy toto pole ukazuje směrodatná odchylka mezi všechny webový test doby trvání (v milisekundách) pro reprezentována datových bodů |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Pole specifické pro výjimky

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Výjimka |
| ExceptionType | Typ výjimky |
| ExceptionMethod | Metoda, která vytvoří výjimka |
| ExceptionAssembly | Sestavení obsahuje rozhraní a verze, jakož i token veřejného klíče |
| ExceptionGroup | Typ výjimky |
| ExceptionHandledAt | Informuje o úrovni, který zpracovává výjimky |
| ExceptionCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| ExceptionMessage | Zpráva výjimky |
| Zásobník výjimky | Úplné zásobníku výjimky |
| ExceptionHasStack | Hodnota TRUE, pokud má zásobník výjimek |



### <a name="request-specific-fields"></a>Pole specifické pro žádost

| Vlastnost | Popis |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Žádost |
| ResponseCode | Odpovědi HTTP odeslané do klienta |
| RequestSuccess | Označuje úspěch nebo selhání. Hodnotu true nebo false. |
| ID žádosti | ID k jednoznačné identifikaci požadavku |
| RequestName | GET nebo POST + základní adresu URL |
| RequestDuration | Čas v sekundách, doby požadavku |
| zprostředkovatele identity | Adresa URL požadavku není včetně hostitele |
| Hostitel | Webový server hostitel |
| URLBase musí | Úplnou adresu URL požadavku |
| ApplicationProtocol | Typ protokolu používaný aplikace |
| RequestCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| RequestDurationCount | 100 /(Sampling Rate). Například, 4 =&gt; 25 % |
| RequestDurationMin | Toto pole jen Vzorkovaná záznamů zobrazuje minimální požadavek trvání (v milisekundách) pro reprezentována datových bodů. |
| RequestDurationMax | Pro jen Vzorkovaná záznamy toto pole ukazuje žádost o maximální dobu trvání (v milisekundách) pro reprezentována datových bodů |
| RequestDurationStdDev | Pro jen Vzorkovaná záznamy toto pole ukazuje směrodatná odchylka mezi všechny doby požadavku (v milisekundách) pro reprezentována datových bodů |

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Toto řešení nemá sadu ukázkový protokol hledání zobrazený na řídicím panelu. Ale ukázkové dotazy vyhledávání protokolu s popisy jsou uvedeny v [informace o zobrazení Application Insights konektoru](#view-application-insights-connector-information) části.

## <a name="next-steps"></a>Další postup

- Použití [hledání protokolů](log-analytics-log-searches.md) Chcete-li zobrazit podrobné informace pro vaše aplikace Application Insights.

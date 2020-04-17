---
title: Inteligentní detekce – anomálie selhání v application insights | Dokumenty společnosti Microsoft
description: Upozorní vás na neobvyklé změny v rychlosti neúspěšných požadavků na webovou aplikaci a poskytuje diagnostickou analýzu. Není nutná žádná konfigurace.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: a1bce3ab86748d8247a72da3bd70e0f2e8155dbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536807"
---
# <a name="smart-detection---failure-anomalies"></a>Inteligentní detekce – anomálie selhání
[Application Insights](../../azure-monitor/app/app-insights-overview.md) vás automaticky upozorní téměř v reálném čase, pokud vaše webová aplikace zažije abnormální nárůst míry neúspěšných požadavků. Detekuje neobvyklé zvýšení rychlosti požadavků HTTP nebo volání závislostí, které jsou hlášeny jako neúspěšné. U požadavků mají neúspěšné požadavky obvykle kódy odpovědí 400 nebo vyšší. Aby chomáč a diagnostikovat problém, analýza charakteristik selhání a související ch dat aplikace je k dispozici v podrobnostech výstrahy. K dispozici jsou také odkazy na portál Application Insights pro další diagnostiku. Funkce nepotřebuje žádné nastavení ani konfiguraci, protože používá algoritmy strojového učení k předvídání normální míry selhání.

Tato funkce funguje pro všechny webové aplikace, hostované v cloudu nebo na vlastních serverech, které generují data žádosti o aplikaci nebo závislostí. Například pokud máte roli pracovního procesu, který volá [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) nebo [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Po nastavení [Application Insights pro váš projekt](../../azure-monitor/app/app-insights-overview.md)a pokud vaše aplikace generuje určité minimální množství dat, inteligentní detekce anomálií selhání trvá 24 hodin, než se dozvíte normální chování vaší aplikace, než se zapne a může odesílat upozornění.

Tady je ukázková výstraha:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

Podrobnosti o výstraze vám řeknou:

* Míra selhání ve srovnání s normální chování aplikace.
* Kolik uživatelů jsou ovlivněny - takže víte, jak moc se bát.
* Charakteristický vzor spojený s poruchami. V tomto příkladu je konkrétní kód odpovědi, název požadavku (operace) a verze aplikace. To vám okamžitě řekne, kde začít hledat ve vašem kódu. Další možnosti mohou být konkrétní prohlížeč nebo klientský operační systém.
* Výjimka, trasování protokolu a selhání závislostí (databáze nebo jiné externí součásti), které se zdají být spojeny s charakterizovanými selháními.
* Odkazy přímo na relevantní vyhledávání na data v Application Insights.

## <a name="benefits-of-smart-detection"></a>Výhody inteligentní detekce
Běžná [upozornění na metriky](../../azure-monitor/app/alerts.md) říkají, že může být problém. Ale Smart Detection začíná diagnostickou práci za vás a provádí mnoho analýz, které byste jinak museli udělat sami. Získáte výsledky úhledně zabalené, což vám pomůže rychle se dostat ke kořeni problému.

## <a name="how-it-works"></a>Jak to funguje
Inteligentní detekce monitoruje data přijatá z vaší aplikace, a zejména míru selhání. Toto pravidlo spočítá počet požadavků, pro které je `Successful request` vlastnost false a `Successful call` počet volání závislostí, pro které je vlastnost false. Pro požadavky, ve `Successful request == (resultCode < 400)` výchozím nastavení (pokud jste napsali vlastní kód pro [filtrování](../../azure-monitor/app/api-filtering-sampling.md#filtering) nebo generování vlastní [chod TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) volání). 

Výkon aplikace má typický vzor chování. Některé požadavky nebo volání závislostí budou náchylnější k selhání než jiné; a celková míra selhání se může zvýšit s tím, jak se zvyšuje zatížení. Inteligentní detekce používá strojové učení k nalezení těchto anomálií.

Když data přicházejí do Application Insights z vaší webové aplikace, inteligentní detekce porovnává aktuální chování se vzory, které se viděly v posledních několika dnech. Pokud je pozorován abnormální nárůst poruchovosti ve srovnání s předchozím výkonem, spustí se analýza.

Při spuštění analýzy, služba provede analýzu clusteru na neúspěšný požadavek, aby se pokusili identifikovat vzor hodnot, které charakterizují selhání. 

Ve výše uvedeném příkladu analýza zjistila, že většina selhání se jedná o konkrétní kód výsledku, název požadavku, hostitele adresy URL serveru a instanci role. 

Pokud je vaše služba instrumentována s těmito voláními, analyzátor hledá výjimku a selhání závislostí, které jsou spojeny s požadavky v clusteru, který identifikoval, spolu s příkladem všech protokolů trasování přidružených k těmto požadavkům.

Výsledná analýza je odeslána jako výstraha, pokud jste ji nenakonfigurovali tak, aby nebyla.

Podobně jako [výstrahy, které nastavíte ručně](../../azure-monitor/app/alerts.md), můžete zkontrolovat stav vypalované výstrahy, kterou lze vyřešit, pokud je problém vyřešen. Nakonfigurujte pravidla výstrah na stránce Výstrahy prostředku Application Insights. Ale na rozdíl od jiných výstrah, nemusíte nastavit nebo konfigurovat inteligentní detekci. Pokud chcete, můžete ji zakázat nebo změnit její cílové e-mailové adresy.

### <a name="alert-logic-details"></a>Podrobnosti logiky výstrahy

Výstrahy jsou spouštěny naším proprietárním algoritmem strojového učení, takže nemůžeme sdílet přesné podrobnosti implementace. S tím řekl, chápeme, že někdy potřebujete vědět více o tom, jak základní logika funguje. Primární faktory, které jsou vyhodnoceny k určení, zda má být výstraha spuštěna, jsou: 

* Analýza procenta selhání požadavků nebo závislostí v časovém okně 20 minut.
* Porovnání procenta selhání za posledních 20 minut s rychlostí za posledních 40 minut a za posledních sedm dní a hledání významných odchylek, které přesahují x-krát tuto směrodatnou odchylku.
* Použití adaptivního limitu pro minimální procento selhání, které se liší v závislosti na objemu požadavků a závislostí aplikace.
* Existuje logika, která může automaticky vyřešit stav monitorování vypalovaných výstrah, pokud problém již není zjištěn po dobu 8-24 hodin.

## <a name="configure-alerts"></a>Konfigurace upozornění

Pravidlo výstrahy inteligentní detekce můžete zakázat z portálu nebo pomocí Správce prostředků Azure ([viz příklad šablony).](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)

Toto pravidlo výstrahy je vytvořeno s přidruženou [skupinou akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) s názvem "Application Insights Smart Detection", která obsahuje akce e-mailu a webhooku a může být rozšířena tak, aby spustila další akce při aktivaci výstrahy.

> [!NOTE]
> E-mailová oznámení odeslaná z tohoto pravidla výstrahy jsou nyní odesílána ve výchozím nastavení uživatelům přidruženým k rolím čtečky monitorování a přispěvatele monitorování předplatného. Více informací o tomto je k dispozici [zde](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
> Oznámení odeslaná z tohoto pravidla výstrahy se řídí [běžným schématem výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
>

Otevřete stránku Výstrahy. Pravidla výstrah anomálií selhání jsou zahrnuta spolu se všemi výstrahami, které jste nastavili ručně, a můžete zjistit, zda je aktuálně ve stavu výstrahy.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

Chcete-li výstrahu nakonfigurovat, klepněte na ni.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

Všimněte si, že můžete zakázat nebo odstranit pravidlo výstrahy Anomálií selhání, ale nemůžete vytvořit další na stejném prostředku Application Insights.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Příklad upozornění na anomálii selhání webhooku

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Třídění a diagnostika výstrahy

Výstraha označuje, že bylo zjištěno abnormální zvýšení míry neúspěšných požadavků. Je pravděpodobné, že došlo k nějakému problému s vaší aplikací nebo jejím prostředím.

Chcete-li prozkoumat další, klikněte na 'Zobrazit úplné podrobnosti v Application Insights' odkazy na této stránce se dostanete přímo na [vyhledávací stránku](../../azure-monitor/app/diagnostic-search.md) filtrované na příslušné požadavky, výjimku, závislost nebo trasování. 

Můžete taky otevřít [portál Azure](https://portal.azure.com), přejít na prostředek Application Insights pro vaši aplikaci a otevřít stránku Selhání.

Kliknutím na "Diagnostikovat selhání" vám pomůže získat další podrobnosti a vyřešit problém.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

Z procenta žádostí a počtu dotčených uživatelů se můžete rozhodnout, jak naléhavý problém je. Ve výše uvedeném příkladu míra selhání 78,5% ve srovnání s normální mírou 2,2%, naznačuje, že se děje něco špatného. Na druhou stranu, pouze 46 uživatelů byly ovlivněny. Pokud by to byla vaše aplikace, mohli byste posoudit, jak vážné to je.

V mnoha případech budete moci rychle diagnostikovat problém z názvu požadavku, výjimky, selhání závislostí a dat trasování.

V tomto příkladu byla výjimka z databáze SQL z důvodu požadavku omezení bylo dosaženo.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>Kontrola nedávných upozornění

Kliknutím na **Upozornění** na stránce prostředků Přehledy aplikací přejdete k nejnovějším vypalujícím se výstrahám:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>Jaký je v tom rozdíl...
Inteligentní detekce anomálií selhání doplňuje další podobné, ale odlišné funkce Application Insights.

* [Upozornění na metriky](../../azure-monitor/app/alerts.md) jsou nastavena vámi a můžete sledovat širokou škálu metrik, jako je obsazenost procesoru, sazby požadavků, časy načítání stránky a tak dále. Můžete je použít k upozornění, například pokud potřebujete přidat další zdroje. Naproti tomu inteligentní detekce anomálií selhání pokrývá malý rozsah kritických metrik (v současné době pouze selhání požadavku), které jsou navrženy tak, aby vás upozornily téměř v reálném čase, jakmile se míra neúspěšných požadavků vaší webové aplikace zvýší ve srovnání s normálním chováním webové aplikace. Na rozdíl od upozornění na metriky inteligentní zjišťování automaticky nastavuje a aktualizuje prahové hodnoty ve změnách odezvy v chování. Inteligentní detekce také spustí diagnostickou práci za vás, což vám ušetří čas při řešení problémů.

* [Inteligentní detekce anomálií výkonu](proactive-performance-diagnostics.md) také používá inteligenci počítače k objevování neobvyklých vzorů ve vašich metrikách a není vyžadována žádná vaše konfigurace. Ale na rozdíl od inteligentní detekce anomálií selhání, účelem inteligentní detekce anomálií výkonu je najít segmenty vašeho zařízení pro použití, které by mohly být špatně obsluhovány - například konkrétními stránkami na konkrétním typu prohlížeče. Analýza se provádí denně, a pokud je nalezen nějaký výsledek, je pravděpodobné, že bude mnohem méně naléhavé než výstraha. Naproti tomu analýza anomálií selhání se provádí nepřetržitě na příchozí data aplikace a budete upozorněni během několika minut, pokud míra selhání serveru jsou větší, než bylo očekáváno.

## <a name="if-you-receive-a-smart-detection-alert"></a>Pokud obdržíte upozornění inteligentní detekce
*Proč se mi toto upozornění dostalo?*

* Zjistili jsme abnormální nárůst míry neúspěšných požadavků ve srovnání s normální majestátností předchozího období. Po analýze selhání a souvisejících dat aplikace si myslíme, že existuje problém, který byste měli prohledat.

*Znamená oznámení znamená, že určitě mám problém?*

* Snažíme se upozornit na narušení nebo degradaci aplikace, ale pouze vy můžete plně pochopit sémantiku a dopad na aplikaci nebo uživatele.

*Takže se díváte na moje data z přihlášky?*

* Ne. Služba je zcela automatická. Oznámení dostáváte jen vy. Vaše data jsou [soukromá](../../azure-monitor/app/data-retention-privacy.md).

*Musím se přihlásit k odběru tohoto upozornění?*

* Ne. Každá aplikace, která odesílá data požadavku, má pravidlo výstrahy inteligentní detekce.

*Mohu se odhlásit nebo místo toho dostávat oznámení zaslaná kolegům?*

* Ano, v pravidlech výstrah klikněte na pravidlo inteligentní detekce a nakonfigurujte ho. Můžete zakázat výstrahu nebo změnit příjemce výstrahy.

*Ztratil jsem e-mail. Kde najdu oznámení na portálu?*

* V protokolech aktivit. V Azure otevřete prostředek Application Insights pro vaši aplikaci a vyberte protokoly aktivit.

*Některé výstrahy jsou o známých problémech a nechci je přijímat.*

* Můžete použít funkci potlačení [pravidel akcí výstrah.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules)

## <a name="next-steps"></a>Další kroky
Tyto diagnostické nástroje vám pomohou zkontrolovat data z vaší aplikace:

* [Průzkumník metrik](../../azure-monitor/platform/metrics-charts.md)
* [Průzkumník hledání](../../azure-monitor/app/diagnostic-search.md)
* [Analytics – výkonný dotazovací jazyk](../../azure-monitor/log-query/get-started-portal.md)

Inteligentní detekce jsou automatická. Ale možná byste chtěli nastavit nějaké další upozornění?

* [Ručně nakonfigurovaná upozornění na metriky](../../azure-monitor/app/alerts.md)
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md)

---
title: Inteligentní zjišťování – anomálie selhání v Application Insights | Microsoft Docs
description: Upozorňuje na neobvyklé změny v rychlosti neúspěšných požadavků na webovou aplikaci a poskytuje analýzu diagnostiky. Není nutná žádná konfigurace.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 978b63d74e6be4104ff53eef66e9633c78b90eb8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510630"
---
# <a name="smart-detection---failure-anomalies"></a>Inteligentní zjišťování – anomálie selhání
[Application Insights](./app-insights-overview.md) vás automaticky upozorní téměř v reálném čase, pokud vaše webová aplikace působí abnormální nárůst frekvence neúspěšných žádostí. Detekuje neobvyklé zvýšení rychlosti požadavků HTTP nebo volání závislostí, která jsou hlášena jako neúspěšná. V případě požadavků mají neúspěšné požadavky obvykle kódy odpovědí 400 nebo vyšší. V podrobnostech výstrahy jsou k dispozici analýzy vlastností selhání a souvisejících dat aplikací, které vám pomůžou při třídění a diagnostikování problému. K dispozici jsou také odkazy na portál Application Insights pro další diagnostiku. Tato funkce nevyžaduje žádné nastavení ani konfiguraci, protože používá algoritmy strojového učení pro předpověď běžné míry selhání.

Tato funkce funguje pro libovolnou webovou aplikaci, která je hostována v cloudu, nebo na vašich vlastních serverech, které generují data žádosti o aplikace nebo data závislostí. Například pokud máte roli pracovního procesu, která volá [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) nebo [TrackDependency ()](./api-custom-events-metrics.md#trackdependency).

Po nastavení [Application Insights projektu](./app-insights-overview.md), a pokud vaše aplikace vygeneruje určité minimální množství dat, inteligentní detekce anomálií selhání trvá 24 hodin, než se přepne na a může odesílat výstrahy.

Tady je ukázka výstrahy:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Ukázka výstrahy inteligentního zjišťování, která zobrazuje analýzu clusteru okolo selhání." lightbox="./media/proactive-failure-diagnostics/013.png":::

Podrobnosti výstrahy vám sdělí:

* Rychlost selhání v porovnání s normálním chováním aplikace
* Kolik uživatelů je ovlivněno, takže víte, kolik jich má starosti.
* Charakteristický vzor spojený s chybami. V tomto příkladu je k dispozici konkrétní kód odpovědi, název požadavku (operace) a verze aplikace. Tím se okamžitě ukáže, kde začít hledat váš kód. Další možností může být konkrétní prohlížeč nebo klientský operační systém.
* Výjimka, trasování protokolu a selhání závislosti (databáze nebo jiné externí komponenty), které se zdají být přidruženy k chybám s formátováním.
* Odkazuje přímo na relevantní hledání dat v Application Insights.

## <a name="benefits-of-smart-detection"></a>Výhody inteligentní detekce
Běžné [výstrahy metriky s upozorněním](../platform/alerts-log.md) , že se může jednat o problém. Inteligentní detekce ale spustí diagnostickou práci za vás a provede mnoho analýz, které byste jinak museli dělat sami. Výsledky získáte v hotovém balení, což vám pomůže rychle se dostat k hlavnímu adresáři problému.

## <a name="how-it-works"></a>Jak to funguje
Inteligentní detekce monitoruje data přijatá z vaší aplikace a zejména míry selhání. Toto pravidlo spočítá počet požadavků, pro které má `Successful request` vlastnost hodnotu false, a počet volání závislostí, pro které má `Successful call` vlastnost hodnotu false. Pro požadavky, ve výchozím nastavení `Successful request == (resultCode < 400)` (Pokud jste nenapsali vlastní kód pro [filtrování](./api-filtering-sampling.md#filtering) nebo generování vlastních volání [TrackRequest](./api-custom-events-metrics.md#trackrequest) ). 

Výkon vaší aplikace má typický vzor chování. Některé požadavky nebo volání závislostí budou lépe náchylné k selhání než jiné. a celková míra selhání může vzrůstá zatížení. Inteligentní detekce vyhledá tyto anomálie pomocí strojového učení.

Protože data přicházejí do Application Insights z vaší webové aplikace, inteligentní detekce porovná aktuální chování se vzorem zjištěným během posledních několika dní. Pokud se v porovnání s předchozím výkonem vyskytne neobvyklé zvýšení míry selhání, aktivuje se analýza.

Když se aktivuje analýza, služba provede analýzu clusteru na neúspěšném požadavku a pokusí se identifikovat vzor hodnot, které charakterizují selhání. 

V předchozím příkladu se při analýze zjistilo, že většina selhání se týká konkrétního kódu výsledku, názvu žádosti, hostitele adresy URL serveru a instance role. 

Když je vaše služba instrumentovaná pomocí těchto volání, analyzátor vyhledá výjimku a chybu závislosti, která je přidružená k žádostem v clusteru, který identifikoval, spolu s příkladem všech protokolů trasování přidružených k těmto žádostem.

Výsledná analýza se vám pošle jako výstraha, pokud jste ji nenakonfigurovali na.

Podobně jako u [výstrah, které jste nastavili ručně](../platform/alerts-log.md), můžete zkontrolovat stav aktivované výstrahy, která může být vyřešena, pokud je problém vyřešen. Nakonfigurujte pravidla výstrahy na stránce výstrahy v prostředku Application Insights. Ale na rozdíl od jiných výstrah nemusíte nastavovat nebo konfigurovat inteligentní zjišťování. Pokud chcete, můžete ho zakázat nebo změnit jeho cílové e-mailové adresy.

### <a name="alert-logic-details"></a>Podrobnosti logiky výstrahy

Výstrahy spouští náš proprietární algoritmus strojového učení, takže nemůžeme sdílet přesné podrobnosti implementace. Díky tomu říkáme, že někdy potřebujete vědět, jak základní logika funguje. Primární faktory, které jsou vyhodnocovány k určení, zda by měla být aktivována výstraha: 

* Analýza procenta selhání požadavků nebo závislostí v časovém intervalu po 20 minutách.
* Porovnání procenta selhání za posledních 20 minut s sazbou za posledních 40 minut a posledních sedm dní a hledání významných odchylek, které překračují standardní odchylku, se rovná hodnotě X.
* Použití adaptivního limitu pro minimální procento selhání, které se liší v závislosti na objemu požadavků a závislostí aplikace.
* K dispozici je logika, která může automaticky vyřešit stav aktivovaného monitorování výstrah, pokud se tento problém již nedetekuje 8-24 hodin.
  Poznámka: v aktuálním návrhu. oznámení nebo akce nebudou odeslány při vyřešení výstrahy inteligentního zjišťování. Můžete zjistit, zda byla v Azure Portal vyřešena výstraha inteligentního zjišťování.

## <a name="configure-alerts"></a>Konfigurace upozornění

Pravidlo výstrahy inteligentního zjišťování můžete zakázat z portálu nebo pomocí Azure Resource Manager ([Viz příklad šablony](./proactive-arm-config.md)).

Toto pravidlo upozornění se vytvoří s přidruženou [skupinou akcí](../platform/action-groups.md) s názvem Application Insights inteligentní zjišťování, která obsahuje akce e-mailu a Webhooku, a dá se rozšířit tak, aby se aktivovaly další akce, když se výstraha aktivuje.

> [!NOTE]
> E-mailová oznámení odesílaná z tohoto pravidla upozornění se teď posílají ve výchozím nastavení uživatelům přidruženým ke čtenářům monitorování předplatného a k monitorování rolí přispěvatele. Další informace, které jsou k dispozici, [najdete tady](./proactive-email-notification.md).
> Oznámení odesílaná z tohoto pravidla výstrahy následují po [běžném schématu výstrah](../platform/alerts-common-schema.md).
>

Otevřete stránku výstrahy. Pravidla výstrah upozorňující na chyby jsou zahrnuta společně se všemi výstrahami, které jste nastavili ručně, a vidíte, zda je aktuálně ve stavu výstrahy.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Na stránce Application Insights prostředek klikněte na dlaždice výstrahy a pak na Spravovat pravidla výstrah." lightbox="./media/proactive-failure-diagnostics/021.png":::

Kliknutím na výstrahu ji nakonfigurujte.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Obrazovka konfigurace pravidla" lightbox="./media/proactive-failure-diagnostics/032.png":::

Všimněte si, že můžete zakázat nebo odstranit pravidlo upozornění na anomálie při selhání, ale nemůžete ho vytvořit na stejném Application Insights prostředku.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Příklad anomálií při selhání datová část Webhooku výstrahy

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
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
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
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Třídění a diagnostika výstrahy

Výstraha indikuje, že byl zjištěn abnormální nárůst frekvence neúspěšných požadavků. Je pravděpodobnější, že došlo k nějakému problému s vaší aplikací nebo jeho prostředím.

Pokud chcete prozkoumat další informace, klikněte na Zobrazit úplné podrobnosti v Application Insights. odkazy na této stránce vám přebírají přímý odkaz na [stránku vyhledávání](./diagnostic-search.md) , která je filtrovaná na příslušné požadavky, výjimku, závislost nebo trasování. 

Můžete také otevřít [Azure Portal](https://portal.azure.com), přejít k prostředku Application Insights pro vaši aplikaci a otevřít stránku s chybami.

Kliknutím na Diagnostika selhání získáte další podrobnosti a vyřešte problém.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Diagnostické vyhledávání." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

V procentech požadavků a počtu ovlivněných uživatelů se můžete rozhodnout, jak naléhavě je problém. V předchozím příkladu je míra selhání 78,5% porovnávána s normální sazbou 2,2%, což znamená, že probíhá něco špatného. Na druhé straně byly zasaženy jenom 46 uživatelů. Pokud by to byla vaše aplikace, měli byste posoudit, jak závažná je.

V mnoha případech bude možné problém rychle diagnostikovat z názvu žádosti, výjimky, selhání závislosti a poskytnutých dat trasování.

V tomto příkladu se vyskytla výjimka z SQL Database z důvodu dosažení limitu požadavků.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Podrobnosti o neúspěšné žádosti" lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Zkontrolovat nedávné výstrahy

Kliknutím na **výstrahy** na stránce Application Insights prostředku se dostanete k nejnovějším výstrahám aktivovaném:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Souhrn výstrah" lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Jaký je rozdíl...
Inteligentní zjišťování anomálií selhání doplňuje jiné podobné, ale odlišné funkce Application Insights.

* [výstrahy metriky](../platform/alerts-log.md) jsou nastavené vámi a můžou monitorovat široké spektrum metrik, jako je obsazení procesoru, počet požadavků, doba načítání stránek a tak dále. Můžete je použít k upozornění, například pokud potřebujete přidat další prostředky. Naproti tomu inteligentní detekce anomálií selhání pokrývá malý rozsah kritických metrik (aktuálně jenom četnost neúspěšných požadavků), která je navržená k upozorňování téměř v reálném čase, jakmile se v porovnání s normálním chováním webové aplikace zvýší míra neúspěšných požadavků vaší webové aplikace. Inteligentní detekce na rozdíl od výstrah metrik automaticky nastavuje a aktualizuje prahové hodnoty v reakci na změny v chování. Inteligentní zjišťování také spouští diagnostickou práci za vás a šetří vám čas při řešení problémů.

* [Inteligentní detekce anomálií s výkonem](proactive-performance-diagnostics.md) taky využívá Machine Intelligence ke zjišťování neobvyklých vzorů ve vašich metrikách a nevyžaduje žádnou konfiguraci. Ale na rozdíl od inteligentní detekce anomálií při selhání je účel inteligentní detekce anomálií ve výkonu najít segmenty vašeho sběrného segmentu, které by mohly být chybně obsluhovány – například konkrétní stránky na konkrétního typu prohlížeče. Analýza se provádí denně a v případě, že se najde nějaký výsledek, bude pravděpodobně mnohem méně naléhavější než výstraha. Naproti tomu je analýza anomálií selhání prováděna průběžně na příchozích datech aplikací a během několika minut budete upozorněni na to, jestli jsou míry selhání serveru větší, než se očekávalo.

## <a name="if-you-receive-a-smart-detection-alert"></a>Pokud se zobrazí výstraha inteligentního zjišťování
*Proč se mi zobrazila tato výstraha?*

* Zjistili jsme neobvyklý nárůst frekvence neúspěšných požadavků v porovnání s normálním směrným plánem předchozího období. Po analýze selhání a přidružených aplikačních dat si myslíme, že se vyskytl problém, který byste měli najít.

*Znamená to, že oznámení má I konečně problém?*

* Zkusíme upozornit na přerušení nebo snížení úrovně aplikace, ale jenom můžete plně pochopit sémantiku a dopad na aplikaci nebo uživatele.

*Teď se díváte na moje data aplikací?*

* No. Služba je zcela automatická. Oznámení se zobrazí jenom vy. Vaše data jsou [soukromá](./data-retention-privacy.md).

*Musím se přihlásit k odběru této výstrahy?*

* No. Každá aplikace, která odesílá data požadavku, má pravidlo výstrahy inteligentního zjišťování.

*Můžu místo toho zrušit odběr nebo dostávat oznámení odesílaná kolegům?*

* Ano, v okně pravidla výstrah klikněte na pravidlo inteligentního zjišťování, které chcete nakonfigurovat. Můžete zakázat výstrahu nebo změnit příjemce výstrahy.

*Ztratil jsem e-mail. Kde najdu oznámení na portálu?*

* V protokolech aktivit. V Azure otevřete prostředek Application Insights pro vaši aplikaci a pak vyberte protokoly aktivit.

*Některé výstrahy se týkají známých problémů a nechci je přijímat.*

* Můžete použít funkci potlačení [pravidel akcí výstrah](../platform/alerts-action-rules.md) .

## <a name="next-steps"></a>Další kroky
Tyto diagnostické nástroje vám pomůžou zkontrolovat data z vaší aplikace:

* [Průzkumník metrik](../platform/metrics-charts.md)
* [Průzkumník vyhledávání](./diagnostic-search.md)
* [Analýza – výkonný dotazovací jazyk](../log-query/log-analytics-tutorial.md)

Inteligentní detekce jsou automatické. Možná byste ale chtěli nastavit ještě nějaké další výstrahy?

* [Ručně nakonfigurované výstrahy metriky](../platform/alerts-log.md)
* [Testy dostupnosti webu](./monitor-web-app-availability.md)
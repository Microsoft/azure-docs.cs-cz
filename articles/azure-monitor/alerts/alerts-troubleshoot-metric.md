---
title: Řešení potíží s upozorněními na metriky Azure
description: Běžné problémy se Azure Monitor výstrahami metrik a možnými řešeními.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 01/21/2021
ms.openlocfilehash: 2bd82d18d6647e47d9838702af45cff68f2bc6cd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037859"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Řešení potíží s upozorněními na metriky služby Azure Monitor 

Tento článek popisuje běžné problémy v Azure Monitor [výstrahy metrik](alerts-metric-overview.md) a jejich řešení.

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. Další informace o upozorňování najdete v tématu [Přehled výstrah v Microsoft Azure](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Výstraha metriky by měla být aktivována, ale nebyla 

Pokud se domníváte, že výstraha metriky by měla být aktivována, ale nenarazila se na Azure Portal, zkuste provést následující kroky:

1. **Konfigurace** – Zkontrolujte konfiguraci pravidla upozornění metrik, abyste měli jistotu, že je správně nakonfigurované:
    - Ověřte, zda je **typ agregace** a **členitost agregace (period)** konfigurovány podle očekávání. **Typ agregace** určuje, jak jsou agregovány hodnoty metriky (Další informace [zde](../essentials/metrics-aggregation-explained.md#aggregation-types)) a **členitost (period)** určuje, jak daleko zpátky vyhodnocení vyhodnocuje hodnoty metrik při každém spuštění pravidla výstrahy.
    -  Ověřte, zda je **prahová hodnota** nebo **Citlivost** konfigurována podle očekávání.
    - Pro pravidlo upozornění, které používá dynamické prahové hodnoty, ověřte, jestli jsou nakonfigurovaná Pokročilá nastavení, protože **počet porušení** může filtrovat výstrahy a **Ignorovat data, aby** mohl ovlivnit, jak se počítají prahové hodnoty.

       > [!NOTE] 
       > Dynamické prahové hodnoty vyžadují nejméně 3 dny a 30 vzorků metriky, než se stanou aktivní.

2. **Aktivováno, ale žádné oznámení** – Projděte si seznam aktivovaných [výstrah](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , abyste viděli, jestli můžete najít aktivované upozornění. Pokud se v seznamu zobrazí výstraha, ale došlo k potížím s některými jeho akcemi nebo oznámeními, přečtěte si [zde](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)Další informace.

3. **Již aktivní** – ověřte, zda již není aktivována výstraha v časové řadě metriky, pro kterou jste očekávali upozornění. Upozornění metrik jsou stavová, to znamená, že po aktivaci upozornění pro konkrétní časovou řadu metrik se další upozornění pro stejnou časovou řadu neaktivují, dokud nepřestane docházet k problému. Tato volba návrhu snižuje šum. Výstraha je vyřešena automaticky, pokud není splněna podmínka upozornění pro tři po sobě jdoucí hodnocení.

4. **Použité dimenze** – Pokud jste vybrali některé [hodnoty dimenzí metriky](./alerts-metric-overview.md#using-dimensions), pravidlo výstrahy monitoruje každou jednotlivou časovou řadu metrik (definovanou kombinací hodnot dimenzí) pro porušení prahové hodnoty. Chcete-li také monitorovat agregovanou časovou řadu metrik (bez vybraných dimenzí), nakonfigurujte další pravidlo výstrahy pro metriku bez výběru dimenzí.

5. **Agregovaná a časová členitost** – Pokud vizualizujete metriku pomocí [grafů metrik](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), ujistěte se, že:
    * Vybraná **agregace** v grafu metriky je stejná jako **typ agregace** v pravidle upozornění.
    * Vybraná **Časová členitost** je stejná jako **členitost agregace (tečka)** ve vašem pravidle výstrahy (a není nastavená na automatické).

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Výstraha metriky, pokud by neměla mít

Pokud se domníváte, že výstraha o metrikě by neměla být aktivována, ale došlo k tomu, může vám tento problém vyřešit následující kroky.

1. Zkontrolujte seznam aktivovaných [výstrah](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) a vyhledejte aktivované upozornění a kliknutím zobrazíte jeho podrobnosti. Projděte si informace uvedené v části **Proč se tato výstraha aktivuje?** Pokud chcete zobrazit graf metriky, **hodnotu metriky** a **prahovou hodnotu** v době, kdy byla výstraha aktivována.

    > [!NOTE] 
    > Pokud používáte typ podmínky dynamického prahového hodnoty a myslíme na to, že použité prahové hodnoty nejsou správné, poskytněte prosím zpětnou vazbu pomocí ikony zamračení. Tato zpětná vazba bude mít vliv na algoritmus Machine Learning algorithming a pomůže vám zlepšit budoucí zjišťování.

2. Pokud jste pro metriku vybrali více hodnot dimenzí, bude tato výstraha aktivována, pokud **kterákoli** z časových řad metriky (definovaná kombinací hodnot dimenzí) nedodržuje prahovou hodnotu. Další informace o používání dimenzí v upozorněních metrik najdete [tady](./alerts-metric-overview.md#using-dimensions).

3. Zkontrolujte konfiguraci pravidla výstrahy, abyste se ujistili, že je správně nakonfigurovaný:
    - Ověřte, že **typ agregace**, **členitost agregace (period)** a **prahová hodnota** nebo **Citlivost** jsou nakonfigurovány podle očekávání.
    - Pro pravidlo upozornění, které používá dynamické prahové hodnoty, ověřte, jestli jsou nakonfigurovaná Pokročilá nastavení, protože **počet porušení** může filtrovat výstrahy a **Ignorovat data, aby** mohl ovlivnit, jak se počítají prahové hodnoty.

   > [!NOTE]
   > Dynamické prahové hodnoty vyžadují nejméně 3 dny a 30 vzorků metriky, než se stanou aktivní.

4. Pokud vizualizujete metriku pomocí [grafu metrik](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), ujistěte se, že:
    - Vybraná **agregace** v grafu metriky je stejná jako **typ agregace** v pravidle upozornění.
    - Vybraná **Časová členitost** je stejná jako **členitost agregace (tečka)** ve vašem pravidle výstrahy (a není nastavená na automatické).

5. Pokud se výstraha vyvolala v době, kdy jsou již aktivována upozornění, která sledují stejná kritéria (která nejsou vyřešena), ověřte, zda bylo pravidlo výstrahy nakonfigurováno s vlastností *Autozmírňování* nastavenou na **hodnotu false** (tuto vlastnost lze nakonfigurovat pouze pomocí REST/PowerShell/CLI), a proto ověřte skript používaný k nasazení pravidla výstrahy. V takovém případě pravidlo výstrahy nevyvolá automaticky aktivované výstrahy a před opětovným vypálením nevyžaduje, aby byla vyřešena výstraha aktivována.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Nejde najít metriku pro upozornění na metriky hosta virtuálních počítačů.

Pokud chcete upozornit na metriky virtuálních počítačů hostovaného operačního systému (například paměť, místo na disku), ujistěte se, že jste nainstalovali potřebného agenta pro shromažďování těchto dat do Azure Monitor metrik:
- [Pro virtuální počítače s Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Pro virtuální počítače s Linuxem](../essentials/collect-custom-metrics-linux-telegraf.md)

Další informace o shromažďování dat z hostovaného operačního systému virtuálního počítače najdete [tady](../vm/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Pokud jste nakonfigurovali metriky hosta k odeslání do Log Analytics pracovního prostoru, zobrazí se metriky pod prostředkem Log Analytics pracovního prostoru a začnou se **zobrazovat data až** po vytvoření pravidla výstrahy, které je monitoruje. Postupujte podle pokynů ke [konfiguraci upozornění na metriku pro protokoly](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Monitorování metriky hosta pro více virtuálních počítačů s jedním pravidlem výstrahy není aktuálně podporováno v upozorněních metriky. Můžete to dosáhnout pomocí [pravidla upozornění protokolu](./alerts-unified-log.md). Chcete-li to provést, zajistěte, aby byly metriky hostů shromažďovány do Log Analyticsho pracovního prostoru, a vytvořte pravidlo upozornění protokolu v pracovním prostoru.

## <a name="cant-find-the-metric-to-alert-on"></a>Nejde najít metriku, na které se má upozornit.

Pokud chcete nastavit upozorňování na konkrétní metriku, ale při vytváření pravidla upozornění se tato metrika nezobrazí, zkontrolujte následující:
- Pokud pro prostředek nemůžete najít žádné metriky, [zkontrolujte, že je daný typ prostředku pro upozornění na metriky podporovaný](./alerts-metric-near-real-time.md).
- Pokud se některé metriky prostředku zobrazují, ale nemůžete najít konkrétní metriku, [zkontrolujte, jestli je daná metrika dostupná](../essentials/metrics-supported.md). Pokud ano, v popisu metriky zkontrolujte, jestli není dostupná pouze v konkrétních verzích nebo edicích prostředku.
- Pokud metrika pro prostředek není dostupná, může být k dispozici v protokolech prostředků a umožňovat monitorování pomocí upozornění protokolu. Tady najdete další informace o [shromažďování a analýze protokolů prostředků Azure](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nejde najít dimenzi metriky, na které se má upozornit.

Pokud chcete upozornit na [konkrétní hodnoty dimenze metriky](./alerts-metric-overview.md#using-dimensions), ale tyto hodnoty nejdou najít, pamatujte na toto:

1. Zobrazení hodnot dimenzí v seznamu **Hodnoty dimenzí** může několik minut trvat.
1. Zobrazené hodnoty dimenzí vycházejí z dat metriky shromážděných za poslední den.
1. Pokud se hodnota dimenze ještě nevygenerovala nebo se nezobrazuje, můžete přidat vlastní hodnotu dimenze pomocí možnosti Přidat vlastní hodnotu.
1. Pokud byste chtěli upozornit na všechny možné hodnoty dimenze (včetně budoucích hodnot), zvolte možnost vybrat všechny aktuální a budoucí hodnoty.

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Pravidla upozornění na metriku jsou pořád definovaná u odstraněného prostředku. 

Při odstranění prostředku Azure nedojde k automatickému odstranění souvisejících pravidel upozornění na metriky. Odstranění pravidel výstrah přidružených k prostředku, který byl odstraněn:

1. Otevřete skupinu prostředků, ve které byl odstraněný prostředek definovaný.
1. V seznamu prostředků zaškrtněte políčko **Zobrazit skryté typy**.
1. Filtrujte seznam podle hodnoty Typ == **microsoft.insights/metricalerts**.
1. Vyberte odpovídající pravidla upozornění a vyberte **Odstranit** .

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Vytváření upozornění na metriky při každém splnění podmínky

Výstrahy metriky jsou ve výchozím stavu stav, a proto se další výstrahy nespustí, pokud je v dané časové řadě již aktivována výstraha. Pokud chcete nastavit konkrétní pravidlo upozornění na metriku bez stavů a upozornit na každé vyhodnocení, ve kterém je podmínka výstrahy splněná, vytvořte pravidlo výstrahy programově (například prostřednictvím [Správce prostředků](./alerts-metric-create-templates.md), [PowerShellu](/powershell/module/az.monitor/), [REST](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert)) a nastavte vlastnost *autozmírňovat* na false.

> [!NOTE] 
> Když se pravidlo upozornění na metriku nevrátí, zaaktivuje se nevyřešené výstrahy, takže i po nesplnění podmínky zůstane aktivované výstrahy v aktivovaném stavu, dokud nebude doba uchování 30 dnů.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definovat pravidlo výstrahy pro vlastní metriku, která ještě nebyla vygenerována

Při vytváření pravidla upozornění na metriky je název metriky ověřený proti [rozhraní API definice metriky](/rest/api/monitor/metricdefinitions/list) , aby se zajistilo, že existuje. V některých případech byste chtěli vytvořit pravidlo upozornění pro vlastní metriky, dokonce i před tím, než se vygeneruje. Například při vytváření (pomocí šablony Správce prostředků) Application Insights prostředek, který vygeneruje vlastní metriku spolu s pravidlem výstrahy, které monitoruje tuto metriku.

Aby nedošlo k selhání nasazení při pokusu o ověření definice vlastní metriky, můžete použít parametr *skipMetricValidation* v části kritéria pro pravidlo výstrahy, což způsobí přeskočení ověření metriky. Pokud chcete použít tento parametr v šabloně Správce prostředků, podívejte se na následující příklad. Další informace najdete v tématu [kompletní ukázky šablon Správce prostředků pro vytváření pravidel upozornění metrik](./alerts-metric-create-templates.md).

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Exportujte šablonu Azure Resource Manager pravidla výstrahy metriky prostřednictvím Azure Portal

Export šablony Správce prostředků pravidla výstrahy metriky vám pomůže pochopit jeho syntaxi a vlastnosti a můžete ho použít k automatizaci budoucích nasazení.
1. Přejděte do části **skupiny prostředků** na portálu a vyberte skupinu prostředků, která pravidlo obsahuje.
2. V části Přehled zaškrtněte políčko **Zobrazit skryté typy** .
3. V poli Filtr **typu** vyberte *Microsoft. Insights/metricalerts*.
4. Vyberte příslušné pravidlo výstrahy, abyste zobrazili jeho podrobnosti.
5. V části **Nastavení** vyberte **Exportovat šablonu**.

## <a name="metric-alert-rules-quota-too-small"></a>Kvóta pravidel upozornění metrik je příliš malá.

Povolený počet pravidel upozornění metrik na předplatné podléhá [omezením kvót](../service-limits.md).

Pokud jste dosáhli limitu kvóty, může vám tento problém vyřešit následující postup:
1. Zkuste odstranit nebo zakázat pravidla upozornění na metriky, které už se nepoužívají.

2. Přejděte na používání pravidel upozornění na metriky, která monitorují více prostředků. Díky této funkci může jedno pravidlo výstrahy monitorovat více prostředků pomocí pouze jednoho pravidla výstrahy započítaného na kvótu. Další informace o této možnosti a podporovaných typech prostředků najdete [tady](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Pokud potřebujete maximální kvótu zvýšit, otevřete žádost o podporu a zadejte následující informace:

    - ID předplatných, pro která je potřeba zvýšit limit kvóty
    - Typ prostředku pro zvýšení kvóty: **výstrahy metrik** nebo **výstrahy metriky (klasické)**
    - Požadovaný limit kvóty

## <a name="check-total-number-of-metric-alert-rules"></a>Zkontroluje celkový počet pravidel upozornění metrik.

Chcete-li zjistit aktuální využití pravidel upozornění metriky, postupujte podle následujících kroků.

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

1. Otevřete obrazovku **Upozornění** a klikněte na **Spravovat pravidla upozornění**.
2. Filtrování na relevantní odběr pomocí ovládacího prvku DropDown ( **Subscription** )
3. Ujistěte se, že nechcete filtrovat konkrétní skupinu prostředků, typ prostředku nebo prostředek.
4. V ovládacím prvku rozevírací seznam **typ signálu** vyberte **metriky** .
5. Ověřte, že je v ovládacím prvku rozevírací seznam **stavů** nastavená možnost **povoleno** .
6. Celkový počet pravidel upozornění metrik se zobrazuje nad seznamem pravidel výstrah.

### <a name="from-api"></a>Pomocí rozhraní API

- PowerShell – [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- Rozhraní REST API – [Seznam podle předplatného](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Správa pravidel výstrah pomocí Správce prostředků šablon, REST API, PowerShellu nebo rozhraní příkazového řádku Azure

Pokud dochází k potížím při vytváření, aktualizaci, načítání nebo odstraňování výstrah metrik pomocí Správce prostředků šablon, REST API, PowerShellu nebo rozhraní příkazového řádku (CLI) Azure, může vám tento problém vyřešit následující kroky.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

- Projděte si seznam [běžných chyb nasazení Azure](../../azure-resource-manager/templates/common-deployment-errors.md) a vyřešte případné potíže.
- V [příkladech výstrahy metriky Azure Resource Manager příklady šablon](./alerts-metric-create-templates.md) , abyste měli jistotu, že předáváte všechny parametry správně.

### <a name="rest-api"></a>REST API

Přečtěte si [průvodce REST API](/rest/api/monitor/metricalerts/) , abyste ověřili, že všechny parametry předáváte správně.

### <a name="powershell"></a>PowerShell

Ujistěte se, že používáte správné rutiny PowerShellu pro výstrahy metrik:

- Rutiny PowerShellu pro upozornění metrik jsou k dispozici v [modulu Az.Monitor](/powershell/module/az.monitor/).
- Nezapomeňte použít rutiny končící na v2 pro nové (neklasické) výstrahy metriky (například [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2)).

### <a name="azure-cli"></a>Azure CLI

Ujistěte se, že používáte pro výstrahy metrik správné příkazy rozhraní příkazového řádku:

- Příkazy rozhraní příkazového řádku pro upozornění metrik začínají na `az monitor metrics alert`. Projděte si [referenční dokumentaci k Azure CLI](/cli/azure/monitor/metrics/alert) a seznamte se se syntaxí.
- Můžete si prohlédnout [ukázku použití rozhraní příkazového řádku pro upozornění metrik](./alerts-metric.md#with-azure-cli).
- Pokud chcete nastavit upozorňování na vlastní metriku, nezapomeňte jako předponu názvu metriky použít odpovídající obor názvů metriky: OBOR_NÁZVŮ.METRIKA

### <a name="general"></a>Obecné

- Pokud se vám zobrazuje `Metric not found` Chyba:

   - Metrika platformy: Ujistěte se, že používáte název **metriky** ze [stránky podporované metriky Azure monitor](../essentials/metrics-supported.md), a ne jako **Zobrazovaný název metriky** .

   - Vlastní metrika: Ujistěte se, že je metrika už vysílaná (nemůžete vytvořit pravidlo upozornění pro vlastní metriku, která ještě neexistuje) a že poskytujete obor názvů vlastní metriky ( [tady](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)najdete příklad šablony Správce prostředků).

- Pokud vytváříte [výstrahy metriky v protokolech](./alerts-metric-logs.md), zajistěte, aby byly zahrnuty příslušné závislosti. Prohlédněte si [ukázkovou šablonu](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Pokud vytváříte pravidlo upozornění, které obsahuje více kritérií, pamatujte na následující omezení:

   - V rámci každého kritéria můžete vybrat jenom jednu hodnotu pro každou dimenzi.
   - Jako hodnotu dimenze nemůžete použít \*.
   - Pokud metriky, které jsou konfigurovány v různých kriteriích, podporují stejnou dimenzi, pak musí být nakonfigurovaná hodnota dimenze explicitně nastavena stejným způsobem pro všechny tyto metriky ( [Viz příklad šablony](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)správce prostředků příklad).


## <a name="no-permissions-to-create-metric-alert-rules"></a>Žádná oprávnění k vytváření pravidel upozornění metrik

Pokud chcete vytvořit pravidlo upozornění na metriky, musíte mít následující oprávnění:

- Oprávnění číst pro cílový prostředek pravidla výstrahy
- Oprávnění k zápisu pro skupinu prostředků, ve které se vytvoří pravidlo výstrahy (Pokud vytváříte pravidlo výstrahy z Azure Portal, pravidlo výstrahy se ve výchozím nastavení vytvoří ve stejné skupině prostředků, ve které se nachází cílový prostředek).
- Oprávnění ke čtení pro jakoukoli skupinu akcí přidruženou k pravidlu výstrahy (Pokud je k dispozici)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Omezení pojmenování pro pravidla upozornění na metriky

Vezměte v úvahu následující omezení pro názvy pravidel upozornění metrik:

- Po vytvoření se názvy pravidel upozornění metriky nedají změnit (Přejmenovat).
- Názvy pravidel upozornění na metriky musí být v rámci skupiny prostředků jedinečné.
- Názvy pravidel upozornění na metriky nesmí obsahovat tyto znaky: * # & +:  < > ? @ % { } \ / 
- Názvy pravidel upozornění na metriky nemůžou končit mezerou nebo tečkou.

> [!NOTE] 
> Pokud název pravidla výstrahy obsahuje znaky, které nejsou abecední nebo číselné (například mezery, interpunkční znaménka nebo symboly), můžou být tyto znaky zakódované v adresách URL, když je načte někteří klienti.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Omezení při použití dimenzí v pravidle upozornění na metriku s několika podmínkami

Výstrahy metrik podporují upozorňování na multidimenzionální metriky i podporu definující více podmínek (až 5 podmínek na pravidlo výstrahy).

Při použití dimenzí v pravidle výstrahy, které obsahuje více podmínek, vezměte v úvahu následující omezení:
- V rámci každé podmínky můžete vybrat jenom jednu hodnotu na dimenzi.
- Nemůžete použít možnost vybrat všechny aktuální a budoucí hodnoty (vybrat \* ).
- Pokud metriky, které jsou konfigurovány v různých podmínkách, podporují stejnou dimenzi, pak musí být nakonfigurovaná hodnota dimenze explicitně nastavena stejným způsobem pro všechny tyto metriky (v příslušných podmínkách).
Například:
    - Vezměte v úvahu pravidlo upozornění metriky, které je definováno v účtu úložiště, a monitorujte dvě podmínky:
        * Celkový počet **transakcí** > 5
        * Průměrná **SuccessE2ELatency** > 250 ms
    - Chci aktualizovat první podmínku a jenom monitorovat transakce, kde se dimenze **ApiName** rovná *getblob*
    - Vzhledem k tomu, že obě **transakce** i metriky **SuccessE2ELatency** podporují dimenzi **ApiName** , budu muset aktualizovat obě podmínky a obě z nich musí určovat dimenzi **ApiName** s hodnotou *getblob* .

## <a name="setting-the-alert-rules-period-and-frequency"></a>Nastavení intervalu a četnosti pravidla výstrahy

Doporučujeme vybrat *členitost agregace (period)* , která je větší než *frekvence vyhodnocení*, aby se snížila pravděpodobnost chybějícího prvního vyhodnocení přidané časové řady v následujících případech:
-   Pravidlo upozornění metriky, které monitoruje více dimenzí – když se přidá nová kombinace hodnot dimenze
-   Pravidlo upozornění metriky, které monitoruje více prostředků – při přidání nového prostředku do oboru
-   Pravidlo upozornění na metriku, které monitoruje metriku, která se nevysílá průběžně (zhuštěná metrika) – když se metrika vygeneruje po dobu delší než 24 hodin, kdy se neemitoval

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>Nevypadá to, že ohraničení dynamických prahových hodnot nevyhovují datům

Pokud se chování metriky v poslední době změnily, změny se nemusí nutně projevit v dynamických mezních hranicích (horní a dolní meze), protože se počítají na základě dat metriky za posledních 10 dnů. Při prohlížení dynamických mezních hodnot pro danou metriku se nezapomeňte podívat na trend metriky za poslední týden, a ne jenom pro poslední hodiny nebo dny.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Proč se týdenní sezónnost nedetekuje pomocí dynamických prahových hodnot?

Pro identifikaci týdenního sezónnost musí model dynamické prahové hodnoty vyžadovat alespoň tři týdny historických dat. Jakmile budou k dispozici dostatek historických dat, identifikují se všechny týdenní sezónnosty, které existují v datech metriky, a model by se odpovídajícím způsobem upravil. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Dynamické prahové hodnoty ukazují záporné dolní meze metriky, i když metrika vždycky má kladné hodnoty.

Když metrika vykazuje velkou výkyv, dynamické prahové hodnoty vytvoří širší model kolem hodnot metrik, což může vést k dolnímu okraji. Konkrétně k tomu může dojít v následujících případech:
1. Citlivost je nastavena na hodnotu Nízká. 
2. Střední hodnoty jsou blízko nuly.
3. Metrika vykazuje nepravidelný postup s vysokou odchylkou (v datech jsou špičky nebo DIP).

Pokud dolní hranice má zápornou hodnotu, znamená to, že je plausible, že metrika dosáhla nulové hodnoty v důsledku nepravidelného chování metriky. Můžete zvážit výběr vyšší citlivosti nebo větší *členitosti (period)* , aby model byl méně citlivý, nebo pomocí možnosti *Ignorovat data před* vyloučením nedávných irregulaity z historických dat použitých k vytvoření modelu.

## <a name="next-steps"></a>Další kroky

- Obecné informace o řešení potíží s výstrahami a oznámeními najdete v tématu [řešení potíží v Azure monitor výstrahy](alerts-troubleshoot.md).
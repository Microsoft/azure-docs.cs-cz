---
title: Řešení potíží s upozorněními na metriky Azure
description: Běžné problémy se Azure Monitor výstrahami metrik a možnými řešeními.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 06/21/2020
ms.subservice: alerts
ms.openlocfilehash: 36ff80bc0858d6d08cc120d126628de02ba6e703
ms.sourcegitcommit: 666303748238dfdf9da30d49d89b915af73b0468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130734"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Řešení potíží s výstrahami Azure Monitor metriky 

Tento článek popisuje běžné problémy v Azure Monitor [výstrahy metrik](alerts-metric-overview.md) a jejich řešení.

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. Další informace o upozorňování najdete v tématu [Přehled výstrah v Microsoft Azure](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Výstraha metriky by měla být aktivována, ale nebyla 

Pokud se domníváte, že výstraha metriky by měla být aktivována, ale nenarazila se na Azure Portal, zkuste provést následující kroky:

1. **Konfigurace** – Zkontrolujte konfiguraci pravidla upozornění metrik, abyste měli jistotu, že je správně nakonfigurované:
    - Ověřte, že **typ agregace**, **členitost agregace (period)** a **prahová hodnota** nebo **Citlivost** jsou nakonfigurovány podle očekávání.
    - Pro pravidlo upozornění, které používá dynamické prahové hodnoty, ověřte, jestli jsou nakonfigurovaná Pokročilá nastavení, protože **počet porušení** může filtrovat výstrahy a **Ignorovat data, aby** mohl ovlivnit, jak se počítají prahové hodnoty.

       > [!NOTE] 
       > Dynamické prahové hodnoty vyžadují nejméně 3 dny a 30 vzorků metriky, než se stanou aktivní.

2. **Aktivováno, ale žádné oznámení** – Projděte si seznam aktivovaných [výstrah](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , abyste viděli, jestli můžete najít aktivované upozornění. Pokud se v seznamu zobrazí výstraha, ale došlo k potížím s některými jeho akcemi nebo oznámeními, přečtěte si [zde](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected)Další informace.

3. **Již aktivní** – ověřte, zda již není aktivována výstraha v časové řadě metriky, pro kterou jste očekávali upozornění. Výstrahy metriky jsou stavové výstrahy, což znamená, že jakmile se výstraha aktivuje na konkrétní časové řadě metriky, neprojeví se další výstrahy v této časové řadě, dokud nebude problém nepozorovaný. Tato volba návrhu snižuje šum. Výstraha je vyřešena automaticky, pokud není splněna podmínka upozornění pro tři po sobě jdoucí hodnocení.

4. **Použité dimenze** – Pokud jste vybrali některé [hodnoty dimenzí metriky](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions), pravidlo výstrahy monitoruje každou jednotlivou časovou řadu metrik (definovanou kombinací hodnot dimenzí) pro porušení prahové hodnoty. Chcete-li také monitorovat agregovanou časovou řadu metrik (bez vybraných dimenzí), nakonfigurujte další pravidlo výstrahy pro metriku bez výběru dimenzí.

5. **Agregovaná a časová členitost** – Pokud vizualizujete metriku pomocí [grafů metrik](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), ujistěte se, že:
    * Vybraná **agregace** v grafu metriky je stejná jako **typ agregace** v pravidle upozornění.
    * Vybraná **Časová členitost** je stejná jako **členitost agregace (tečka)** ve vašem pravidle výstrahy (a není nastavená na automatické).

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Výstraha metriky, pokud by neměla mít

Pokud se domníváte, že výstraha o metrikě by neměla být aktivována, ale došlo k tomu, může vám tento problém vyřešit následující kroky.

1. Zkontrolujte seznam aktivovaných [výstrah](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) a vyhledejte aktivované upozornění a kliknutím zobrazíte jeho podrobnosti. Projděte si informace uvedené v části **Proč se tato výstraha aktivuje?** Pokud chcete zobrazit graf metriky, **hodnotu metriky**a **prahovou hodnotu** v době, kdy byla výstraha aktivována.

    > [!NOTE] 
    > Pokud používáte typ podmínky Dynamické prahové hodnoty a myslíte si, že použité prahové hodnoty nebyly správné, pomocí ikony zamračeného smajlíka nám poskytněte zpětnou vazbu. Tato zpětná vazba bude mít vliv na algoritmus Machine Learning algorithming a pomůže vám zlepšit budoucí zjišťování.

2. Pokud jste pro metriku vybrali více hodnot dimenzí, bude tato výstraha aktivována, pokud **kterákoli** z časových řad metriky (definovaná kombinací hodnot dimenzí) nedodržuje prahovou hodnotu. Další informace o používání dimenzí v upozorněních metrik najdete [tady](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions).

3. Zkontrolujte konfiguraci pravidla výstrahy, abyste se ujistili, že je správně nakonfigurovaný:
    - Ověřte, že **typ agregace**, **členitost agregace (period)** a **prahová hodnota** nebo **Citlivost** jsou nakonfigurovány podle očekávání.
    - Pro pravidlo upozornění, které používá dynamické prahové hodnoty, ověřte, jestli jsou nakonfigurovaná Pokročilá nastavení, protože **počet porušení** může filtrovat výstrahy a **Ignorovat data, aby** mohl ovlivnit, jak se počítají prahové hodnoty.

   > [!NOTE]
   > Dynamické prahové hodnoty vyžadují nejméně 3 dny a 30 vzorků metriky, než se stanou aktivní.

4. Pokud vizualizujete metriku pomocí [grafu metrik](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), ujistěte se, že:
    - Vybraná **agregace** v grafu metriky je stejná jako **typ agregace** v pravidle upozornění.
    - Vybraná **Časová členitost** je stejná jako **členitost agregace (tečka)** ve vašem pravidle výstrahy (a není nastavená na automatické).

5. Pokud se výstraha vyvolala v době, kdy jsou již aktivována upozornění, která sledují stejná kritéria (která nejsou vyřešena), ověřte, zda bylo pravidlo výstrahy nakonfigurováno s vlastností *Autozmírňování* nastavenou na **hodnotu false** (tuto vlastnost lze nakonfigurovat pouze pomocí REST/PowerShell/CLI), a proto ověřte skript používaný k nasazení pravidla výstrahy. V takovém případě pravidlo výstrahy nevyvolá automaticky aktivované výstrahy a před opětovným vypálením nevyžaduje, aby byla vyřešena výstraha aktivována.


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>Nejde najít metriku pro upozornění na virtuálních počítačích. 

Chcete-li upozornit na metriky hostů na virtuálních počítačích (pro paměť, místo na disku), ujistěte se, že jste nastavili nastavení diagnostiky k odesílání dat do Azure Monitor jímky:
    * [Pro virtuální počítače s Windows](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [Pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> Pokud jste nakonfigurovali metriky hosta k odeslání do Log Analytics pracovního prostoru, zobrazí se metriky pod prostředkem Log Analytics pracovního prostoru a začnou se **zobrazovat data až** po vytvoření pravidla výstrahy, které je monitoruje. Postupujte podle pokynů ke [konfiguraci upozornění na metriku pro protokoly](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs).

## <a name="cant-find-the-metric-to-alert-on"></a>Nejde najít metriku, na které se má upozornit.

Pokud chcete upozornit na konkrétní metriku, ale nevidíte žádné metriky pro daný prostředek, [Zkontrolujte, jestli je typ prostředku podporovaný pro výstrahy metriky](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time).
Pokud vidíte některé metriky pro prostředek, ale nemůžete najít konkrétní metriky, [Zkontrolujte, jestli je tato metrika k dispozici](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), a pokud ano, podívejte se na popis metriky a zjistěte, jestli je k dispozici jenom pro konkrétní verze nebo edice prostředku.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nejde najít dimenzi metriky, na které se má upozornit.

Pokud chcete upozornit na [konkrétní hodnoty dimenze metriky](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions), ale tyto hodnoty nejdou najít, pamatujte na toto:

1. Zobrazení hodnot dimenzí v seznamu **Hodnoty dimenzí** může několik minut trvat.
1. Zobrazené hodnoty dimenzí vycházejí z dat metriky shromážděných za poslední tři dny.
1. Pokud hodnota dimenze ještě není vygenerována, přidejte vlastní hodnotu kliknutím na znaménko +.
1. Pokud byste chtěli upozornit na všechny možné hodnoty dimenze (včetně budoucích hodnot), zaškrtněte políčko "vybrat *".

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Pravidla upozornění na metriku jsou pořád definovaná u odstraněného prostředku. 

Při odstranění prostředku Azure nedojde k automatickému odstranění souvisejících pravidel upozornění na metriky. Odstranění pravidel výstrah přidružených k prostředku, který byl odstraněn:

1. Otevřete skupinu prostředků, ve které byl odstraněný prostředek definovaný.
1. V seznamu prostředků zaškrtněte políčko **Zobrazit skryté typy**.
1. Filtrujte seznam podle hodnoty Typ == **microsoft.insights/metricalerts**.
1. Vyberte odpovídající pravidla upozornění a vyberte **Odstranit** .

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Vytváření upozornění na metriky při každém splnění podmínky

Výstrahy metriky jsou ve výchozím stavu stav, a proto se další výstrahy nespustí, pokud je v dané časové řadě již aktivována výstraha. Pokud chcete nastavit konkrétní pravidlo upozornění na metriku bez stavů a upozornit na každé vyhodnocení, ve kterém je podmínka výstrahy splněná, vytvořte pravidlo výstrahy programově (například prostřednictvím [Správce prostředků](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates), [PowerShellu](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1), [REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate), [CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) a nastavte vlastnost *autozmírňovat* na false.

> [!NOTE] 
> Když se pravidlo upozornění na metriku nevrátí, zaaktivuje se nevyřešené výstrahy, takže i po nesplnění podmínky zůstane aktivované výstrahy v aktivovaném stavu, dokud nebude doba uchování 30 dnů.


## <a name="metric-alert-rules-quota-too-small"></a>Kvóta pravidel upozornění metrik je příliš malá.

Povolený počet pravidel upozornění metrik na předplatné podléhá [omezením kvót](https://docs.microsoft.com/azure/azure-monitor/service-limits).

Pokud jste dosáhli limitu kvóty, může vám s řešením tohoto problému pomoct následující postup:
1. Zkuste odstranit nebo zakázat pravidla upozornění na metriky, které už se nepoužívají.

2. Přejděte na používání pravidel upozornění na metriky, která monitorují více prostředků. Díky této funkci může jedno pravidlo výstrahy monitorovat více prostředků pomocí pouze jednoho pravidla výstrahy započítaného na kvótu. Další informace o této možnosti a podporovaných typech prostředků najdete [tady](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

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
6. Celkový počet pravidel upozornění metrik se zobrazí nad seznamem pravidel.

### <a name="from-api"></a>Pomocí rozhraní API

- PowerShell – [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- Rozhraní REST API – [Seznam podle předplatného](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Správa pravidel výstrah pomocí Správce prostředků šablon, REST API, PowerShellu nebo rozhraní příkazového řádku Azure

Pokud dochází k potížím při vytváření, aktualizaci, načítání nebo odstraňování výstrah metrik pomocí Správce prostředků šablon, REST API, PowerShellu nebo rozhraní příkazového řádku (CLI) Azure, může vám tento problém vyřešit následující kroky.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

- Projděte si seznam [běžných chyb nasazení Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) a vyřešte případné potíže.
- V [příkladech výstrahy metriky Azure Resource Manager příklady šablon](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates) , které vám zajistí správné předání všech parametrů.

### <a name="rest-api"></a>REST API

Projděte si [příručku k rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/) a ověřte, že správně předáváte všechny parametry.

### <a name="powershell"></a>PowerShell

Ujistěte se, že používáte správné rutiny PowerShellu pro výstrahy metrik:

- Rutiny PowerShellu pro upozornění metrik jsou k dispozici v [modulu Az.Monitor](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1).
- Nezapomeňte použít rutiny končící na v2 pro nové (neklasické) výstrahy metriky (například [Add-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1)).

### <a name="azure-cli"></a>Azure CLI

Ujistěte se, že používáte pro výstrahy metrik správné příkazy rozhraní příkazového řádku:

- Příkazy rozhraní příkazového řádku pro upozornění metrik začínají na `az monitor metrics alert`. Projděte si [referenční dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest) a seznamte se se syntaxí.
- Můžete si prohlédnout [ukázku použití rozhraní příkazového řádku pro upozornění metrik](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli).
- Pokud chcete nastavit upozorňování na vlastní metriku, nezapomeňte jako předponu názvu metriky použít odpovídající obor názvů metriky: OBOR_NÁZVŮ.METRIKA

### <a name="general"></a>Obecné

- Pokud se vám zobrazuje chyba `Metric not found`:

   - Metrika platformy: Ujistěte se, že používáte název **metriky** ze [stránky podporované metriky Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), a ne jako **Zobrazovaný název metriky** .

   - Vlastní metrika: Ujistěte se, že je metrika už vysílaná (nemůžete vytvořit pravidlo upozornění pro vlastní metriku, která ještě neexistuje) a jestli poskytujete obor názvů vlastní metriky ( [tady](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)najdete příklad šablony ARM).

- Pokud vytváříte [upozornění na metriky týkající se protokolů](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs), zkontrolujte, jestli jsou zahrnuté příslušné závislosti. Prohlédněte si [ukázkovou šablonu](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs).

- Pokud vytváříte pravidlo upozornění, které obsahuje více kritérií, pamatujte na následující omezení:

   - V rámci každého kritéria můžete vybrat jenom jednu hodnotu pro každou dimenzi.
   - Jako hodnotu dimenze nemůžete použít \*.
   - Pokud metriky, které jsou konfigurovány v různých kriteriích, podporují stejnou dimenzi, pak musí být nakonfigurovaná hodnota dimenze explicitně nastavena stejným způsobem pro všechny tyto metriky ( [Viz příklad šablony](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)správce prostředků příklad).


## <a name="no-permissions-to-create-metric-alert-rules"></a>Žádná oprávnění k vytváření pravidel upozornění metrik

Pokud chcete vytvořit pravidlo upozornění na metriky, musíte mít následující oprávnění:

- Oprávnění číst pro cílový prostředek pravidla výstrahy
- Oprávnění k zápisu pro skupinu prostředků, ve které se vytvoří pravidlo výstrahy (Pokud vytváříte pravidlo výstrahy z Azure Portal, pravidlo výstrahy se vytvoří ve stejné skupině prostředků, ve které se nachází cílový prostředek).
- Oprávnění ke čtení pro jakoukoli skupinu akcí přidruženou k pravidlu výstrahy (Pokud je k dispozici)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Omezení pojmenování pro pravidla upozornění na metriky

Pro názvy pravidel upozornění metriky si prosím pamatujte následující omezení:

- Po vytvoření se názvy pravidel upozornění metriky nedají změnit (Přejmenovat).
- Názvy pravidel upozornění na metriky musí být v rámci skupiny prostředků jedinečné.
- Názvy pravidel upozornění na metriky nesmí obsahovat tyto znaky: * # & +:  < > ? @ % { } \ / 
- Názvy pravidel upozornění na metriky nemůžou končit tímto znakem:.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Omezení při použití dimenzí v pravidle upozornění na metriku s několika podmínkami

Výstrahy metrik podporují upozorňování na multidimenzionální metriky i podporu definující více podmínek (až 5 podmínek na pravidlo výstrahy).

Při použití dimenzí v pravidle výstrahy obsahujícím více podmínek Pamatujte na následující omezení:
1. V rámci každé podmínky můžete vybrat jenom jednu hodnotu na dimenzi.
2. Nemůžete použít možnost vybrat všechny aktuální a budoucí hodnoty (vybrat \* ).
3. Pokud metriky, které jsou konfigurovány v různých podmínkách, podporují stejnou dimenzi, pak musí být nakonfigurovaná hodnota dimenze explicitně nastavena stejným způsobem pro všechny tyto metriky (v příslušných podmínkách).
Příklad:
    - Vezměte v úvahu pravidlo upozornění metriky, které je definováno v účtu úložiště, a monitorujte dvě podmínky:
        * Celkový počet **transakcí** > 5
        * Průměrná **SuccessE2ELatency** > 250 ms
    - Chci aktualizovat první podmínku a jenom monitorovat transakce, kde se dimenze **ApiName** rovná *getblob*
    - Vzhledem k tomu, že obě **transakce** i metriky **SuccessE2ELatency** podporují dimenzi **ApiName** , budu muset aktualizovat obě podmínky a obě z nich musí určovat dimenzi **ApiName** s hodnotou *getblob* .


## <a name="next-steps"></a>Další kroky

- Obecné informace o řešení potíží s výstrahami a oznámeními najdete v tématu [řešení potíží v Azure monitor výstrahy](alerts-troubleshoot.md).

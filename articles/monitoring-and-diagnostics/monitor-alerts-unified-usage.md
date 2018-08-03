---
title: Vytvoření, zobrazení a Správa výstrah pomocí Azure monitoru
description: Nové prostředí upozornění jednotné Azure použijte k vytváření, zobrazení a správa metriky a pravidel upozornění protokolů z jednoho místa.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 77aa6805094d44d16eea9ac671316c9702423e6c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433398"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Vytvoření, zobrazení a Správa výstrah pomocí Azure monitoru  

## <a name="overview"></a>Přehled
V tomto článku se dozvíte, jak nastavit výstrahy pomocí nového rozhraní výstrahy v portálu Azure portal. Definice pravidla upozornění není ve třech částech:
- Cíl: Konkrétní Azure prostředek, který se má monitorovat
- Kritérií: Konkrétní podmínky nebo logiku, která při vidět v signál, by měly aktivovat akce
- Akce: Konkrétní volání odesílat příjemce oznámení – e-mail, SMS, webhooku atd.

Upozornění Azure také poskytuje jednotné zobrazení všech pravidel upozornění a umožňuje je spravovat na jednom místě; včetně zobrazení žádné nevyřešené výstrahy. Další informace o funkcích od [upozornění v Azure – přehled](monitoring-overview-unified-alerts.md).

Výstraha používá termín **upozornění protokolů** k popisu výstrahy, pokud je signál vlastní dotaz na základě [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-analytics.md). [Nová funkce upozornění metriky](monitoring-near-real-time-metric-alerts.md) poskytuje možnost upozornění na [vícerozměrné metriky](monitoring-metric-charts.md) pro konkrétní prostředky Azure. Výstrahy pro takové prostředků můžete použít další filtry u dimenzí vytváření **vícerozměrné metriky upozornění**.


> [!NOTE]
> Při upozornění v Azure nabízí nové a vylepšené prostředí při vytváření výstrah v Azure. Existující [upozornění (klasická)](monitoring-overview-alerts.md) prostředí zůstává použitelný.
>

Další podrobné je podrobného průvodce můžete pomocí upozornění v Azure.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Vytvořit pravidlo upozornění s portálem Azure Portal
1. V [portál](https://portal.azure.com/)vyberte **monitorování** a v části monitorování – tlačítko **výstrahy**.  
    ![Monitorování](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

1. Vyberte **nové pravidlo upozornění** pro vytvoření nového upozornění v Azure.
    ![Přidat výstrahu](./media/monitor-alerts-unified/AlertsPreviewOption.png)

1. V části Vytvoření upozornění se zobrazí s tři části, který se skládá z: *Definujte podmínku upozornění*, *definujte podrobnosti o upozornění*, a *definujte skupinu akcí*.

    ![Vytvořit pravidlo](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

1.  Definujte podmínku upozornění pomocí **vybrat prostředek** odkaz a zadáte cíl, tak, že vyberete prostředku. Filtrovat podle výběru * předplatného, * typ prostředku a nakonec vyberte požadované *prostředků*.

    >[!NOTE]

    > Předtím, než budete pokračovat, ověřte dostupné signály pro vybraný prostředek.

    ![Vybrat prostředek](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Uživatelské rozhraní umožňuje vytvářet různé typy výstrah z jednoho místa. Podle typu upozornění požadovaného zvolte jako další krok:

    - Pro **upozornění metriky**: postupujte podle kroků 5 až 7; přejděte přímo ke kroku 11
    - Pro **upozornění protokolů**, přejděte ke kroku 8.

    > [!NOTE]

    > Upozornění protokolu aktivit jsou také podporovány, ale jsou ve verzi preview. [Další informace](monitoring-activity-log-alerts-new-experience.md).

1. *Upozornění na metriku*: Zkontrolujte **typ prostředku** je vybraná signálu typu jako **metrika**, pak po příslušné **prostředků** je vybrán kliknutím  *Provádí* se vrátit na vytvoření výstrahy. Potom pomocí **přidat kritéria** určitý signál ukládat vybírat ze seznamu možností signál, jejich monitorovací službu a typ uvedené – tlačítko, které jsou k dispozici pro prostředků vybrané výše.

    ![Vybrat prostředek](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Všechny [téměř upozornění v reálném čase](monitoring-near-real-time-metric-alerts.md) podporuje prostředky patří službou monitorování **platformy** a signalizuje, že typ jako **metrika**

1. *Upozornění na metriku*: Po výběru signálu může zobrazovat logiku pro výstrahy. Pro srovnání, se zobrazí historických datech signálem pomocí možnosti upravit pomocí okna čas **zobrazit historii**, různou z posledních šest hodin na poslední týden. S vizualizací na místě **Alert Logic** lze vybrat ze zobrazených možností podmínku, agregace a nakonec prahovou hodnotu. Jako logiky k dispozici ve verzi preview podmínka se zobrazí ve vizualizaci spolu s historie signál o tom, kdy bude mít výstraha. Nakonec zadejte pro jakou dobu trvání, by měl vypadat oznámení pro zadanou podmínku výběrem ze **období** možnost spolu s jak často výstraha spouštět tak, že vyberete **frekvence**.

    ![Konfigurovat logiku signálů metriky](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

1. *Upozornění na metriku*: Pokud signálu se metrika a okna s upozorněním, dají se filtrovat pomocí více datových bodů nebo dimenze pro uvedené prostředky Azure. 

    a. Zvolte dobu trvání z **zobrazit historii** rozevíracího seznamu k vizualizaci různých časovém intervalu. Můžete také dimenze pro podporované metriky pro filtrování v časové řadě; Výběr dimenze je volitelný a můžou používat až pět dimenze. 

    b. **Upozornění logiky** lze vybrat ze zobrazených možností *podmínku*, * agregace, a *prahová hodnota*. Jako logiky k dispozici ve verzi preview podmínka se zobrazí ve vizualizaci spolu s historie signál o tom, kdy výstraha by byla aktivována v minulosti. 

    c. Chcete-li určit dobu trvání, zvolte **období** spolu s jak často by měla spustit upozornění tak, že vyberete **frekvence**.

    ![Konfigurovat logiku signálů vícerozměrné metriky](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

1. *Upozornění protokolů*: Zkontrolujte **typ prostředku** je zdrojem analytics jako *Log Analytics* nebo *Application Insights* a signalizuje, že typ jako **protokolu** , pak po příslušné **prostředků** je vybrán, klikněte na tlačítko *provádí*. Potom pomocí **přidat kritéria** tlačítko Zobrazit seznam možností signál k dispozici pro prostředek a v seznamu signál **prohledávání protokolů vlastní** možnosti zvolené protokolu monitorování službě, jako je *protokolu Analytics* nebo *Application Insights*.

   ![Vyberte prostředek - hledání vlastního protokolu](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Upozornění seznamy můžete importovat dotaz analytics jako typ signálu - **protokolu (uložený dotaz)**, jak je znázorněno výše obrázku. Tak, že uživatelé mohou zdokonalujete dotazu v Analytics a pak je ukládejte pro budoucí použití v upozornění – další podrobnosti o použití uložení dotazu, které jsou k dispozici na [pomocí prohledávání protokolů v log analytics](../log-analytics/log-analytics-log-searches.md) nebo [sdíleného dotazu ve službě application insights Analytics](../log-analytics/log-analytics-overview.md). 

1.  *Upozornění protokolů*: Po výběru dotazu pro generování výstrah můžou zobrazovat v **vyhledávací dotaz** pole; Pokud syntaxe dotazu není správná, pole se zobrazí chyba červeně. Pokud je správná syntaxe dotazu – pro referenci historických dat stanovených dotazu se zobrazuje jako graf s možností pro upravit časový interval z posledních šest hodin na poslední týden.

 ![Konfigurace pravidla výstrahy](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Vizualizaci historických dat mohou být zobrazeny pouze pokud výsledky dotazu obsahovat podrobnosti času. Pokud váš dotaz vrátí souhrnná data nebo konkrétní sloupec hodnot – stejný se zobrazuje jako jednotný vykreslení.

    >  Pro typ měření metriky upozornění protokolů pomocí Application insights, můžete zadat které určité proměnné k seskupení dat pomocí **agregační na** možnosti, jak je znázorněno níže:

    ![agregace na možnost](./media/monitor-alerts-unified/aggregate-on.png)

1.  *Upozornění protokolů*: S vizualizací na místě, **Alert Logic** lze vybrat ze zobrazených možností podmínku, agregace a nakonec prahovou hodnotu. Nakonec zadejte v logice, čas pro zadanou podmínku pomocí **období** možnost. Spolu s jak často výstraha spouštět tak, že vyberete **frekvence**.
Pro **upozornění protokolů** výstrahy může být založené na:
   - *Počet záznamů, které*: Pokud je počet záznamů vrácených dotazem větší nebo menší než hodnota zadaná, vytvoří se výstraha.
   - *Metriky měření*: vytvoření výstrahy, pokud každý *agregovat hodnotu* ve výsledcích překračuje prahovou hodnotu k dispozici a je *seskupené podle* zvolena hodnota. Počet porušení upozornění je počet pokusů, které se ve zvoleném časovém období překročil prahovou hodnotu. Můžete určit celkový počet porušení pro libovolnou kombinací těchto porušení přes sadu výsledků nebo po sobě jdoucí porušení tak, aby vyžadovala, porušení se musí vyskytovat v po sobě jdoucích vzorků. Další informace o [upozornění protokolů a jejich typy](monitor-alerts-unified-log.md).

    > [!TIP]
    > Aktuálně v upozornění – upozornění prohledávání protokolu můžete provést vlastní *období* a *frekvence* hodnotu v min. Hodnoty mohou lišit od 5 sekund až 1 440 minut (to znamená) 24 hodin. Takže pokud chcete upozornění limit Dejme tomu, že tři hodiny, převeďte jej do minut – 180 minut před použitím

1. Jako druhý krok definuje název pro upozornění v **název pravidla upozornění** pole společně s **popis** s podrobnostmi o podrobné informace o upozornění a **závažnost** hodnotu možnosti k dispozici. Tyto podrobnosti údaje znovu použijí ve všech výstrah e-mailů, oznámení nebo push provádí Azure Monitor. Kromě toho uživatel může zvolit okamžitě aktivovat pravidlo upozornění na vytvoření odpovídajícím způsobem přepnutím **Povolit pravidlo po vytvoření** možnost.

    Pro **upozornění protokolů** pouze, některé další funkce jsou dostupné v podrobnostech výstrahy:

    - **Potlačit výstrahy**: při zapnutí potlačení pro pravidla upozornění jsou zakázané akce pro pravidlo definované dobu, po vytvoření nové výstrahy. Toto pravidlo je pořád spuštěný a vytvoří záznamy upozornění, pokud je splněna kritéria. Umožňuje vám čas k opravě problému bez spuštění duplicitní akce.

        ![Potlačit oznámení pro výstrahy protokolu](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Zadat potlačit výstrahy hodnotu větší než četnost upozornění, ujistěte se, že oznámení jsou ukončeny bez překrytí

1. Jako třetí a poslední krok, zadejte případná **skupiny akcí** je potřeba pro pravidlo upozornění aktivuje, když je splněná podmínka výstrahy. Můžete zvolit žádné existující skupiny akcí s výstrahou nebo vytvořte novou skupinu akcí. Podle vybrané skupiny akcí, když je výstraha se aktivační událost Azure: Odeslat email(s), odeslat SMS(s), volání webhooky, opravit pomocí Runbooků Azure, nabízených oznámení do vaší nástroji ITSM atd. Další informace o [skupiny akcí](monitoring-action-groups.md).

    Pro **upozornění protokolů** některé další funkce, je možné přepsat výchozí akce:

    - **E-mailová oznámení**: přepíše *předmět e-mailu* v e-mailu, prostřednictvím skupiny akcí; Pokud je jeden nebo více e-mailových akcí v uvedené skupiny akcí. Nelze změnit text e-mailu a toto pole je **není** e-mailových adres.
    - **Zahrnout vlastní datovou část Json**: přepíše webhooku JSON používá skupiny akcí; Pokud je jeden nebo více akcí webhooku v uvedené skupiny akcí. Uživatel může určit formát JSON, který má být použit pro všechny nakonfigurované ve skupině přidružené akce; webhooků Další informace o formátech webhooku, najdete v části [akce webhooku pro upozornění protokolu](monitor-alerts-unified-log-webhook.md). Test Webhooku možnost je k dispozici ke kontrole formátu a zpracování cílová pomocí ukázky JSON a tuto možnost, jak je označené jako určená pouze pro **testování** účely.

        ![Akce přepsání pro výstrahy protokolu](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > Pro **otestovat Webhook** možnost pracovat, koncový bod by měl podporovat [napříč sdílení prostředků zdroji (CORS)](https://www.w3.org/TR/cors/) a uživatele můžete použít proxy server CORS k vyřešení problémů "Žádná hlavička Access-Control-Allow-Origin"

1. Pokud všechna pole jsou platná a s zelené značky **vytvořit pravidlo upozornění** kliknutí na tlačítko a výstrahy se vytvoří ve službě Azure Monitor – výstrahy. Všechny výstrahy můžete zobrazit z výstrah řídicí panel.

    ![Vytvoření pravidla](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Během několika minut upozornění je aktivní a aktivuje jak bylo popsáno dříve.

## <a name="view-your-alerts-in-azure-portal"></a>Zobrazit upozornění na webu Azure Portal

1. V [portál](https://portal.azure.com/)vyberte **monitorování** a v části monitorování – tlačítko **výstrahy**.  

1. **Řídicí panel výstrah** se zobrazí – ve které jsou všechny výstrahy Azure unified a zobrazí v jednotném čísle panel ![řídicí panel výstrah](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
1. Zprava nahoře doleva řídicího panelu ukazuje na první pohled, následující – což se dá kliknout zobrazíte podrobné informace:
    - *Vyvolané výstrahy*: počet v současné době výstrah, které splněny logiky a v aktivuje stavu
    - *Celkový počet pravidel upozornění*: číslo pravidla upozornění vytvořená a získat jeho část, číslo, které jsou aktuálně povoleny 
    

        > [!NOTE]
        > K zajištění konzistentní řídicího panelu s podrobnostmi o všech aktivovaná upozornění včetně upozornění protokolů pro služby application insights a log analytics; [Rozšířené jednotné upozornění (preview)](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview) by měla sloužit
  
  
1. Seznam všech aktivovaná upozornění se zobrazí, které může uživatel kliknout, chcete-li zobrazit podrobnosti
1. Pomoc ve zjištění konkrétní výstrahy; Možnosti rozevíracího seznamu v horní části jedné můžete použít pro filtrování konkrétního *předplatné, skupinu prostředků nebo prostředek*. Další pro žádné nevyřešené výstrahy, jedno použití *upozornění filtru* možnost Najít pro zadaný – klíčové slovo - konkrétní odpovídající výstrahy s *název, výstraha kritéria, skupinu prostředků a cílový prostředek*

## <a name="managing-your-alerts-in-azure-portal"></a>Správa upozornění na webu Azure Portal
1. V [portál](https://portal.azure.com/)vyberte **monitorování** a v části monitorování – tlačítko **výstrahy**.  
1. Vyberte **spravovat pravidla** tlačítko na horním panelu přejděte do části Správa pravidlo – kde jsou uvedeny všechny pravidla upozornění vytvořená; včetně výstrahy, které byly zakázány.
1. Pro konkrétní pravidla upozornění naleznete jednu můžete použít rozevírací seznam filtrů v horní části, které umožňují shortlist pravidla upozornění pro konkrétní *předplatného, skupiny prostředků nebo prostředek*. Můžete také pomocí vyhledávání označené podokně nad seznamem pravidlo upozornění *filtrovat výstrahy*, jeden zadejte klíčové slovo, které je hledána *název výstrahy, podmínky a cílový prostředek*; Chcete-li zobrazit pouze odpovídající pravidla.
   ![Spravovat pravidla výstrah](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
1. Chcete-li zobrazit nebo upravit konkrétní pravidlo upozornění, klikněte na jeho název, který bude zobrazen jako prokliknutelný odkaz.
1. Se zobrazí výstraha definované - ve struktuře tři fáze: 1) oznámení podmínka 2) výstraha podrobností (3) akci skupiny. **Cílová kritéria** můžete ke kliknutí na změnit logika upozornění nebo nová kritéria lze přidat po odstranění starší logiky pomocí ikony koše. Podobně, v části Podrobnosti upozornění - **popis** a **závažnost** je možné upravit. Skupina akcí můžete změnit a nový můžou být tak, aby dokázal propojení na výstrahy pomocí **nová skupina akcí** tlačítko.

   ![Upravit pravidlo upozornění](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

1. Horní panel, změny výstrahy může být použití reflektivní včetně: **Uložit** potvrďte všechny změny provedené na upozornění, **zahodit** bez potvrzení změny provedené na výstrahu, přejdete zpět **zakázat**  deaktivovat výstrahu - po jejímž uplynutí ho už běží nebo aktivuje žádnou akci. A nakonec **odstranit** trvale odeberete celý pravidla upozornění z Azure.

## <a name="next-steps"></a>Další postup

- Další informace o novém [téměř v reálném čase upozornění na metriku](monitoring-near-real-time-metric-alerts.md)
- Získat [přehled shromažďování metrik](insights-how-to-customize-monitoring.md) k Ujistěte se, že vaše služba není k dispozici a reagují.
- Další informace o [upozornění protokolů ve výstrahách Azure](monitor-alerts-unified-log.md)
- [Další informace o upozornění protokolu aktivit v prostředí upozornění (Preview)](monitoring-activity-log-alerts-new-experience.md)

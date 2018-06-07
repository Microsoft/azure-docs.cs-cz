---
title: Vytvořit, zobrazit a spravovat výstrahy monitorování pomocí Azure | Microsoft Docs
description: Pomocí nové sjednocené Azure výstrahy prostředí vytvořit, zobrazit a spravovat metrika a protokolu pravidla výstrah na jednom místě.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 36729da3-e002-4a64-86b2-2513ca2cbb58
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: bb532f5e249947534ba44a41edfadac22ef9e217
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824910"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Vytvoření, zobrazení a Správa výstrah pomocí Azure monitorování  

## <a name="overview"></a>Přehled
Tento článek ukazuje, jak nastavit výstrah pomocí rozhraní nové výstrahy v portálu Azure. Definice pravidla výstrahy se tří částí:
- Cíl: Konkrétní prostředků Azure, který má být monitorován
- Kritéria: Určité podmínky nebo logiku, když zobrazená v signál, by měly aktivovat akce
- Akce: Odeslané konkrétní volání k příjemce oznámení – e-mailu, SMS, webhooku atd.

Azure výstrahy také poskytuje jednotný pohled na všechna pravidla výstrah a schopnost spravovat na jednom místě; včetně zobrazení žádné nevyřešené výstrahy. Další informace o funkcích z [Azure upozornění – přehled](monitoring-overview-unified-alerts.md).

Výstrahy pod pojmem **protokolu výstrahy** k popisu výstrahy, kde je signál vlastní dotaz na základě [analýzy protokolů](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-analytics.md). [Nová metriky výstrah funkce](monitoring-near-real-time-metric-alerts.md) poskytuje schopnost upozornění na [multidimenzionální metriky](monitoring-metric-charts.md) pro konkrétní prostředky Azure. Výstrahy pro takové prostředků můžete použít další filtry v dimenzích vytváření **Multi-Dimensional metrika výstrahy**.


> [!NOTE]
> Při Azure výstrahy nabízí nové a vylepšené prostředí pro vytváření výstrah v Azure. Existující [výstrahy (klasické)](monitoring-overview-alerts.md) prostředí zůstává použitelný.
>

Další podrobné je podrobný návod, jak pomocí Azure výstrahy.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Pravidlo výstrahy vytvořit pomocí portálu Azure
1. V [portál](https://portal.azure.com/), vyberte **monitorování** a v části monitorování – zvolte **výstrahy**.  
    ![Monitorování](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Vyberte **nové pravidlo výstrahy** tlačítko Vytvořit nové oznámení v Azure.
    ![Přidání oznámení](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. V části Vytvoření upozornění se zobrazí se tří částí, který se skládá z: *definovat výstrahy podmínku*, *definovat podrobnosti výstrahy*, a *definovat akce skupiny*.

    ![Vytvořit pravidlo](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Definovat podmínka upozornění pomocí **vyberte prostředek** odkaz a určení cílového výběrem prostředku. Filtrovat podle výběru * předplatného, * typ prostředku a nakonec vyberte požadované *prostředků*.

    >[!NOTE]

    > Než budete pokračovat, ověřte signály dostupné pro vybraný zdroj.

    ![Vybrat prostředek](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Uživatelské rozhraní umožňuje vytvořit různé typy výstrah z jednoho místa. Na základě typu výstrahy potřeby vyberte jako další krok:

    - Pro **metrika výstrahy**: postupujte podle kroků 5 až 7; přejít přímo na krok 11
    - Pro **protokolu výstrahy**, přejít ke kroku 8.

    > [!NOTE]

    > Aktivity protokolu výstrahy jsou také podporovány, ale jsou ve verzi preview. [Další informace](monitoring-activity-log-alerts-new-experience.md).

5. * Metriky výstrahy: Zkontrolujte **typ prostředku** je vybrána s typem signál jako **metrika**, pak jednou odpovídající **prostředků** je zvolen klikněte na tlačítko *provádí*tlačítko se vrátíte k vytvoření výstrahy. Potom použít **přidat kritéria** tlačítko můžete vybrat ze seznamu možnosti signál, jejich monitorování služby a typ seznamu - určitý signál, které jsou k dispozici pro prostředek vybrali dříve.

    ![Vybrat prostředek](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Všechny [v blízkosti upozornění v reálném čase](monitoring-near-real-time-metric-alerts.md) podporující prostředky jsou uvedené službou monitorování **platformy** a signálu typu jako **metrika**

6. *Metriky výstrahy*: po signál zaškrtnuto, lze stanovit, logiku pro výstrahy. Pro odkaz, se zobrazí historických dat signálem s možností pro upravit současně pomocí okna **zobrazit historii**, různých z posledních šest hodin k minulého týdne. S vizualizaci na místě **výstrahy logiku** je možné vybrat z zobrazené možnosti podmínku, agregace a nakonec prahovou hodnotu. Jako verze preview poskytuje logiku podmínku se zobrazí v vizualizaci společně s historie signál, o tom, kdy by mít výstraha. Nakonec zadejte pro jaké doby trvání, by měla vypadat výstrahy pro zadanou podmínku tak, že zvolíte **období** možnost společně s jak často se má spouštět výstraha výběrem **frekvence**.

    ![Konfigurovat logiku signál pro metriku](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Metriky výstrahy*: Pokud signál je metriky a pak výstrahy okna je možné filtrovat pomocí několika datových bodů nebo dimenze pro dané prostředků Azure. 

    a. Vyberte dobu trvání z **zobrazit historii** rozevírací k vizualizaci jiné časové období. Můžete zvolit dimenze pro podporované metriky pro filtrování časové řady; Výběr dimenze je volitelný a až pět dimenze mohou být použity. 

    b. **Výstrahy logiku** je možné vybrat z zobrazené možnosti *podmínku*, * agregace, a *prahová hodnota*. Jako verze preview poskytuje logiku podmínku se zobrazí v vizualizaci společně s signál historie signalizují, že výstraha by byla spuštěna v minulosti. 

    c. K určení doby trvání, zvolte **období** společně s jak často má výstraha spustit výběrem **frekvence**.

    ![Konfigurovat logiku signál pro multidimenzionální metrika](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Protokolování výstrahy*: Zkontrolujte **typ prostředku** je zdrojem analytics jako *analýzy protokolů* nebo *Application Insights* a signálu typu jako **protokolu** , pak jednou odpovídající **prostředků** je vybraná, klikněte na tlačítko *provádí*. Dále používat **přidat kritéria** tlačítko Zobrazit seznam možností signál k dispozici pro daný prostředek a ze seznamu signál **hledání protokolů vlastní** možnost pro zvolený protokolu monitorování služby jako *protokolu Analýza* nebo *Application Insights*.

   ![Vyberte prostředek – vlastní protokol hledání](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Seznamy můžete importovat analytics dotazu jako typ signál – výstrahy **protokolu (uložený dotaz)**, jak je vidět v výše obrázku. Tak, aby uživatelé můžete ideální dotazu v analýzy a pak je uložit pro budoucí použití v upozornění – další podrobnosti o použití uložení dotazu, které jsou k dispozici na [pomocí hledání protokolů v analýzy protokolů](../log-analytics/log-analytics-log-searches.md) nebo [sdílené dotazu ve službě application insights Analýza](../log-analytics/log-analytics-overview.md). 

9.  *Přihlaste se výstrahy*: Po výběru, v lze stanovit dotazu pro zobrazení výstrah **vyhledávací dotaz** pole; Pokud syntaxe dotazu je nesprávný poli se zobrazí chyba červeně. Pokud syntaxe dotazu je správný – historických dat stanovené dotazu pro referenci zobrazí jako graf s možností pro upravit časový interval z posledních šest hodin minulého týdne.

 ![Konfigurace pravidla výstrah](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Vizualizaci historických dat lze zobrazit pouze pokud výsledky dotazu obsahovat podrobnosti času. Pokud výsledky dotazu v souhrnná data nebo konkrétní sloupec hodnoty - zobrazí se stejné jako singulární vykreslení.

    >  Pro typ měrné metrika protokolu výstrah pomocí Application insights, můžete určit které konkrétní proměnnou pro seskupení dat pomocí **agregační na** možnost; jak je uvedeno v dále:

    ![agregace na možnost](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Protokolování výstrahy*: S vizualizaci na místě, **výstrahy logiku** je možné vybrat z zobrazené možnosti podmínku, agregace a nakonec prahovou hodnotu. Nakonec zadejte v logiku, čas pro vyhodnocení zadanou podmínku pomocí **období** možnost. Společně s jak často se má spouštět výstraha výběrem **frekvence**.
Pro **protokolu výstrahy** výstrahy může být založené na:
   - *Počet záznamů*: výstraha se vytvoří, pokud je počet záznamů vrácených dotazem větší nebo menší než zadaná hodnota.
   - *Metriky měření*: vytváří výstrahu, pokud každý *agregovat hodnotu* ve výsledcích překračuje prahovou hodnotu zadaná a je *seskupené podle* vybrali hodnotu. Počet porušení u výstraha je počet, který je prahová hodnota ve zvoleném časovém období. Můžete určit celkový počet porušení u libovolné kombinace narušení přes sadu výsledků nebo po sobě jdoucích narušení tak, aby vyžadovala, musí dojít k porušení v po sobě jdoucích vzorků. Další informace o [protokolu výstrahy a jejich typy](monitor-alerts-unified-log.md).

    > [!TIP]
    > Aktuálně v výstrahy – výstrahy vyhledávání protokolu může trvat vlastní *období* a *frekvence* hodnota v minuty. Hodnoty se může lišit od 5 minut až 1 440 minut (tj.) 24 hodin. Takže pokud chcete výstrahu limit vyslovte tři hodiny, převeďte ho do minut – 180 minut, než použití

11. Druhým krokem, definujte název upozornění v **název pravidla výstrahy** pole spolu s **popis** s podrobnostmi o podrobností pro výstrahu a **závažnost** z hodnoty poskytuje možnosti. Tyto podrobnosti jsou opakovaně v všechny výstrahy e-mailů, oznámení nebo nabízené provádí monitorování Azure. Kromě toho uživatel okamžitě aktivovat pravidlo výstrahy na vytváření správně přepnutím **pravidlo povolit po vytvoření** možnost.

    Pro **protokolu výstrahy** pouze některé další funkce je k dispozici v podrobnostech výstrahy:

    - **Potlačit upozornění**: po zapnutí potlačení pro pravidlo výstrahy akce pravidla jsou zakázány definované dobu, po vytvoření nová výstraha. Pravidlo je stále spuštěná a vytváří výstrahy záznamy zadaný splnění kritérií. Povolení je čas k odstranění problému bez spuštění duplicitní akce.

        ![Potlačit oznámení pro výstrahy protokolu](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Zadejte potlačit výstrahy hodnotu větší než četnost upozornění Ujistěte se, že oznámení jsou zastaveny bez překrytí

12. Jako třetí a poslední krok, zadejte případných **akce skupiny** musí aktivuje pro pravidlo výstrahy, když je splněna podmínka výstrahy. Můžete vybrat všechny existující skupiny akce s výstraha nebo vytvořte novou skupinu akce. Podle vybrané akce skupiny, když je výstraha bude aktivace Azure: Odeslat email(s), odeslání SMS(s), volání Webhook(s), opravit pomocí Azure Runbooků, nabízené ITSM nástroj, atd. Další informace o [skupiny akcí](monitoring-action-groups.md).

    Pro **protokolu výstrahy** některé další funkce, je možné přepsat výchozí akce:

    - **Oznámení e-mailem**: přepsání *předmět e-mailu* v e-mailu, odeslána prostřednictvím akce skupiny; Pokud jeden nebo více akcí e-mailu neexistuje ve skupině dané akce. Nelze upravit text e-mailu a toto pole je **není** pro e-mailovou adresu.
    - **Zahrnout vlastní datovou část Json**: přepsání webhooku JSON používané skupiny akcí; Pokud jeden nebo více akcí webhooku neexistuje ve skupině dané akce. Uživatel může určit formát JSON, který se má použít pro všechny webhooků, které jsou nakonfigurované ve skupině přidružené akce; Další informace o formátech webhooku najdete v tématu [akce webhooku pro výstrahy protokolu](monitor-alerts-unified-log-webhook.md). Test Webhooku možnost je k dispozici Zkontrolujte formát a zpracování cílová pomocí ukázkových JSON a tuto možnost jako označený určená pouze pro **testování** účely.

        ![Akce přepsání pro výstrahy protokolu](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > Pro **Test Webhooku** možnost pracovat, by měly podporovat koncový bod [křížové sdílení prostředků zdroji (CORS)](https://www.w3.org/TR/cors/) a uživatele můžete použít CORS proxy k vyřešení problému "Žádné hlavičky Access-Control-Allow-Origin"

13. Pokud všechna pole jsou platné a s zelená značka **vytvořit pravidlo výstrahy** kliknutí na tlačítko a výstraha je vytvořen v Azure monitorování – výstrahy. Z výstrah řídicího panelu lze zobrazit všechny výstrahy.

    ![Vytvoření pravidla](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Během několika minut výstraha je aktivní a jak se popisuje výš se aktivuje.

## <a name="view-your-alerts-in-azure-portal"></a>Zobrazit upozornění na portálu Azure

1. V [portál](https://portal.azure.com/), vyberte **monitorování** a v části monitorování – zvolte **výstrahy**.  

2. **Řídicí panel výstrahy** se zobrazí – ve kterém jsou všechny výstrahy Azure unified a zobrazí v jednotném čísle Tabule ![výstrahy řídicí panel](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Z horní části zleva doprava řídicího panelu ukazuje na první pohled, následující – které sloužící k najdete podrobné informace:
    - *Aktivováno výstrahy*: počet v současné době výstrahy, které se mají splněna logiku a v aktivováno stavu
    - *Celkový počet pravidla výstrah*: číslo vytvořit pravidla výstrah a v subtext, číslo, které jsou nyní zapnuta
4. Se zobrazí seznam všechny aktivní výstrahy, které může uživatel kliknutím zobrazíte podrobnosti
5. Pomoc v konkrétní hledání výstrah; možnosti rozevírací seznam v horní části jeden můžete použít pro filtrování konkrétní *předplatné, skupinu prostředků nebo prostředek*. Další pro žádné nevyřešené výstrahy, jedno použití *upozornění filtru* možnost Najít pro zadaný – klíčové slovo - konkrétní odpovídající výstrahy s *název, výstraha kritéria, skupinu prostředků a cílový prostředek*

## <a name="managing-your-alerts-in-azure-portal"></a>Správa upozornění na portálu Azure
1. V [portál](https://portal.azure.com/), vyberte **monitorování** a v části monitorování – zvolte **výstrahy**.  
2. Vyberte **spravovat pravidla** tlačítko na horním panelu přejděte do části Správa pravidlo – kde jsou uvedeny všechny výstrahy pravidla vytvořená; včetně výstrahy, které byly zakázány.
3. Pro konkrétní pravidla výstrah najdete jednu buď pomocí rozevíracího seznamu filtrů v horní části, které umožňují shortlist pravidla výstrah pro konkrétní *předplatné, skupiny prostředků nebo prostředek*. Můžete také na pomocí vyhledávání označena podokně nad seznamu pravidlo výstrahy *filtrování výstrah*, jeden může poskytnout – klíčové slovo, které je shoda na základě *název výstrahy, podmínku a cílový prostředek*; Chcete-li zobrazit pouze odpovídající pravidla.
   ![Spravovat pravidla výstrah](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Chcete-li zobrazit nebo upravit konkrétní pravidlo výstrahy, klikněte na jeho název, který se bude zobrazovat jako prokliknutelný odkaz.
5. Se zobrazí výstraha definované – struktura tři fáze: 1) výstraha podmínka výstrahy 2) podrobností (3) akci skupiny. **Cíl kritéria** sloužící k úpravě logice výstrah nebo novou po použití na ikonu koše odstranit starší logiku lze přidat kritéria. Podobně, v části Podrobnosti výstrahy - **popis** a **závažnost** je možné upravit. A skupina akce lze změnit nebo může být tak nový, aby propojení na výstrahy pomocí **nové skupiny akce** tlačítko.

   ![Upravit pravidlo výstrahy](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Pomocí horním panelu, změny výstrahy může být odrážející včetně: **Uložit** potvrzení změny provést s výstrahou, **zahodit** a vraťte se bez potvrzení změny provedené v výstrahy **zakázat**  deaktivovat výstrahu - po jejímž uplynutí ho už běží nebo se aktivuje žádnou akci. A nakonec **odstranit** trvale odeberete celý pravidlo výstrahy z Azure.

## <a name="next-steps"></a>Další postup

- Další informace o nové [téměř v reálném čase metriky výstrahy](monitoring-near-real-time-metric-alerts.md)
- Získat [přehled metriky kolekce](insights-how-to-customize-monitoring.md) zkontrolovat služby je k dispozici a dobře reagovaly.
- Další informace o [protokolu výstrahy ve výstrahách Azure](monitor-alerts-unified-log.md)
- [Další informace o protokolu upozornění v prostředí výstrahy (Preview)](monitoring-activity-log-alerts-new-experience.md)

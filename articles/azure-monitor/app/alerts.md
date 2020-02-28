---
title: Nastavení upozornění v Azure Application Insights
description: Získejte oznámení o pomalých časech odezvy, výjimkách a dalších změnách výkonu nebo využití ve vaší webové aplikaci.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 80759c94d7cc5b60b6e38a34b85fb64c3c18fd2e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666713"
---
# <a name="set-alerts-in-application-insights"></a>Nastavení výstrah v Application Insights

[Azure Application Insights][start] vás může upozorňovat na změny v metrikách výkonu nebo využití ve vaší webové aplikaci. 

Application Insights monitoruje svou živou aplikaci na nejrůznějších [platformách][platforms] , které vám pomůžou diagnostikovat problémy s výkonem a pochopit vzorce používání.

Existuje několik typů výstrah:

* [**Výstrahy metriky upozorňují**](../../azure-monitor/platform/alerts-metric-overview.md) na to, že metrika překročí prahovou hodnotu pro určitou dobu – například doba odezvy, počty výjimek, využití procesoru nebo zobrazení stránek.
* [**Výstrahy protokolu**](../../azure-monitor/platform/alerts-unified-log.md) se používají k popisu výstrah, ve kterých je signál výstrahy založen na vlastním dotazu Kusto.
* [**Webové testy**][availability] vás informují, že váš web není dostupný na internetu, nebo reaguje pomalu. [Další informace][availability].
* [**Proaktivní Diagnostika**](../../azure-monitor/app/proactive-diagnostics.md) jsou nakonfigurovány automaticky, aby vás upozornily na neobvyklé vzorce výkonu.

## <a name="set-a-metric-alert"></a>Nastavení upozornění na metriku

Otevřete kartu pravidla upozornění a pak použijte tlačítko Přidat.

![Na kartě pravidla výstrah vyberte Přidat výstrahu. Nastavte svoji aplikaci jako prostředek k měření, zadejte název výstrahy a vyberte metriku.](./media/alerts/01-set-metric.png)

* Nastavte prostředek před ostatními vlastnostmi. **Vyberte prostředek "(komponenty)"** , chcete-li nastavit výstrahy na metriky výkonu nebo využití.
* Název, který poskytnete výstraze, musí být jedinečný v rámci skupiny prostředků (ne jenom vaší aplikace).
* Nezapomeňte si uvědomit jednotky, ve kterých jste vyzváni k zadání prahové hodnoty.
* Pokud zaškrtnete políčko vlastníci e-mailu..., výstrahy se odesílají e-mailem všem, kdo mají přístup k této skupině prostředků. Pokud chcete tuto skupinu osob rozšířit, přidejte je do [skupiny prostředků nebo předplatného](../../azure-monitor/app/resources-roles-access-control.md) (ne prostředku).
* Pokud zadáte "další e-maily", odesílají se těmto jednotlivcům nebo skupinám výstrahy (bez ohledu na to, jestli jste kontrolovali "vlastníky e-mailu"... " Box). 
* Pokud jste nastavili webovou aplikaci, která bude reagovat na výstrahy, nastavte [adresu Webhooku](../../azure-monitor/platform/alerts-webhooks.md) . Je volána při aktivaci výstrahy a při jejím vyřešení. (Ale Všimněte si, že parametry dotazu nejsou v současné době předávány jako vlastnosti Webhooku.)
* Výstrahu můžete zakázat nebo povolit: Podívejte se na tlačítka v horní části.

*Nevidím tlačítko Přidat výstrahu.*

* Používáte účet organizace? Pokud máte oprávnění vlastníka nebo přispěvatele k tomuto prostředku aplikace, můžete nastavit výstrahy. Podívejte se na kartu Access Control. [Přečtěte si o řízení přístupu][roles].

> [!NOTE]
> V okně výstrahy vidíte, že už je nastavená výstraha: [Proactive Diagnostics](../../azure-monitor/app/proactive-failure-diagnostics.md). Automatická výstraha sleduje jednu konkrétní metriku, četnost neúspěšných požadavků. Pokud se nerozhodnete zakázat proaktivní výstrahu, nemusíte nastavit vlastní výstrahu na základě míry selhání žádosti.
> 
> 

## <a name="see-your-alerts"></a>Zobrazit vaše výstrahy
Když se změní stav výstrahy mezi neaktivní a aktivní, dostanete e-mail. 

Aktuální stav každé výstrahy je zobrazen na kartě pravidla výstrah.

V rozevíracím seznamu výstrahy se zobrazí souhrn nedávné aktivity:

![Rozevírací seznam výstrah](./media/alerts/010-alert-drop.png)

Historie změn stavu je v protokolu aktivit:

![Na kartě Přehled klikněte na nastavení, protokoly auditu.](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak výstrahy fungují
* Výstraha má tři stavy: "nikdy aktivované", "aktivované" a "Vyřešeno". Aktivováno znamená, že zadaná podmínka byla pravdivá, kdy byla naposledy vyhodnocena.
* Oznámení se vygeneruje při změně stavu výstrahy. (Pokud je podmínka upozornění již pravdivá při vytváření výstrahy, nemůžete obdržet oznámení, dokud podmínka nevrátí hodnotu false.)
* Každé oznámení vygeneruje e-mail, pokud jste zaškrtli políčko e-maily, nebo zadané e-mailové adresy. Můžete se také podívat na rozevírací seznam oznámení.
* Výstraha se vyhodnocuje pokaždé, když přijde metrika, ale ne jinak.
* Vyhodnocení agreguje metriku v předchozím období a pak ji porovná s prahovou hodnotou pro určení nového stavu.
* Zvolené období určuje interval, za který se budou metriky agregovat. Neovlivňuje, jak často se výstraha vyhodnocuje: závisí na četnosti příchodu metrik.
* Pokud žádná data pro určitou metriku nějakou dobu nepřijde, má mezera jiné účinky na vyhodnocení výstrahy a grafy v Průzkumníkovi metrik. Pokud se v Průzkumníkovi metrik nezobrazí žádná data déle než interval vzorkování grafu, zobrazí se v grafu hodnota 0. Výstraha založená na stejné metrikě ale nebude znovu vyhodnocena a stav výstrahy zůstane beze změny. 
  
    Když data nakonec dorazí, graf přejde zpět na nenulovou hodnotu. Výstraha se vyhodnocuje na základě dat dostupných v zadaném období. Je-li nový datový bod jediným dostupným v období, agregace je založena pouze na tomto datovém bodě.
* Výstraha může blikat často mezi výstrahami a dobrými stavy, i když nastavíte dlouhou dobu. K tomu může dojít, pokud se hodnota metriky pohybuje kolem prahové hodnoty. V prahové hodnotě neexistuje Hysteresis: přechod na výstrahu probíhá na stejné hodnotě jako přechod do stavu v pořádku.

## <a name="what-are-good-alerts-to-set"></a>Co jsou dobré výstrahy k nastavení?
Záleží na vaší aplikaci. Aby bylo možné začít používat, je vhodné nastavit příliš mnoho metrik. Věnujte nějaký čas prohlížení grafů metriky, když je vaše aplikace spuštěná, a získáte tak dojem, jak se normálně chová. Tento postup vám pomůže najít způsoby, jak vylepšit jeho výkon. Pak nastavte výstrahy a sdělte vám, že metriky přecházejí mimo normální zónu. 

Mezi oblíbené výstrahy patří:

* [Metriky prohlížeče][client], zejména **časy načítání stránek**prohlížeče, jsou vhodné pro webové aplikace. Pokud vaše stránka obsahuje mnoho skriptů, měli byste hledat **výjimky prohlížeče**. Aby bylo možné získat tyto metriky a výstrahy, je nutné nastavit [monitorování webové stránky][client].
* **Doba odezvy serveru** pro webové aplikace na straně serveru. Stejně jako při nastavování výstrah Sledujte tuto metriku, abyste zjistili, jestli se neúměrně mění vysoké rychlosti požadavků: variace může znamenat, že vaše aplikace nemá dostatek prostředků. 
* **Výjimky serveru** – Chcete-li je zobrazit, je nutné provést [Další nastavení](../../azure-monitor/app/asp-net-exceptions.md).

Nezapomeňte, že [Diagnostika proaktivní frekvence chyb](../../azure-monitor/app/proactive-failure-diagnostics.md) automaticky monitoruje rychlost, s jakou vaše aplikace reaguje na požadavky s kódy chyb.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Nastavení upozornění na výjimku pomocí vlastního prohledávání protokolu

V této části se naučíme, jak nastavit výstrahu pro výjimku založenou na dotazech. V tomto příkladu řekněme, že chceme, aby byla výstraha, když je neúspěšná míra větší než 10% za posledních 24 hodin.

1. V Azure Portal přejdete do svého prostředku Application Insight.
2. Vlevo v části konfigurovat klikněte na **výstrahu**.

    ![Vlevo v části konfigurace výstrahy kliknutí](./media/alerts/1appinsightalert.png)

3. V horní části karty výstraha vyberte **nové pravidlo výstrahy**.

     ![V horní části karty výstraha klikněte na nové pravidlo výstrahy.](./media/alerts/2createalert.png)

4. Váš prostředek by měl být vybraný automaticky. Pokud chcete nastavit podmínku, klikněte na **Přidat podmínku**.

    ![Klikněte na Přidat podmínku.](./media/alerts/3addcondition.png)

5. Na kartě Konfigurace logiky signálu vyberte **vlastní prohledávání protokolu** .

    ![Kliknout na vlastní prohledávání protokolu](./media/alerts/4customlogsearch.png)

6. Na kartě vlastní hledání protokolu zadejte dotaz do pole vyhledávací dotaz. V tomto příkladu použijeme následující dotaz Kusto.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Zadejte dotaz do pole vyhledávací dotaz](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Tyto kroky můžete použít i na jiné typy výstrah založených na dotazech. Další informace o dotazovacím jazyku Kusto najdete v [dokumentu Začínáme s Kusto](https://docs.microsoft.com/azure/kusto/concepts/) nebo tento dokument [SQL pro Kusto tahák](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet) .

7. V části logika výstrahy vyberte, jestli je založená na počtu výsledků, nebo měření metriky. Pak vyberte podmínku (větší než, rovná se, je menší než) a prahovou hodnotu. Když měníte tyto hodnoty, můžete si všimnout změny podmínky verze Preview. V tomto příkladu používáme "EQUAL to".

    ![V části logika výstrahy zvolte jednu z možností na základě podmínky a a pak zadejte prahovou hodnotu.](./media/alerts/6alertlogic.png)

8. V části "vyhodnocené na základě" nastavte období a četnost. Toto období se musí shodovat s hodnotou, kterou zadáte pro období výše v dotazu. Pak klikněte na **Hotovo**.

    ![Nastavte tečku a četnost v dolní části a potom klikněte na Hotovo.](./media/alerts/7evaluate.png)

9. Nyní se zobrazí podmínka, kterou jsme vytvořili, a Odhadované měsíční náklady. Pod položkou ["skupiny akcí"](../platform/action-groups.md) můžete vytvořit novou skupinu nebo vybrat některou z existujících. Pokud chcete, můžete akce přizpůsobit.

    ![v části Skupina akcí klikněte na tlačítka Vybrat nebo vytvořit.](./media/alerts/8actiongroup.png)

10. Nakonec přidejte podrobnosti výstrahy (název pravidla výstrahy, popis, závažnost). Až skončíte, klikněte dole na **vytvořit pravidlo výstrahy** .

    ![V části Podrobnosti výstrahy zadejte název pravidla upozornění, napište popis a vyberte závažnost.](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Zrušení odběru oznámení z klasického e-mailového upozornění

Tato část se vztahuje na **klasické výstrahy dostupnosti**, **výstrahy klasického Application Insights metriky**a na klasické výstrahy týkající se **anomálií selhání**.

Pokud platí některá z následujících možností, dostanete e-mailová oznámení pro tyto klasické výstrahy:

* Vaše e-mailová adresa je uvedená v poli příjemci e-mailu s oznámením v nastavení pravidla výstrahy.

* Aktivuje se možnost odesílání e-mailových oznámení uživatelům, kteří mají určité role pro předplatné, a u tohoto konkrétního předplatného Azure udržujete příslušnou roli.

![Snímek obrazovky s oznámením o výstrahách](./media/alerts/alert-notification.png)

Pro lepší kontrolu zabezpečení a ochrany osobních údajů obecně doporučujeme, abyste příjemcům oznámení pro klasické výstrahy explicitně zadali v poli **příjemci e-mailových oznámení** . Možnost upozorňování všech uživatelů, kteří drží určité role, je poskytována z důvodu zpětné kompatibility.

Pokud chcete odhlásit odběr e-mailových oznámení vytvořených pomocí určitého pravidla výstrahy, odeberte e-mailovou adresu z pole **příjemci e-mailu s oznámením** .

Pokud vaše e-mailová adresa není uvedená explicitně, doporučujeme vám zakázat možnost oznamovat všem členům určitých rolí automaticky a místo toho uvést všechny e-maily uživatelů, kteří potřebují dostávat oznámení pro toto pravidlo výstrahy v e-mailu s oznámením. pole příjemci.

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo obdrží oznámení o výstrahách (Classic)?

Tato část platí jenom pro klasické výstrahy a pomůže vám optimalizovat oznámení o výstrahách, abyste zajistili, že oznámení budou dostávat jenom vaši dožádaný příjemce. Pokud chcete získat další informace o rozdílu mezi [klasickými výstrahami](../platform/alerts-classic.overview.md) a novými výstrahami, přečtěte si [článek Přehled výstrah](../platform/alerts-overview.md). Chcete-li řídit oznamování výstrah v nové možnosti upozornění, použijte [skupiny akcí](../platform/action-groups.md).

* Pro klasická oznámení o výstrahách doporučujeme používat konkrétní příjemce.

* Pro výstrahy týkající se jakékoli metriky Application Insights (včetně metrik dostupnosti), pokud **je tato možnost** povolena, odesílá uživatelům v rámci předplatného role vlastník, přispěvatel nebo čtenář. V důsledku toho budou mít _Všichni_ uživatelé s přístupem k předplatnému Application Insights prostředek v oboru a budou dostávat oznámení.

> [!NOTE]
> Pokud v tuto chvíli používáte možnost **hromadného a skupinového** zaškrtávacího políčka a zakážete ji, nebudete moct změnu vrátit.

Pokud potřebujete upozornit uživatele na základě jejich rolí, použijte nové výstrahy Výstrahy a možnosti téměř v reálném čase. Pomocí [skupin akcí](../platform/action-groups.md)můžete nakonfigurovat e-mailová oznámení uživatelům pomocí kterékoli role Přispěvatel/vlastník/čtenář (bez kombinace společně s jednou možností).

## <a name="automation"></a>Automation
* [Použití PowerShellu k automatizaci nastavení výstrah](../../azure-monitor/app/powershell-alerts.md)
* [Použití webhooků k automatizaci reakce na výstrahy](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Viz také
* [Webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizace nastavení výstrah](../../azure-monitor/app/powershell-alerts.md)
* [Proaktivní Diagnostika](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md


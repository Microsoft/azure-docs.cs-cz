---
title: Nastavení upozornění ve službě Azure Application Insights | Dokumentace Microsoftu
description: Oznámení o pomalé odezvy, výjimky a další výkonu nebo použití změn ve vaší webové aplikaci.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: eb8e98f66d000290ce7eb07d3d73e82fbc43514a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693148"
---
# <a name="set-alerts-in-application-insights"></a>Nastavení upozornění ve službě Application Insights
[Azure Application Insights] [ start] může vás upozornit na změny v metrikách výkonu nebo využití ve vaší webové aplikaci. 

Application Insights monitoruje vaše živé aplikace na [široké škály platforem] [ platforms] můžete diagnostikovat problémy s výkonem a porozumět různým způsobům využití.

Existuje více typů výstrah:

* [**Upozornění na metriku** ](../../azure-monitor/platform/alerts-metric-overview.md) říct, pokud metrika překročí prahovou hodnotu určitou dobu – například dobu odezvy, počet výjimek, využití procesoru nebo zobrazení stránek.
* [**Upozornění protokolů** ](../../azure-monitor/platform/alerts-unified-log.md) se používá k popisu výstrahy, kde je signál upozornění podle vlastní dotaz Kusto.
* [**Webové testy** ] [ availability] zjistíte, když váš web není k dispozici na Internetu, nebo odpovídá pomalu. [Další informace][availability].
* [**Proaktivní Diagnostika** ](../../azure-monitor/app/proactive-diagnostics.md) umožňují automaticky informovat o výkonu neobvyklé vzory.

## <a name="set-a-metric-alert"></a>Nastavení upozornění na metriku
Otevřete kartu pravidla upozornění a potom použijte tlačítko Přidat.

![Na kartě pravidla upozornění zvolte možnost přidat oznámení. Nastavte aplikaci jako prostředek, který chcete měřit, zadejte název pro výstrahu a zvolte metriku.](./media/alerts/01-set-metric.png)

* Nastavení prostředku před dalšími vlastnostmi. **Zvolte prostředek "(součásti)"** Pokud budete chtít nastavit upozornění na metriky výkonu a využití.
* Název, který poskytnete k upozornění musí být jedinečný v rámci skupiny prostředků (ne jenom vaše aplikace).
* Věnujte jednotky, ve kterých budete vyzváni k zadání prahovou hodnotu.
* Pokud zaškrtnete políčko "E-mailu vlastníky...", oznámení odesílají e-mailem do každého, kdo má přístup k této skupině prostředků. Chcete-li rozbalit tuto skupinu lidí, přidejte je do [skupiny prostředků nebo předplatného](../../azure-monitor/app/resources-roles-access-control.md) (ne prostředků).
* Pokud zadáte "Další e-mailů", oznámení se odešlou do těchto jednotlivců nebo skupin (Určuje, jestli je zaškrtnuté políčko "e-mailová vlastníky..."). 
* Nastavte [webhooku adresu](../../azure-monitor/platform/alerts-webhooks.md) Pokud nastavíte webovou aplikaci, která bude reagovat na výstrahy. Je volána při aktivaci upozornění a po jeho vyřešení. (Všimněte si, že v současné době parametry dotazu neprocházejí jako vlastnosti webhooku.)
* Můžete zakázat nebo povolit upozornění: zobrazení tlačítek v horní části.

*Nevidím tlačítko Přidat oznámení.*

* Používáte účet organizace? Výstrahy můžete nastavit, pokud jste vlastníkem nebo přispěvatelem přístup k tomuto prostředku aplikace. Podívejte se na kartě pro řízení přístupu. [Další informace o řízení přístupu][roles].

> [!NOTE]
> V okně výstrahy uvidíte, že již sadu výstrah: [Proaktivní Diagnostika](../../azure-monitor/app/proactive-failure-diagnostics.md). Automatické výstrahy monitoruje jednu konkrétní metriky, požadavek chybovost. Pokud budete chtít zakázat proaktivní výstrahu, není nutné nastavit vlastní oznámení na míru selhání požadavku.
> 
> 

## <a name="see-your-alerts"></a>Zobrazit upozornění
Dostanete e-mail, když dostane do stavu oznámení změny mezi aktivní a neaktivní. 

Aktuální stav jednotlivých výstrah se zobrazí na kartě pravidla upozornění.

Zde je uveden seznam poslední aktivita u výstrah rozevíracího seznamu:

![Rozevírací seznam upozornění](./media/alerts/010-alert-drop.png)

Historii změn stavu je v protokolu aktivit:

![Na kartě Přehled klikněte na nastavení, protokoly auditu](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak výstrahy fungují
* Upozornění má tři stavy: "Nikdy neaktivoval", "Aktivní" a "Vyřešeno". Aktivovaná znamená, že podmínku, kterou jste zadali dřív platilo, pokud bylo naposled vyhodnoceno.
* Oznámení se vygeneruje, když se výstraha změní stav. (Pokud se vyskytl výstražný stav dřív platilo již při vytvoření výstrahy, můžete nemusí oznámení, dokud podmínka přestane false.)
* Jednotlivým oznámením generuje e-mailu, pokud je zaškrtnuté políčko e-mailů, nebo zadaná e-mailové adresy. Můžete se také podívat na rozevírací seznam oznámení.
* Výstraha se vyhodnocuje pokaždé, když dorazí metriky, ale není jinak.
* Vyhodnocení agreguje metriku předchozím období a porovná ho s prahovou hodnotu k určení nového stavu.
* Doba, kterou zvolíte, určuje interval, po kterou metriky se agregují. Nemá vliv, jak často se vyhodnotí upozornění:, který závisí na četnosti přijetí metrik.
* Dorazí žádná data pro konkrétní metrika po určitou dobu, mezery se různé účinky na výstrahy hodnocení a u grafů v Průzkumníku metrik. V Průzkumníku metrik Pokud je zobrazena žádná data po dobu delší než interval vzorkování na graf, tento graf zobrazuje hodnotu 0. Ale výstrahy na základě stejné metriky se znovu vyhodnotit, a stav výstrahy zůstane beze změny. 
  
    Po přijetí nakonec dat, graf přejde zpět na nenulovou hodnotu. Upozornění vyhodnotí na základě dat, které jsou k dispozici pro období, které jste zadali. Pokud nový datový bod je k dispozici v období pouze jeden, agregace je založená jenom na datových bodů.
* Výstrahu můžete často blikat mezi stavy výstrah a v pořádku, i když nastavíte dlouhou dobu. To může nastat, když hodnota metriky pohybuje okolo prahovou hodnotu. Neexistuje žádné hystereze v prahové hodnotě: přechod na výstrahy se odehrává na stejnou hodnotu jako přechodu na v pořádku.

## <a name="what-are-good-alerts-to-set"></a>Co jsou dobré upozornění pro nastavení?
To závisí na vaší aplikace. Začněte tím doporučujeme nenastavovat příliš mnoho metriky. Věnujte nějaký čas pohledu na vaše grafy metrik, když vaše aplikace běží, jak se normálně chová získat představu. Tento postup vám pomůže najít způsoby, jak vylepšit výkon. Potom můžete nastavit výstrahy a zjistíte, když metriky výlet za hranice normální zóny. 

Oblíbené výstrahy obsahují:

* [Prohlížeč metriky][client], zejména prohlížeče **stránce dobu načítání**, jsou vhodné pro webové aplikace. Pokud vaše stránka obsahuje mnoho skriptů, je vhodné vyhledat **výjimky prohlížeče**. Pokud chcete získat tyto metriky a výstrahy, budete muset nastavit [webové stránky monitorování][client].
* **Doba odezvy serveru** pro webové aplikace na straně serveru. A také nastavení výstrah, sledovat tuto metriku, pokud chcete zobrazit, pokud se mění nepřiměřeně s vysokou požadavků: změna může znamenat, že vaše aplikace běží nemá dostatek prostředků. 
* **Serverové výjimky** – Pokud chcete zobrazit, budete muset provést některé [další nastavení](../../azure-monitor/app/asp-net-exceptions.md).

Nezapomeňte, že [proaktivní míra Diagnostika chyb](../../azure-monitor/app/proactive-failure-diagnostics.md) automaticky sledovat rychlost, jakou aplikace reaguje na požadavky s kódy selhání.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Jak nastavit výstrahy výjimek pomocí hledání vlastních protokolů

V této části provedeme nastavení upozornění výjimka na základě dotazu. Například Řekněme, že chceme výstrahu, jestliže je míra selhání větší než 10 % za posledních 24 hodin.

1. Přejděte do prostředku Application Insights na webu Azure Portal.
2. Na levé straně v části konfigurace klikněte na **výstrah**.

    ![Na levé straně v části konfigurace klikněte na výstrahu](./media/alerts/1appinsightalert.png)

3. V horní části na kartu výstraha vyberte **nové pravidlo upozornění**.

     ![V horní části na kartu výstraha klikněte na nové pravidlo upozornění](./media/alerts/2createalert.png)

4. Prostředek by měl být automaticky vybraná. Pokud chcete nastavit podmínky, klikněte na tlačítko **přidat podmínku**.

    ![Klikněte na tlačítko Přidat podmínku](./media/alerts/3addcondition.png)

5. Na kartě Konfigurace signál logiky vyberte **vlastní prohledávání protokolů**

    ![Klikněte na tlačítko Hledat vlastní protokol](./media/alerts/4customlogsearch.png)

6. Na kartě vlastní protokol hledání zadejte dotaz do pole "Vyhledávací dotaz". V tomto příkladu budeme používat pod dotaz Kusto.
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

    ![Zadejte dotaz do vyhledávacího pole dotazu](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Tyto kroky můžete provést také u jiných typů výstrah založených na dotazech. Další informace o jazyce dotaz Kusto z tohoto [Kusto Začínáme doc](https://docs.microsoft.com/azure/kusto/concepts/) nebo to [SQL pro Kusto tahák](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. V části "Alert logic" zvolte, zda je založena na počtu výsledků nebo metriky měření. Potom si vyberte podmínku (větší než, rovna, méně) a prahovou hodnotu. Když chcete změnit tyto hodnoty, můžete si všimnout změny věty podmínky ve verzi preview. V tomto příkladu používáme "rovno".

    ![V části Alert logic vybírat možnosti, které jsou k dispozici na základě a podmínky a pak zadejte prahové hodnoty](./media/alerts/6alertlogic.png)

8. V části "Evaluated podle" nastavte období a četnost. Období zde musí odpovídat hodnotě, máme pro období ve výše uvedený dotaz. Pak klikněte na tlačítko **provádí**.

    ![Nastavte období a frekvenci v dolní části a pak klikněte na tlačítko Hotovo](./media/alerts/7evaluate.png)

9. Teď vidíme podmínku, kterou jsme vytvořili s odhadované měsíční náklady. Níže v části ["Skupiny akcí"](../platform/action-groups.md) můžete vytvořit novou skupinu nebo vyberte existující. Pokud chcete, můžete přizpůsobit akce.

    ![Klikněte na Vybrat nebo vytvořit tlačítka v části skupiny akcí](./media/alerts/8actiongroup.png)

10. Nakonec přidejte podrobnosti o vašem výstrah (výstrahy název pravidla, popis, závažnosti). Jakmile budete hotovi, klikněte na tlačítko **vytvořit pravidlo upozornění** v dolní části.

    ![V části podrobností výstrahy zadejte název pravidla upozornění, napište popis a vybrat závažnost](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Jak zrušit odběr klasického upozornění e-mailová oznámení

Tato část se týká **upozornění na dostupnost klasické**, **klasického upozornění metrik Application Insights**a získat **upozornění na anomálie selhání classic**.

Vám posíláme e-mailová oznámení pro tyto klasických upozornění, pokud platí některý z následujících akcí:

* E-mailová adresa je uveden v poli příjemci e-mailová oznámení v nastavení pravidla upozornění.

* Je aktivovaná možnost odeslat e-mailová oznámení pro uživatele, která uchovává určité role pro předplatné a podržte příslušné role pro toto konkrétní předplatné Azure.

![Snímek obrazovky oznámení výstrah](./media/alerts/alert-notification.png)

Pro lepší kontrolu nad zabezpečení a ochrany osobních údajů obecně doporučujeme explicitně zadat příjemce oznámení pro upozornění classic ve **příjemců e-mailová oznámení** pole. Umožňuje všem uživatelům, která uchovává určité role je k dispozici z důvodu zpětné kompatibility.

Pokud chcete zrušit odběr vygenerována pravidlem určitých výstrah e-mailová oznámení, odeberte e-mailovou adresu z **příjemců e-mailová oznámení** pole.

Pokud vaše e-mailová adresa není uvedená explicitně, doporučujeme zakázat možnost automaticky upozornění všechny členy určité role a místo toho seznamu všechny e-maily uživatele, kteří se chtějí dostávat oznámení pro dané pravidlo upozornění v e-mailové oznámení pole příjemců.

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo přijímá oznámení výstrah (klasické)?

V této části pouze platí pro upozornění classic a pomůže vám optimalizovat vaše oznámení o výstrahách Ujistěte se, že pouze požadované příjemci dostávat oznámení. Bližší informace o rozdílech mezi [klasických upozornění](../platform/alerts-classic.overview.md) a nové výstrahy prostředí, přečtěte si [výstrahy přehledovém článku](../platform/alerts-overview.md). Pokud chcete nastavit oznámení upozornění v nové prostředí upozornění, použijte [skupiny akcí](../platform/action-groups.md).

* Doporučujeme vám používat konkrétním příjemcům classic oznámení výstrah.

* Pro výstrahy týkající se všech metrik Application Insights (včetně metrik dostupnosti) **hromadné/skupiny** zaškrtávací políčko, pokud je povoleno, odešle uživatelům s rolí vlastník, Přispěvatel nebo Čtenář v rámci předplatného. V důsledku toho _všechny_ uživatelé s přístupem k předplatnému prostředku Application Insights jsou v rozsahu a budou dostávat oznámení.

> [!NOTE]
> Pokud aktuálně používáte službu **hromadné/skupiny** zaškrtávací políčko a zakázat, nebude možné vrátit zpět změny.

Pomocí nové výstrahy prostředí/v – v reálném čase výstrahy, pokud je potřeba upozornit uživatele na základě jejich rolí. S [skupiny akcí](../platform/action-groups.md), můžete nakonfigurovat e-mailová oznámení pro uživatele s libovolnou z role přispěvatele nebo vlastníka/reader (ne zkopírovat dohromady jako jednu možnost).

## <a name="automation"></a>Automation
* [Použití Powershellu k automatizaci nastavení výstrahy](../../azure-monitor/app/powershell-alerts.md)
* [Automatizace reagování na výstrahy pomocí webhooků](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizace nastavení výstrahy](../../azure-monitor/app/powershell-alerts.md)
* [Proaktivní Diagnostika](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md


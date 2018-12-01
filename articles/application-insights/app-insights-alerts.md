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
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: ac91632656e83fc4262f28240525c8fb106b99ec
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679117"
---
# <a name="set-alerts-in-application-insights"></a>Nastavení upozornění ve službě Application Insights
[Azure Application Insights] [ start] může vás upozornit na změny v metrikách výkonu nebo využití ve vaší webové aplikaci. 

Application Insights monitoruje vaše živé aplikace na [široké škály platforem] [ platforms] můžete diagnostikovat problémy s výkonem a porozumět různým způsobům využití.

Existují tři druhy výstrah:

* **Upozornění na metriku** říct, pokud metrika překročí prahovou hodnotu určitou dobu – například dobu odezvy, počet výjimek, využití procesoru nebo zobrazení stránek. 
* [**Webové testy** ] [ availability] zjistíte, když váš web není k dispozici na Internetu, nebo odpovídá pomalu. [Další informace][availability].
* [**Proaktivní Diagnostika** ](app-insights-proactive-diagnostics.md) umožňují automaticky informovat o výkonu neobvyklé vzory.

Zaměřujeme se na upozornění metrik v tomto článku.

## <a name="set-a-metric-alert"></a>Nastavení upozornění na metriku
Otevře se okno pravidla upozornění a potom použijte tlačítko Přidat. 

![V okně pravidla upozornění zvolte Přidat oznámení. Nastavte aplikaci jako prostředek, který chcete měřit, zadejte název pro výstrahu a zvolte metriku.](./media/app-insights-alerts/01-set-metric.png)

* Nastavení prostředku před dalšími vlastnostmi. **Zvolte prostředek "(součásti)"** Pokud budete chtít nastavit upozornění na metriky výkonu a využití.
* Název, který poskytnete k upozornění musí být jedinečný v rámci skupiny prostředků (ne jenom vaše aplikace).
* Věnujte jednotky, ve kterých budete vyzváni k zadání prahovou hodnotu.
* Pokud zaškrtnete políčko "E-mailu vlastníky...", oznámení odesílají e-mailem do každého, kdo má přístup k této skupině prostředků. Chcete-li rozbalit tuto skupinu lidí, přidejte je do [skupiny prostředků nebo předplatného](app-insights-resources-roles-access-control.md) (ne prostředků).
* Pokud zadáte "Další e-mailů", oznámení se odešlou do těchto jednotlivců nebo skupin (Určuje, jestli je zaškrtnuté políčko "e-mailová vlastníky..."). 
* Nastavte [webhooku adresu](../monitoring-and-diagnostics/insights-webhooks-alerts.md) Pokud nastavíte webovou aplikaci, která bude reagovat na výstrahy. Je volána při aktivaci upozornění a po jeho vyřešení. (Všimněte si, že v současné době parametry dotazu neprocházejí jako vlastnosti webhooku.)
* Můžete zakázat nebo povolit upozornění: zobrazení tlačítek v horní části okna.

*Nevidím tlačítko Přidat oznámení.* 

* Používáte účet organizace? Výstrahy můžete nastavit, pokud jste vlastníkem nebo přispěvatelem přístup k tomuto prostředku aplikace. Podívejte se v okně řízení přístupu. [Další informace o řízení přístupu][roles].

> [!NOTE]
> V okně výstrahy uvidíte, že již existuje upozornění sady: [proaktivní Diagnostika](app-insights-proactive-failure-diagnostics.md). Automatické výstrahy monitoruje jednu konkrétní metriky, požadavek chybovost. Pokud budete chtít zakázat proaktivní výstrahu, není nutné nastavit vlastní oznámení na míru selhání požadavku. 
> 
> 

## <a name="see-your-alerts"></a>Zobrazit upozornění
Dostanete e-mail, když dostane do stavu oznámení změny mezi aktivní a neaktivní. 

Aktuální stav jednotlivých výstrah se zobrazí v okně pravidla upozornění.

Zde je uveden seznam poslední aktivita u výstrah rozevíracího seznamu:

![Rozevírací seznam upozornění](./media/app-insights-alerts/010-alert-drop.png)

Historii změn stavu je v protokolu aktivit:

![V okně Přehled klikněte na nastavení, protokoly auditu](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak výstrahy fungují
* Upozornění má tři stavy: "Neaktivoval", "Aktivní" a "Vyřešeno". Aktivovaná znamená, že podmínku, kterou jste zadali dřív platilo, pokud bylo naposled vyhodnoceno.
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
* **Serverové výjimky** – Pokud chcete zobrazit, budete muset provést některé [další nastavení](app-insights-asp-net-exceptions.md).

Nezapomeňte, že [proaktivní míra Diagnostika chyb](app-insights-proactive-failure-diagnostics.md) automaticky sledovat rychlost, jakou aplikace reaguje na požadavky s kódy selhání. 

## <a name="automation"></a>Automation
* [Použití Powershellu k automatizaci nastavení výstrahy](app-insights-powershell-alerts.md)
* [Automatizace reagování na výstrahy pomocí webhooků](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Další informace najdete v tématech
* [Testy dostupnosti webu](app-insights-monitor-web-app-availability.md)
* [Automatizace nastavení výstrahy](app-insights-powershell-alerts.md)
* [Proaktivní Diagnostika](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


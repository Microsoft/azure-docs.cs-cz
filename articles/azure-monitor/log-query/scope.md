---
title: Protokolovat oboru dotazu ve službě Azure Monitor Log Analytics | Dokumentace Microsoftu
description: Popisuje obor a časový rozsah pro dotaz protokolu ve službě Azure Monitor Log Analytics.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297365"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Protokol dotazu obor a časový rozsah ve službě Azure Monitor Log Analytics
Při spuštění [dotaz protokolu](log-query-overview.md) v [Log Analytics na portálu Azure portal](get-started-portal.md), sada dat vyhodnotit dotazem závisí na obor a časový rozsah, který jste vybrali. Tento článek popisuje obor a časový rozsah a jak můžete nastavit každou podle vašich potřeb. Také popisuje chování různých typů oborů.


## <a name="query-scope"></a>Rozsah dotazu
Obor dotazu určuje záznamy, které jsou vyhodnocovány v dotazu. Obvykle bude zahrnovat všechny záznamy v jeden pracovní prostor Log Analytics nebo aplikaci Application Insights. Log Analytics můžete také nastavit obor pro konkrétní monitorovaných prostředků Azure. Vlastník prostředku chcete zaměřit pouze na svých dat díky tomu i v případě, že tento prostředek se zapíše do několika pracovních prostorů.

Obor je vždy zobrazen v horní levé části okna Log Analytics. Ikona označuje, zda je oboru pracovního prostoru Log Analytics nebo z některé aplikace Application Insights. Žádná ikona určuje jiný prostředek Azure.

![Scope](media/scope/scope.png)

Rozsah je určen pomocí metody používáte ke spuštění Log Analytics, a v některých případech můžete kliknutím na změnit rozsah. Následující tabulka uvádí různé druhy oborem použitým a různé podrobnosti pro každou.

| Rozsah dotazu | Záznamy v oboru | Jak vybrat | Změna rozsahu |
|:---|:---|:---|:---|
| Pracovní prostor Log Analytics | Všechny záznamy v pracovním prostoru Log Analytics. | Vyberte **protokoly** z **Azure Monitor** nabídky nebo **pracovních prostorů Log Analytics** nabídky.  | Můžete změnit obor k žádným jiným typům prostředků. |
| Aplikace Application Insights | Všechny záznamy v aplikaci Application Insights. | Vyberte **Analytics** z **přehled** stránky služby Application Insights. | Můžete pouze změnit obor na jinou aplikaci Application Insights. |
| Skupina prostředků | Záznamy vytvořené všechny prostředky ve skupině prostředků. Můžou zahrnovat data z několika pracovních prostorů Log Analytics. | Vyberte **protokoly** v nabídce skupiny prostředků. | Nelze změnit obor.|
| Předplatné | Záznamy vytvořené všechny prostředky v předplatném. Můžou zahrnovat data z několika pracovních prostorů Log Analytics. | Vyberte **protokoly** z nabídky předplatného.   | Nelze změnit obor. |
| Další prostředky Azure | Záznamy vytvořené zdroje. Můžou zahrnovat data z několika pracovních prostorů Log Analytics.  | Vyberte **protokoly** v nabídce prostředků.<br>NEBO<br>Vyberte **protokoly** z **Azure Monitor** nabídky a pak vyberte nový obor. | Můžete pouze změnit obor na stejný typ prostředku. |

### <a name="limitations-when-scoped-to-a-resource"></a>Omezení při omezená na prostředek

Když rozsah dotazu je pracovní prostor Log Analytics nebo z některé aplikace Application Insights, jsou k dispozici všechny možnosti na portálu a všechny příkazy dotazu. Když ale vymezené na prostředek, následující možnosti není k dispozici na portálu, protože jsou přidružené k jeden pracovní prostor nebo aplikace:

- Uložení
- Průzkumník dotazů
- Nové pravidlo upozornění

V dotazu, pokud obor na prostředek, protože oboru dotazu bude již obsahovat žádné pracovní prostory s daty pro tento prostředek nebo sadu prostředků nelze použít následující příkazy:

- [app](app-expression.md)
- [Pracovní prostor](workspace-expression.md)
 


## <a name="time-range"></a>Časové rozmezí
Časový rozsah Určuje sadu záznamů, která se vyhodnotí pro dotaz na základě když se záznam vytvořil. Toto je definováno pomocí standardní vlastnosti na každý záznam v pracovním prostoru nebo aplikace, jak je uvedeno v následující tabulce.

| Location | Vlastnost |
|:---|:---|
| Pracovní prostor Log Analytics          | TimeGenerated |
| Aplikace Application Insights | timestamp     |

Nastavte časový rozsah tak, že ji vyberete výběr času v horní části okna Log Analytics.  Můžete vybrat určitou dobu nebo vyberte **vlastní** určit konkrétní časové období.

![Výběr času](media/scope/time-picker.png)

Pokud nastavíte filtru v dotazu, který používá vlastnost (běžný čas), jak je znázorněno v předchozí tabulce, výběr času se změní na **nastavit v dotazu**, a výběr času je zakázán. V takovém případě je nejefektivnější do filtru v horní části dotazu tak, aby všechny následné zpracování potřebuje pouze pro práci s filtrované záznamy.

![Filtrovaného dotazu](media/scope/query-filtered.png)

Pokud používáte [pracovní prostor](workspace-expression.md) nebo [aplikace](app-expression.md) příkaz k načtení dat z jiného pracovního prostoru nebo aplikace, nástroje pro výběr času může chovat jinak. Pokud obor je pracovní prostor Log Analytics a používáte **aplikace**, nebo pokud obor je aplikace Application Insights a použijete **pracovní prostor**, pak Log Analytics nemusíte rozumět tomu, že je vlastnost použita v Filtr by měl určit filtr času.

V následujícím příkladu rozsah se nastaví na pracovní prostor Log Analytics.  Dotaz používá **pracovní prostor** k načtení dat z jiného pracovního prostoru Log Analytics. Výběr času se změní na **nastavit v dotazu** protože vidí filtr, který používá očekávané **TimeGenerated** vlastnost.

![Dotazování pomocí pracovního prostoru](media/scope/query-workspace.png)

Pokud dotaz používá **aplikace** ale načíst data z aplikace pro Application Insights, Log Analytics nerozpoznal **časové razítko** vlastnost filtr a výběr času zůstane beze změny. V tomto případě jsou použity oba filtry. V tomto příkladu pouze záznamy vytvořené za posledních 24 hodin jsou zahrnuty v dotazu i v případě, že určuje 7 dní v **kde** klauzuli.

![Dotazování pomocí aplikace](media/scope/query-app.png)

## <a name="next-steps"></a>Další postup

- Projít [kurz o používání Log Analytics na portálu Azure portal](get-started-portal.md).
- Projít [kurz o psaní dotazů](get-started-queries.md).
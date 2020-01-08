---
title: Rozsah dotazu protokolu v Azure Monitor Log Analytics | Microsoft Docs
description: Popisuje rozsah a časový rozsah pro dotaz protokolu v Azure Monitor Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: dec81bfde160cd9913db07bb99629b8fbcc37364
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365202"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Rozsah dotazu protokolu a časový rozsah ve Azure Monitor Log Analytics
Když spustíte [dotaz protokolu](log-query-overview.md) v [Log Analytics Azure Portal](get-started-portal.md), sada dat vyhodnocených dotazem závisí na rozsahu a časovém rozsahu, který jste vybrali. Tento článek popisuje rozsah a časový rozsah a způsob, jakým můžete nastavit jednotlivé požadavky v závislosti na vašich požadavcích. Popisuje také chování různých typů oborů.


## <a name="query-scope"></a>Rozsah dotazu
Obor dotazu definuje záznamy, které jsou vyhodnocovány dotazem. Tato akce obvykle zahrnuje všechny záznamy v jednom pracovním prostoru Log Analytics nebo v Application Insights aplikaci. Log Analytics taky umožňuje nastavit obor pro konkrétní monitorovaný prostředek Azure. To umožňuje vlastníkovi prostředků soustředit se jenom na svá data, a to i v případě, že se tento prostředek zapisuje do několika pracovních prostorů.

Obor je vždy zobrazen v levém horním rohu okna Log Analytics. Ikona označuje, zda je oborem Log Analytics pracovní prostor nebo aplikace Application Insights. Žádná ikona indikuje jiný prostředek Azure.

![Rozsah](media/scope/scope.png)

Rozsah je určen metodou, kterou použijete ke spuštění Log Analytics, a v některých případech můžete obor změnit kliknutím na něj. V následující tabulce jsou uvedeny různé typy použitého oboru a různé podrobnosti pro každou z nich.

| Rozsah dotazu | Záznamy v oboru | Jak vybrat | Změna oboru |
|:---|:---|:---|:---|
| Pracovní prostor Log Analytics | Všechny záznamy v pracovním prostoru Log Analytics. | V nabídce **Azure monitor** nebo v nabídce **Log Analytics pracovní prostory** vyberte **protokoly** .  | Může změnit obor na libovolný jiný typ prostředku. |
| Application Insights aplikace | Všechny záznamy v aplikaci Application Insights. | Na stránce s **přehledem** Application Insights vyberte **Analytics** . | Rozsah lze změnit pouze na jinou Application Insights aplikaci. |
| Skupina prostředků | Záznamy vytvořené všemi prostředky ve skupině prostředků. Může zahrnovat data z více Log Analytics pracovních prostorů. | V nabídce skupina prostředků vyberte **protokoly** . | Nelze změnit obor.|
| Předplatné | Záznamy vytvořené všemi prostředky v předplatném. Může zahrnovat data z více Log Analytics pracovních prostorů. | V nabídce předplatné vyberte **protokoly** .   | Nelze změnit obor. |
| Další prostředky Azure | Záznamy vytvořené prostředkem. Může zahrnovat data z více Log Analytics pracovních prostorů.  | V nabídce prostředek vyberte **protokoly** .<br>NEBO<br>V nabídce **Azure monitor** vyberte **protokoly** a pak vyberte nový obor. | Obor lze změnit pouze na stejný typ prostředku. |

### <a name="limitations-when-scoped-to-a-resource"></a>Omezení při vymezení prostředku

Pokud je oborem dotazu Log Analytics pracovní prostor nebo aplikace Application Insights, jsou k dispozici všechny možnosti na portálu a příkazy dotazů. V případě oboru na prostředek, následující možnosti portálu nejsou k dispozici, protože jsou přidruženy k jednomu pracovnímu prostoru nebo aplikaci:

- Uložit
- Průzkumník dotazů
- Nové pravidlo upozornění

V dotazu, který je vymezený na prostředek, nemůžete použít následující příkazy, protože obor dotazu již bude obsahovat všechny pracovní prostory s daty pro daný prostředek nebo sadu prostředků:

- [app](app-expression.md)
- [stejných](workspace-expression.md)
 

## <a name="query-limits"></a>Omezení dotazů
Můžete mít obchodní požadavky na prostředek Azure, abyste mohli zapisovat data do několika pracovních prostorů Log Analytics. Pracovní prostor nemusí být ve stejné oblasti jako prostředek a jeden pracovní prostor může shromažďovat data z prostředků v různých oblastech.  

Nastavení oboru na prostředek nebo sadu prostředků je zvláště výkonná funkce Log Analytics, protože umožňuje automaticky konsolidovat distribuovaná data v jednom dotazu. I když je potřeba načíst data z pracovních prostorů napříč několika oblastmi Azure, může to významně ovlivnit výkon.

Log Analytics pomáhá chránit před nadměrnými nároky na dotazy, které zaujímají pracovní prostory v několika oblastech, a to vyvoláním upozornění nebo chyby, když se používá určitý počet oblastí. Dotaz zobrazí upozornění, pokud obor obsahuje pracovní prostory v 5 nebo více oblastech. Pořád se spustí, ale dokončení může trvat příliš dlouho.

![Upozornění dotazu](media/scope/query-warning.png)

Spuštění dotazu bude zablokováno, pokud obor obsahuje pracovní prostory ve 20 nebo více oblastech. V takovém případě se zobrazí výzva, abyste snížili počet oblastí pracovního prostoru a pokusili jste se spustit dotaz znovu. V rozevíracím seznamu se zobrazí všechny oblasti v oboru dotazu. před pokusem o spuštění dotazu byste měli snížit počet oblastí.

![Dotaz se nezdařil](media/scope/query-failed.png)


## <a name="time-range"></a>Časové rozmezí
Časový rozsah určuje sadu záznamů, které jsou vyhodnocovány pro dotaz na základě vytvoření záznamu. Tato vlastnost je definována standardní vlastností u každého záznamu v pracovním prostoru nebo v aplikaci, jak je uvedeno v následující tabulce.

| Umístění | Vlastnost |
|:---|:---|
| Pracovní prostor Log Analytics          | TimeGenerated |
| Application Insights aplikace | časové razítko     |

Nastavte časový rozsah tak, že ho vyberete v poli pro výběr času v horní části okna Log Analytics.  Můžete vybrat předdefinované období nebo vybrat **vlastní** a zadat konkrétní časový rozsah.

![Výběr času](media/scope/time-picker.png)

Pokud nastavíte filtr v dotazu, který používá vlastnost standardního času, jak je znázorněno v tabulce výše, výběr času se změní na **nastaveno v dotazu**a výběr času je zakázán. V tomto případě je nejefektivnější umístit filtr na začátek dotazu, aby jakékoli následné zpracování fungovalo pouze s filtrovanými záznamy.

![Filtrovaný dotaz](media/scope/query-filtered.png)

Pokud použijete příkaz [pracovní prostor](workspace-expression.md) nebo [aplikace](app-expression.md) k načtení dat z jiného pracovního prostoru nebo aplikace, výběr času se může chovat jinak. Pokud je oborem Log Analytics pracovní prostor a používáte **aplikaci**, nebo pokud je oborem Application Insights aplikace a používáte **pracovní prostor**, Log Analytics nemusí pochopit, že by vlastnost použitá ve filtru měla určovat časový filtr.

V následujícím příkladu je obor nastaven na Log Analytics pracovní prostor.  Dotaz používá **pracovní prostor** k načtení dat z jiného pracovního prostoru Log Analytics. Výběr času se změní na **nastavení v dotazu** , protože vidí filtr, který používá očekávanou vlastnost **TimeGenerated** .

![Dotaz s pracovním prostorem](media/scope/query-workspace.png)

Pokud dotaz používá **aplikaci** k načtení dat z Application Insights aplikace, Log Analytics ale nerozpozná vlastnost **timestamp** ve filtru a výběr času zůstane beze změny. V tomto případě jsou aplikovány oba filtry. V tomto příkladu jsou do dotazu zahrnuty pouze záznamy vytvořené za posledních 24 hodin, i když v klauzuli **WHERE** zadává 7 dní.

![Dotazování pomocí aplikace](media/scope/query-app.png)

## <a name="next-steps"></a>Další kroky

- Projděte si [kurz použití Log Analytics v Azure Portal](get-started-portal.md).
- Projděte si [kurz týkající se psaní dotazů](get-started-queries.md).

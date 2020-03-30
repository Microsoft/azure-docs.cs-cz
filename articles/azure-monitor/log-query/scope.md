---
title: Protokolovat obor dotazu v Azure Monitor Log Analytics | Dokumenty společnosti Microsoft
description: Popisuje rozsah a časový rozsah pro dotaz protokolu v Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249591"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Log query scope and time range in Azure Monitor Log Analytics
Při spuštění [dotazu protokolu](log-query-overview.md) v [Log Analytics na webu Azure Portal](get-started-portal.md), sada dat vyhodnocených dotazem závisí na oboru a časové oblasti, které vyberete. Tento článek popisuje rozsah a časový rozsah a jak můžete nastavit každý v závislosti na vašich požadavcích. Popisuje také chování různých typů oborů.


## <a name="query-scope"></a>Rozsah dotazu
Obor dotazu definuje záznamy, které jsou vyhodnocovány dotazem. To obvykle zahrnuje všechny záznamy v jednom pracovním prostoru Log Analytics nebo application Insights aplikace. Log Analytics také umožňuje nastavit obor pro konkrétní monitorovaný prostředek Azure. To umožňuje vlastníkovi prostředku zaměřit se pouze na jejich data, i v případě, že prostředek zapíše do více pracovních prostorů.

Obor se vždy zobrazí v levém horním rohu okna Analýzy protokolů. Ikona označuje, zda je obor pracovní prostor Analýzy protokolů nebo aplikace Application Insights. Žádná ikona označuje jiný prostředek Azure.

![Rozsah](media/scope/scope.png)

Obor je určen metodou, kterou používáte ke spuštění analýzy protokolů, a v některých případech můžete změnit obor kliknutím na něj. V následující tabulce jsou uvedeny různé typy použitého oboru a různé podrobnosti pro každý z nich.

| Rozsah dotazu | Záznamy v oboru | Jak vybrat | Změna oboru |
|:---|:---|:---|:---|
| Pracovní prostor služby Log Analytics | Všechny záznamy v pracovním prostoru Log Analytics. | V nabídce **Azure Monitor** nebo **v pracovních prostorech Analýzy protokolů** vyberte **Protokoly.**  | Můžete změnit obor na jiný typ prostředku. |
| Aplikace Application Insights | Všechny záznamy v aplikaci Application Insights. | Na stránce **Přehled** aplikací vyberte **Analytics.** | Můžete změnit pouze obor na jinou aplikaci Application Insights. |
| Skupina prostředků | Záznamy vytvořené všemi prostředky ve skupině prostředků. Může obsahovat data z více pracovních prostorů Log Analytics. | V nabídce skupiny prostředků vyberte **Protokoly.** | Rozsah nelze změnit.|
| Předplatné | Záznamy vytvořené všemi prostředky v předplatném. Může obsahovat data z více pracovních prostorů Log Analytics. | V nabídce odběr vyberte **Protokoly.**   | Rozsah nelze změnit. |
| Další prostředky Azure | Záznamy vytvořené zdrojem. Může obsahovat data z více pracovních prostorů Log Analytics.  | V nabídce prostředků vyberte **Protokoly.**<br>NEBO<br>Z nabídky **Azure Monitor** vyberte **Protokoly** a pak vyberte nový obor. | Rozsah lze změnit pouze na stejný typ prostředku. |

### <a name="limitations-when-scoped-to-a-resource"></a>Omezení při vymezení rozsahu pro prostředek

Pokud je obor dotazu pracovní prostor Analýzy protokolů nebo aplikace Application Insights, jsou k dispozici všechny možnosti na portálu a všechny příkazy dotazu. Při vymezení rozsahu pro prostředek však následující možnosti na portálu nejsou k dispozici, protože jsou přidruženy k jednomu pracovnímu prostoru nebo aplikaci:

- Uložit
- Průzkumník dotazů
- Nové pravidlo výstrahy

Při vymezení oboru na prostředek nelze v dotazu použít následující příkazy, protože obor dotazu již bude obsahovat všechny pracovní prostory s daty pro tento prostředek nebo sadu prostředků:

- [App](app-expression.md)
- [Pracovního prostoru](workspace-expression.md)
 

## <a name="query-limits"></a>Omezení dotazů
Můžete mít obchodní požadavky na prostředek Azure pro zápis dat do více pracovních prostorů Log Analytics. Pracovní prostor nemusí být ve stejné oblasti jako zdroj a jeden pracovní prostor může shromažďovat data ze zdrojů v různých oblastech.  

Nastavení oboru na prostředek nebo sadu prostředků je obzvláště výkonnou funkcí Log Analytics, protože umožňuje automaticky konsolidovat distribuovaná data v jednom dotazu. Může výrazně ovlivnit výkon, i když pokud je potřeba načíst data z pracovních prostorů ve více oblastech Azure.

Log Analytics pomáhá chránit před nadměrnou režií z dotazů, které pokrývají pracovní prostory ve více oblastech vydáním upozornění nebo chyby při použití určitého počtu oblastí. Dotaz obdrží upozornění, pokud obor zahrnuje pracovní prostory v 5 nebo více oblastech. bude stále spuštěna, ale může trvat příliš dlouho.

![Upozornění na dotaz](media/scope/query-warning.png)

Spuštění dotazu bude zablokováno, pokud obor zahrnuje pracovní prostory ve 20 nebo více oblastech. V takovém případě budete vyzváni ke snížení počtu oblastí pracovního prostoru a pokuste se dotaz spustit znovu. Rozevírací obsah zobrazí všechny oblasti v oboru dotazu a před pokusem o opětovné spuštění dotazu byste měli snížit počet oblastí.

![Dotaz se nezdařil.](media/scope/query-failed.png)


## <a name="time-range"></a>Časové rozmezí
Časový rozsah určuje sadu záznamů, které jsou vyhodnocovány pro dotaz na základě toho, kdy byl záznam vytvořen. To je definováno standardní vlastností na každém záznamu v pracovním prostoru nebo aplikaci, jak je uvedeno v následující tabulce.

| Umístění | Vlastnost |
|:---|:---|
| Pracovní prostor služby Log Analytics          | TimeGenerated |
| Aplikace Application Insights | časové razítko     |

Nastavte časový rozsah tak, že jej vyberete z výběru času v horní části okna Analýzy protokolů.  Můžete vybrat předdefinované období nebo vybrat **Vlastní a** určit určitý časový rozsah.

![Výběr času](media/scope/time-picker.png)

Pokud v dotazu nastavíte filtr, který používá vlastnost standardního času, jak je znázorněno v tabulce výše, výběr času se změní na **Nastavit v dotazu**a výběr času je zakázán. V tomto případě je nejúčinnější umístit filtr v horní části dotazu tak, aby jakékoli následné zpracování musí pracovat pouze s filtrovanými záznamy.

![Filtrovaný dotaz](media/scope/query-filtered.png)

Pokud použijete [pracovní prostor](workspace-expression.md) nebo příkaz [aplikace](app-expression.md) k načtení dat z jiného pracovního prostoru nebo aplikace, může se výběr času chovat jinak. Pokud je obor emitovaný pracovní prostor Analýzy protokolů a používáte **aplikaci**, nebo pokud je obor aplikací Application Insights a používáte **pracovní prostor**, nemusí protokolová analýza pochopit, že vlastnost použitá ve filtru by měla určit časový filtr.

V následujícím příkladu je obor nastaven na pracovní prostor Analýzy protokolů.  Dotaz používá **pracovní prostor** k načtení dat z jiného pracovního prostoru Analýzy protokolů. Výběr času se změní na **Nastavit v dotazu,** protože vidí filtr, který používá očekávanou vlastnost **TimeGenerated.**

![Dotaz s pracovním prostorem](media/scope/query-workspace.png)

Pokud dotaz používá **aplikaci** k načtení dat z aplikace Application Insights, ale Log Analytics nerozpozná vlastnost **časového razítka** ve filtru a výběr času zůstane beze změny. V tomto případě jsou použity oba filtry. V příkladu jsou do dotazu zahrnuty pouze záznamy vytvořené za posledních 24 hodin, přestože určuje 7 dní v klauzuli **where.**

![Dotaz pomocí aplikace](media/scope/query-app.png)

## <a name="next-steps"></a>Další kroky

- Projděte si [kurz o používání Log Analytics na webu Azure Portal](get-started-portal.md).
- Projděte si [návod na psaní dotazů](get-started-queries.md).

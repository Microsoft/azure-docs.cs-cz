---
title: Upgrade analýzy protokolů Azure na nové hledání protokolu | Microsoft Docs
description: Nové dotazovací jazyk analýzy protokolů je téměř sem a ve verzi public preview se můžete zapojit.  Tento článek popisuje výhody nový jazyk a jak převést pracovního prostoru.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 3bb54f7897876d656da6f1a4b349c9db202a142d
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
ms.locfileid: "28983984"
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Azure Log Analytics upgradovat na nové hledání protokolu

Nové dotazovací jazyk analýzy protokolů je zde a pracovního prostoru je potřeba upgradovat na verzi jej využít.  Můžete buď upgradovat pracovního prostoru, sami, nebo počkejte na její automaticky upgradovat během období zavedení, který začíná v říjnu pozdní a přejde do konce roku.  Tento článek popisuje výhody nový jazyk a jak převést pracovního prostoru.  

## <a name="why-the-new-language"></a>Proč nový jazyk?
Chápeme, že je problémové v jakékoli přechod a jsme právě nejsou změna dotazovací jazyk pro zábavu ho.  Tady je několik důvodů, které tato změna bude poskytovat významné zlepšení našich zákazníků analýzy protokolů.

- **Jednoduché, ale výkonné.** Nový jazyk je čtení snadněji pochopit a podobná SQL s konstrukce více jako přirozeného jazyka než starší verze jazyka.
- **Úplné zřetězení jazyk.**  Nový jazyk má rozsáhlejší zřetězení funkce než pro starší verze jazyka.  Prakticky žádný výstup lze přesměrovat do jiného příkazu vytvořit složitější dotazy, než bylo dřív možné.
- **Pole pro vyhledávání čas extrakce.**  Nový jazyk podporuje pokročilejší runtime počítaného pole než starší verze jazyka.  Můžete využít komplexní výpočty pro rozšířené pole a pak pomocí počítaných polí pro další příkazy, včetně spojování a agregaci.
- **Pokročilé spojení.**  Nový jazyk poskytuje pokročilejší spojení než starší verze jazyka, včetně možnosti připojení tabulky více polí, použijte vnitřního a vnějšího spojení a připojení na rozšířených polích.
- **Funkce data a času.**  Nový jazyk má pokročilejší funkce data a času než starší verze jazyka.
- **Inteligentní analýzy.**  Nový jazyk má pokročilé algoritmy k vyhodnocení vzory v datových sadách a porovnání různých datových sad.
- **Pokročilé analýzy portálu.**  Portálu pro pokročilou analýzu nabízí analytické funkce není k dispozici na portálu analýzy protokolů, včetně víceřádkových úpravy dotazů, další vizualizace a pokročilé diagnostiky.
- **Konzistence s jinými aplikacemi.**  Nový jazyk a portálu pokročilé analýzy se již používá pro analýzy ve službě Application Insights.  Implementace pro analýzy protokolů poskytuje konzistence napříč službami Azure.
- **Lepší integrace s Power BI.** Dotazy v požadovaném jazyce je možné exportovat do Power BI Desktop, můžete využívat jeho funkce transformace bohaté data.
- **Mnohé další.** Odkazovat [Azure Log Analytics Query Language](https://docs.loganalytics.io) kompletní informace a kurzy o nový jazyk lokality.


## <a name="when-can-i-upgrade"></a>Pokud můžete upgradovat?
Upgrade bude vrácena přes všechny oblasti Azure, mohou být k dispozici v některé oblasti než jiné.  Víte, když je možné upgradovat, je-li v horní části pracovního prostoru pozvat můžete upgradovat zobrazí nápis informující o pracovního prostoru.

![Upgrade 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Pokud automaticky upgradován pracovního prostoru, se zobrazí banner indikující, který je upgradovaná souhrn stanovení, zda byly zjištěny problémy.

 ![Automatický upgrade](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>Co se stane po upgradu?
Při převodu se do pracovního prostoru provedeny následující změny:

- Všechny uložená hledání, pravidla výstrah a zobrazení, které jste vytvořili pomocí návrháře zobrazení je automaticky převeden na nový jazyk.  Hledání součástí řešení se nepřevádějí automaticky, ale budou se místo toho převést za chodu při jejich otevření.  
- [Můj řídicí panel](log-analytics-dashboards.md) je zastaralá pro [Návrhář zobrazení](log-analytics-view-designer.md) a [řídicí panely Azure](../azure-portal/azure-portal-dashboards.md).  Dlaždice, které jste přidali do vlastní řídicí panel jsou stále k dispozici, ale budou se jen pro čtení.
- [Power BI integrace](log-analytics-powerbi.md) se nahradí nový proces.  Všechny existující plán Power BI, které jste vytvořili bude zakázáno a budete muset nahraďte nový proces.
- Odpovědí z [výstrahy akce](log-analytics-alerts-actions.md) pomocí webhooků a sady runbook mají nový formát, a budete muset aktualizovat vaše pravidla výstrah odpovídajícím způsobem.
- Podívejte se na [– nejčastější dotazy vyhledávání protokolu](log-analytics-log-search-faq.md) pro běžné dotazy týkající se upgradu.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Jak poznám, pokud byly všechny problémy z upgradu?
Po dokončení upgradu bude **upgradu Souhrn** část v pracovním prostoru nastavení.  Zkontrolujte informace o upgradu a chcete-li zobrazit všechny problémy, které byly zjištěny této části.

 ![Souhrn upgradu](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Jak ručně provádět upgrade?
Pracovní prostor můžete upgradovat až uvidíte informační zpráva v horní části portálu.  

1.  Zahájit proces upgradu kliknutím na informační zprávě, která uvádí, že **Další informace a upgradujte**.

    ![Upgrade 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Si přečíst další informace o upgradu na stránce informace o upgradu.

    ![Upgrade 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Klikněte na tlačítko **upgradovat nyní** spusťte upgrade.

    ![Upgrade 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Pole oznámení v pravém horním rohu zobrazuje stav.
    
    ![Upgrade 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  A to je vše!  Přejděte na stránku hledání protokolů a podívejte se na pracovní prostor upgradovaný.

    ![Upgrade 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Pokud narazíte na problém, který způsobuje selhání upgradu, můžete přejít na [diskusní fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) a vystavte tady svůj dotaz nebo [vytvořit žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md) z portálu Azure.

## <a name="how-do-i-learn-the-new-language"></a>Jak zjistit nový jazyk?
Vzhledem k tomu, že se používají v několika služeb vytvořili jsme [externí web pro hostování v dokumentaci](https://docs.loganalytics.io/) pro nové jazyky.  To zahrnuje kurzy, ukázky a úplný referenční můžete pocházet urychlit. Si můžete projít kurzu nového jazyka v [Začínáme s dotazy](https://go.microsoft.com/fwlink/?linkid=856078) a přístup k referenční příručka jazyka v [analýzy protokolů dotazu jazyka](https://go.microsoft.com/fwlink/?linkid=856079).  

Pokud chcete vyzkoušet nový jazyk v ukázce prostředí včetně spoustu ukázková data, podívejte se na [playground prostředí](https://portal.loganalytics.io/demo#/discover/home).

Pokud jste již obeznámeni s starší verze dotazovacího jazyka pro analýzy protokolů, když, můžete tento převaděč jazyk, který se přidal do pracovního prostoru jako součást upgradu.  Jednoduše zadejte starší verze dotaz a pak klikněte na tlačítko **převést** zobrazíte přeložená verze.  Můžete buď klikněte na tlačítko Hledat spuštění vyhledávání nebo kopírování a vložení převedený dotaz, použít někde jinde, například pravidlo výstrahy.  Můžete taky nechat podívejte se na naše [tahák](log-analytics-log-search-transition.md) , přímo porovná běžné dotazy ze starší verze jazyka.

![Převaděč jazyk](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Další postup
- Podívejte se [kurz na nový jazyk](https://go.microsoft.com/fwlink/?linkid=856078).
- Provede [kurz na portálu hledání protokolů](log-analytics-log-search-log-search-portal.md) v požadovaném jazyce dotazu.
- Získejte Úvod do nové [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587).

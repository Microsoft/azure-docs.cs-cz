---
title: Azure Monitor přehledy skupiny prostředků | Microsoft Docs
description: Pochopte stav a výkon distribuovaných aplikací a služeb na úrovni skupiny prostředků pomocí Azure Monitor
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ab2c56b5e5cfc3dcb7411db27e7faabb2589be6c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704323"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorování skupin prostředků pomocí Azure Monitor (Preview)

Moderní aplikace jsou často složité a vysoce distribuované s mnoha diskrétními částmi, které dohromady spolupracují za účelem poskytování služby. Při rozpoznávání této složitosti Azure Monitor poskytuje přehledy monitorování pro skupiny prostředků. To usnadňuje třídění a diagnostikování všech problémů, které jednotlivé prostředky narazí, a přitom nabízí kontext jako stav a výkon skupiny prostředků &mdash; a aplikace &mdash; jako celku.

## <a name="access-insights-for-resource-groups"></a>Přístup k přehledům pro skupiny prostředků

1. Na panelu navigace na levé straně vyberte **skupiny prostředků**  .
2. Vyberte jednu ze skupin prostředků, které chcete prozkoumat. (Pokud máte velký počet skupin prostředků s filtrováním podle předplatného, může být někdy užitečné.)
3. Pokud chcete získat přístup k přehledům pro skupinu prostředků, klikněte na **přehledy** v nabídce na levé straně libovolné skupiny prostředků.

![Snímek stránky s přehledem informací o skupině prostředků](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Prostředky s aktivními výstrahami a problémy se stavem

Na stránce Přehled se zobrazuje, kolik výstrah bylo aktivováno a jsou stále aktivní, spolu s aktuálními Azure Resource Health každého prostředku. Tyto informace společně vám pomůžou rychle vystavovat všechny prostředky, u kterých dochází k problémům. Výstrahy vám pomůžou detekovat problémy v kódu a způsob konfigurace infrastruktury. Azure Resource Health problémy s platformou Azure, které nejsou specifické pro vaše jednotlivé aplikace.

![Snímek obrazovky s podoknem Azure Resource Health](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Chcete-li zobrazit Azure Resource Health, zaškrtněte políčko **zobrazit Azure Resource Health** nad tabulkou. Tento sloupec je ve výchozím nastavení skrytý a pomůže vám rychle se načíst stránky.

![Snímek obrazovky s přidaným grafem stavu prostředku](./media/resource-group-insights/0003-overview.png)

Ve výchozím nastavení se prostředky seskupují podle aplikační vrstvy a typu prostředku. **Vrstva aplikace** je jednoduchá kategorizace typů prostředků, která je k dispozici pouze v kontextu stránky přehled pro skupinu prostředků. Existují typy prostředků související s kódem aplikace, výpočetní infrastrukturou, sítí, úložištěm a databázemi. Nástroje pro správu získají vlastní vrstvy aplikací a každý další prostředek je zařazený do kategorie patřící do **jiné** vrstvy aplikace. Toto seskupení vám může pokaždé, když jsou v pořádku, i v případě, že jsou jeho podsystémy v pořádku a není v pořádku.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnostikujte problémy ve vaší skupině prostředků

Stránka přehledy skupiny prostředků nabízí několik dalších nástrojů s rozsahem, které vám pomůžou s diagnostikou problémů.

   |         |          |
   | ---------------- |:-----|
   | [**Výstrahy**](../alerts/alerts-overview.md)      |  Zobrazovat, vytvářet a spravovat výstrahy. |
   | [**Metriky**](../data-platform.md) | Vizualizujte a zkoumejte data založená na metrikách.    |
   | [**Protokoly aktivit**](../essentials/platform-logs-overview.md) | Události na úrovni předplatného, ke kterým došlo v Azure  |
   | [**Mapa aplikace**](../app/app-map.md) | Pokud chcete identifikovat problémová místa výkonu nebo výpadky selhání, přejděte k topologii distribuované aplikace. |

## <a name="failures-and-performance"></a>Selhání a výkon

Co když jste si všimli, že aplikace běží pomalu, nebo si uživatelé nahlásili chyby? Hledáním všech vašich prostředků je časově náročné, aby bylo možné izolovat problémy.

Karty **výkon** a **selhání** zjednodušují tento proces tím, že se pro mnoho běžných typů prostředků spojí zobrazení diagnostiky výkonu a selhání.

Většina typů prostředků otevře galerii šablon sešitu Azure Monitor. Každý sešit, který vytvoříte, můžete přizpůsobit, uložit, sdílet s týmem a znovu použít v budoucnu k diagnostice podobných problémů.

### <a name="investigate-failures"></a>Prozkoumat selhání

Pokud chcete otestovat kartu chyby, v nabídce na levé straně vyberte **selhání** v části **prozkoumat** .

Po provedení výběru se změní panel nabídek na levé straně a nabídne vám nové možnosti.

![Snímek obrazovky s podoknem s přehledem selhání](./media/resource-group-insights/00004-failures.png)

Když vyberete App Service, zobrazí se Galerie Azure Monitor šablon sešitu.

![Snímek obrazovky Galerie sešitu aplikace](./media/resource-group-insights/0005-failure-insights-workbook.png)

Když zvolíte šablonu pro přehled selhání, otevře se sešit.

![Snímek obrazovky se zprávou o selhání](./media/resource-group-insights/0006-failure-visual.png)

Můžete vybrat kterýkoli z řádků. Výběr se pak zobrazí v grafickém zobrazení podrobností.

![Snímek obrazovky s podrobnostmi o selhání](./media/resource-group-insights/0007-failure-details.png)

Sešity mají nepřehlednou práci při vytváření vlastních sestav a vizualizací do snadno použitelného formátu. I když někteří uživatelé můžou upravovat jenom předem sestavené parametry, sešity jsou zcela přizpůsobitelné.

Chcete-li získat představu o tom, jak tento sešit funguje interně, vyberte v horním panelu možnost **Upravit** .

![Snímek obrazovky s dalšími možnostmi úprav](./media/resource-group-insights/0008-failure-edit.png)

V blízkosti různých prvků sešitu se zobrazí řada **textových polí.** V tabulce operace vyberte pole pro **Úpravy** .

![Snímek obrazovky textových polí](./media/resource-group-insights/0009-failure-edit-graph.png)

Tím se odhalí základní dotaz protokolu, který řídí vizualizaci tabulky.

 ![Snímek obrazovky okna dotazu protokolu](./media/resource-group-insights/0010-failure-edit-query.png)

Dotaz můžete změnit přímo. Nebo ho můžete použít jako referenci a při navrhování vlastního parametrizovaného sešitu ho z něho vypůjčit.

### <a name="investigate-performance"></a>Prozkoumat výkon

Výkon nabízí svou vlastní galerii sešitů. Pro App Service sešit výkonu předem sestavené aplikace nabízí následující zobrazení:

 ![Snímek obrazovky s zobrazením výkonu](./media/resource-group-insights/0011-performance.png)

V takovém případě, pokud vyberete možnost upravit, uvidíte, že se tato sada vizualizací používá Azure Monitor metrikami.

 ![Snímek obrazovky s zobrazeními výkonu a metrikami Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="enabling-access-to-alerts"></a>Povolení přístupu k výstrahám

Pokud chcete zobrazit výstrahy v Azure Monitor pro skupiny prostředků, musí mít někdo s rolí vlastníka nebo přispěvatele v tomto předplatném pro všechny skupiny prostředků v předplatném otevřít Azure Monitor pro skupiny prostředků. Tím umožníte ostatním uživatelům s oprávněním ke čtení zobrazit výstrahy v Azure Monitor pro skupiny prostředků pro všechny skupiny prostředků v rámci předplatného. Pokud máte roli vlastníka nebo přispěvatele, aktualizujte tuto stránku za několik minut.

Azure Monitor pro skupiny prostředků spoléhá na to, že systém pro správu výstrah Azure Monitor načítá stav výstrahy. Správa výstrah není ve výchozím nastavení nakonfigurována pro každou skupinu prostředků a předplatné a může ji povolit pouze osoba s rolí vlastníka nebo přispěvatele. Dá se povolit buď pomocí:
* Otevírají se Azure Monitor pro skupiny prostředků pro libovolnou skupinu prostředků v předplatném.
* Nebo kliknutím na předplatné, kliknutím na **poskytovatelé prostředků** klikněte na **zaregistrovat pro výstrahy. Management**.

## <a name="next-steps"></a>Další kroky

- [Azure Monitor sešity](../visualize/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Upozornění služby Azure Monitor](../alerts/alerts-overview.md)

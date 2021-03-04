---
title: Nevýpadky, smlouva SLA a výpadek sešitu – Application Insights
description: Vypočítejte a ohlaste smlouvu SLA pro webový test prostřednictvím jediného podokna ve skleněných prostředcích pro Application Insights a předplatných Azure.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714047"
---
# <a name="downtime-sla-and-outages-workbook"></a>Sešit výpadků, smlouvy SLA a výpadků

Představujeme jednoduchý způsob, jak vypočítat a ohlásit smlouvu SLA (smlouva o úrovni služeb) pro webové testy prostřednictvím jediného podokna ze skla v rámci vašich Application Insightsch prostředků a předplatných Azure. Zpráva o Výpadkech a Výpadkech poskytuje výkonné předem připravené dotazy a vizualizace dat, které vylepšují vaše znalosti o připojení zákazníka, typickou dobu odezvy aplikace a dobu provozu.

Šablona sešitu SLA je přístupná prostřednictvím Galerie sešitů v prostředku Application Insights nebo prostřednictvím karty dostupnost výběrem **sestav SLA** v horní části.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Snímek obrazovky karty dostupnosti se zvýrazněnými sestavami SLA" lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Snímek obrazovky Galerie sešitů se zvýrazněným výpadkem a sešitem výpadků" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Flexibilita parametru

Parametry nastavené v sešitu ovlivňují zbývající část sestavy.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Obrazovka karty parametry výpadku/údržby v sešitu výpadků a výpadků." lightbox ="./media/sla-report/outages.png":::

`Subscriptions``App Insights Resources`parametry, a `Web Test` určují vaše možnosti prostředků na vysoké úrovni. Tyto parametry jsou založené na dotazech Log Analytics a používají se v každém dotazu sestavy.

`Failure Threshold` a `Outage Window` umožňují určit vlastní kritéria pro výpadek služby, například kritéria výstrahy dostupnosti Application Insights v závislosti na čítači nezdařeného umístění v rámci zvoleného období. Typická prahová hodnota je tři místa v intervalu pěti minut.

`Maintenance Period` umožňuje vybrat typickou četnost údržby a `Maintenance Window` pro ukázkovou dobu údržby je selektor data a času. Všechna data, ke kterým dojde během identifikovaného období, budou ve výsledcích ignorována.

`Availability Target 9s` Určuje cíl cíle devítky ze dvou devítky na pět devítky.

## <a name="overview-page"></a>Stránka přehledu

Stránka Přehled obsahuje informace vysoké úrovně o celkové úrovni služeb (SLA) (s výjimkou dob údržby, pokud jsou definovány), koncovým instancím výpadku a výpadkům aplikací. Instance výpadku jsou definovány v případě, že test začne selhat, dokud nebude úspěšný na základě parametrů výpadku. Pokud se test spustí neúspěšně v 8:00 a znovu proběhne znovu v 10:00 ráno, pak se celé období dat považuje za stejný výpadek.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" OBRÁZEK stránky s přehledem, který zobrazuje tabulku přehledů podle testů" lightbox="./media/sla-report/overview.gif":::

Můžete také prozkoumat nejdelší výpadek, ke kterému došlo během období generování sestav.

Některé testy jsou k dispozici pro další šetření zpátky na jejich Application Insights prostředek, ale je to možné pouze v [prostředku Application Insights založeném na pracovním prostoru](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Výpadky, výpadky a selhání

Karta výpadky **a výpadků** obsahuje informace o celkovém počtu výpadků a celkový čas v členění podle testů. Karta **selhání podle umístění** má geografickou mapu neúspěšných testovacích umístění, která vám pomůžou identifikovat možné oblasti připojení k problému.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" Obrázek karty výpadky a výpadky a karta umístění v sešitu výpadků a výpadků." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Úprava sestavy

Sestavu můžete upravit stejným způsobem jako kterýkoli jiný [Azure monitor sešit](../visualize/workbooks-overview.md). Dotazy nebo vizualizace můžete přizpůsobit podle potřeb vašeho týmu.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF pro výběr tlačítka upravit pro změnu vizualizace na výsečový graf." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

Dotazy mohou být všechny spouštěny v [Log Analytics](../logs/log-analytics-overview.md) a použity v jiných sestavách nebo řídicích panelech. Odeberte omezení parametru a znovu použijte základní dotaz.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" Formát dotazu protokolu GIF." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Přístup a sdílení

Sestavu můžete sdílet s vašimi týmy, pracovat nebo připnuté na řídicí panel, abyste je mohli dál používat. Uživatel musí mít oprávnění ke čtení nebo přístup k prostředku Application Insights, kde je uložen skutečný sešit.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Snímek obrazovky s sdílenou touto šablonou" lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Další kroky

- [Log Analytics tipů pro optimalizaci dotazů](../logs/query-optimization.md)
- Naučte se, jak [v sešitech vytvořit graf](../visualize/workbooks-chart-visualizations.md).
- Naučte se monitorovat web pomocí [testů dostupnosti](monitor-web-app-availability.md).
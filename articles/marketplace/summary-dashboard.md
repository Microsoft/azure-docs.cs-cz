---
title: Souhrnný řídicí panel pro analýzy partnerského centra na komerčním webu Marketplace
description: Naučte se, jak získat přístup k grafům, trendům a hodnotám agregovaných dat, která shrnují aktivity Marketplace z řídicího panelu souhrnu v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462987"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Řídicí panel Souhrn v analýzách komerčního marketplace

Tento článek poskytuje informace o řídicím panelu souhrnu v partnerském centru. Tento řídicí panel zobrazuje grafy, trendy a hodnoty agregovaných dat, která shrnují aktivity Marketplace pro vaše nabídky.

Pokud chcete získat přístup k řídicímu panelu souhrnu v partnerském centru, vyberte v části **komerční Marketplace** možnost **[analyzovat](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Souhrn**.

>[!NOTE]
> Podrobné definice terminologie analýz najdete v tématu [terminologie a běžné otázky k komerčním](./analytics-faq.md)analýzám na webu Marketplace.

## <a name="summary-dashboard"></a>Souhrnný řídicí panel

Souhrnný řídicí panel představuje přehled Azure Marketplace a Microsoft AppSource nabízí "obchodní výkon". Řídicí panel poskytuje širokou škálu následujících funkcí:

- Objednávky zákazníků
- Zákazníci
- Využití nabídek pro zákazníky
- Návštěvy stránek zákazníků v Azure Marketplace a AppSource

## <a name="elements-of-the-summary-dashboard"></a>Prvky řídicího panelu souhrnu

Následující části popisují, jak používat souhrnný řídicí panel a jak číst data.

### <a name="month-range"></a>Rozsah měsíce

Výběr rozsahu měsíce můžete najít v pravém horním rohu každé stránky. Výběrem rozsahu měsíců na základě posledních 3, 6 nebo 12 měsíců nebo vybráním vlastního rozsahu měsíců s maximální dobou trvání 12 měsíců upravte výstup grafů stránky **souhrnu** . Výchozí rozsah měsíce (perioda výpočtu) je šest měsíců.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Znázorňuje možnosti měsíčních rozsahů na řídicím panelu souhrnu.":::

> [!NOTE]
> Všechny metriky v widgetech a sestavách vizualizace jsou v době výpočtu vybrané uživatelem.

### <a name="orders-widget"></a>Pomůcka objednávek

Pomůcka objednávky na řídicím panelu Souhrn * * zobrazuje aktuální objednávky pro všechny vaše nabídky založené na jazyku Transact. Pomůcka objednávky zobrazuje počet a trend všech nakupovaných objednávek (s výjimkou zrušených objednávek) pro vybrané období výpočtu. **Objednávky** procentuálních hodnot představují množství růstu během vybraného období výpočtu.

[![Znázorňuje widget Orders na řídicím panelu souhrnu.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Můžete také přejít na sestavu objednávky tak, že v levém dolním rohu widgetu vyberete odkaz **řídicí panel objednávky** .

### <a name="customers-widget"></a>Widget pro zákazníky

Widget **Customer** na řídicím panelu **souhrnu** zobrazuje celkový počet zákazníků, kteří získali nabídky pro vybrané období výpočtu. Pomůcka Customers (zákazníci) zobrazuje počet a trend celkového počtu aktivních (včetně nových i stávajících) zákazníků (s výjimkou změn zákazníků) pro vybrané období výpočtu. Procentuální hodnota v rámci **zákazníků** představuje množství růstu během vybraného období výpočtu.

[![Znázorňuje widgety zákazníků na řídicím panelu souhrnu.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

Kliknutím na odkaz na **řídicí panel Customers (zákazníci** ) v levém dolním rohu widgetu můžete také přejít na sestavu podrobná zákazníci.

### <a name="usage-widget"></a>Pomůcka využití

Pomůcka pro **použití** na **souhrnném** řídicím panelu představuje celkové normalizované a nezpracované hodiny využití pro všechny nabídky virtuálních počítačů Azure. Pomůcka využití zobrazuje počet a trend celkových hodin využití vybraného období výpočtu.

Tabulka Souhrn využití zobrazuje hodiny používání zákazníka pro všechny nabídky, které si zakoupili.

- Normalizované hodiny používání jsou definovány jako hodiny využití normalizované na účet pro počet jader virtuálních počítačů ([počet jader virtuálních počítačů] × [hodiny nezpracovaného využití]). Virtuální počítače označené jako "SHAREDCORE" používají 1/6 (nebo 0,1666) jako násobitel [počet jader virtuálních počítačů].
- Nezpracované hodiny využití jsou definovány jako doba, po kterou byly virtuální počítače spuštěny v hodinách.

Procentuální hodnota nižší než celková doba využití představuje množství nárůstu v hodinách využití během vybraného období výpočtu.

[![Znázorňuje pomůcku využití na řídicím panelu souhrnu.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

Kliknutím na odkaz **řídicího panelu využití** v levém dolním rohu widgetu můžete také přejít na sestavu využití.

### <a name="marketplace-insights"></a>Přehledy Marketplace

Přehledy Marketplace zobrazují počet uživatelů, kteří navštívili stránky nabídek v Azure Marketplace a AppSource. **Počet navštívených stránek** zobrazuje Shrnutí komerčních webových analýz na webu Marketplace, které vydavatelům umožňují měřit zapojení zákazníků na příslušné stránky s podrobnostmi o produktu, které jsou uvedené v online obchodech na komerčním webu marketplace: Microsoft AppSource a Azure Marketplace. Tato pomůcka zobrazuje počet a trend celkového počtu návštěv stránky během vybraného období výpočtu.

[![Znázorňuje pomůcku počtu navštívených stránek na řídicím panelu souhrnu.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

Můžete také přejít na sestavu přehledy Marketplace tak, že v levém dolním rohu widgetu vyberete odkaz **řídicí panel Insights Insights** .

### <a name="geographical-spread"></a>Geografické rozprostření

Pro vybrané období výpočtu heatmapu zobrazuje celkový počet zákazníků, objednávek a normalizovaných hodin využití na geografickou dimenzi.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Znázorňuje widget rozmístění zemí na řídicím panelu souhrnu.":::

Všimněte si, že:

- Mapu můžete přesunout tak, aby se zobrazilo přesné umístění.
- Můžete přiblížit konkrétní umístění.
- Heatmapu má doplňkovou mřížku pro zobrazení podrobností o počtu zákazníků, počtu objednávek a normalizovaných hodinách používání pro konkrétní umístění.
- V mřížce můžete vyhledat a vybrat zemi nebo oblast, která se přiblíží umístění na mapě. Vraťte se k původnímu zobrazení výběrem tlačítka **Domů** na mapě.

> [!TIP]
> Pomocí ikony stáhnout v pravém horním rohu libovolného widgetu můžete data stáhnout. Svůj názor na každé widgety můžete poskytnout tak, že vyberete ikonu "palec nahoru" nebo "palec".

## <a name="next-steps"></a>Další kroky

- Přehled analytických sestav dostupných na komerčním webu Marketplace najdete v tématu [přístup k analytickým sestavám pro komerční tržiště v partnerském centru](./partner-center-portal/analytics.md).
- Informace o vašich objednávkách v grafickém formátu a ve formátu ke stažení najdete v tématu věnovaném [řídicímu panelu objednávky v rámci komerčních tržišť](orders-dashboard.md).
- U virtuálních počítačů nabízí metriky využití a měření fakturace, viz [řídicí panel využití v komerčních obchodech na webu Marketplace](usage-dashboard.md).
- Podrobné informace o vašich zákaznících, včetně trendů růstu, najdete [v tématu řídicí panel zákazníka v analytickém obchodě na komerčním webu](customer-dashboard.md).
- Seznam vašich žádostí o stažení za posledních 30 dní najdete [v tématu řídicí panel ke stažení v komerčních obchodech na webu Marketplace](./partner-center-portal/downloads-dashboard.md).
- Chcete-li zobrazit konsolidované zobrazení zpětné vazby od zákazníků pro nabídky Azure Marketplace a AppSource, přečtěte si téma [hodnocení & kontroly řídicího panelu Analytics v partnerském centru](./partner-center-portal/ratings-reviews.md).
- Nejčastější dotazy týkající se komerčních analýz na webu Marketplace a ucelený slovník datových podmínek najdete v tématu [terminologie a běžné otázky ke komerčním analýzám na webu Marketplace](./analytics-faq.md).
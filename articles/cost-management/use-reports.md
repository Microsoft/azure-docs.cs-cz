---
title: Použití sestav náklady na správu v Azure náklady na správu | Microsoft Docs
description: Tento článek popisuje použití různých náklady na správu sestav na portálu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: fa263a4b6f41e2b31328f46b8d1341d0d74c9a85
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="use-cost-management-reports"></a>Použití sestav Cost Managementu

Tento článek popisuje použití různých náklady na správu sestav na portálu Cloudyn. Většina Cloudyn sestavy jsou intuitivní a mít jednotný vzhled a chování. Přehled o Cloudyn sestavách najdete v tématu [Principy náklady sestavy](understanding-cost-reports.md). Článek také popisuje různé možnosti a pole použít ve většině sestavy.

## <a name="cost-analysis-reports"></a>Náklady sestavy analýzy

Analýza sestavy zobrazit fakturační data z vašeho poskytovatele cloudových nákladů. Pomocí sestav, můžete skupiny a potom přejít do různých datových segmentů uvedeno v souboru fakturace. Sestavy povolit podrobné náklady navigace mezi nezpracovaná data fakturace na cloudu dodavatele.

Cenově sestavy analýzy není skupiny náklady podle značky. Vytváření sestav založené na značky je k dispozici pouze v sestavách přidělení nákladů nastavit po vytvoření modelu náklady pomocí 360 přidělení náklady.

### <a name="actual-cost-analysis"></a>Analýza skutečných nákladů

Sestava analýzy skutečné náklady zobrazí vaše náklady na hlavní přispěvatele, včetně průběžných nákladů a jednorázové poplatky.

 Pomocí sestavy analýzy skutečné náklady na:

- Analýza a skutečné náklady monitorování strávil během zadaného časového rámce
- Plán prahová hodnota výstrahy
- Analýza nákladů na kompletní přehled nákladů a vrácení peněz

#### <a name="to-use-the-actual-cost-analysis-report"></a>Chcete-li pomocí sestavy analýzy skutečné náklady

Minimálně proveďte následující kroky. Můžete také použít další možnosti a pole.

1. Vyberte rozsah dat.
2. Vyberte filtr.

Klikněte pravým tlačítkem výsledky sestavy je k podrobnostem a zobrazit podrobnější informace.

![Skutečné náklady Analysis sestavy příklad](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Skutečné náklady v čase

Sestava skutečné náklady v čase je standardní náklady sestavy analýzy distribuce náklady přes řešení definované časové. Sestava zobrazí výdaje časem vám umožní sledovat trendy a zjistit útraty nesrovnalosti. Tato sestava zobrazí vaše náklady na hlavní přispěvatele, včetně průběžných nákladů a poplatků jednorázové vyhrazená instance, které jsou stráveno během vybraný časový rámec.

Pomocí sestavy skutečné náklady v čase do:

- Zobrazení náklady trendy v průběhu času.
- Zjistí nesrovnalosti v náklady.
- Najít všechny otázky související s náklady související s Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Použití sestav skutečné náklady v čase:

Minimálně proveďte následující kroky. Můžete také použít další možnosti a pole.

- Vyberte rozsah dat.

Například můžete vybrat skupiny k zobrazení jejich náklady v čase. A pak přidejte filtry, chcete-li zúžit výsledky.

![Příklad sestavy skutečné náklady v čase](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Amortizovaný náklady na sestavy

Tato sada amortizovaný náklady sestavy ukazuje lineárním jiný využití na základě služby poplatky nebo jednorázové závazky náklady a rozloženy rovnoměrně době jejich životnost jejich náklady.

Například jednorázové poplatky mohou zahrnovat:

- Roční poplatky podpory
- Roční poplatky součást zabezpečení
- Vyhrazená instance zakoupit poplatků
- Některé položky Azure Marketplace

V souboru fakturace jsou charakteristické jednorázové poplatky při spouštění služby spotřeby a koncovém datu nebo časové razítko, mají stejné hodnoty. Cloudyn pak rozpozná jako jednorázové poplatky, které může být amortizovaný. Nelze amortizovaný jiných služeb na základě spotřeby s nákladům na vyžádání.

Pro ilustraci amortizovaný náklady, přečtěte si následující příklad bitové kopie zprávu o čas skutečné náklady přes. V příkladu zobrazuje Špička náklady na srpen 23. Mohou vám anomálií ve srovnání s obvyklé trend denní náklady. Příčina analýzy a procházení dat označený náklady na tento roční AWS služby APN rezervaci, což je jednorázový poplatek zakoupených a účtují v daný den. Uvidíte, jak se tyto poplatky amortizovaný v další části.

![Skutečné náklady v čase sestavy Příklad zobrazující jednorázové náklady](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Použití sestav amortizovaný náklady v čase:

Minimálně proveďte následující kroky. Můžete také použít další možnosti a pole.

1. Vyberte rozsah dat.
2. Vyberte služby a zprostředkovatele.

Provedení dalšího předchozí příklad, uvidíte, že náklady na jednorázové je nyní amortizovaný na následujícím obrázku:

![Amortizovaný příklad sestava náklady v čase](./media/use-reports/amort-cost-over-time.png)

Předchozí obrázek znázorňuje amortized náklady na nákladů na rezervace APN v průběhu času. Tato sestava zobrazí jako nákupu roční rezervace amortizace jednorázový poplatek a náklady na APN. Náklady na APN je rozdělena rovnoměrně denně jako 1/365th rezervace počáteční nákladů.

## <a name="cost-allocation-analysis-reports"></a>Náklady sestavy analýzy přidělení

Sestavy analýzy přidělení náklady jsou k dispozici po vytvoření náklady model pomocí 360 přidělení náklady. Cloudyn zpracovává náklady nebo fakturační údaje a odpovídá data se využití a značky data vaše cloudové účty. Cloudyn tak, aby odpovídaly data, vyžaduje přístup k data o využití. Účty, kterým chybí přihlašovací údaje jsou označeny jako Nezařazeno do kategorie prostředky.

### <a name="cost-analysis-report"></a>Sestava analýzy nákladů

Sestava analýzy náklady poskytuje náhled do vaší spotřeby cloudu a výdaje během vybraný časový rámec. Zásady nastavené v správce přidělení náklady se používají v sestavě analýzy náklady.

Jak Cloudyn vypočítat tuto sestavu?

Cloudyn zajistí, že přidělení zachová integrita jednotlivé propojené účty s použitím účtu spřažení. Spřažení zajišťuje, že účet, který nepoužívá konkrétní služby nemá žádné náklady na této služby přidělených. Náklady na rozloží v, že účet zůstat v daném účtu a nejsou vypočítány pomocí zásad přidělení. Například můžete mít pět propojené účty. Pokud jenom tři z nich používat služby úložiště, náklady na služby úložiště přidělené pouze ve mezi značky v tři účty.

 Pomocí sestavy analýzy náklady na:

- Zobrazte souhrnné celého nasazení pro určitý časový rámec.
- Zobrazit náklady podle značky kategorií na základě zásad, které jsou vytvořené v modelu náklady.

#### <a name="to-use-the-cost-analysis-report"></a>Sestava analýzy náklady na použití:

1. Vyberte rozsah dat.
2. Přidání značek, podle potřeby.
3. Přidejte skupiny.
4. Vyberte náklady na model, který jste vytvořili dříve.

Následující obrázek ukazuje příklad sestava analýzy nákladů ve formátu sunburst. Kroužky zobrazit skupiny. Vnější prstenec zobrazuje služby a vnitřní kruhu zobrazuje jednotky.

![Příklad sestavy analýzy náklady](./media/use-reports/cost-analysis01.png)



Tady je příklad těchto informací v tabulkovém zobrazení.

![Příklad sestavy analýzy náklady](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Sestava nákladů v průběhu času

Sestava náklady v čase zobrazí výdaje v čase, takže můžete sledovat trendy a zjistit nesrovnalosti ve vašem nasazení. V podstatě zobrazuje náklady v průběhu za definované období. Sestava obsahuje vaše náklady na hlavní přispěvatele, včetně průběžných nákladů a poplatků jednorázové vyhrazená instance, které jsou stráveno během vybraný časový rámec. Zásady nastavené ve Správci náklady 360° lze v této sestavě.

Pomocí sestavy náklady v čase do:

- Podívejte se změny přes čas a které vlivy změnit z jeden den (nebo rozsah dat) na další.
- Analýza nákladů v čase pro konkrétní instanci.
- Pochopit, proč došlo zvýšení náklady pro konkrétní instanci.

#### <a name="to-use-the-cost-over-time-report"></a>Použití sestav náklady v čase:

1. Vyberte rozsah dat.
2. Přidání značek, podle potřeby.
3. Přidejte skupiny.
4. Vyberte náklady na model, který jste vytvořili dříve.
5. Vyberte skutečné náklady a náklady na amortizovaný.
6. Vyberte, zda chcete použít pravidla přidělení pro zobrazení nezpracovaná fakturace zobrazení dat nebo k přepočítat náklady na zobrazení.

Tady je příklad sestavy.

![Příklad nákladů v čase](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Další postup

- Pokud jste ještě nedokončili první kurz pro náklady na správu, přečtěte si ho na [zkontrolujte využití a náklady na](tutorial-review-usage.md).

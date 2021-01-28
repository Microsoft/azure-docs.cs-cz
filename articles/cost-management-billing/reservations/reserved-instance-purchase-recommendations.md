---
title: Doporučení pro rezervace Azure
description: Seznamte se s doporučeními pro rezervace Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928533"
---
# <a name="reservation-recommendations"></a>Doporučení pro rezervace

Doporučení k nákupu rezervovaných instancí (RI) Azure se poskytují prostřednictvím [rozhraní API pro doporučení rezervací](/rest/api/consumption/reservationrecommendations) služby Azure Consumption, [Azure Advisoru](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)a prostředí pro nákup rezervací na webu Azure Portal.

Následující kroky definují, jak se vypočítávají doporučení:

1. Modul doporučení vyhodnotí hodinové využití vašich prostředků v daném rozsahu za posledních 7, 30 a 60 dnů.
2. Na základě těchto dat o využití modul simuluje vaše náklady s rezervacemi a bez nich.
3. Náklady se simulují pro různá množství a doporučí se množství, které zajišťuje maximální úspory.
4. Pokud se vaše prostředky pravidelně vypínají, simulace nezjistí žádné úspory a neposkytnou se žádná doporučení k nákupu.
5. Výpočty doporučení zahrnují jakékoli speciální slevy, které můžete mít na základě sazeb za využití na vyžádání.

## <a name="recommendations-in-the-azure-portal"></a>Doporučení na webu Azure Portal

Doporučení k nákupu rezervací se zobrazují také při nákupu na webu Azure Portal. U doporučení se zobrazuje **doporučené množství**. Nákupem množství, které Azure doporučuje, dosáhnete maximálních úspor. I když si koupíte jiné množství, které si můžete koupit, a pokud si koupíte jiné množství, vaše úspory nebude optimální.

Podívejme se na několik příkladů.

V příkladu vybraného doporučení na následujícím obrázku Azure doporučuje zakoupit 6 rezervací.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Příklad znázorňující doporučení k nákupu rezervací" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Další informace o doporučení se zobrazí, když vyberete **Zobrazit podrobnosti**. Následující obrázek ukazuje podrobnosti o doporučení. Doporučené množství se počítá z hlediska nejvyššího možného využití a vychází z historických využití. Pokud máte nekonzistentní využití, vaše doporučení nemusí odpovídat 100% využití. V tomto příkladu si všimněte, že využití se v průběhu času pohybovalo. Zobrazují se náklady na rezervaci, možné úspory a procentní využití.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Příklad zobrazení podrobností pro doporučení nákupu rezervace " :::

Graf a odhadované hodnoty se změní při zvýšení doporučeného množství. Zvýšením počtu rezervací se sníží vaše úspory, protože skončíte se sníženým využitím rezervace. Jinými slovy budete platit za rezervace, které nejsou plně využité.

Pokud snížíte rezervované množství, sníží se i vaše úspory. Přestože budete mít vyšší využití, pravděpodobně nastanou období, kdy vaše rezervace plně nepokryjí využití. Využití nad rámec vašeho množství rezervací bude spotřebovávat dražší prostředky s průběžnými platbami. Tuto situaci znázorňuje příklad na následujícím obrázku. Ručně jsme snížili množství rezervací na 4. Využití rezervace se zvyšuje, ale celkové úspory se sníží, protože jsou k dispozici náklady na průběžné platby.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Příklad znázorňující podrobnosti o změněném doporučení k nákupu rezervací" :::

Pokud chcete maximalizovat úspory z rezervací, zkuste zakoupit rezervace co nejblíže doporučenému množství.

## <a name="recommendations-in-azure-advisor"></a>Doporučení od služby Azure Advisor

Doporučení k nákupu rezervací jsou k dispozici v Azure Advisoru. Mějte na paměti následující skutečnosti:

- Advisor poskytuje doporučení jenom v rámci jednotlivých předplatných. Pokud chcete zobrazit doporučení pro celý obor fakturace (fakturační účet nebo Fakturační profil), pak:
  -  V Azure Portal přejděte na **rezervace**  >  **Přidat** a potom vyberte typ, pro který chcete zobrazit doporučení.
- Doporučení k dispozici ve službě Advisor vám podíváme na svůj uplynulý 30denní trend využití.
- Množství a úspory doporučení jsou pro rezervaci na tři roky, pokud je k dispozici. Pokud se pro službu neprodala roční rezervace, doporučení se vypočítá pomocí ceny za jednoletou rezervaci.
- Výpočty doporučení zahrnují jakékoli speciální slevy, které můžete mít na základě sazeb za využití na vyžádání.
- Pokud si koupíte rezervaci sdíleného oboru, doporučení pro nákup rezervací Advisoru může trvat až pět dní, než zmizí.

## <a name="other-expected-api-behavior"></a>Další očekávané chování rozhraní API

- Když použijete shrnutí za sedm dní, je možné, že nedostanete doporučení pro případ, kdy se virtuální počítače vypnou na déle než jeden den.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o tom, [Jak se na virtuální počítače uplatňuje sleva za rezervaci Azure](../manage/understand-vm-reservation-charges.md).

---
title: Doporučení pro rezervace Azure
description: Seznamte se s doporučeními pro rezervace Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 661e3bfa149718eb2893c5722ab3931a8a9f9afe
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797930"
---
# <a name="reservation-recommendations"></a>Doporučení pro rezervace

Doporučení k nákupu rezervovaných instancí (RI) Azure se poskytují prostřednictvím [rozhraní API pro doporučení rezervací](/rest/api/consumption/reservationrecommendations) služby Azure Consumption, [Azure Advisoru](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)a prostředí pro nákup rezervací na webu Azure Portal.

Následující kroky definují, jak se vypočítávají doporučení:

1. Modul doporučení vyhodnotí hodinové využití vašich prostředků v daném rozsahu za posledních 7, 30 a 60 dnů.
2. Na základě těchto dat o využití modul simuluje vaše náklady s rezervacemi a bez nich.
3. Náklady se simulují pro různá množství a doporučí se množství, které zajišťuje maximální úspory.
4. Pokud se vaše prostředky pravidelně vypínají, simulace nezjistí žádné úspory a neposkytnou se žádná doporučení k nákupu.

## <a name="recommendations-in-the-azure-portal"></a>Doporučení na webu Azure Portal

Doporučení k nákupu rezervací se zobrazují také při nákupu na webu Azure Portal. U doporučení se zobrazuje **doporučené množství**. Nákupem množství, které Azure doporučuje, dosáhnete maximálních úspor. Přestože si můžete zakoupit libovolné množství, nákupem jiného množství nedosáhnete optimálních úspor.

Podívejme se na několik příkladů.

V příkladu vybraného doporučení na následujícím obrázku Azure doporučuje zakoupit 6 rezervací.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Příklad znázorňující doporučení k nákupu rezervací" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Další informace o doporučení se zobrazí po výběru odkazu **Zobrazit podrobnosti**. Následující obrázek ukazuje podrobnosti o doporučení. Doporučené množství se počítá pro nejvyšší možné využití na základě vašeho historického využití. Pokud máte nekonzistentní využití, vaše doporučení nemusí odpovídat 100% využití. Všimněte si, že využití v příkladu v průběhu času kolísalo. Zobrazují se náklady na rezervaci, možné úspory a procentní využití.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Příklad znázorňující podrobnosti o doporučení k nákupu rezervací" :::

Když doporučené množství rezervací zvýšíte nebo snížíte, graf a odhadované hodnoty se změní. Zvýšením množství rezervací se sníží vaše úspory, protože budete mít nižší využití rezervací. Jinými slovy budete platit za rezervace, které nejsou plně využité.

Vaše úspory se sníží také v případě, že množství rezervací snížíte. Přestože budete mít vyšší využití, pravděpodobně nastanou období, kdy vaše rezervace plně nepokryjí využití. Využití nad rámec vašeho množství rezervací bude spotřebovávat dražší prostředky s průběžnými platbami. Tuto situaci znázorňuje příklad na následujícím obrázku. Ručně jsme snížili množství rezervací na 4. Využití rezervací se zvýšilo, ale kvůli nákladům na průběžné platby jsou celkové úspory nižší.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Příklad znázorňující podrobnosti o změněném doporučení k nákupu rezervací" :::

Pokud chcete maximalizovat úspory z rezervací, zkuste zakoupit rezervace co nejblíže doporučenému množství.

## <a name="recommendations-in-azure-advisor"></a>Doporučení od služby Azure Advisor

Doporučení k nákupu rezervací jsou k dispozici v Azure Advisoru. Mějte na paměti následující skutečnosti:

- Advisor poskytuje doporučení jenom v rámci jednotlivých předplatných.
- Doporučení se počítají na základě trendu využití za posledních 30 dnů.
- Pokud je to možné, doporučené množství a úspory platí pro rezervaci na 3 roky. Pokud se pro určitou službu rezervace na 3 roky neprodává, doporučení se počítá s využitím ceny rezervace na 1 rok.
- V doporučeních se zohledňují všechny vaše případné speciální slevy na sazby využití na vyžádání.
- Pokud si koupíte rezervaci se sdíleným rozsahem, doporučení pro nákup rezervací Advisoru může trvat až 30 dnů, než zmizí.

## <a name="other-expected-api-behavior"></a>Další očekávané chování rozhraní API

- Když použijete shrnutí za sedm dní, je možné, že nedostanete doporučení pro případ, kdy se virtuální počítače vypnou na déle než jeden den.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o tom, [Jak se na virtuální počítače uplatňuje sleva za rezervaci Azure](../manage/understand-vm-reservation-charges.md).

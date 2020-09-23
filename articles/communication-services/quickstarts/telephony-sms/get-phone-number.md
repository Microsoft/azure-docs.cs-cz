---
title: Rychlý Start – získání telefonního čísla z komunikačních služeb Azure
description: Naučte se koupit telefonní číslo komunikačních služeb pomocí Azure Portal.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/09/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 2743dc0164f604c9c5e033aacc3e58fae42a1fd2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947039"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Rychlý Start: získání telefonního čísla pomocí Azure Portal

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Začněte s komunikačními službami Azure pomocí Azure Portal k nákupu telefonního čísla.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktivní prostředek služby Communication Services.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Získat telefonní číslo

Čísla zřizování zahájíte tak, že přejdete na prostředek služby Communication Services na [Azure Portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

### <a name="getting-new-phone-numbers"></a>Získávání nových telefonních čísel

V nabídce prostředek přejděte na okno telefonní čísla.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Snímek obrazovky s telefonickou stránkou zdroje komunikačních služeb.":::

Kliknutím na `Get` tlačítko spusťte průvodce. Průvodce v okně vás provede `Phone numbers` řadou otázek, které vám pomůžou vybrat telefonní číslo, které nejlépe vyhovuje vašemu scénáři. 

Nejdřív budete muset zvolit, kde se má `Country/region` telefonní číslo zřídit. Po výběru země nebo oblasti budete muset vybrat, `phone plan` co nejlépe vyhovuje vašim potřebám. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Snímek obrazovky s zobrazením získat čísla telefonů":::

### <a name="select-a-phone-plan"></a>Vybrat plán pro telefon

Výběr telefonního plánu je rozdělen do dvou kroků: 

1. Výběr [typu čísla](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Výběr [plánu](../../concepts/telephony-sms/plan-solution.md#plans)

Nabízíme nyní dva číselné typy: `Geographic` a `Toll-free` . Když vyberete typ čísla, budete mít k dispozici několik plánů, ze kterých si můžete vybrat.

> [!NOTE]
> V současné době podporujeme pouze výběr telefonních čísel s příchozím nebo odchozím voláním. Můžete ale koupit telefonní číslo s povoleným příchozím voláním a potom nakonfigurovat odchozí ID volajícího tak, aby odpovídalo číslu telefonního čísla s povoleným příchozím voláním (co se uživatelům zobrazuje při volání z vaší aplikace služby Communication Services).
> Tato možnost se vztahuje pouze na obousměrný volání. Obousměrný server SMS je nativně podporován.

V našem příkladu jsme vybrali `Toll-free` Typ čísla s `Outbound calling` plánem.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Snímek obrazovky znázorňující zobrazení vybrat plány":::

### <a name="declare-purpose"></a>Deklarovat účel

V dalším kroku vás průvodce vyzve k zadání účelu použití tohoto čísla. Tyto informace shromažďujeme, aby se projevily správné daňové předpisy a předpisy pro tísňové volání.

:::image type="content" source="../media/quickstart-search-and-acquire-bot-or-human.png" alt-text="Snímek obrazovky znázorňující výběr robota nebo člověka v rámci procesu získání telefonního čísla.":::

Tady můžete kliknutím na `Next: Numbers` tlačítko v dolní části stránky přizpůsobit telefonní čísla, která chcete zřídit.

### <a name="customizing-phone-numbers"></a>Přizpůsobení telefonních čísel

Na `Numbers` stránce budete přizpůsobovat telefonní čísla, která byste chtěli zřídit.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Snímek obrazovky zobrazující stránku výběr čísel":::

> [!NOTE]
> V tomto rychlém startu se zobrazuje `Toll-free` tok přizpůsobení typu čísla. Prostředí může být mírně odlišné, pokud jste zvolili `Geographic` Typ čísla, ale konečný výsledek bude stejný.

Vyberte `Area code` ze seznamu dostupných kódů oblastí a zadejte množství, které chcete zřídit, a potom kliknutím `Search` vyhledejte čísla, která splňují vaše vybrané požadavky. Telefonní čísla, která vyhovují vašim potřebám, se zobrazí spolu s jejich měsíčními náklady.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Snímek obrazovky zobrazující stránku pro výběr čísel se rezervovanými čísly":::

> [!NOTE]
> Dostupnost závisí na typu čísla, umístění a plánu, který jste vybrali.
> Čísla jsou vyhrazena pro krátkou dobu před vypršením platnosti transakce. Pokud vyprší platnost transakce, bude nutné čísla znovu vybrat.

Chcete-li zobrazit souhrn nákupu a umístit objednávku, klikněte na `Next: Summary` tlačítko v dolní části stránky.

### <a name="place-order"></a>Umístit objednávku

Stránka Souhrn zkontroluje typ čísla, funkce, telefonní čísla a celkové měsíční náklady, aby bylo možné telefonní čísla zřídit.

> [!NOTE]
> Uvedené ceny jsou **měsíční periodické poplatky** , které pokrývají náklady na zapůjčení vybraného telefonního čísla. Nezahrnuté v tomto zobrazení jsou **náklady** na průběžné platby, které vznikají při provádění nebo přijímání hovorů. Ceníky jsou [k dispozici zde](../../concepts/pricing.md). Tyto náklady závisí na typu čísla a cílech s názvem. Například cena za minutu pro volání z regionálního čísla Praha k regionálnímu číslu v New Yorku a volání ze stejného čísla do mobilního čísla v ČR se může lišit.

Nakonec klikněte na tlačítko `Place order` v dolní části stránky a potvrďte je.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Snímek obrazovky zobrazující stránku souhrnu s typem čísla, funkcemi, telefonními čísly a celkovými měsíčně zobrazenými náklady":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Vyhledejte telefonní čísla na Azure Portal

Přejděte ke zdroji komunikace Azure na [Azure Portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

V nabídce vyberte kartu telefonní čísla, abyste mohli spravovat telefonní čísla.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Snímek obrazovky se stránkou s telefonním číslem prostředku komunikační služby":::

> [!NOTE]
> Na této stránce může trvat několik minut, než se zřízené počty zobrazí.

## <a name="troubleshooting"></a>Řešení potíží

Běžné otázky a problémy:

- V tuto chvíli jsou k dispozici pouze telefonní čísla, podpora USA a Kanady. To je založené na fakturační adrese předplatného, ke kterému je prostředek přidružený. V tuto chvíli nemůžete přesunout prostředek do jiného předplatného.

- Po odstranění telefonního čísla se telefonní číslo uvolní nebo nebude možné ho znovu koupit až do konce fakturačního cyklu.

- Po odstranění prostředku služby komunikace se budou automaticky vydávat telefonní čísla přidružená k tomuto prostředku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

> [!div class="checklist"]
> * Koupit telefonní číslo
> * Správa telefonního čísla
> * Vydání telefonního čísla

> [!div class="nextstepaction"]
> [Odeslat SMS](../telephony-sms/send.md) 
>  [Začínáme s voláním](../voice-video-calling/getting-started-with-calling.md)

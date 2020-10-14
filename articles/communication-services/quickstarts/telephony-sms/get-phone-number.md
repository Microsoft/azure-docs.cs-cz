---
title: Rychlý Start – získání telefonního čísla z komunikačních služeb Azure
description: Naučte se koupit telefonní číslo komunikačních služeb pomocí Azure Portal.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: a9c33fad6d376c2f568f4eefbb0379486b5c40f3
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044764"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Rychlý Start: získání telefonního čísla pomocí Azure Portal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Začněte s komunikačními službami Azure pomocí Azure Portal k nákupu telefonního čísla.

## <a name="prerequisites"></a>Požadované součásti

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktivní prostředek služby Communication Services.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Získat telefonní číslo

Čísla zřizování zahájíte tak, že přejdete na prostředek služby Communication Services na [Azure Portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

### <a name="getting-new-phone-numbers"></a>Získávání nových telefonních čísel

V nabídce prostředek přejděte na okno telefonní čísla.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

Kliknutím na `Get` tlačítko spusťte průvodce. Průvodce v okně vás provede `Phone numbers` řadou otázek, které vám pomůžou vybrat telefonní číslo, které nejlépe vyhovuje vašemu scénáři. 

Nejdřív budete muset zvolit, kde se má `Country/region` telefonní číslo zřídit. Po výběru země nebo oblasti budete muset vybrat, `use case` co nejlépe vyhovuje vašim potřebám. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

### <a name="select-a-phone-plan"></a>Vybrat plán pro telefon

Výběr telefonního plánu je rozdělen do dvou kroků: 

1. Výběr [typu čísla](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Výběr [plánu](../../concepts/telephony-sms/plan-solution.md#phone-number-plans-in-azure-communication-services)

Nabízíme nyní dva číselné typy: `Geographic` a `Toll-free` . Když vyberete typ čísla, budete mít k dispozici několik plánů, ze kterých si můžete vybrat.

V našem příkladu jsme vybrali `Toll-free` Typ čísla s `Outbound calling` `Inbound and Outbound SMS` plány a.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

Tady můžete kliknutím na `Next: Numbers` tlačítko v dolní části stránky přizpůsobit telefonní čísla, která chcete zřídit.

### <a name="customizing-phone-numbers"></a>Přizpůsobení telefonních čísel

Na `Numbers` stránce budete přizpůsobovat telefonní čísla, která byste chtěli zřídit.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

> [!NOTE]
> V tomto rychlém startu se zobrazuje `Toll-free` tok přizpůsobení typu čísla. Prostředí může být mírně odlišné, pokud jste zvolili `Geographic` Typ čísla, ale konečný výsledek bude stejný.

Vyberte `Area code` ze seznamu dostupných kódů oblastí a zadejte množství, které chcete zřídit, a potom kliknutím `Search` vyhledejte čísla, která splňují vaše vybrané požadavky. Telefonní čísla, která vyhovují vašim potřebám, se zobrazí spolu s jejich měsíčními náklady.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

> [!NOTE]
> Dostupnost závisí na typu čísla, umístění a plánu, který jste vybrali.
> Čísla jsou vyhrazena pro krátkou dobu před vypršením platnosti transakce. Pokud vyprší platnost transakce, bude nutné čísla znovu vybrat.

Chcete-li zobrazit souhrn nákupu a umístit objednávku, klikněte na `Next: Summary` tlačítko v dolní části stránky.

### <a name="place-order"></a>Umístit objednávku

Stránka Souhrn zkontroluje typ čísla, funkce, telefonní čísla a celkové měsíční náklady, aby bylo možné telefonní čísla zřídit.

> [!NOTE]
> Uvedené ceny jsou **měsíční periodické poplatky** , které pokrývají náklady na zapůjčení vybraného telefonního čísla. Nezahrnuté v tomto zobrazení jsou **náklady** na průběžné platby, které vznikají při provádění nebo přijímání hovorů. Ceníky jsou [k dispozici zde](../../concepts/pricing.md). Tyto náklady závisí na typu čísla a cílech s názvem. Například cena za minutu pro volání z regionálního čísla Praha k regionálnímu číslu v New Yorku a volání ze stejného čísla do mobilního čísla v ČR se může lišit.

Nakonec klikněte na tlačítko `Place order` v dolní části stránky a potvrďte je.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Vyhledejte telefonní čísla na Azure Portal

Přejděte ke zdroji komunikace Azure na [Azure Portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

Pokud chcete spravovat telefonní čísla, vyberte v nabídce okno telefonní čísla.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

> [!NOTE]
> Na této stránce může trvat několik minut, než se zřízené počty zobrazí.

### <a name="customizing-phone-number-features"></a>Přizpůsobení funkcí telefonního čísla
Na `Numbers` stránce můžete vybrat telefonní číslo kliknutím na číslo, pro které chcete plán přizpůsobit.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

Vyberte možnosti ze seznamu dostupných volání a možností SMS a potom kliknutím `Confirm` použijte výběr.

## <a name="troubleshooting"></a>Řešení potíží

Běžné otázky a problémy:

- V tuto chvíli se k nákupu telefonních čísel podporuje jenom námi. To je založené na fakturační adrese předplatného, ke kterému je prostředek přidružený. V tuto chvíli nemůžete přesunout prostředek do jiného předplatného.

- Po uvolnění telefonního čísla se telefonní číslo uvolní nebo nebude možné ho znovu koupit až do konce fakturačního cyklu.

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

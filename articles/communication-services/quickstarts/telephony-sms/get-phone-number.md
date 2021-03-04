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
ms.openlocfilehash: 4fda626581265b4b1f6541a3b453b164ccd27963
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690443"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Rychlý Start: získání telefonního čísla pomocí Azure Portal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Začněte s komunikačními službami Azure pomocí Azure Portal k nákupu telefonního čísla.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktivní prostředek služby Communication Services.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Získat telefonní číslo

Čísla zřizování zahájíte tak, že přejdete na prostředek služby Communication Services na [Azure Portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

### <a name="getting-new-phone-numbers"></a>Získávání nových telefonních čísel

V nabídce prostředek přejděte na okno **telefonní čísla** .

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Snímek obrazovky s telefonickou stránkou zdroje komunikačních služeb.":::

Kliknutím na tlačítko **získat** spusťte průvodce. Průvodce v okně **telefonní čísla** vás provede řadou otázek, které vám pomůžou vybrat telefonní číslo, které nejlépe vyhovuje vašemu scénáři. 

Nejdřív budete muset zvolit **zemi nebo oblast** , kde byste chtěli telefonní číslo zřídit. Po výběru země nebo oblasti budete muset vybrat **případ použití** , který nejlépe vyhovuje vašim potřebám. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Snímek obrazovky s zobrazením získat čísla telefonů":::

### <a name="select-your-phone-number-features"></a>Výběr funkcí telefonního čísla

Konfigurace telefonního čísla je rozdělená do dvou kroků: 

1. Výběr [typu čísla](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Výběr [číselných funkcí](../../concepts/telephony-sms/plan-solution.md#phone-number-features-in-azure-communication-services)

Můžete si vybrat ze dvou typů telefonních čísel: **geografické** a **bezplatné**. Když vyberete typ čísla, můžete vybrat funkci.

V našem příkladu jsme vybrali typ čísla **bez poplatků** s **odchozími voláními** a **příchozími a odchozími funkcemi SMS** .

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Snímek obrazovky znázorňující zobrazení vybrat funkce":::

Tady můžete kliknutím na tlačítko **Další: čísla** v dolní části stránky přizpůsobit telefonní čísla, která chcete zřídit.

### <a name="customizing-phone-numbers"></a>Přizpůsobení telefonních čísel

Na stránce **čísla** si přizpůsobíte telefonní čísla, která chcete zřídit.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Snímek obrazovky zobrazující stránku výběr čísel":::

> [!NOTE]
> V tomto rychlém startu se zobrazuje tok vlastního nastavení typu **bezplatného pro telefonní** číslo. Prostředí může být mírně odlišné, pokud jste zvolili typ **zeměpisného** čísla, ale konečný výsledek bude stejný.

V seznamu dostupných kódů oblastí zvolte **směrové číslo** oblasti a zadejte množství, které chcete zřídit, a kliknutím na **Hledat** vyhledejte čísla, která splňují vybrané požadavky. Telefonní čísla, která vyhovují vašim potřebám, se zobrazí spolu s jejich měsíčními náklady.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Snímek obrazovky zobrazující stránku pro výběr čísel se rezervovanými čísly":::

> [!NOTE]
> Dostupnost závisí na typu čísla, umístění a funkcích, které jste vybrali.
> Čísla jsou vyhrazena pro krátkou dobu před vypršením platnosti transakce. Pokud vyprší platnost transakce, bude nutné čísla znovu vybrat.

Chcete-li zobrazit souhrn nákupu a umístit objednávku, klikněte na tlačítko **Další: Souhrn** v dolní části stránky.

### <a name="place-order"></a>Umístit objednávku

Stránka Souhrn zkontroluje typ čísla, funkce, telefonní čísla a celkové měsíční náklady, aby bylo možné telefonní čísla zřídit.

> [!NOTE]
> Uvedené ceny jsou **měsíční periodické poplatky** , které pokrývají náklady na zapůjčení vybraného telefonního čísla. Nezahrnuté v tomto zobrazení jsou **náklady** na průběžné platby, které vznikají při provádění nebo přijímání hovorů. Ceníky jsou [k dispozici zde](../../concepts/pricing.md). Tyto náklady závisí na typu čísla a cílech s názvem. Například cena za minutu pro volání z regionálního čísla Praha k regionálnímu číslu v New Yorku a volání ze stejného čísla do mobilního čísla v ČR se může lišit.

Nakonec kliknutím na **umístit objednávku** v dolní části stránky potvrďte.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Snímek obrazovky zobrazující stránku souhrnu s typem čísla, funkcemi, telefonními čísly a celkovými měsíčně zobrazenými náklady":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Vyhledejte telefonní čísla na Azure Portal

Přejděte ke zdroji komunikace Azure na [Azure Portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

Pokud chcete spravovat telefonní čísla, vyberte v nabídce okno telefonní čísla.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Snímek obrazovky se stránkou s telefonním číslem prostředku komunikační služby":::

> [!NOTE]
> Na této stránce může trvat několik minut, než se zřízené počty zobrazí.


### <a name="customizing-phone-numbers"></a>Přizpůsobení telefonních čísel

Na stránce **čísla** můžete vybrat telefonní číslo a nakonfigurovat ho.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Snímek obrazovky zobrazující stránku funkcí aktualizace":::

Vyberte funkce z dostupných možností a potom kliknutím na **Potvrdit** použijte svůj výběr.

## <a name="troubleshooting"></a>Řešení potíží

Běžné otázky a problémy:

- Nákup telefonu se podporuje jenom v USA. Pokud si chcete koupit telefonní čísla, ujistěte se, že:
  - Přidružená fakturační adresa předplatného Azure se nachází v USA. V tuto chvíli nemůžete přesunout prostředek do jiného předplatného.
  - Prostředek služby Communications se zřídí v umístění USA dat. V tuto chvíli nemůžete přesunout prostředek do jiného umístění dat.

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

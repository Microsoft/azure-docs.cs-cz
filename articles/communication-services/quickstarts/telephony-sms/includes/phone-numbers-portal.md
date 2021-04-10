---
ms.openlocfilehash: 82e475c97aa2c68a33a48b04fe3e45fb13728b88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629341"
---

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktivní prostředek služby Communication Services.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Získat telefonní číslo

Čísla zřizování zahájíte tak, že přejdete na prostředek služby Communication Services na [Azure Portal](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

### <a name="search-for-available-phone-numbers"></a>Vyhledat dostupná telefonní čísla

V nabídce prostředek přejděte na okno **telefonní čísla** .

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Snímek obrazovky s telefonickou stránkou zdroje komunikačních služeb.":::

Kliknutím na tlačítko **získat** spusťte průvodce. Průvodce v okně **telefonní čísla** vás provede řadou otázek, které vám pomůžou vybrat telefonní číslo, které nejlépe vyhovuje vašemu scénáři. 

Nejdřív budete muset zvolit **zemi nebo oblast** , kde byste chtěli telefonní číslo zřídit. Po výběru země nebo oblasti budete muset vybrat **případ použití** , který nejlépe vyhovuje vašim potřebám. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Snímek obrazovky s zobrazením získat čísla telefonů":::

### <a name="select-your-phone-number-features"></a>Výběr funkcí telefonního čísla

Konfigurace telefonního čísla je rozdělená do dvou kroků: 

1. Výběr [typu čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Výběr [možností čísel](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

Můžete si vybrat ze dvou typů telefonních čísel: **geografické** a **bezplatné**. Když vyberete typ čísla, můžete vybrat funkci.

V našem příkladu jsme vybrali typ čísla **bez poplatků** s **odchozími voláními** a **příchozími a odchozími funkcemi SMS** .

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Snímek obrazovky znázorňující zobrazení vybrat funkce":::

Tady můžete kliknutím na tlačítko **Další: čísla** v dolní části stránky přizpůsobit telefonní čísla, která chcete zřídit.

### <a name="customizing-phone-numbers"></a>Přizpůsobení telefonních čísel

Na stránce **čísla** si přizpůsobíte telefonní čísla, která chcete zřídit.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Snímek obrazovky zobrazující stránku výběr čísel":::

> [!NOTE]
> V tomto rychlém startu se zobrazuje tok vlastního nastavení typu **bezplatného pro telefonní** číslo. Prostředí může být mírně odlišné, pokud jste zvolili typ **zeměpisného** čísla, ale konečný výsledek bude stejný.

V seznamu dostupných kódů oblastí zvolte **směrové číslo** oblasti a zadejte množství, které chcete zřídit, a kliknutím na **Hledat** vyhledejte čísla, která splňují vybrané požadavky. Telefonní čísla, která vyhovují vašim potřebám, se zobrazí spolu s jejich měsíčními náklady.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Snímek obrazovky zobrazující stránku pro výběr čísel se rezervovanými čísly":::

> [!NOTE]
> Dostupnost závisí na typu čísla, umístění a funkcích, které jste vybrali.
> Čísla jsou vyhrazena pro krátkou dobu před vypršením platnosti transakce. Pokud vyprší platnost transakce, bude nutné čísla znovu vybrat.

Chcete-li zobrazit souhrn nákupu a umístit objednávku, klikněte na tlačítko **Další: Souhrn** v dolní části stránky.

### <a name="purchase-phone-numbers"></a>Koupit telefonní čísla

Stránka Souhrn zkontroluje typ čísla, funkce, telefonní čísla a celkové měsíční náklady, aby bylo možné telefonní čísla zřídit.

> [!NOTE]
> Uvedené ceny jsou **měsíční periodické poplatky** , které pokrývají náklady na zapůjčení vybraného telefonního čísla. Nezahrnuté v tomto zobrazení jsou **náklady** na průběžné platby, které vznikají při provádění nebo přijímání hovorů. Ceníky jsou [k dispozici zde](../../../concepts/pricing.md). Tyto náklady závisí na typu čísla a cílech s názvem. Například cena za minutu pro volání z regionálního čísla Praha k regionálnímu číslu v New Yorku a volání ze stejného čísla do mobilního čísla v ČR se může lišit.

Nakonec kliknutím na **umístit objednávku** v dolní části stránky potvrďte.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Snímek obrazovky zobrazující stránku souhrnu s typem čísla, funkcemi, telefonními čísly a celkovými měsíčně zobrazenými náklady":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Vyhledejte telefonní čísla na Azure Portal

Přejděte ke zdroji komunikace Azure na [Azure Portal](https://portal.azure.com):

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Snímek obrazovky znázorňující hlavní stránku prostředku komunikační služby.":::

Pokud chcete spravovat telefonní čísla, vyberte v nabídce okno telefonní čísla.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Snímek obrazovky se stránkou s telefonním číslem prostředku komunikační služby":::

> [!NOTE]
> Na této stránce může trvat několik minut, než se zřízené počty zobrazí.


### <a name="update-phone-number-capabilities"></a>Aktualizace možností telefonního čísla

Na stránce **čísla** můžete vybrat telefonní číslo a nakonfigurovat ho.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Snímek obrazovky zobrazující stránku funkcí aktualizace":::

Vyberte funkce z dostupných možností a potom kliknutím na **Potvrdit** použijte svůj výběr.

### <a name="release-phone-number"></a>Telefonní číslo verze

Na stránce **čísla** můžete uvolnit telefonní čísla.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Snímek obrazovky zobrazující stránku telefonní čísla vydání":::

Vyberte telefonní číslo, které chcete uvolnit, a potom klikněte na tlačítko **verze** .
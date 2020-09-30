---
title: Kontrola jednotlivého vyúčtování Azure
description: Naučte se porozumět svému vyúčtování a využití prostředků a ověřit poplatky za jednotlivá předplatná Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 0c93375abd57ff5e96906ef7befbbb546a3a1caf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270521"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Kurz: Kontrola jednotlivého vyúčtování Azure

Tento článek vám pomůže pochopit a zkontrolovat vyúčtování Azure. Pro každé fakturační období obvykle obdržíte fakturu e-mailem. Faktura představuje vyúčtování Azure. Stejné informace o nákladech jako na faktuře najdete i na webu Azure Portal. V tomto kurzu porovnáte svou fakturu se souborem s podrobným denním využitím a s analýzou nákladů na webu Azure Portal.

Tento kurz se týká pouze zákazníků Azure, kteří mají jednotlivé předplatné. Běžná jednotlivá předplatná jsou předplatná s tarify průběžných plateb zakoupená přímo na webu Azure.

Pokud potřebujete pomoc s vysvětlením neočekávaných poplatků, přečtěte si téma [Analýza neočekávaných poplatků](analyze-unexpected-charges.md). Pokud potřebujete zrušit předplatné Azure, přečtěte si téma [Zrušení předplatného Azure](../manage/cancel-azure-subscription.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Porovnat fakturované poplatky se souborem využití
> * Porovnat poplatky a využití v analýze nákladů

## <a name="prerequisites"></a>Požadavky

Musíte mít placený fakturační účet *programu Microsoft Online Services*. Tento účet se vytvoří při registraci Azure prostřednictvím webu Azure. Taková situace například nastane, když máte [účet s tarify průběžných plateb](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo pokud jste [odběratelem sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Faktury pro [bezplatné účty Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) se vytvoří, pouze pokud dojde k překročení částky měsíčního kreditu.

Od vytvoření vašeho předplatného Azure musí uplynout více než 30 dnů. Azure se fakturuje na konci fakturačního období.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="compare-invoiced-charges-with-usage-file"></a>Porovnat fakturované poplatky se souborem využití

<a name="charges"></a>

Prvním krokem při porovnání využití a nákladů je stažení faktury a souboru využití. Podrobný soubor CSV s využitím zobrazuje vaše poplatky podle fakturačního období a denního využití. Nezahrnuje žádné informace o dani. Abyste si mohli tyto soubory stáhnout, musíte být správcem účtu nebo mít roli Vlastník.

Na webu Azure Portal do vyhledávacího pole zadejte *předplatná* a potom klikněte na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

[![Přechod na předplatná](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

V seznamu předplatných klikněte na předplatné.

V části **Fakturace** klikněte na **Faktury**.

V seznamu faktur vyhledejte fakturu, kterou chcete stáhnout, a potom klikněte na symbol stažení. Abyste mohli zobrazit starší faktury, budete možná muset změnit časový interval. Vygenerování souboru s podrobnostmi o využití a faktury může trvat několik minut.

![Snímek obrazovky ukazující fakturační období, možnost stažení a celkové poplatky za každé fakturační období](./media/review-individual-bill/download-invoice.png)

V okně Stáhnout využití a poplatky klikněte na **Stáhnout CSV** a **Stáhnout fakturu**.

![Screenshot zobrazující stránku využití a stažení faktury](./media/review-individual-bill/usageandinvoice.png)

Pokud se zobrazí zpráva **Není k dispozici**, existuje několik důvodů, proč není možné zobrazit podrobnosti o využití nebo fakturu:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.
- Ve fakturačním období nedošlo k žádnému využití.
- Faktura se zatím nevygenerovala. Počkejte na konec fakturačního období.
- Nemáte oprávnění k zobrazení faktur. Staré faktury budete moci zobrazit, jen pokud jste správcem účtu. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](../manage/manage-billing-access.md).
- Pokud máte bezplatnou zkušební verzi nebo máte v rámci svého předplatného měsíční kredit, který jste ještě nevyčerpali, a zároveň nemáte smlouvu se zákazníkem Microsoftu, nedostanete žádnou fakturu.

Dále zkontrolujete poplatky. Na faktuře se zobrazují hodnoty daní a poplatky za využití.

![Příklad faktury Azure](./media/review-individual-bill/invoice-usage-charge.png)

Otevřete soubor CSV s informacemi o využití, který jste stáhli. Na konci souboru sečtěte hodnotu všech položek ve sloupci *Náklady*.

![Ukázkový soubor využití se souhrnnými náklady](./media/review-individual-bill/usage-file-usage-charges.png)

 Souhrnná hodnota *Náklady* musí přesně odpovídat *poplatkům za využití* na faktuře.

Poplatky za využití se zobrazují na úrovni měřiče. Následující výrazy znamenají totéž na faktuře i v souboru s podrobným využitím. Například fakturační cyklus na faktuře je totéž jako fakturační období uvedené v souboru s podrobným využitím.

| Faktura (PDF) | Podrobné využití (CSV)|
| --- | --- |
|Fakturační cyklus | BillingPeriodStartDate BillingPeriodEndDate |
|Název |Kategorie měření |
|Typ |Podkategorie měřiče |
|Prostředek |MeterName |
|Oblast |MeterRegion |
|Spotřebované | Množství |
|Zahrnuje |Zahrnuté množství |
|Fakturovatelné |Překročené množství |
|Sazba | EffectivePrice (Platná cena)|
| Hodnota | Náklady |

V sekci **Poplatky za využití** faktury se zobrazuje celková hodnota (náklady) za každý měřič spotřebovaná během fakturačního období. Následující obrázek například ukazuje poplatek za využití služby Azure Storage u prostředku *Disky P10*.

![Poplatky za využití na faktuře](./media/review-individual-bill/invoice-usage-charges.png)

V souboru CSV s využitím vyfiltrujte *MeterName* pro odpovídající prostředek zobrazený na faktuře. Potom sečtěte hodnotu *Náklady* položek ve sloupci. Tady je příklad se zaměřením na název měřiče (disky P10), který odpovídá stejné položce řádku na faktuře.

![Souhrnná hodnota pro MeterName v souboru využití](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Souhrnná hodnota *Náklady* musí přesně odpovídat *poplatkům za využití* jednotlivých prostředků uvedeným na faktuře.

Další informace najdete v tématu [Vysvětlení faktury za Azure](understand-invoice.md) a [Vysvětlení podrobného využití Azure](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Porovnání poplatků a využití v analýze nákladů

S ověřením poplatků vám může pomoci také analýza nákladů na webu Azure Portal. Pokud chcete získat rychlý přehled fakturovaného využití a poplatků, vyberte předplatné na [stránce Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal. Dále klikněte na **Analýza nákladů** a potom v seznamu zobrazení klikněte na **Podrobnosti o faktuře**.

![Příklad znázorňující výběr možnosti Podrobnosti o faktuře](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Dále v seznamu rozsahů dat vyberte časové období faktury. Přidejte filtr čísla faktury a potom vyberte číslo faktury, které odpovídá číslu na vaší faktuře. Analýza nákladů zobrazí podrobnosti nákladů fakturovaných položek.

![Příklad znázorňující podrobnosti fakturovaných nákladů v analýze nákladů](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Náklady v analýze nákladů musí přesně odpovídat *poplatkům za využití* pro jednotlivé prostředky naúčtované na faktuře.

![Poplatky za využití na faktuře](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-services-billed-separately"></a><a name="external"></a>Externí služby fakturované samostatně

Poplatky za externí služby nebo Marketplace se účtují za prostředky, které byly vytvořeny dodavateli softwaru třetích stran. Tyto prostředky jsou dostupné z Azure Marketplace. Například Barracuda Firewall je prostředek z Azure Marketplace nabízený třetí stranou. Všechny poplatky za tuto bránu firewall a její odpovídající měřiče se zobrazují jako poplatky za externí služby.

Poplatky za externí služby se fakturují zvlášť. Tyto poplatky nejsou uvedené na faktuře za Azure. Další informace najdete v článku [Vysvětlení poplatků za externí služby Azure](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Prostředky fakturované měřiči využití

Azure se nefakturuje přímo na základě nákladů na určitý prostředek. Poplatky za prostředek se počítají pomocí jednoho nebo více měřičů. Měřiče slouží ke sledování využití prostředků během doby jejich života. Tyto měřiče se pak použijí k výpočtu faktury.

Když vytvoříte určitý prostředek Azure, například virtuální počítač, vytvoří se u něj jedna nebo více instancí měřiče. Měřiče slouží ke sledování využití prostředku v průběhu času. Každý měřič generuje záznamy využití, které Azure používá k výpočtu faktury.

Například u jednoho virtuálního počítače vytvořeného v Azure se mohou vytvořit následující měřiče sledující jeho využití:

- Výpočetní hodiny
- Hodiny IP adresy
- Přenos příchozích dat
- Přenos odchozích dat
- Spravovaný disk úrovně Standard
- Operace spravovaných disků úrovně Standard
- Standardní V/V – disk
- Standardní V/V – čtení objektů blob bloku
- Standardní V/V – zápis objektů blob bloku
- Standardní V/V – odstranění objektů blob bloku

Po vytvoření virtuálního počítače začne každý měřič generovat záznamy o využití. Toto využití a cena z měřiče se sledují v měřicím systému Azure.

Na měřiče, které byly použity k výpočtu faktury, se můžete podívat v souboru CSV s údaji o využití (stejně jako v předchozím příkladu).

## <a name="pay-your-bill"></a><a name="payment"></a>Úhrada faktury

Pokud jako způsob platby nastavíte platební kartu, strhne se platba automaticky do 10 dnů od skončení fakturačního období. Ve výpisu platební karty by na řádkové položce bylo uvedeno **MSFT Azure**.

Pokud chcete změnit platební kartu, ze které se mají strhávat poplatky, přečtěte si článek [Přidání, aktualizace nebo odebrání platební karty v Azure](../manage/change-credit-card.md).

Pokud [platíte pomocí faktury](../manage/pay-by-invoice.md), zašlete svoji platbu na adresu uvedenou ve spodní části faktury.

Pokud chcete zjistit stav platby, [vytvořte lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Porovnat fakturované poplatky se souborem využití
> * Porovnat poplatky a využití v analýze nákladů

Dokončete rychlý start, abyste mohli začít používat analýzu nákladů.

> [!div class="nextstepaction"]
> [Začínáme s analýzou nákladů](../costs/quick-acm-cost-analysis.md)

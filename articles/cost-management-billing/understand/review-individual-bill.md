---
title: Kontrola konkrétního účtu Azure
description: Zjistěte, jak porozumět využití vašeho vyúčtování a prostředků a ověřit poplatky za vaše individuální předplatné Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2019
ms.author: banders
ms.openlocfilehash: 1249758e5dd5b7e823c11890e57549195da22529
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987345"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Kurz: Projděte si jednotlivé poplatky za Azure.

Tento článek vám pomůže pochopit a zkontrolovat vaše faktury za Azure. Pro každé fakturační období obdržíte obvykle fakturu v e-mailu. Faktura je reprezentace vašeho účtu Azure. Stejné informace o nákladech na faktuře jsou k dispozici v Azure Portal. V tomto kurzu porovnáte fakturu s detailním denním využitím a analýzou nákladů v Azure Portal.

Tento kurz se vztahuje jenom na zákazníky Azure s jednotlivými předplatnými. Společné individuální odběry jsou ty s tarify průběžných plateb zakoupenými přímo na webu Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Porovnat fakturované poplatky se souborem využití
> * Porovnání poplatků a využití při analýze nákladů

## <a name="prerequisites"></a>Požadavky

Musíte mít placená fakturační účet *programu Microsoft Online Services* . Účet se vytvoří při registraci do Azure prostřednictvím webu Azure. Pokud máte například [účet s tarify](https://azure.microsoft.com/offers/ms-azr-0003p/) průběžných plateb nebo jste [předplatitelem sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Faktury pro [bezplatné účty Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) se vytvářejí jenom v případě, že se překročila měsíční částka kreditu.

Musí být delší než 30 dní ode dne, kdy jste se přihlásili k Azure. Azure se fakturuje na konci fakturačního období.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="compare-invoiced-charges-with-usage-file"></a>Porovnat fakturované poplatky se souborem využití

<a name="charges"></a>

Prvním krokem pro porovnání využití a nákladů je stažení vašich faktur a souborů využití. Podrobný soubor CSV s využitím zobrazuje vaše poplatky podle fakturačního období a denního využití. Neobsahuje žádné daňové informace. Aby bylo možné soubory stáhnout, musíte být správcem účtu nebo mít roli vlastníka.

Do Azure Portal do vyhledávacího pole zadejte *odběry* a klikněte na [odběry](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Přejít k předplatným](./media/review-individual-bill/navigate-subscriptions.png)

V seznamu předplatných klikněte na předplatné.

V části **fakturace**klikněte na **faktury**.

V seznamu faktur vyhledejte ten, který chcete stáhnout, a pak klikněte na symbol stažení. Možná budete muset změnit časový interval, aby se zobrazily starší faktury. Generování souboru podrobností o využití a faktury může trvat několik minut.

![Snímek obrazovky ukazující fakturační období, možnost stažení a celkové poplatky za každé fakturační období](./media/review-individual-bill/download-invoice.png)

V okně stáhnout využití a poplatky klikněte na **Stáhnout soubor CSV** a **stáhnout fakturu**.

![Screenshot zobrazující stránku využití a stažení faktury](./media/review-individual-bill/usageandinvoice.png)

Pokud **není k dispozici** , existuje několik důvodů, proč nevidíte podrobnosti o využití nebo fakturu:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.
- Pro fakturační období neexistuje žádné využití.
- Faktura ještě nebyla vygenerována. Počkejte na konec fakturačního období.
- Nemáte oprávnění k zobrazení faktur. Pokud nejste správcem účtu, možná se vám nezobrazuje původní faktury. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](../manage/manage-billing-access.md).
- Pokud máte bezplatnou zkušební verzi nebo máte v rámci svého předplatného měsíční kredit, který jste ještě nevyčerpali, a zároveň nemáte smlouvu se zákazníkem Microsoftu, nedostanete žádnou fakturu.

V dalším kroku provedete kontrolu poplatků. Vaše faktura zobrazuje hodnoty pro daně a poplatky za využití.

![Příklad faktury Azure](./media/review-individual-bill/invoice-usage-charge.png)

Otevřete soubor využití CSV, který jste stáhli. Na konci souboru sečtěte hodnotu pro všechny položky ve sloupci *cost (náklady* ).

![Příklad souboru použití se sečtenými náklady](./media/review-individual-bill/usage-file-usage-charges.png)

 Hodnota součtu *nákladů* by měla být přesně shodná s náklady na *poplatky za využití* na faktuře.

Poplatky za využití se zobrazují na úrovni měřiče. Následující výrazy znamenají totéž na faktuře i v souboru s podrobným využitím. Například fakturační cyklus na faktuře je totéž jako fakturační období uvedené v souboru s podrobným využitím.

| Faktura (PDF) | Podrobné využití (CSV)|
| --- | --- |
|Fakturační cyklus | BillingPeriodStartDate BillingPeriodEndDate |
|Name (Název) |Kategorie měřiče |
|Typ |Podkategorie měřiče |
|Prostředek |MeterName |
|Region (Oblast) |MeterRegion |
|Spotřebované | Množství |
|Zahrnuté |Zahrnuté množství |
|Fakturovatelné |Množství překročení |
|Sazba | EffectivePrice|
| Hodnota | Náklady |

Část s **poplatky za využití** ve vaší faktuře zobrazuje celkovou hodnotu (náklady) pro každý měřič spotřebovaný během fakturačního období. Například na následujícím obrázku vidíte poplatek za využití služby Azure Storage pro prostředek *disky P10* .

![Poplatky za využití na faktuře](./media/review-individual-bill/invoice-usage-charges.png)

V souboru použití sdíleného svazku clusteru filtrujte podle *měřiče* pro odpovídající prostředek uvedený na faktuře. Pak sečtěte hodnotu *nákladů* pro položky ve sloupci. Tady je příklad:

![Soubor využití sčítají hodnotu pro měřič](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Hodnota součtu *nákladů* by měla být přesně shodná s náklady na *poplatky za využití* jednotlivých prostředků, které se účtují na faktuře.

Další informace najdete v tématu [pochopení vaší faktury Azure](understand-invoice.md) a [pochopení podrobného využití Azure](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Porovnání poplatků a využití při analýze nákladů

Analýza nákladů v Azure Portal vám taky může pomáhat ověřit poplatky. Pokud chcete získat rychlý přehled fakturovaného využití a poplatků, vyberte své předplatné ze [stránky předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) v Azure Portal. V dalším kroku klikněte na **Analýza nákladů** a potom v seznamu zobrazení klikněte na **Podrobnosti o fakturaci**.

![Příklad znázorňující výběr podrobností faktury](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

V dalším kroku vyberte v seznamu rozsah dat časové období faktury. Přidejte filtr pro číslo faktury a pak vyberte InvoiceNumber, který odpovídá vašemu fakturaci. Analýza nákladů zobrazuje podrobnosti o nákladech fakturovaných položek.

![Příklad zobrazující podrobnosti fakturovaných nákladů při analýze nákladů](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Náklady zobrazené v analýze nákladů by se měly přesně shodovat s náklady na *poplatky za využití* jednotlivých prostředků, které se účtují na faktuře.

![Poplatky za využití na faktuře](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>Externí služby fakturované samostatně

Poplatky za externí služby nebo Marketplace se účtují za prostředky, které byly vytvořeny dodavateli softwaru třetích stran. Tyto prostředky jsou dostupné z Azure Marketplace. Například Barracuda Firewall je prostředek z Azure Marketplace nabízený třetí stranou. Všechny poplatky za tuto bránu firewall a její odpovídající měřiče se zobrazují jako poplatky za externí služby.

Poplatky za externí služby se fakturují zvlášť. Tyto poplatky nejsou uvedené na faktuře za Azure. Další informace najdete v článku [Vysvětlení poplatků za externí služby Azure](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Prostředky fakturované měřiči využití

Azure se nefakturuje přímo na základě nákladů na určitý prostředek. Poplatky za prostředek se počítají pomocí jednoho nebo více měřičů. Měřiče slouží ke sledování využití prostředků během doby jejich života. Tyto měřiče se pak použijí k výpočtu faktury.

Při vytváření jednoho prostředku Azure, jako je třeba virtuální počítač, má vytvořenou jednu nebo více instancí měřiče. Měřiče slouží ke sledování využití prostředku v průběhu času. Každý měřič generuje záznamy využití, které Azure používá k výpočtu faktury.

Například u jednoho virtuálního počítače vytvořeného v Azure se mohou vytvořit následující měřiče sledující jeho využití:

- Výpočetní čas
- Hodiny IP adresy
- Přenos příchozích dat
- Odchozí přenosy dat
- Spravovaný disk úrovně Standard
- Operace spravovaných disků úrovně Standard
- Standardní V/V – disk
- Standardní V/V – čtení objektů blob bloku
- Standardní V/V – zápis objektů blob bloku
- Standardní V/V – odstranění objektů blob bloku

Po vytvoření virtuálního počítače začne každý měřič generovat záznamy o využití. Toto využití a cena z měřiče se sledují v měřicím systému Azure.

Měřiče, které se použily k výpočtu faktury, můžete zobrazit v souboru CSV o využití.

## <a name="payment"></a>Úhrada faktury

Pokud jste jako způsob platby nastavili platební kartu, platba se účtuje automaticky během 10 dnů od konce fakturačního období. Ve výpisu platební karty by na řádkové položce bylo uvedeno **MSFT Azure**.

Pokud chcete změnit platební kartu, ze které se mají strhávat poplatky, přečtěte si článek [Přidání, aktualizace nebo odebrání platební karty v Azure](../manage/change-credit-card.md).

Pokud [platíte pomocí faktury](../manage/pay-by-invoice.md), zašlete svoji platbu na adresu uvedenou ve spodní části faktury.

Pokud chcete zjistit stav platby, [vytvořte lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Porovnat fakturované poplatky se souborem využití
> * Porovnání poplatků a využití při analýze nákladů

Dokončete rychlý Start, abyste mohli začít používat analýzu nákladů.

> [!div class="nextstepaction"]
> [Začínáme s analýzou nákladů](../costs/quick-acm-cost-analysis.md)

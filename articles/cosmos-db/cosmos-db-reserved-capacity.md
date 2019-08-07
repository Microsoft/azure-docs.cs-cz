---
title: Optimalizace nákladů na prostředky Azure Cosmos DB s využitím rezervované kapacity
description: Naučte se koupit Azure Cosmos DB rezervovanou kapacitu, abyste ušetřili náklady na výpočetní výkon.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: ab42c600b975adac9f13e8e75da9696e51e7e94d
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779866"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimalizace nákladů pomocí rezervované kapacity v Azure Cosmos DB

Azure Cosmos DB Rezervovaná kapacita vám pomůže ušetřit peníze tím, že se předem platíte za prostředky Azure Cosmos DB po dobu jednoho roku nebo tří let. Díky Azure Cosmos DB rezervované kapacity můžete získat slevu na propustnost zajištěnou pro prostředky Cosmos DB. Příklady prostředků jsou databáze a kontejnery (tabulky, kolekce a grafy).

Azure Cosmos DB Rezervovaná kapacita může významně snížit náklady&mdash;Cosmos dB až 65 procent za běžné ceny s předplatným na jednoletém nebo tříletém závazku. Rezervovaná kapacita poskytuje fakturační slevu a nemá vliv na běhový stav vašich Azure Cosmos DBch prostředků.

Rezervovaná kapacita Azure Cosmos DB pokrývá propustnost zajištěná pro vaše prostředky. Nepokrývá vám poplatky za úložiště a sítě. Jakmile si koupíte rezervaci, poplatky za propustnost, které odpovídají atributům rezervace, se už nebudou účtovat podle tarifů průběžných plateb. Další informace o rezervacích najdete v článku věnovaném [rezervacím Azure](../billing/billing-save-compute-costs-reservations.md) .

Z [Azure Portal](https://portal.azure.com)můžete koupit Azure Cosmos DB rezervovanou kapacitu. Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.  
* U podnikových předplatných se musí na [portálu EA](https://ea.azure.com)povolit možnost **Přidat rezervované instance** . Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* Pro program poskytovatele Cloud Solution Provider (CSP) můžou koupit Azure Cosmos DB rezervovanou kapacitu jenom agenti správce nebo prodejní agenti.

## <a name="determine-the-required-throughput-before-purchase"></a>Určení požadované propustnosti před nákupem

Velikost rezervace by měla být založena na celkovém objemu propustnosti, které budou používat existující prostředky nebo Azure Cosmos DB prostředky, které jsou již brzy nasazeny. Požadovanou propustnost můžete určit následujícími způsoby:

* Získejte historická data z celkové zajištěné propustnosti napříč vašimi účty Azure Cosmos DB, databázemi a kolekcemi napříč všemi oblastmi. Můžete například vyhodnotit denní průměr zajištěné propustnosti stažením příkazu denního využití z `https://account.azure.com`.

* Pokud jste zákazníkem smlouva Enterprise (EA), můžete si stáhnout soubor s využitím a získat podrobnosti o Azure Cosmos DB propustnosti. Podívejte se na hodnotu **typ služby** v části **Další informace** v souboru Usage.

* Průměrnou propustnost můžete sečíst pro všechny úlohy Azure Cosmos DB účtů, u kterých očekáváte, že se budou spouštět po dobu jednoho nebo tří let. Pak můžete použít toto množství pro rezervaci.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Koupit Azure Cosmos DB rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  

2. Vyberte **všechny** > rezervaceslužeb > **Přidat**.  

3. V podokně **rezervace nákupu** vyberte možnost **Azure Cosmos DB** a kupte novou rezervaci.  

4. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

   ![Vyplnit formulář rezervované kapacity](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Pole  |Popis  |
   |---------|---------|
   |Scope   |   Možnost, která určuje, kolik předplatných může využívat fakturační výhody spojené s rezervací. Také určuje, jak se rezervace aplikují na konkrétní předplatná. <br/><br/>  Pokud vyberete Shared ( **sdílená**), použije se sleva rezervace pro Azure Cosmos DB instance spuštěné v jakémkoli předplatném v rámci vašeho fakturačního kontextu. Fakturační kontext vychází z toho, jak jste se zaregistrovali do Azure. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami se sdíleným oborem jsou všechna jednotlivá předplatná s tarify průběžných plateb vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, použije se sleva rezervace pro Azure Cosmos DB instance ve vybraném předplatném. <br/><br/> Když vyberete **jednu skupinu prostředků**, použije se sleva rezervace pro Azure Cosmos DB instance ve vybraném předplatném a v rámci daného předplatného. <br/><br/> Rozsah rezervací můžete změnit po zakoupení rezervované kapacity.  |
   |Subscription  |   Předplatné, které se používá k placení Azure Cosmos DB rezervované kapacity. Způsob platby ve vybraném předplatném se používá při zpoplatnění nákladů na front-end. Předplatné musí být jeden z následujících typů: <br/><br/>  Smlouva Enterprise (počet nabídek: MS-AZR-0017P nebo MS-AZR-0148P): U podnikového předplatného se poplatky odečtou z bilance peněžního závazku registrace nebo se účtují jako nadlimitní využití. <br/><br/> Individuální předplatné s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P): U jednotlivých předplatných s tarify průběžných plateb se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.    |
   | Skupina prostředků | Skupina prostředků, ve které se rezervovaná zvýhodněná kapacita používá |
   |Termín  |   Jeden rok nebo tři roky.   |
   |Typ propustnosti   |  Propustnost je zřízena jako jednotky žádosti. Můžete zakoupit rezervaci pro zřízenou propustnost pro obě nastavení – zápisy v jedné oblasti i pro zápis do více oblastí. Typ propustnosti má dvě hodnoty, ze kterých si můžete vybrat: 100 RU/s za hodinu a 100 multi-Master RU/s za hodinu.|
   | Rezervované jednotky kapacity| Množství propustnosti, které chcete rezervovat. Tuto hodnotu můžete vypočítat tak, že určíte propustnost potřebnou pro všechny prostředky Cosmos DB (například databáze nebo kontejnery) na oblast. Potom ji vynásobte počtem oblastí, které přiřadíte k databázi Cosmos DB. Příklad: Pokud máte v každé oblasti pět oblastí s 1 000 000 RU/s, vyberte pro nákup kapacity rezervace 5 000 000 RU/s. |


5. Po vyplnění formuláře se vypočítá cena požadovaná k nákupu rezervované kapacity. Výstup také ukazuje procentuální hodnotu slevy, kterou získáte se zvolenými možnostmi. Další klikněte na **Vybrat** .

6. V podokně **rezervace nákupu** zkontrolujte slevu a cenu rezervace. Tato rezervovaná cena se vztahuje na Azure Cosmos DB prostředky s propustností zajištěnou ve všech oblastech.  

   ![Souhrn rezervované kapacity](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Vyberte **zkontrolovat a koupit** a pak **koupit**. Po úspěšném nákupu se zobrazí následující stránka:

Po zakoupení rezervace se okamžitě použije na všechny existující Azure Cosmos DB prostředky, které odpovídají podmínkám rezervace. Pokud nemáte žádné existující prostředky Azure Cosmos DB, bude tato rezervace platit při nasazení nové instance Cosmos DB, která odpovídá podmínkám rezervace. V obou případech se období rezervace začíná ihned po úspěšném nákupu.

Po vypršení platnosti vaší rezervace se vaše Azure Cosmos DB instance budou dál spouštět a účtují se podle standardních tarifů průběžných plateb.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervace zrušení, výměna nebo refundace

Nápovědu k určení správné rezervované kapacity najdete v tématu [Vysvětlení způsobu použití slevy rezervace na Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md).

Rezervace můžete zrušit, vyměnit nebo vrátit refundaci s určitými omezeními. Další informace najdete v tématu věnovaném [výměnám samoobslužných služeb a refundacím pro Azure reservations](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Další postup

Sleva rezervace se automaticky použije na Azure Cosmos DB prostředky, které odpovídají oboru rezervace a atributům. Rozsah rezervace můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo rozhraní API.

*  Informace o tom, jak se na Azure Cosmos DB vztahují slevy na rezervované kapacity, najdete v tématu [vysvětlení slevy za rezervaci Azure](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Další informace o rezervacích Azure najdete v následujících článcích:

   * [Co jsou rezervace Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Správa rezervací Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Vysvětlení použití rezervací pro podnikovou registraci](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)
   * [Rezervace Azure v programu partner Center CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

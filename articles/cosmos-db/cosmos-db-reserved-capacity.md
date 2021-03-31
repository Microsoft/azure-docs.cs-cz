---
title: Rezervovaná kapacita v Azure Cosmos DB pro optimalizaci nákladů
description: Naučte se koupit Azure Cosmos DB rezervovanou kapacitu, abyste ušetřili náklady na výpočetní výkon.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5c2d1d286572b21879742a1a9c6ab3975441373d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602679"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimalizace nákladů s využitím rezervované kapacity ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Rezervovaná kapacita služby Azure Cosmos DB pomáhá šetřit peníze tím, že potvrzuje závazek využívání prostředků služby Azure Cosmos DB na jeden nebo tři roky. S rezervovanou kapacitou služby Azure Cosmos DB můžete získat slevu na propustnost zřízenou pro prostředky služby Cosmos DB. Příkladem prostředků jsou databáze nebo kontejnery (tabulky, kolekce a grafy).

Azure Cosmos DB Rezervovaná kapacita může významně snížit náklady Cosmos DB &mdash; až 65 procent za běžné ceny s předplatným na jednoletém nebo tříletém závazku. Rezervovaná kapacita poskytuje fakturační slevu a nemá vliv na běhový stav vašich Azure Cosmos DBch prostředků.

Rezervovaná kapacita Azure Cosmos DB pokrývá propustnost zajištěná pro vaše prostředky. Nevztahuje se na poplatky za úložiště a sítě. Jakmile si koupíte rezervaci, poplatky za propustnost, které odpovídají atributům rezervace, se už nebudou účtovat podle tarifů průběžných plateb. Další informace o rezervacích najdete v článku věnovaném [rezervacím Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) .

Z [Azure Portal](https://portal.azure.com)můžete koupit Azure Cosmos DB rezervovanou kapacitu. Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../cost-management-billing/reservations/prepare-buy-reservation.md). Pro nákup rezervované kapacity platí:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.  
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com) povolená možnost **Přidat rezervované instance**. Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* Pro program poskytovatele Cloud Solution Provider (CSP) můžou koupit Azure Cosmos DB rezervovanou kapacitu jenom agenti správce nebo prodejní agenti.

## <a name="determine-the-required-throughput-before-purchase"></a>Určení požadované propustnosti před nákupem

Velikost nákupu rezervovaných kapacit by měla být založena na celkovém objemu propustnosti, které stávající nebo již dříve nasazené Azure Cosmos DB prostředky použijí po hodinách. Příklad: Rezervovaná kapacita nákupu 30 000 RU/s, pokud je to váš konzistentní vzor hodinového používání. V tomto příkladu se veškerá zajištěná propustnost nad 30 000 RU/s bude účtovat s využitím tarifu průběžných plateb. Pokud je zajištěná propustnost nižší než 30 000 RU/s za hodinu, pak bude využívána navíc Rezervovaná kapacita za tuto hodinu.

Vypočítejte doporučení nákupu na základě hodinového způsobu použití. Doporučuje se využití za posledních 7, 30 a 60 dní a na nákup rezervovaných kapacit, který maximalizuje vaše úspory. Doporučené velikosti rezervace můžete zobrazit v Azure Portal pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).  

2. Vyberte **všechny**  >  **rezervace** služeb  >  **Přidat**.

3. V podokně **rezervace nákupu** vyberte možnost **Azure Cosmos DB**.

4. Vyberte kartu **Doporučené** pro zobrazení doporučených rezervací:

Doporučení můžete filtrovat podle následujících atributů:

- **Termín** (1 rok nebo 3 roky)
- **Četnost fakturace** (měsíčně nebo předem)
- **Typ propustnosti** (ru/s vs – zápis ru/s v několika oblastech)

Kromě toho můžete určit rozsah doporučení v rámci jedné skupiny prostředků, jediného předplatného nebo celého zápisu Azure. 

Tady je příklad doporučení:

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="Doporučení na vyhrazenou kapacitu":::

Toto doporučení k zakoupení rezervace 30 000 RU/s indikuje, že mezi 3 roky rezervace bude velikost rezervace 30 000 RU/s maximalizovat úspory. V takovém případě se doporučení vypočítá na základě posledních 30 dnů od použití Azure Cosmos DB. Pokud tento zákazník očekává, že uplynulé 30 dnů od Azure Cosmos DB využití je reprezentativní pro budoucí použití, maximalizuje se tím nákupem rezervace 30 000 RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Koupit Azure Cosmos DB rezervovanou kapacitu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).  

2. Vyberte **všechny**  >  **rezervace** služeb  >  **Přidat**.  

3. V podokně **rezervace nákupu** vyberte možnost **Azure Cosmos DB** a kupte novou rezervaci.  

4. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="Vyplnit formulář rezervované kapacity":::

   |Pole  |Description  |
   |---------|---------|
   |Obor   |   Možnost, která určuje, kolik předplatných může využívat fakturační výhody spojené s rezervací. Také určuje, jak se rezervace aplikují na konkrétní předplatná. <br/><br/>  Pokud vyberete **Shared (sdílená**), použije se sleva rezervace pro Azure Cosmos DB instance spuštěné v jakémkoli předplatném v rámci vašeho fakturačního kontextu. Fakturační kontext vychází z toho, jak jste se zaregistrovali do Azure. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami se sdíleným oborem jsou všechna jednotlivá předplatná s tarify průběžných plateb vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, použije se sleva rezervace pro Azure Cosmos DB instance ve vybraném předplatném. <br/><br/> Když vyberete **jednu skupinu prostředků**, použije se sleva rezervace pro Azure Cosmos DB instance ve vybraném předplatném a v rámci daného předplatného. <br/><br/> Rozsah rezervací můžete změnit po zakoupení rezervované kapacity.  |
   |Předplatné  |   Předplatné, které se používá k placení Azure Cosmos DB rezervované kapacity. Způsob platby ve vybraném předplatném se používá při zpoplatnění nákladů. Předplatné musí být jeden z následujících typů: <br/><br/>  Smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P): u podnikového předplatného se poplatky odečtou z předplacených plateb Azure (dříve označované jako peněžní závazek), nebo se účtují jako překročení částky. <br/><br/> Individuální předplatné s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P): u jednotlivých předplatných s tarify pro průběžné platby se poplatky účtují na základě platební karty nebo platby faktury v předplatném.    |
   | Skupina prostředků | Skupina prostředků, ve které se rezervovaná zvýhodněná kapacita používá |
   |Označení  |   Jeden rok nebo tři roky.   |
   |Typ propustnosti   |  Propustnost je zřízena jako jednotky žádosti. Můžete zakoupit rezervaci pro zřízenou propustnost pro obě nastavení – zápisy v jedné oblasti i pro zápis do více oblastí. Typ propustnosti má dvě hodnoty pro výběr: 100 RU/s za hodinu a 100 zápisy na více oblastí RU/s za hodinu.|
   | Rezervované jednotky kapacity| Množství propustnosti, které chcete rezervovat. Tuto hodnotu můžete vypočítat tak, že určíte propustnost potřebnou pro všechny prostředky Cosmos DB (například databáze nebo kontejnery) na oblast. Potom ji vynásobte počtem oblastí, které přiřadíte k databázi Cosmos. Příklad: Pokud máte v každé oblasti pět oblastí s 1 000 000 RU/s, vyberte pro nákup kapacity rezervace 5 000 000 RU/s. |


5. Po vyplnění formuláře se vypočítá cena požadovaná k nákupu rezervované kapacity. Výstup také ukazuje procentuální hodnotu slevy, kterou získáte se zvolenými možnostmi. Další klikněte na **Vybrat** .

6. V podokně **rezervace nákupu** zkontrolujte slevu a cenu rezervace. Tato rezervovaná cena se vztahuje na Azure Cosmos DB prostředky s propustností zajištěnou ve všech oblastech.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="Souhrn rezervované kapacity":::

7. Vyberte **zkontrolovat a koupit** a pak **koupit**. Po úspěšném nákupu se zobrazí následující stránka:

Po zakoupení rezervace se okamžitě použije na všechny existující Azure Cosmos DB prostředky, které odpovídají podmínkám rezervace. Pokud nemáte žádné existující prostředky Azure Cosmos DB, bude tato rezervace platit při nasazení nové instance Cosmos DB, která odpovídá podmínkám rezervace. V obou případech se období rezervace začíná ihned po úspěšném nákupu.

Po vypršení platnosti vaší rezervace se vaše Azure Cosmos DB instance budou dál spouštět a účtují se podle standardních tarifů průběžných plateb.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Další kroky

Sleva rezervace se automaticky použije na Azure Cosmos DB prostředky, které odpovídají oboru rezervace a atributům. Rozsah rezervace můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo rozhraní API.

*  Informace o tom, jak se na Azure Cosmos DB vztahují slevy na rezervované kapacity, najdete v tématu [vysvětlení slevy za rezervaci Azure](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Další informace o rezervacích Azure najdete v následujících článcích:

   * [Co jsou rezervace Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Správa rezervací Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Vysvětlení využití rezervací u smlouvy Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Rezervace Azure v programu partner Center CSP](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
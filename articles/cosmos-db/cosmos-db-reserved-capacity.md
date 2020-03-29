---
title: Rezervovaná kapacita v db Azure Cosmos pro optimalizaci nákladů
description: Zjistěte, jak koupit rezervovanou kapacitu Azure Cosmos DB, abyste ušetřili na výpočetních nákladech.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505967"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimalizace nákladů s využitím rezervované kapacity ve službě Azure Cosmos DB

Rezervovaná kapacita služby Azure Cosmos DB pomáhá šetřit peníze tím, že potvrzuje závazek využívání prostředků služby Azure Cosmos DB na jeden nebo tři roky. S rezervovanou kapacitou služby Azure Cosmos DB můžete získat slevu na propustnost zřízenou pro prostředky služby Cosmos DB. Příkladem prostředků jsou databáze nebo kontejnery (tabulky, kolekce a grafy).

Rezervovaná kapacita Azure Cosmos DB může&mdash;výrazně snížit náklady na Cosmos DB až o 65 procent při běžných cenách s jednoletým nebo tříletým závazkem předem. Rezervovaná kapacita poskytuje fakturační slevu a nemá vliv na stav běhu prostředků Azure Cosmos DB.

Vyhrazená kapacita Azure Cosmos DB pokrývá propustnost zřízenou pro vaše prostředky. Nevztahuje se na poplatky za úložiště a sítě. Jakmile si zakoupíte rezervaci, poplatky za propustnost, které odpovídají atributům rezervace, se již nebudou účtovat podle průběžných sazeb. Další informace o rezervacích najdete v článku [rezervace Azure.](../cost-management-billing/reservations/save-compute-costs-reservations.md)

Rezervovanou kapacitu Azure Cosmos DB si můžete zakoupit na [webu Azure Portal](https://portal.azure.com). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../cost-management-billing/reservations/monthly-payments-reservations.md). Pro nákup rezervované kapacity platí:

* Musíte být v roli vlastníka alespoň pro jedno podnikové nebo individuální předplatné s průběžnými platbami.  
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com) povolená možnost **Přidat rezervované instance**. Nebo pokud je toto nastavení zakázané, musíte být správcem EA v předplatném.
* Pro program zprostředkovatele cloudových řešení (CSP) mohou vyhrazená kapacita Azure Cosmos DB zakoupit pouze agenti správce nebo prodejci.

## <a name="determine-the-required-throughput-before-purchase"></a>Určení požadované propustnosti před nákupem

Velikost nákupu rezervované kapacity by měla být založena na celkovém množství propustnosti, kterou budou používat stávající prostředky Azure Cosmos DB nebo brzy nasazené na azure cosmos db na hodinovém základě. Například: Zakupte rezervovanou kapacitu 30 000 RU/s, pokud je to váš konzistentní vzor hodinového používání. V tomto příkladu se jakákoli zřízená propustnost nad 30 000 RU/s bude účtovat pomocí sazby průběžných plateb. Pokud je zřízená propustnost nižší než 30 000 RU/s za hodinu, bude zbytečná kapacita pro tuto hodinu.

Doporučení pro nákuppočítáme na základě vašeho hodinového způsobu používání. Využití za posledních 7, 30 a 60 dní je analyzováno a doporučuje se nákup rezervované kapacity, který maximalizuje vaše úspory. Doporučené velikosti rezervací můžete zobrazit na webu Azure Portal pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).  

2. Vyberte **Všechny služby** > **Reservations** > Rezervace**Přidat**.

3. V podokně **Rezervace nákupu** zvolte **Azure Cosmos DB**.

4. Chcete-li zobrazit doporučené rezervace, vyberte kartu **Doporučeno:**

Doporučení můžete filtrovat podle následujících atributů:

- **Termín** (1 rok nebo 3 roky)
- **Frekvence fakturace** (měsíčně nebo předem)
- **Typ propustnost** (ŽP vs. Vícenásobné RU)

Kromě toho můžete obor doporučení být v rámci jedné skupiny prostředků, jedno předplatné nebo celý zápis Azure. 

Zde je příklad doporučení:

![Doporučení vyhrazené kapacity](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Toto doporučení zakoupit rezervaci 30 000 RU/s naznačuje, že mezi 3letými rezervacemi bude velikost rezervace 30 000 RU/s maximalizovat úspory. V tomto případě se doporučení vypočítá na základě posledních 30 dnů využití Azure Cosmos DB. Pokud tento zákazník očekává, že posledních 30 dní využití Azure Cosmos DB je reprezentativní pro budoucí použití, by maximalizovat úspory zakoupením rezervace 30 000 RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Kupte si rezervovanou kapacitu Azure Cosmos DB

1. Přihlaste se k [portálu Azure](https://portal.azure.com).  

2. Vyberte **Všechny služby** > **Reservations** > Rezervace**Přidat**.  

3. V podokně **Rezervace nákupu** zvolte **Azure Cosmos DB** a kupte si novou rezervaci.  

4. Vyplňte požadovaná pole popsaná v následující tabulce:

   ![Vyplnění formuláře rezervované kapacity](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Pole  |Popis  |
   |---------|---------|
   |Rozsah   |   Možnost, která určuje, kolik předplatných můžete použít fakturační výhodu přidruženou k rezervaci. Také určuje, jak se rezervace použije na konkrétní odběry. <br/><br/>  Pokud vyberete **Sdílené**, sleva na rezervaci se použije na instance Azure Cosmos DB, které běží v libovolném předplatném v rámci vašeho fakturačního kontextu. Kontext fakturace je založen na tom, jak jste se zaregistrovali do Azure. Pro podnikové zákazníky je sdílený obor registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami jsou sdíleným oborem všechna jednotlivá předplatná s průběžnými platbami vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, sleva na rezervaci se použije na instance Azure Cosmos DB ve vybraném předplatném. <br/><br/> Pokud vyberete **skupinu prostředků Single**, sleva rezervace se použije na instance Azure Cosmos DB ve vybraném předplatném a vybrané skupině prostředků v rámci tohoto předplatného. <br/><br/> Rozsah rezervace můžete změnit po zakoupení rezervované kapacity.  |
   |Předplatné  |   Předplatné, které se používá k platbě za rezervovanou kapacitu Azure Cosmos DB. Způsob platby u vybraného předplatného se používá při účtování nákladů. Předplatné musí být jedním z následujících typů: <br/><br/>  Smlouva Enterprise Agreement (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P): U předplatného Enterprise se poplatky odečtou z peněžního zůstatku závazku zápisu nebo se účtují jako nadbytku. <br/><br/> Individuální předplatné s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P): U individuálního předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury u předplatného.    |
   | Skupina prostředků | Skupina prostředků, na kterou je použita sleva na rezervovanou kapacitu. |
   |Označení  |   Jeden rok nebo tři roky.   |
   |Typ propustné   |  Propustnost se zřizována jako jednotky požadavku. Můžete si koupit rezervaci pro zřízenou propustnost pro obě nastavení – zápisy v jedné oblasti i více zápisů v oblasti. Typ propustnosti má dvě hodnoty na výběr: 100 RU/s za hodinu a 100 Vícehlavních RU/s za hodinu.|
   | Jednotky rezervované kapacity| Množství propustnost, kterou chcete rezervovat. Tuto hodnotu můžete vypočítat určením propustnosti potřebné pro všechny prostředky Cosmos DB (například databáze nebo kontejnery) pro každou oblast. Potom vynásobte počet oblastí, které budete přidružit k databázi Cosmos. Například: Pokud máte pět oblastí s 1 milionem RU/s v každé oblasti, vyberte pro nákup kapacity rezervace 5 milionů RU/s. |


5. Po vyplnění formuláře se vypočítá cena potřebná k zakoupení rezervované kapacity. Výstup také zobrazuje procento slevy, kterou získáte s vybranými možnostmi. Další klepněte na **tlačítko Vybrat**

6. V podokně **Nákupní rezervace** zkontrolujte slevu a cenu rezervace. Tato cena rezervace se vztahuje na prostředky Azure Cosmos DB s propustností zřízenou ve všech oblastech.  

   ![Souhrn rezervované kapacity](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Vyberte **Zkontrolovat + koupit** a pak koupit **nyní**. Při úspěšném nákupu se zobrazí následující stránka:

Po zakoupení rezervace se okamžitě použije na všechny existující prostředky Azure Cosmos DB, které odpovídají podmínkám rezervace. Pokud nemáte žádné existující prostředky Azure Cosmos DB, rezervace se použije při nasazení nové instance Cosmos DB, která odpovídá podmínkám rezervace. V obou případech začíná doba rezervace bezprostředně po úspěšném nákupu.

Po vypršení platnosti rezervace budou vaše instance Azure Cosmos DB nadále spuštěny a budou se účtovat podle běžných sazeb průběžných plateb.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Další kroky

Sleva rezervace se automaticky použije na prostředky Azure Cosmos DB, které odpovídají oboru rezervace a atributům. Rozsah rezervace můžete aktualizovat prostřednictvím portálu Azure, PowerShellu, rozhraní API Azure nebo rozhraní API.

*  Informace o tom, jak se na Azure Cosmos DB vztahují slevy na rezervovanou kapacitu, [najdete v tématu Principy slevy na rezervaci Azure](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Další informace o rezervacích Azure najdete v následujících článcích:

   * [Co jsou rezervace Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Správa rezervací Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Vysvětlení využití rezervací u smlouvy Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Rezervace Azure v programu CSP partnerského centra](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

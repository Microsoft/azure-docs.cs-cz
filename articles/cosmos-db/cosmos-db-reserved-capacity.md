---
title: Optimalizovat náklady na prostředky Azure Cosmos DB pomocí vyhrazené kapacity
description: Informace o možnostech nákupu služby Azure Cosmos DB vyhrazené kapacity a Ušetřete na svých výpočetních nákladech.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b74fcc2e08f02be7adeeab4cfee5f36d5194392c
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508628"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimalizace nákladů s využitím rezervované kapacity ve službě Azure Cosmos DB

Azure Cosmos DB vyhrazené kapacity je využíván Šetřete peníze optimalizací pro jeden rok nebo tři roky předem platíte za prostředky Azure Cosmos DB. S Azure Cosmos DB vyhrazené kapacity můžete získat a uplatnit tak slevu na zajištěné propustnosti pro prostředky Cosmos DB. Příklady prostředků jsou databáze a kontejnerů (tabulek, kolekcí a grafy).

Azure Cosmos DB vyhrazené kapacity může výrazně snížit náklady na služby Cosmos DB&mdash;až 65 procent u běžných cen se předem k ničemu zavázat jeden rok nebo tři roky. Záložní kapacitu poskytuje fakturace slevy a neovlivní jejich běhový stav vašich prostředků Azure Cosmos DB.

Azure Cosmos DB vyhrazené kapacity se věnuje zajištěné propustnosti pro vaše prostředky. Nezahrnuje úložiště a sítě poplatky. Poté, co můžete koupit rezervaci, propustnost poplatky, které odpovídají rezervace, které atributy jsou už účtovat platit jako budete přejít sazby. Další informace o rezervacích, najdete v článku [Azure rezervace](../billing/billing-save-compute-costs-reservations.md) článku.

Můžete si koupit službu Azure Cosmos DB vyhrazené kapacity z [webu Azure portal](https://portal.azure.com). Koupit rezervované kapacity:

* Musí být v roli vlastník pro jeden nebo samostatného předplatného s průběžnými sazbami.  
* Předplatné Enterprise **přidat Reserved Instances** musí být povolená v [portál EA](https://ea.azure.com). Nebo, pokud je toto nastavení zakázané, musíte být správce EA na předplatné.
* Programu Cloud Solution Provider (CSP) můžete koupit jenom správce agentů nebo prodejní agenty služby Azure Cosmos DB vyhrazené kapacity.

## <a name="determine-the-required-throughput-before-purchase"></a>Určení požadované propustnosti před nákupem

Velikost rezervace by měla vycházet z celkové množství propustnost, které budou používat existující nebo brzy na--nasadit prostředky Azure Cosmos DB. Požadované propustnosti může určit následujícími způsoby:

* Získáte historická data pro celková zřízená propustnost mezi účty Azure Cosmos DB, databáze a kolekce ve všech oblastech. Například můžete vyhodnotit denní průměrný zřízená propustnost stažením váš denní využití výpis z `https://account.azure.com`.

* Pokud jste zákazník se smlouvou Enterprise (EA), si můžete stáhnout soubor využití pro získání podrobností o propustnosti služby Azure Cosmos DB. Odkazovat **typ služby** hodnota v **Další informace o** část souboru využití.

* Průměrná propustnost pro všemi úlohami může sečíst na vaše účty služby Azure Cosmos DB, které očekáváte, že ke spuštění pro další jeden nebo tři roky. Můžete pak toto množství pro rezervaci.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Koupit služby Azure Cosmos DB vyhrazené kapacity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  

2. Vyberte **všechny služby** > **rezervace** > **přidat**.  

3. Z **rezervace zakoupit** podokně zvolte **služby Azure Cosmos DB** koupit novou rezervaci.  

4. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

   ![Vyplňte formulář záložní kapacitu](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Pole  |Popis  |
   |---------|---------|
   |Scope   |   Možnost, která určuje, kolik předplatných fakturační benefit přidružené k rezervaci můžete využít. Také určuje, jak se má rezervace použít na konkrétní předplatné. <br/><br/>  Pokud vyberete **Shared**, sleva za rezervaci se použije pro instance služby Azure Cosmos DB, které běží v kterékoli předplatné v rámci vaší fakturační kontextu. Kontext fakturace je založená na tom, jak jste zaregistrovali službu Azure. Pro podnikové zákazníky sdílený obor je registrace a obsahuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami sdílený obor je všechny jednotlivých předplatných s využitím průběžné platby, vytváří správce účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, sleva za rezervaci se použije pro instance služby Azure Cosmos DB ve vybraném předplatném. <br/><br/> Pokud vyberete **jedna skupina prostředků**, sleva za rezervaci se použije pro instance služby Azure Cosmos DB ve vybraném předplatném a vybrané skupiny prostředků v rámci tohoto předplatného. <br/><br/> Po nákupu rezervované kapacity můžete změnit obor rezervace.  |
   |Předplatné  |   Předplatné, které se používá k úhradě služby Azure Cosmos DB rezervované kapacity. Způsob platby u vybraného předplatného se používá v účtování pořizovací náklady. Tento typ předplatného musí být jeden z následujících akcí: <br/><br/>  Smlouvy Enterprise (nabízejí čísla: MS-AZR-0017P or MS-AZR-0148P): Předplatné Enterprise jsou poplatky odečteno od zůstatku peněžním závazkem registrace nebo účtovat jako překročení částky. <br/><br/> Samostatného předplatného s průběžnými sazbami (nabízejí čísla: MS-AZR-0003P or MS-AZR-0023P): Pro samostatného předplatného s průběžnými sazbami účtují se poplatky kreditní kartou nebo fakturou způsobu platby v rámci předplatného.    |
   | Skupina prostředků | Skupina prostředků, ke kterému se sleva záložní kapacitu. |
   |Termín  |   Jeden nebo tři roky.   |
   |Typ propustnost   |  Propustnost je zřízená jako jednotkách požadavků. Můžete si koupit rezervaci zřízená propustnost pro obě nastavení - jedné oblasti zapíše i více oblastí zápisu. Typ propustnost má dvě hodnoty na výběr: 100 RU/s za hodinu a 100 RU/s několika hlavními uzly za hodinu.|
   | Jednotky rezervované kapacity| Množství propustnost, které chcete rezervovat. Tuto hodnotu můžete vypočítat tak, že určíte propustnost potřebné pro všechny vaše služby Cosmos DB prostředky (například databáze nebo kontejnery) v jedné oblasti. Můžete pak vynásobit se podle počtu oblastí, které budete přidružíte k databázi Cosmos DB. Příklad: Pokud máte pět oblastí s 1 milion RU/s v každé oblasti, vyberte 5 milionů RU/s pro nákup rezervace kapacitu. |
 

5. Po vyplnění formuláře se počítá cena muset zakoupit vyhrazené kapacitu. Výstup zobrazuje také procento slevy, získáte s zvolené možnosti. Klepnutím na tlačítko **vyberte**

6. V **rezervace zakoupit** podokně zkontrolujte sleva a cenu za rezervaci. Tato rezervace ceny platí pro prostředky Azure Cosmos DB s propustností zřízené ve všech oblastech.  

   ![Souhrn záložní kapacitu](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Vyberte **revize + nákup** a potom **koupit**. Po úspěšné nákupu, získáte na následující stránce:

Po nákupu rezervace se okamžitě použije na nějaké stávající prostředky Azure Cosmos DB, které odpovídají podmínkám použití rezervace. Pokud nemáte k dispozici nějaké stávající prostředky Azure Cosmos DB, rezervace platit, když nasadíte novou instanci služby Cosmos DB, která odpovídá podmínky rezervace. V obou případech se období rezervace začne hned po úspěšném nákupu.

Když vyprší platnost vaší rezervace, nadále spouštět vaše instance služby Azure Cosmos DB a jsou účtovány za běžné sazby průběžných plateb.

## <a name="cancellation-and-exchanges"></a>Zrušení a výměny

Pomoc při identifikaci přímo rezervované kapacity najdete v tématu [pochopit, jak sleva za rezervaci se použije ke službě Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md). Pokud budete muset zrušit nebo exchange rezervací služby Azure Cosmos DB, najdete v článku [výměny rezervaci a vrácení peněz](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Další postup

Sleva za rezervaci se automaticky využije na prostředky Azure Cosmos DB, které odpovídají obor rezervace a atributy. Rozsah rezervace prostřednictvím webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní API můžete aktualizovat.

*  Zjistěte, jak záložní kapacitu slevy na služby Azure Cosmos DB, najdete v článku [pochopit slevy na rezervaci Azure](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Další informace o Azure rezervace, naleznete v následujících článcích:

   * [Co jsou Azure rezervace?](../billing/billing-save-compute-costs-reservations.md)  
   * [Správa rezervací Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)
   * [Rezervace Azure v programu Partnerské centrum CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

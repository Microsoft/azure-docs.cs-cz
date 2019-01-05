---
title: Optimalizovat náklady na prostředky Azure Cosmos DB pomocí vyhrazené kapacity
description: Informace o možnostech nákupu služby Azure Cosmos DB vyhrazené kapacity a Ušetřete na svých výpočetních nákladech.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b4f36abb2136223be8d4eec67935317041a41b93
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044276"
---
# <a name="optimize-cost-of-azure-cosmos-db-resources-with-reserved-capacity"></a>Optimalizovat náklady na prostředky Azure Cosmos DB pomocí vyhrazené kapacity

Azure Cosmos DB vyhrazené kapacity je využíván Šetřete peníze optimalizací pro jeden rok nebo tři roky předem platíte za prostředky Azure Cosmos DB. S Azure Cosmos DB vyhrazené kapacity můžete získat a uplatnit tak slevu na zajištěné propustnosti pro prostředky Cosmos DB. Příklady prostředků jsou databáze a kontejnerů (tabulek, kolekcí a grafy).

Azure Cosmos DB vyhrazené kapacity může výrazně snížit náklady na služby Cosmos DB&mdash;až 65 procent u běžných cen se předem k ničemu zavázat jeden rok nebo tři roky. Záložní kapacitu poskytuje fakturace slevy a neovlivní jejich běhový stav vašich prostředků Azure Cosmos DB.

Azure Cosmos DB vyhrazené kapacity se věnuje zajištěné propustnosti pro vaše prostředky. Nezahrnuje úložiště a sítě poplatky. Poté, co můžete koupit rezervaci, propustnost poplatky, které odpovídají rezervace, které atributy jsou už účtovat platit jako budete přejít sazby. Další informace o rezervacích, najdete v článku [Azure rezervace](../billing/billing-save-compute-costs-reservations.md) článku. 

Můžete si koupit službu Azure Cosmos DB vyhrazené kapacity z [webu Azure portal](https://portal.azure.com). Koupit rezervované kapacity:

* Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.  
* Předplatné Enterprise, musí být povoleno nákupy na Azure rezervace v [portál EA](https://ea.azure.com/).  
* Programu Cloud Solution Provider (CSP) můžete koupit jenom správce agentů nebo prodejní agenty služby Azure Cosmos DB vyhrazené kapacity.

## <a name="determine-the-required-throughput-before-purchase"></a>Určení požadované propustnosti před nákupem

Velikost rezervace by měla vycházet z celkové množství propustnost, které budou používat existující nebo brzy na--nasadit prostředky Azure Cosmos DB. Požadované propustnosti může určit následujícími způsoby:

* Získáte historická data pro celková zřízená propustnost mezi účty Azure Cosmos DB, databáze a kolekce ve všech oblastech. Například můžete vyhodnotit denní průměrný zřízená propustnost stažením váš denní využití výpis z `https://account.azure.com`.

* Pokud jste zákazník se smlouvou Enterprise (EA), si můžete stáhnout soubor využití pro získání podrobností o propustnosti služby Azure Cosmos DB. Odkazovat **typ služby** hodnota v **Další informace o** část souboru využití.

* Průměrná propustnost pro všemi úlohami může sečíst na vaše účty služby Azure Cosmos DB, které očekáváte, že ke spuštění pro další jeden nebo tři roky. Můžete pak toto množství pro rezervaci.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Koupit služby Azure Cosmos DB vyhrazené kapacity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  

2. Vyberte **všechny služby** > **rezervace** > **přidat**.  

3. Z **vybrat typ produktu** podokně zvolte **služby Azure Cosmos DB** > **vyberte** koupit novou rezervaci.  

4. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

   ![Vyplňte formulář záložní kapacitu](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Pole  |Popis  |
   |---------|---------|
   |Název   |    Název rezervace. Toto pole se automaticky vyplní `CosmosDB_Reservation_<timeStamp>`. Zadejte jiný název při vytváření rezervace. Nebo můžete ji jakkoli přejmenovat po vytvoření rezervace.      |
   |Předplatné  |   Předplatné, které se používá k úhradě služby Azure Cosmos DB rezervované kapacity. Způsob platby u vybraného předplatného se používá v účtování pořizovací náklady. Tento typ předplatného musí být jeden z následujících akcí: <br/><br/>  [Smlouva Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (číslo nabídky: MS-AZR-0017P): Předplatné Enterprise jsou poplatky odečteno od zůstatku peněžním závazkem registrace nebo účtovat jako překročení částky. <br/><br/> [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) (číslo nabídky: MS-AZR-0003P): Pro předplatné s průběžnými platbami se poplatky účtují kreditní kartou nebo fakturou způsobu platby v rámci předplatného.    |
   |Rozsah   |   Možnost, která určuje, kolik předplatných fakturační benefit přidružené k rezervaci můžete využít. Také určuje, jak se má rezervace použít na konkrétní předplatné.   <br/><br/>  Pokud vyberete **jedno předplatné**, sleva za rezervaci se použije pro instance služby Azure Cosmos DB ve vybraném předplatném. <br/><br/>  Pokud vyberete **Shared**, sleva za rezervaci se použije pro instance služby Azure Cosmos DB, které běží v kterékoli předplatné v rámci vaší fakturační kontextu. Kontext fakturace je založená na tom, jak jste zaregistrovali službu Azure. Pro podnikové zákazníky sdílený obor je registraci a zahrnuje všechna předplatná (s výjimkou předplatná pro vývoj/testování) během registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.  <br/><br/> Po nákupu rezervované kapacity můžete změnit obor rezervace.  |
   |Typ rezervované kapacity   |  Jako jednotky žádostí zřízenou propustnost.|
   |Rezervované jednotky kapacity  |      Množství propustnost, které chcete rezervovat. Tuto hodnotu můžete vypočítat tak, že určíte propustnost potřebné pro všechny vaše služby Cosmos DB prostředky (například databáze nebo kontejnery) v jedné oblasti. Můžete pak vynásobit se podle počtu oblastí, které budete přidružíte k databázi Cosmos DB.  <br/><br/> Příklad: Pokud máte pět oblastí s 1 milion RU/s v každé oblasti, vyberte 5 milionů RU/s pro nákup rezervace kapacitu.    |
   |Označení  |   Jeden nebo tři roky.   |

5. Sleva a cenu za rezervaci v **náklady** oddílu. Tato rezervace ceny platí pro prostředky Azure Cosmos DB s propustností zřízené ve všech oblastech.  

6. Vyberte **Koupit**. Po úspěšné nákupu, získáte na následující stránce: 

   ![Vyplňte formulář záložní kapacitu](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Po nákupu rezervace se okamžitě použije na nějaké stávající prostředky Azure Cosmos DB, které odpovídají podmínkám použití rezervace. Pokud nemáte k dispozici nějaké stávající prostředky Azure Cosmos DB, rezervace platit, když nasadíte novou instanci služby Cosmos DB, která odpovídá podmínky rezervace. V obou případech se období rezervace začne hned po úspěšném nákupu. 

Když vyprší platnost vaší rezervace, nadále spouštět vaše instance služby Azure Cosmos DB a jsou účtovány za běžné sazby průběžných plateb.

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


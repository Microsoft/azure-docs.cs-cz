---
title: Předplatíte prostředky Azure Cosmos DB ušetřit | Dokumentace Microsoftu
description: Informace o možnostech nákupu služby Azure Cosmos DB vyhrazené kapacity a Ušetřete na svých výpočetních nákladech.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 2291b2429e6c5c25e051c8f3eca30e1cc3f64611
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247325"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Předplatíte prostředky Azure Cosmos DB pomocí vyhrazené kapacity

Azure Cosmos DB vyhrazené kapacity je využíván Šetřete peníze optimalizací dobu jeden rok nebo tři roky předem platíte za prostředky Azure Cosmos DB. Azure Cosmos DB vyhrazené kapacity můžete získat slevu na zajištěné propustnosti pro Cosmos DB prostředky, například databáze, kontejnery (kolekce/tabulky/grafu). Azure Cosmos DB vyhrazené kapacity může výrazně snížit náklady na služby Cosmos DB – až 65 procent u běžných cen – s jeden rok nebo tři roky předem k ničemu zavázat. Záložní kapacitu poskytuje fakturace slevy a neovlivní jejich běhový stav vašich prostředků služby Cosmos DB.

Popisuje zajištěné propustnosti pro vaše prostředky Azure Cosmos DB vyhrazené kapacity, nezahrnuje poplatky za úložiště a sítě. Poté, co můžete koupit rezervaci, propustnost poplatky, které odpovídají rezervace, které atributy jsou už účtovat platit jako budete přejít sazby. Další informace o rezervacích, naleznete v tématu [Azure rezervace](../billing/billing-save-compute-costs-reservations.md) článku. 

Můžete si koupit službu Azure Cosmos DB vyhrazené kapacity z [webu Azure portal](https://portal.azure.com). Koupit rezervované kapacity:

* Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.  
* Předplatné Enterprise, musí být povoleno nákupy na Azure rezervace v [portál EA.](https://ea.azure.com/)  
* Programu Cloud Solution Provider (CSP) můžou nakupovat jenom správce agentů nebo prodejní agentů služby Azure Cosmos DB vyhrazené kapacity.

## <a name="determine-the-required-throughput-before-purchase"></a>Určení požadované propustnosti před nákupem

Velikost rezervace by měla vycházet z celkové množství propustnost, které používají existující nebo brzy na--nasadit prostředky služby Azure Cosmos DB (například databáze nebo kontejnery – kolekce, tabulky, grafy). Požadované propustnosti může určit následujícími způsoby:

* Získáte historická data pro celková zřízená propustnost mezi účty Azure Cosmos DB, databáze a kolekce ve všech oblastech. Například můžete vyhodnotit denní průměrný zřízená propustnost stažením váš denní využití výpis z `https://account.azure.com`

* Případně, pokud se smlouvou Enterprise (EA), můžete stáhnout soubor využití a odkazovat na **typ služby** hodnotu **Další informace o** části souboru o využití se získat rozhraní Azure Cosmos DB Podrobnosti o propustnosti.

* Můžete také sečíst průměrná propustnost pro všemi úlohami na vaše účty služby Azure Cosmos DB předvídat spuštění pro další jeden nebo tři roky a použít toto množství pro rezervaci.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Koupit služby Azure Cosmos DB vyhrazené kapacity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  

2. Vyberte **všechny služby** > **rezervace** > **přidat**.  

3. Z **vybrat typ produktu** podokně zvolte **služby Azure Cosmos DB**a potom **vyberte** koupit novou rezervaci.  

4. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

   ![Vyplňte formulář záložní kapacitu](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Pole  |Popis  |
   |---------|---------|
   |Název   |    Název rezervace. Toto pole se vyplní automaticky `CosmosDB_Reservation_<timeStamp>`. Můžete zadat jiný název při vytváření rezervace nebo přejmenujte ho po vytvoření rezervace.      |
   |Předplatné  |   Předplatné použité k úhradě služby Azure Cosmos DB vyhrazené kapacity. Způsob platby u vybraného předplatného se používá v účtování pořizovací náklady. Tento typ předplatného musí být jeden z následujících akcí: <br/><br/>  [Smlouva Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (číslo nabídky: MS-AZR - 0017 P) – pro předplatné enterprise, se poplatky za odečteno od zůstatku peněžním závazkem registrace nebo účtovat jako překročení částky. <br/><br/> [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) (číslo nabídky: MS-AZR - 0003 P) – pro předplatné s průběžnými platbami, poplatky se fakturují na kreditní kartou nebo fakturou způsob platby v rámci předplatného.    |
   |Rozsah   |   Rezervace pro obor Určuje, kolik předplatných lze využít fakturační výhodu přidružené k rezervaci a určuje, jak se má rezervace použít na konkrétní předplatné. Rezervace má obor předplatného jednu nebo Shared. Pokud vyberete:   <br/><br/>  **Jedno předplatné** – sleva za rezervaci se použije pro instance služby Azure Cosmos DB ve vybraném předplatném. <br/><br/>  **Sdílené** – sleva za rezervaci se použije pro instance služby Azure Cosmos DB běžící v rámci kontextu vaší fakturace žádné předplatné. Kontext fakturace závisí na tom, jak jste zaregistrovali službu Azure. Pro podnikové zákazníky sdílený obor je registraci a zahrnuje všechna předplatná (s výjimkou předplatná pro vývoj/testování) během registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.  <br/><br/> Po nákupu rezervované kapacity můžete změnit obor rezervace.  |
   |Typ rezervované kapacity   |  Typ rezervované kapacity je jednotkách žádosti zřízené propustnosti.|
   |Jednotky rezervované kapacity  |      Množství propustnost, které chcete rezervovat. Můžete tuto hodnotu vypočítat tak, že určíte propustnost potřebné pro všechny vaše prostředky služby Cosmos DB (například databáze nebo kontejnery) na oblast a potom ho podle počtu oblastí, které přidružíte k databázi Cosmos DB násobení.  <br/> Příklad: Pokud máte pět oblastí s 1 milion RU/s v každé oblasti, můžete vybrat 5 milionů RU/s pro nákup rezervace kapacitu.    |
   |Označení  |   Jeden nebo tři roky.   |

5. Sleva a cenu za rezervaci v **náklady** oddílu. Tato rezervace cena se vztahuje na prostředky Azure Cosmos DB s propustností zřízené ve všech oblastech.  

6. Vyberte **Koupit**. Po úspěšné nákupu vidíte na následujícím snímku obrazovky. 

   ![Vyplňte formulář záložní kapacitu](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Po nákupu rezervace se uplatní se okamžitě na nějaké stávající prostředky Azure Cosmos DB, které odpovídají podmínkám použití rezervace. Pokud nemáte k dispozici nějaké stávající prostředky Azure Cosmos DB, rezervace platit, když nasadíte novou instanci služby Cosmos DB, která odpovídá podmínky rezervace. V obou případech se období rezervace začne hned po úspěšném nákupu. 

Když vyprší platnost vaší rezervace, vaše instance služby Azure Cosmos DB bude nadále spuštěna a jsou účtovány za běžné sazby průběžných plateb.

## <a name="next-steps"></a>Další postup

Sleva za rezervaci se automaticky využije na prostředky Azure Cosmos DB, které odpovídají obor rezervace a atributy. Rozsah rezervace prostřednictvím webu Azure portal, Powershellu, rozhraní příkazového řádku, nebo prostřednictvím rozhraní API můžete aktualizovat.

*  Zjistěte, jak záložní kapacitu slevy na služby Azure Cosmos DB, najdete v článku [slevy pochopit rezervace Azure](../billing/billing-understand-cosmosdb-reservation-charges.md)

* Další informace o Azure rezervace, naleznete v následujících článcích:

   * [Co jsou Azure rezervace?](../billing/billing-save-compute-costs-reservations.md)  
   * [Správa Azure rezervace](../billing/billing-manage-reserved-vm-instance.md).  
   * [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)
   * [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.


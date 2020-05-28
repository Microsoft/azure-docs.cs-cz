---
title: Úspora výpočetních nákladů s využitím rezervované kapacity
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Naučte se koupit Azure SQL Database a rezervovanou kapacitu SQL spravované instance, abyste ušetřili náklady na výpočetní výkon.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: c02368b5713c0743cdca764275f76dda0e0926d2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119099"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Úspora nákladů pro prostředky s rezervovanou kapacitou – Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Šetřete peníze pomocí Azure SQL Database a spravované instance SQL tak, že v porovnání s cenami za průběžné platby potvrdíte rezervaci pro výpočetní prostředky. Díky rezervované kapacitě provedete závazek SQL Database a/nebo spravované instance SQL za období jednoho nebo tří let, abyste získali značnou slevu za výpočetní náklady. Pokud si chcete koupit rezervovanou kapacitu, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín.

Nemusíte přiřadit rezervaci konkrétním SQL Database nebo spravované instanci SQL. Tato výhoda se shoduje se stávajícími nasazeními, která jsou již spuštěná nebo jsou nově nasazená automaticky. Když zakoupíte rezervaci, potvrdíte využití výpočetních nákladů po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za výpočetní prostředky, které odpovídají atributům rezervace, se už nebudou účtovat podle tarifů průběžných plateb. Rezervace nepokrývá software, sítě ani poplatky za úložiště přidružené k této službě. Na konci rezervovaného období vyprší platnost fakturačního přínosu a SQL Database nebo spravovaná instance SQL se účtují podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete v [nabídce vyhrazené kapacity](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Rezervovanou kapacitu můžete koupit v [Azure Portal](https://portal.azure.com). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../../cost-management-billing/reservations/prepare-buy-reservation.md). Pro nákup rezervované kapacity platí:

- Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
- U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com) povolená možnost **Přidat rezervované instance**. Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném. Rezervovaná kapacita.

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) a [pochopení využití rezervace Azure pro vaše předplatné](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)s průběžnými platbami.

## <a name="determine-correct-size-before-purchase"></a>Určení správné velikosti před nákupem

Velikost rezervace by měla být založena na celkovém počtu výpočtů používaných stávajícím nebo již nasazeným SQL Database nebo spravovanou instancí SQL v konkrétní oblasti a pomocí stejné úrovně výkonu a generování hardwaru.

Předpokládejme například, že máte spuštěný jeden z obecných účelů, Gen5 – 16 vCore elastický fond a dvě samostatné databáze Gen5 – 4 pro podnikové důležité databáze. V dalším měsíci byste si měli naplánovat nasazení za další měsíc a další elastický fond Gen5 – 16 vCore a jeden z podnikových důležitých Gen5 – 32 vCore elastického fondu. Řekněme taky, že víte, že tyto prostředky budete potřebovat aspoň 1 rok. V takovém případě byste si měli koupit 32 (2x16) virtuální jádra, rezervaci za 1 roky pro izolovanou databázi nebo elastický fond pro obecné účely – Gen5 a 40 (2x4 + 32) vCore 1 rok rezervace pro izolovanou databázi nebo elastický fond – Gen5.

## <a name="buy-reserved-capacity"></a>Nákup rezervované kapacity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a potom v podokně **rezervace nákupních** oblastí vyberte **SQL Database** , abyste si nakoupili novou rezervaci pro SQL Database.
4. Vyplňte požadovaná pole. Existující databáze SQL (samostatné nebo sdružené) nebo spravované instance SQL, které odpovídají vybraným atributům, mají nárok na získání rezervované slevy kapacity. Skutečný počet databází SQL nebo spravovaných instancí SQL, které získají slevu, závisí na zvoleném rozsahu a množství.
    ![Snímek obrazovky před odesláním nákupu rezervované kapacity](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

V následující tabulce jsou popsána povinná pole.

| Pole      | Popis|
|------------|--------------|
|Předplatné|Předplatné použité pro platbu za rezervaci kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.|
|Rozsah       |Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: <br/><br/>**Shared**, SQL Database nebo spravovaná instance SQL běžící na všech předplatných v rámci vašeho fakturačního kontextu se používá sleva za rezervaci Vcore. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.<br/><br/>U **jednoho předplatného**se pro databáze SQL nebo spravované instance SQL v tomto předplatném použije sleva na Vcore rezervaci. <br/><br/>**Jedna skupina prostředků**, pro databáze SQL nebo spravované instance SQL ve vybraném předplatném a vybrané skupině prostředků v rámci tohoto předplatného se použije sleva rezervace.|
|Oblast      |Oblast Azure, která je pokrytá rezervací kapacity.|
|Typ nasazení|Typ prostředku SQL, pro který chcete zakoupit rezervaci.|
|Úroveň výkonu|Úroveň služby pro databáze SQL nebo spravované instance SQL. |
|Pojem        |Jeden rok nebo tři roky.|
|Množství    |Množství výpočetních prostředků, které se zakoupí v rámci rezervace kapacity Množství je počet virtuální jádra ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované, a obdrží fakturační slevu. Pokud například používáte nebo plánujete spustit více databází SQL s celkovou výpočetní kapacitou Gen5 16 virtuální jádra v oblasti Východní USA, pak byste měli zadat množství jako 16 pro maximalizaci výhod pro všechny databáze. |

1. Projděte si náklady na rezervaci kapacity v části **náklady** .
1. Vyberte **Koupit**.
1. Pokud chcete zobrazit stav nákupu, vyberte **Zobrazit tuto rezervaci** .

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore vám pomůže škálovat směrem nahoru nebo dolů v rámci úrovně výkonu a oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity. Rezervovaná kapacita také poskytuje flexibilitu pro dočasné přesunutí aktivních databází SQL do a z elastických fondů (ve stejné oblasti a na úrovni výkonu) jako součást vašich normálních operací, aniž by došlo ke ztrátě zvýhodněné vyhrazené kapacity. Když v rezervaci zachováte nepoužitou vyrovnávací paměť, můžete efektivně spravovat špičky výkonu, aniž byste museli překročit svůj rozpočet.

## <a name="limitation"></a>Omezení

V SQL Database nemůžete rezervovat databáze založené na úrovni serveru nebo DTU (Basic, Standard nebo Premium).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet databází SQL nebo spravovaných instancí SQL, které odpovídají rozsahu rezervace kapacity a atributům. Rozsah rezervace kapacity můžete aktualizovat prostřednictvím [Azure Portal](https://portal.azure.com), PowerShellu, CLI nebo přes rozhraní API.

Informace o tom, jak spravovat rezervaci kapacity, najdete v tématu [Správa rezervované kapacity](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Správa rezervací Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Vysvětlení slev za rezervace Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

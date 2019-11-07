---
title: Úspora nákladů pro Azure SQL Database virtuální jádra
description: Naučte se koupit Azure SQL Database rezervovanou kapacitu, abyste ušetřili náklady na výpočetní výkon.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 70e274d765b409d4024155f8553ba92200598394
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687523"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Úspora nákladů za SQL Database výpočetních prostředků s využitím Azure SQL Database rezervované kapacity

Šetřete peníze s Azure SQL Database tím, že se potvrdili rezervaci za výpočetní prostředky ve srovnání s cenami za průběžné platby. Díky Azure SQL Database rezervované kapacity provedete závazek SQL Database použít po dobu jednoho nebo tří let, abyste získali značnou slevu za výpočetní náklady. Pokud chcete koupit SQL Database rezervovanou kapacitu, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín.


Nemusíte přiřazovat rezervaci konkrétním SQL Database instancí (izolované databáze, elastické fondy nebo spravované instance). Tato výhoda automaticky získá odpovídající instance SQL Database, které jsou již spuštěny nebo nově nasazeny. Když zakoupíte rezervaci, zavedete se k využití výpočetních nákladů po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci SQL Database, poplatky za výpočetní prostředky, které se shodují s atributy rezervace, už nebudou účtovány podle tarifů průběžných plateb. Rezervace nepokrývá software, sítě ani poplatky za úložiště spojené s instancí SQL Database. Na konci rezervovaného období vyprší platnost fakturačního přínosu a databáze SQL se účtují podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete v [nabídce SQL Database rezervované kapacity](https://azure.microsoft.com/pricing/details/sql-database/managed/).

V [Azure Portal](https://portal.azure.com)si můžete koupit Azure SQL Database rezervovanou kapacitu. Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../billing/billing-monthly-payments-reservations.md). Zakoupení SQL Database rezervované kapacity:

- Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
- U předplatných se smlouvou Enterprise musí být na webu **EA Portal** povolená možnost [Přidat rezervované instance](https://ea.azure.com). Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
- V případě programu Cloud Solution Provider (CSP) mohou SQL Database rezervované kapacity koupit pouze agenti správce nebo prodejní agenti.

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](../billing/billing-understand-reserved-instance-usage-ea.md) a [pochopení využití rezervace Azure pro vaše průběžné platby. předplatné](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Před nákupem určit správnou velikost SQL

Velikost rezervace by měla být založena na celkovém počtu výpočtů používaných stávajícími nebo dříve nasazenými izolovanými databázemi, elastickými fondy nebo spravovanými instancemi v konkrétní oblasti a pomocí stejné úrovně výkonu a generování hardwaru.

Předpokládejme například, že máte spuštěný jeden z obecných účelů, Gen5 – 16 vCore elastický fond a dvě samostatné databáze Gen5 – 4 pro podnikové důležité databáze. V dalším měsíci byste si měli naplánovat nasazení za další měsíc a další elastický fond Gen5 – 16 vCore a jeden z podnikových důležitých Gen5 – 32 vCore elastického fondu. Řekněme taky, že víte, že tyto prostředky budete potřebovat aspoň 1 rok. V takovém případě byste si měli koupit 32 (2x16) virtuální jádra, rezervaci za 1 roky pro izolovanou databázi nebo elastický fond pro obecné účely – Gen5 a 40 (2x4 + 32) vCore 1 rok rezervace pro izolovanou databázi nebo elastický fond – Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Koupit SQL Database rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a potom v podokně rezervace nákupních oblastí vyberte **SQL Database** , abyste si nakoupili novou rezervaci pro SQL Database.
4. Vyplňte požadovaná pole. Stávající nebo nové izolované databáze, elastické fondy nebo spravované instance, které odpovídají atributům, které vyberete, mají nárok na získání rezervované slevy kapacity. Skutečný počet instancí SQL Database, které získají slevu, závisí na zvoleném rozsahu a množství.
    ![snímku obrazovky před odesláním SQL Database nákupu rezervovaných kapacit](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

V následující tabulce jsou popsána povinná pole.

| Pole      | Popis|
|------------|--------------|
|Předplatné|Předplatné použité pro platbu SQL Database rezervované rezervace kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci rezervované kapacity SQL Database. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.|
|Rozsah       |Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: <br/><br/>V rámci vašeho fakturačního kontextu se pro SQL Database instance, které běží v jakýchkoli předplatných, používá **sleva za rezervaci**Vcore. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.<br/><br/>Pro SQL Database instance v tomto předplatném se použije **sleva za rezervované**Vcore. <br/><br/>**Jedna skupina prostředků**, použije se sleva rezervace pro SQL Database instance ve vybraném předplatném a v rámci daného předplatného.|
|Region (Oblast)      |Oblast Azure, která je pokrytá rezervací rezervované kapacity SQL Database.|
|Typ nasazení|Typ prostředku SQL, pro který chcete zakoupit rezervaci.|
|Úroveň výkonu|Úroveň služby pro instance SQL Database.
|Označení        |Jeden rok nebo tři roky.|
|Množství    |Množství výpočetních prostředků, které se zakoupí v rámci rezervace rezervované kapacity SQL Database. Množství je počet virtuální jádra ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované, a obdrží fakturační slevu. Pokud například používáte nebo plánujete spouštět instance SQL Database s celkovou výpočetní kapacitou Gen5 16 virtuální jádra v oblasti Východní USA, pak zadáte množství na 16, aby se pro všechny instance maximalizovala výhoda. |

1. Přečtěte si náklady na rezervaci rezervované kapacity SQL Database v části **náklady** .
1. Vyberte **Koupit**.
1. Pokud chcete zobrazit stav nákupu, vyberte **Zobrazit tuto rezervaci** .

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore vám pomůže škálovat směrem nahoru nebo dolů v rámci úrovně výkonu a oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity. SQL Database Rezervovaná kapacita také poskytuje flexibilitu pro dočasné přesouvání aktivních databází mezi fondy a izolovanými databázemi v rámci normálních operací (ve stejné oblasti a úrovni výkonu), aniž by došlo ke ztrátě rezervované kapacity. výhodnosti. Když v rezervaci zachováte nepoužitou vyrovnávací paměť, můžete efektivně spravovat špičky výkonu, aniž byste museli překročit svůj rozpočet.

## <a name="limitation"></a>Omezení

Nemůžete rezervovat databáze SQL založené na DTU (Basic, Standard nebo Premium).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet instancí SQL Database, které odpovídají SQL Database rezervovaných rezervací kapacity a atributů. Rozsah rezervované rezervace kapacity SQL Database můžete aktualizovat prostřednictvím [Azure Portal](https://portal.azure.com), PowerShellu, CLI nebo přes rozhraní API.

Informace o tom, jak spravovat rezervaci rezervované kapacity SQL Database, najdete v tématu [správa SQL Database rezervované kapacity](../billing/billing-manage-reserved-vm-instance.md).

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](../billing/billing-save-compute-costs-reservations.md)
- [Správa rezervací Azure](../billing/billing-manage-reserved-vm-instance.md)
- [Vysvětlení slev za rezervace Azure](../billing/billing-understand-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

---
title: Platba za výpočetní prostředky s využitím rezervované kapacity – Azure Database for MySQL
description: Platba za Azure Database for MySQL výpočetních prostředků s rezervovanou kapacitou
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 27cd1c9085771bd9ac2b18c37b73235d7f18ad5a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538081"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Platba za Azure Database for MySQL výpočetních prostředků s rezervovanou kapacitou

Azure Database for MySQL nyní pomáhá ušetřit peníze tím, že se předplatí pro výpočetní prostředky v porovnání s cenami průběžných plateb. Díky Azure Database for MySQL rezervované kapacity vytvoříte na serveru MySQL předem závazek na jeden nebo tři roky, abyste dosáhli výrazné slevy za výpočetní náklady. Pokud chcete koupit Azure Database for MySQL rezervovanou kapacitu, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín. </br>

Nemusíte přiřadit rezervaci konkrétním Azure Database for MySQLm serverům. Již běžící Azure Database for MySQL, nebo nově nasazené, budou automaticky využívat výhod rezervovaných cen. Když si koupíte rezervaci, platíte za výpočetní náklady po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za výpočetní výkon Azure Database for MySQL se už nebudou účtovat podle tarifů průběžných plateb. Rezervace nepokrývá software, sítě ani poplatky za úložiště spojené s databázovým serverem MySQL. Na konci rezervovaného období vyprší platnost fakturačního přínosu a Azure Database for MySQL se účtují podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete v [nabídce Azure Database for MySQL rezervované kapacity](https://azure.microsoft.com/pricing/details/mysql/). </br>

V [Azure Portal](https://portal.azure.com/)si můžete koupit Azure Database for MySQL rezervovanou kapacitu. Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../cost-management-billing/reservations/prepare-buy-reservation.md). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance** . Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* V případě programu Cloud Solution Provider (CSP) mohou Azure Database for MySQL rezervované kapacity koupit pouze agenti správce nebo prodejní agenti. </br>

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) a [pochopení využití rezervace Azure pro vaše předplatné](../cost-management-billing/reservations/understand-reserved-instance-usage.md)s průběžnými platbami.


## <a name="determine-the-right-database-size-before-purchase"></a>Určení správné velikosti databáze před nákupem

Velikost rezervace by měla být založená na celkovém množství výpočetních operací používaných existujícím nebo již nasazeným serverem v konkrétní oblasti a pomocí stejné úrovně výkonu a generování hardwaru.</br>

Předpokládejme například, že máte spuštěný jeden pro obecné účely, Gen5 – 32 vCore MySQL Database a dvě paměťově optimalizované databáze MySQL Gen5 – 16 vCore. V dalším měsíci byste si měli naplánovat nasazení za další měsíc, což je další obecné účely, Gen5 – 32 vCore databázového serveru a jedna paměťově optimalizovaná, Gen5 – 16 vCore databázový server. Dejme tomu, že víte, že tyto prostředky budete potřebovat aspoň 1 rok. V takovém případě byste si měli koupit 64 (2x32) virtuální jádra, rezervaci 1 roku pro izolovanou databázi pro obecné účely – Gen5 a 48 (2x16 + 16) pro paměť s izolovanými databázemi – Vcore.


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Koupit Azure Database for MySQL rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace** .
3. Vyberte **Přidat** a potom v podokně rezervace nákupu vyberte **Azure Database for MySQL** a Zakupte novou rezervaci pro databáze MySQL.
4. Vyplňte požadovaná pole. Stávající nebo nové databáze, které odpovídají atributům, které vyberete, mají nárok na získání rezervované slevy kapacity. Skutečný počet serverů Azure Database for MySQL, které získávají slevu, závisí na zvoleném rozsahu a množství.


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="Přehled rezervovaných cen":::


V následující tabulce jsou popsána povinná pole.

| Pole | Popis |
| :------------ | :------- |
| Předplatné   | Předplatné použité pro platbu Azure Database for MySQL rezervované rezervace kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci rezervované kapacity Azure Database for MySQL. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.
| Obor | Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: </br></br> **Shared** , Azure Database for MySQL na servery běžící v rámci vašeho fakturačního kontextu se použije sleva za rezervaci Vcore. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.</br></br> Pro Azure Database for MySQL servery v tomto **předplatném** se použije sleva za rezervované Vcore. </br></br> **Jedna skupina prostředků** , použije se sleva rezervace pro Azure Database for MySQL servery ve vybraném předplatném a v rámci daného předplatného.
| Region (Oblast) | Oblast Azure, která je pokrytá rezervací rezervované kapacity Azure Database for MySQL.
| Typ nasazení | Typ prostředku Azure Database for MySQL, pro který chcete zakoupit rezervaci.
| Úroveň výkonu | Vrstva služby pro servery Azure Database for MySQL.
| Pojem | Jeden rok
| Množství | Množství výpočetních prostředků, které se zakoupí v rámci rezervace rezervované kapacity Azure Database for MySQL. Množství je počet virtuální jádra ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované, a obdrží fakturační slevu. Pokud například používáte nebo plánujete spustit Azure Database for MySQL servery s celkovou výpočetní kapacitou Gen5 16 virtuální jádra v oblasti Východní USA, pak byste zadali množství na 16, aby se zajistilo zvýšení výhod pro všechny servery.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore vám pomůže škálovat směrem nahoru nebo dolů v rámci úrovně výkonu a oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity. 

## <a name="need-help--contact-us"></a>Potřebujete tuto podporu? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet serverů Azure Database for MySQL, které odpovídají Azure Database for MySQL rezervovaných rezervací kapacity a atributů. Rozsah rezervace rezervované kapacity Azure Database for MySQL můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, CLI nebo přes rozhraní API. </br></br>
Informace o tom, jak spravovat Azure Database for MySQL rezervovanou kapacitu, najdete v tématu Správa Azure Database for MySQL rezervované kapacity.

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Správa rezervací Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Vysvětlení slev za rezervace Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
* [Vysvětlení využití rezervací u smlouvy Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)
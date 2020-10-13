---
title: Platba za výpočetní prostředky s využitím rezervované kapacity – Azure Database for MariaDB
description: Platba za Azure Database for MariaDB výpočetních prostředků s rezervovanou kapacitou
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 47ddad70b4764fedefb50b93de2b7f078cf9fd27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82732899"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Platba za Azure Database for MariaDB výpočetních prostředků s rezervovanou kapacitou

Azure Database for MariaDB nyní pomáhá ušetřit peníze tím, že se předplatí pro výpočetní prostředky v porovnání s cenami průběžných plateb. Díky Azure Database for MariaDB rezervované kapacity vytvoříte předem závazek na serverech MariaDB po dobu jednoho nebo tří let, abyste dosáhli výrazné slevy na výpočetní náklady. Pokud chcete koupit Azure Database for MariaDB rezervovanou kapacitu, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín. </br>

Nemusíte přiřadit rezervaci konkrétním Azure Database for MariaDBm serverům. Již běžící Azure Database for MariaDB, nebo nově nasazené, budou automaticky využívat výhod rezervovaných cen. Když si koupíte rezervaci, platíte za výpočetní náklady po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za výpočetní výkon služby Azure Database for MariaDB se už nebudou účtovat podle tarifů průběžných plateb. Rezervace nepokrývá software, sítě ani poplatky za úložiště přidružené k databázovému serveru MariaDB. Na konci rezervovaného období vyprší platnost fakturačního přínosu a Azure Database for MariaDB se účtují podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete v [nabídce Azure Database for MariaDB rezervované kapacity](https://azure.microsoft.com/pricing/details/mariadb/). </br>

V [Azure Portal](https://portal.azure.com/)si můžete koupit Azure Database for MariaDB rezervovanou kapacitu. Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../cost-management-billing/reservations/monthly-payments-reservations.md). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* V případě programu Cloud Solution Provider (CSP) mohou Azure Database for MariaDB rezervované kapacity koupit pouze agenti správce nebo prodejní agenti. </br>

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a [pochopení využití rezervace Azure pro vaše předplatné](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)s průběžnými platbami.


## <a name="determine-the-right-server-size-before-purchase"></a>Určení správné velikosti serveru před nákupem

Velikost rezervace by měla být založena na celkovém počtu výpočtů používaných stávající nebo již nasazenou instancí databází v konkrétní oblasti a pomocí stejné úrovně výkonu a generování hardwaru.</br>

Předpokládejme například, že máte spuštěný jeden z obecných účelů, Gen5 – 32 vCore MariaDB Database a dvě paměťově optimalizované databáze Gen5 – 16 vCore MariaDB. V dalším měsíci byste si měli naplánovat nasazení za další měsíc, což je další obecné účely, Gen5 – 32 vCore databázového serveru a jedna paměťově optimalizovaná, Gen5 – 16 vCore databázový server. Dejme tomu, že víte, že tyto prostředky budete potřebovat aspoň 1 rok. V takovém případě byste si měli koupit 64 (2x32) virtuální jádra, rezervaci 1 roku pro izolovanou databázi pro obecné účely – Gen5 a 48 (2x16 + 16) pro paměť s izolovanými databázemi – Vcore.


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Koupit Azure Database for MariaDB rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace**.
3.  Vyberte **Přidat** a potom v podokně rezervace nákupu vyberte **Azure Database for MariaDB** a Zakupte novou rezervaci pro databáze MariaDB.
4.  Vyplňte požadovaná pole. Stávající nebo nové databáze, které odpovídají atributům, které vyberete, mají nárok na získání rezervované slevy kapacity. Skutečný počet serverů Azure Database for MariaDB, které získávají slevu, závisí na zvoleném rozsahu a množství.


![Přehled rezervovaných cen](media/concepts-reserved-pricing/mariadb-reserved-price.png)


V následující tabulce jsou popsána povinná pole.

| Pole | Popis |
| :------------ | :------- |
| Předplatné   | Předplatné použité pro platbu Azure Database for MariaDB rezervované rezervace kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci rezervované kapacity Azure Database for MariaDB. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.
| Rozsah | Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: </br></br> **Shared**, Azure Database for MariaDB na servery běžící v rámci vašeho fakturačního kontextu se použije sleva za rezervaci Vcore. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.</br></br> Pro Azure Database for MariaDB servery v tomto **předplatném**se použije sleva za rezervované Vcore. </br></br> **Jedna skupina prostředků**, použije se sleva rezervace pro Azure Database for MariaDB servery ve vybraném předplatném a v rámci daného předplatného.
| Oblast | Oblast Azure, která je pokrytá rezervací rezervované kapacity Azure Database for MariaDB.
| Typ nasazení | Typ prostředku Azure Database for MariaDB, pro který chcete zakoupit rezervaci.
| Úroveň výkonu | Vrstva služby pro servery Azure Database for MariaDB.
| Označení | Jeden rok
| Množství | Množství výpočetních prostředků, které se zakoupí v rámci rezervace rezervované kapacity Azure Database for MariaDB. Množství je počet virtuální jádra ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované, a obdrží fakturační slevu. Pokud například používáte nebo plánujete spustit Azure Database for MariaDB servery s celkovou výpočetní kapacitou Gen5 16 virtuální jádra v oblasti Východní USA, pak byste zadali množství na 16, aby se zajistilo zvýšení výhod pro všechny servery.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore vám pomůže škálovat směrem nahoru nebo dolů v rámci úrovně výkonu a oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity. 

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet serverů Azure Database for MariaDB, které odpovídají Azure Database for MariaDB rezervovaných rezervací kapacity a atributů. Rozsah rezervace rezervované kapacity Azure Database for MariaDB můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, CLI nebo přes rozhraní API. </br></br>
Informace o tom, jak spravovat Azure Database for MariaDB rezervovanou kapacitu, najdete v tématu Správa Azure Database for MariaDB rezervované kapacity.

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou Azure reservations](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Vysvětlení slev za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Vysvětlení využití rezervací u smlouvy Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

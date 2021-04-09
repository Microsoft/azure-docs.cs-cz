---
title: Rezervované ceny za výpočetní prostředky – Azure Database for PostgreSQL – jeden server
description: Platba za Azure Database for PostgreSQL výpočetních prostředků s rezervovanou kapacitou
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 9b8dafa4a69358b3f6f09551ac426b908750e2f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735468"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Zaplatit za Azure Database for PostgreSQL výpočetních prostředků na jednom serveru s rezervovanou kapacitou

Azure Database for PostgreSQL nyní pomáhá ušetřit peníze tím, že se předplatí pro výpočetní prostředky v porovnání s cenami průběžných plateb. Díky Azure Database for PostgreSQL rezervované kapacity vytvoříte předem závazek na PostgreSQL Server po dobu jednoho nebo tří let, abyste dosáhli výrazné slevy za výpočetní náklady. Pokud chcete koupit Azure Database for PostgreSQL rezervovanou kapacitu, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín. </br>

Nemusíte přiřadit rezervaci konkrétním Azure Database for PostgreSQLm serverům. Již spuštěná Azure Database for PostgreSQL (nebo nově nasazená) automaticky získá výhodu rezervovaných cen. Když si koupíte rezervaci, platíte za výpočetní náklady po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za výpočetní výkon služby Azure Database for PostgreSQL se už nebudou účtovat podle tarifů průběžných plateb. Rezervace nepokrývá software, sítě ani poplatky za úložiště spojené s databázovými servery PostgreSQL. Na konci rezervovaného období vyprší platnost fakturačního přínosu a Azure Database for PostgreSQL se účtují podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete v [nabídce Azure Database for PostgreSQL rezervované kapacity](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> K dispozici jsou ceny rezervovaných kapacit pro Azure Database for PostgreSQL na [jednom serveru](./overview.md#azure-database-for-postgresql---single-server) i v možnostech nasazení [Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) . Informace o cenách na úrovni Citus (RI) najdete na [této stránce](concepts-hyperscale-reserved-pricing.md).

V [Azure Portal](https://portal.azure.com/)si můžete koupit Azure Database for PostgreSQL rezervovanou kapacitu. Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../cost-management-billing/reservations/prepare-buy-reservation.md). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* V případě programu Cloud Solution Provider (CSP) mohou Azure Database for PostgreSQL rezervované kapacity koupit pouze agenti správce nebo prodejní agenti. </br>

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) a [pochopení využití rezervace Azure pro vaše předplatné](../cost-management-billing/reservations/understand-reserved-instance-usage.md)s průběžnými platbami.


## <a name="determine-the-right-server-size-before-purchase"></a>Určení správné velikosti serveru před nákupem

Velikost rezervace by měla být založená na celkovém množství výpočetních prostředků používaných stávajícími nebo již nasazenými servery v konkrétní oblasti a pomocí stejné úrovně výkonu a generování hardwaru.</br>

Předpokládejme například, že máte spuštěnou Gen5 databázi pro obecné účely – 32 vCore a dvě paměťově optimalizované databáze Gen5 – 16 vCore PostgreSQL. V příštím měsíci byste tak měli v úmyslu nasadit další Gen5 databázový server pro obecné účely – 8 vCore a jeden paměťově optimalizovaný Gen5 – 32 vCore databázový server. Řekněme, že víte, že tyto prostředky budete potřebovat aspoň jeden rok. V takovém případě byste si měli koupit 40 (32 + 8) virtuální jádra, jednoletou rezervaci pro izolovanou databázi pro obecné účely – Gen5 a 64 (2x16 + 32) vCore za paměť pro izolovanou databázi, která je optimalizována – Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Koupit Azure Database for PostgreSQL rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a potom v podokně rezervace nákupu vyberte **Azure Database for PostgreSQL** a Zakupte novou rezervaci pro databáze PostgreSQL.
4. Vyplňte požadovaná pole. Stávající nebo nové databáze, které odpovídají atributům, které vyberete, mají nárok na získání rezervované slevy kapacity. Skutečný počet serverů Azure Database for PostgreSQL, které získávají slevu, závisí na zvoleném rozsahu a množství.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Přehled rezervovaných cen":::


V následující tabulce jsou popsána povinná pole.

| Pole | Popis |
| :------------ | :------- |
| Předplatné   | Předplatné použité pro platbu Azure Database for PostgreSQL rezervované rezervace kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci rezervované kapacity Azure Database for PostgreSQL. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají ze zůstatku Azure Prepaymentu (dřív označovaný jako peněžní závazek) v rámci dané registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.
| Obor | Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: </br></br> **Shared**, Azure Database for PostgreSQL na servery běžící v rámci vašeho fakturačního kontextu se použije sleva za rezervaci Vcore. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.</br></br> Pro Azure Database for PostgreSQL servery v tomto **předplatném** se použije sleva za rezervované Vcore. </br></br> **Jedna skupina prostředků**, použije se sleva rezervace pro Azure Database for PostgreSQL servery ve vybraném předplatném a v rámci daného předplatného.
| Oblast | Oblast Azure, která je pokrytá rezervací rezervované kapacity Azure Database for PostgreSQL.
| Typ nasazení | Typ prostředku Azure Database for PostgreSQL, pro který chcete zakoupit rezervaci.
| Úroveň výkonu | Vrstva služby pro servery Azure Database for PostgreSQL.
| Označení | Jeden rok
| Množství | Množství výpočetních prostředků, které se zakoupí v rámci rezervace rezervované kapacity Azure Database for PostgreSQL. Množství je počet virtuální jádra ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované, a obdrží fakturační slevu. Pokud například používáte nebo plánujete spustit Azure Database for PostgreSQL servery s celkovou výpočetní kapacitou Gen5 16 virtuální jádra v oblasti Východní USA, pak byste zadali množství na 16, aby se zajistilo zvýšení výhod pro všechny servery.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore vám pomůže škálovat směrem nahoru nebo dolů v rámci úrovně výkonu a oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity. Pokud budete škálovat na vyšší virtuální jádra, než jakou má Rezervovaná kapacita, bude se vám účtovat nadměrný virtuální jádra s využitím cen průběžných plateb.


## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet serverů Azure Database for PostgreSQL, které odpovídají Azure Database for PostgreSQL rezervovaných rezervací kapacity a atributů. Rozsah rezervace rezervované kapacity Azure Database for PostgreSQL můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, CLI nebo přes rozhraní API.

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Správa rezervací Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Vysvětlení slev za rezervace Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Vysvětlení využití rezervací u smlouvy Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)
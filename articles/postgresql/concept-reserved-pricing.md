---
title: Rezervované ceny za výpočetní prostředky – Azure Database for PostgreSQL – jeden server
description: Platba za Azure Database for PostgreSQL výpočetních prostředků s rezervovanou kapacitou
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 634ab645ea49c29bb5624afd82e549dfbc8d0fdf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769893"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>Platba za Azure Database for PostgreSQL výpočetních prostředků s rezervovanou kapacitou

Azure Database for PostgreSQL nyní pomáhá ušetřit peníze tím, že se předplatí pro výpočetní prostředky v porovnání s cenami průběžných plateb. Díky Azure Database for PostgreSQL rezervované kapacity vytvoříte na PostgreSQL serveru po dobu jednoho roku roční závazek, který na výpočetní náklady získá významnou slevu. Pokud chcete koupit Azure Database for PostgreSQL rezervovanou kapacitu, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín. </br>

Nemusíte přiřadit rezervaci konkrétním Azure Database for PostgreSQLm serverům. Již běžící Azure Database for PostgreSQL, nebo nově nasazené, budou automaticky využívat výhod rezervovaných cen. Když si koupíte rezervaci, platíte za výpočetní náklady po dobu jednoho roku. Jakmile si koupíte rezervaci, poplatky za výpočetní výkon služby Azure Database for PostgreSQL se už nebudou účtovat podle tarifů průběžných plateb. Rezervace nepokrývá software, sítě ani poplatky za úložiště spojené s databázovými servery PostgreSQL. Na konci rezervovaného období vyprší platnost fakturačního přínosu a Azure Database for PostgreSQL se účtují podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete v [nabídce Azure Database for PostgreSQL rezervované kapacity](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Ceny za rezervovanou kapacitu jsou dostupné jenom pro nasazení Azure Database for PostgreSQLho jednoho serveru, ne pro nasazení Citus s [jednoduchým](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) [škálováním](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) .

V [Azure Portal](https://portal.azure.com/)si můžete koupit Azure Database for PostgreSQL rezervovanou kapacitu. Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* V případě programu Cloud Solution Provider (CSP) mohou Azure Database for PostgreSQL rezervované kapacity koupit pouze agenti správce nebo prodejní agenti. </br>

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a [pochopení využití rezervace Azure pro vaše předplatné](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)s průběžnými platbami.


## <a name="determine-the-right-server-size-before-purchase"></a>Určení správné velikosti serveru před nákupem

Velikost rezervace by měla být založená na celkovém množství výpočetních prostředků používaných stávajícími nebo již nasazenými servery v konkrétní oblasti a pomocí stejné úrovně výkonu a generování hardwaru.</br>

Předpokládejme například, že máte spuštěný jeden z obecných účelů, Gen5 – 32 vCore PostgreSQL Database a dvě paměťově optimalizované databáze Gen5 – 16 vCore PostgreSQL. V dalším měsíci byste si měli naplánovat nasazení za další měsíc, což je další obecné účely, Gen5 – 32 vCore databázového serveru a jedna paměťově optimalizovaná, Gen5 – 16 vCore databázový server. Dejme tomu, že víte, že tyto prostředky budete potřebovat aspoň 1 rok. V takovém případě byste si měli koupit 64 (2x32) virtuální jádra, rezervaci 1 roku pro izolovanou databázi pro obecné účely – Gen5 a 48 (2x16 + 16) pro paměť s izolovanými databázemi – Vcore.


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Koupit Azure Database for PostgreSQL rezervovanou kapacitu

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a potom v podokně rezervace nákupu vyberte **Azure Database for PostgreSQL** a Zakupte novou rezervaci pro databáze PostgreSQL.
4. Vyplňte požadovaná pole. Stávající nebo nové databáze, které odpovídají atributům, které vyberete, mají nárok na získání rezervované slevy kapacity. Skutečný počet serverů Azure Database for PostgreSQL, které získávají slevu, závisí na zvoleném rozsahu a množství.


![Přehled rezervovaných cen](media/concepts-reserved-pricing/postgresql-reserved-price.png)


V následující tabulce jsou popsána povinná pole.

| Pole | Popis |
| :------------ | :------- |
| Předplatné   | Předplatné použité pro platbu Azure Database for PostgreSQL rezervované rezervace kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci rezervované kapacity Azure Database for PostgreSQL. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.
| Rozsah | Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: </br></br> **Shared**, Azure Database for PostgreSQL na servery běžící v rámci vašeho fakturačního kontextu se použije sleva za rezervaci Vcore. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.</br></br> Pro Azure Database for PostgreSQL servery v tomto **předplatném**se použije sleva za rezervované Vcore. </br></br> **Jedna skupina prostředků**, použije se sleva rezervace pro Azure Database for PostgreSQL servery ve vybraném předplatném a v rámci daného předplatného.
| Oblast | Oblast Azure, která je pokrytá rezervací rezervované kapacity Azure Database for PostgreSQL.
| Typ nasazení | Typ prostředku Azure Database for PostgreSQL, pro který chcete zakoupit rezervaci.
| Úroveň výkonu | Vrstva služby pro servery Azure Database for PostgreSQL.
| Doba účinnosti | Jeden rok
| Množství | Množství výpočetních prostředků, které se zakoupí v rámci rezervace rezervované kapacity Azure Database for PostgreSQL. Množství je počet virtuální jádra ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované, a obdrží fakturační slevu. Pokud například používáte nebo plánujete spustit Azure Database for PostgreSQL servery s celkovou výpočetní kapacitou Gen5 16 virtuální jádra v oblasti Východní USA, pak byste zadali množství na 16, aby se zajistilo zvýšení výhod pro všechny servery.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore vám pomůže škálovat směrem nahoru nebo dolů v rámci úrovně výkonu a oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity. 

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet serverů Azure Database for PostgreSQL, které odpovídají Azure Database for PostgreSQL rezervovaných rezervací kapacity a atributů. Rozsah rezervace rezervované kapacity Azure Database for PostgreSQL můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, CLI nebo přes rozhraní API. </br></br>
Informace o tom, jak spravovat Azure Database for PostgreSQL rezervovanou kapacitu, najdete v tématu Správa Azure Database for PostgreSQL rezervované kapacity.

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou Azure reservations](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Vysvětlení slev za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Vysvětlení využití rezervací u smlouvy Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

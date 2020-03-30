---
title: Platba předem za výpočetní prostředky s rezervovanou kapacitou – Azure Database for MySQL
description: Předplatit azure databáze pro výpočetní prostředky MySQL s rezervovanou kapacitou
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 78c8750de7189bad33e9bbc766a3d7543a646f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159349"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Předplatit azure databáze pro výpočetní prostředky MySQL s rezervovanou kapacitou

Azure Database for MySQL vám teď pomůže ušetřit peníze tím, že předplatíte výpočetní prostředky ve srovnání s průběžnými platbami. S vyhrazenou kapacitou Azure Database for MySQL se na serveru MySQL předem zavážete na dobu jednoho nebo tří let, abyste získali výraznou slevu na výpočetní náklady. Chcete-li zakoupit Azure Database pro rezervovanou kapacitu MySQL, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín. </br>

Není nutné přiřadit rezervaci konkrétní Azure Database pro servery MySQL. Již spuštěná databáze Azure pro MySQL nebo ty, které jsou nově nasazené, automaticky získají výhodu rezervovaných cen. Zakoupením rezervace platíte předem výpočetní náklady na dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, databáze Azure pro výpočetní poplatky MySQL, které odpovídají atributům rezervace, se už nebudou účtovat podle průběžných plateb. Rezervace se nevztahuje na poplatky za software, sítě nebo úložiště spojené s databázovým serverem MySQL. Na konci období rezervace vyprší platnost fakturační výhody a databáze Azure pro MySQL se účtuje za průběžnou průběžnou platbu. Rezervace se neobnovují automaticky. Informace o cenách najdete v tématu [Azure Database for MySQL rezervované kapacity nabídky](https://azure.microsoft.com/pricing/details/mysql/). </br>

Rezervovanou kapacitu Azure Database for MySQL si můžete zakoupit na [webu Azure Portal](https://portal.azure.com/). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň pro jedno předplatné Enterprise nebo individuální předplatné s průběžnými platbami.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Nebo pokud je toto nastavení zakázané, musíte být správcem EA v předplatném.
* V programu Zprostředkovatel cloudových řešení (CSP) mohou azure database pro rezervovanou kapacitu MySQL zakoupit jenom agenti pro správu nebo prodejci. </br>

Podrobnosti o tom, jak se podnikovým zákazníkům a zákazníkům s průběžným implacem účtují poplatky za nákupy rezervací, najdete [v tématu vysvětlení využití rezervací Azure pro registraci enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a [vysvětlení využití rezervací Azure pro vaše předplatné s průběžným platbou](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-database-size-before-purchase"></a>Určení správné velikosti databáze před nákupem

Velikost rezervace by měla být založena na celkovém množství výpočetních prostředků používaných existujícím serverem nebo serverem, který bude brzy nasazen v rámci určité oblasti, a pomocí stejné úrovně výkonu a generování hardwaru.</br>

Předpokládejme například, že používáte jeden obecný účel, Gen5 – 32 vCore MySQL databáze a dvě optimalizované paměti, Gen5 – 16 vCore MySQL databáze. Dále předpokládáme, že plánujete nasadit během příštího měsíce další obecný účel, Gen5 – 32 virtuálních jader databázový server a jednu optimalizovanou paměť, Gen5 – 16 virtuálních jader databázový server. Předpokládejme, že víte, že budete potřebovat tyto zdroje po dobu nejméně 1 roku. V takovém případě byste si měli zakoupit 64 (2x32) virtuálních jader, 1 rok rezervace pro jednu databázi obecnéúčely - Gen5 a 48 (2x16 + 16) vCore 1 rok rezervace pro jednu databázi paměti optimalizované - Gen5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Koupit Azure Database pro rezervovanou kapacitu MySQL

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Vyberte **všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a potom v podokně Rezervace nákupu vyberte **Azure Database for MySQL** a zakoupíte novou rezervaci pro databáze MySQL.
4. Vyplňte požadovaná pole. Existující nebo nové databáze, které odpovídají vybraným atributům, mají nárok na získání slevy na rezervovanou kapacitu. Skutečný počet databáze Azure pro servery MySQL, které získají slevu, závisí na vybraném oboru a množství.


![Přehled rezervovaných cen](media/concepts-reserved-pricing/mysql-reserved-price.png)


Následující tabulka popisuje povinná pole.

| Pole | Popis |
| :------------ | :------- |
| Předplatné   | Předplatné slouží k platbě za Azure Database pro rezervaci rezervované kapacity MySQL. Způsob platby na předplatné se účtuje předem náklady na Azure Database pro MySQL rezervace rezervované kapacity. Typ předplatného musí být smlouva enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo individuální smlouva s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U individuálního předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury v rámci předplatného.
| Rozsah | Obor rezervace virtuálních jader může zahrnovat jedno předplatné nebo více předplatných (sdílený obor). Pokud vyberete: </br></br> **Sdílená**sleva na rezervaci virtuálních jader se použije na Azure Database pro servery MySQL spuštěné ve všech předplatných v rámci vašeho fakturačního kontextu. Pro podnikové zákazníky je sdílený obor registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.</br></br> **Jedno předplatné**, sleva rezervace virtuálních jader se použije na Azure Database pro servery MySQL v tomto předplatném. </br></br> **Jedna skupina prostředků**, sleva rezervace se použije na Azure Database pro servery MySQL ve vybraném předplatném a vybrané skupině prostředků v rámci tohoto předplatného.
| Region (Oblast) | Oblast Azure, která je pokryta Azure Database for MySQL rezervace rezervované kapacity.
| Typ nasazení | Typ prostředku Azure Database for MySQL, pro který chcete rezervaci zakoupit.
| Úroveň výkonu | Úroveň služby pro azure databázi pro servery MySQL.
| Označení | Jeden rok
| Množství | Množství výpočetních prostředků nakupovaných v rámci databáze Azure pro rezervaci rezervované kapacity MySQL. Množství je počet virtuálních jader ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované a získají fakturační slevu. Pokud například spouštěte nebo plánujete spouštět azure databázi pro servery MySQL s celkovou výpočetní kapacitou virtuálních jader Gen5 16 v oblasti USA – východní USA, určíte množství jako 16, abyste maximalizovali přínos pro všechny servery.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti virtuálních jader

Flexibilita velikosti virtuálních jader vám pomůže vertikálně navýšit nebo snížit kapacitu v rámci úrovně výkonu a oblasti, aniž byste ztratili výhodu rezervované kapacity. 

## <a name="need-help--contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva na rezervaci virtuálních jader se automaticky použije na počet azure databáze pro servery MySQL, které odpovídají oboru rezervace rezervované kapacity Azure pro MySQL a atributům. Můžete aktualizovat obor databáze Azure pro rezervaci rezervované kapacity MySQL prostřednictvím portálu Azure, PowerShell, CLI nebo prostřednictvím rozhraní API. </br></br>
Informace o tom, jak spravovat rezervovanou kapacitu Azure Database for MySQL, najdete v tématu správa vyhrazené kapacity Azure Database for MySQL.

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou rezervace Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Vysvětlení slev za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [Vysvětlení využití rezervací u smlouvy Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)


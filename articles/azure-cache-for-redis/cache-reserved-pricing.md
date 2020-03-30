---
title: Platba předem za výpočetní prostředky s rezervovanou kapacitou – Azure Cache for Redis
description: Platba předem pro výpočetní prostředky Azure Cache for Redis s rezervovanou kapacitou
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530299"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Platba předem pro výpočetní prostředky Azure Cache for Redis s rezervovanou kapacitou

Azure Cache for Redis vám teď pomůže ušetřit peníze tím, že předplatíte výpočetní prostředky ve srovnání s průběžnými platbami. S vyhrazenou kapacitou Azure Cache for Redis se předem zavazujete k mezipaměti po dobu jednoho nebo tří let, abyste získali výraznou slevu na výpočetní náklady. Chcete-li zakoupit azure cache pro redis rezervované kapacity, musíte zadat oblast Azure, úroveň služby a termín.

Není nutné přiřadit rezervaci ke konkrétním ubožnutí Azure pro instance Redis. Již spuštěná Azure Cache pro Redis nebo ty, které jsou nově nasazené automaticky získat výhodu rezervované ceny, až do velikosti rezervované mezipaměti. Zakoupením rezervace platíte předem výpočetní náklady na dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, Azure Cache for Redis výpočetní poplatky, které odpovídají atributy rezervace se již neúčtují na průběžných sazeb. Rezervace se nevztahuje na síťové nebo úložné poplatky spojené s mezipamětí. Na konci období rezervace vyprší platnost fakturační výhody a Azure Cache pro Redis se účtuje za průběžnou platbu. Rezervace se neobnovují automaticky. Informace o cenách najdete v tématu [Azure Cache for Redis rezervované kapacity nabídky](https://azure.microsoft.com/pricing/details/cache).

Rezervovanou kapacitu Azure Cache for Redis si můžete zakoupit na [webu Azure Portal](https://portal.azure.com/). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň pro jedno předplatné Enterprise nebo individuální předplatné s průběžnými platbami.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Nebo pokud je toto nastavení zakázané, musíte být správcem EA v předplatném.
* V programu Zprostředkovatel cloudových řešení (CSP) mohou azure cache pro redis rezervovat pouze agenti správce nebo prodejci.

Podrobnosti o tom, jak se podnikovým zákazníkům a zákazníkům s průběžným inami s průběžným platbami účtují poplatky za nákupy rezervací, najdete v [tématu vysvětlení využití rezervací Azure pro registraci enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a [vysvětlení využití rezervací Azure pro vaše předplatné s průběžným platbou](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-cache-size-before-purchase"></a>Určení správné velikosti mezipaměti před nákupem

Velikost rezervace by měla být založena na celkovém množství výpočetních prostředků používaných existující mezipamětí nebo brzy nasazené mezipaměti v rámci určité oblasti a pomocí stejné úrovně služby.

Předpokládejme například, že používáte jeden obecný účel, Gen5 – 32 virtuálních jader mezipaměti a dvě optimalizované paměti, Gen5 – 16 virtuálních jader mezipamětí. Dále předpokládáme, že plánujete nasadit během příštího měsíce další obecný účel, Gen5 – 32 virtuálních jader databázový server a jednu optimalizovanou paměť, Gen5 – 16 virtuálních jader databázový server. Předpokládejme, že víte, že budete potřebovat tyto zdroje po dobu nejméně 1 roku. V takovém případě byste si měli zakoupit 64 (2x32) virtuálních jader, 1 rok rezervace pro jednu databázi obecnéúčely - Gen5 a 48 (2x16 + 16) vCore 1 rok rezervace pro jednu databázi paměti optimalizované - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Zakoupení vyhrazené kapacity Azure Cache pro redis

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Vyberte **všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a potom v podokně Rezervace nákupu vyberte **Azure Cache for Redis** a zakoupíte novou rezervaci pro vaše mezipaměti.
4. Vyplňte požadovaná pole. Existující nebo nové databáze, které odpovídají vybraným atributům, mají nárok na získání slevy na rezervovanou kapacitu. Skutečný počet instancí Azure Cache for Redis, které získají slevu, závisí na vybraném oboru a množství.


![Přehled rezervovaných cen](media/cache-reserved-pricing/cache-reserved-price.png)


Následující tabulka popisuje povinná pole.

| Pole | Popis |
| :------------ | :------- |
| Předplatné   | Předplatné slouží k platbě za Azure Cache pro rezervaci rezervované kapacity Redis. Za způsob platby v předplatném se účtují počáteční náklady na rezervaci rezervované kapacity Azure Cache for Redis. Typ předplatného musí být smlouva enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo individuální smlouva s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U individuálního předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury v rámci předplatného.
| Rozsah | Obor rezervace může zahrnovat jedno předplatné nebo více předplatných (sdílený obor). Pokud vyberete: </br></br> **Sdílená**sleva na rezervaci se použije na instance Azure Cache for Redis spuštěné ve všech předplatných v rámci vašeho fakturačního kontextu. Pro podnikové zákazníky je sdílený obor registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.</br></br> **Jedno předplatné**, sleva rezervace se použije na Azure Cache pro redis instance v tomto předplatném. </br></br> **Jedna skupina prostředků**, sleva rezervace se použije na Azure Cache pro redis instance ve vybraném předplatném a vybrané skupiny prostředků v rámci tohoto předplatného.
| Region (Oblast) | Oblast Azure, která je pokryta Azure Cache pro redis rezervace rezervované kapacity.
| Cenová úroveň | Úroveň služby pro azure mezipaměť pro servery Redis.
| Označení | Jeden rok nebo tři roky
| Množství | Množství výpočetních prostředků nakupovaných v rámci Azure Cache pro rezervaci rezervované kapacity Redis. Množství je počet mezipamětí ve vybrané oblasti Azure a úrovně služeb, které jsou rezervované a získají fakturační slevu. Pokud například spouštěte nebo plánujete spouštět servery Azure Cache for Redis s celkovou kapacitou mezipaměti 26 GB v oblasti USA – východ, určíte množství jako 26, abyste maximalizovali přínos pro všechny mezipaměti.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flexibilita velikosti mezipaměti

Flexibilita velikosti mezipaměti vám pomůže vertikálně navýšit nebo snížit kapacitu v rámci úrovně služby a oblasti, aniž by došlo ke ztrátě výhod rezervované kapacity.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervace se automaticky použije na instance Azure Cache for Redis, které odpovídají oboru rezervace a atributům. Rozsah rezervace můžete aktualizovat prostřednictvím portálu Azure, PowerShellu, rozhraní API Azure nebo rozhraní API.

*  Informace o tom, jak se na Azure Cache for Redis vztahují slevy na rezervovanou kapacitu, najdete [v tématu Principy slevy na rezervaci Azure.](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Další informace o rezervacích Azure najdete v následujících článcích:

    * [Co jsou rezervace Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Vysvětlení slev za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Vysvětlení využití rezervací u předplatného s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Vysvětlení využití rezervací u smlouvy Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)


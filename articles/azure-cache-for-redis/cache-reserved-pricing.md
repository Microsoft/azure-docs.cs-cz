---
title: Platba za výpočetní prostředky s využitím rezervované kapacity – Azure cache pro Redis
description: Platba za Azure cache pro výpočetní prostředky Redis s využitím rezervované kapacity
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77530299"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Platba za Azure cache pro výpočetní prostředky Redis s využitím rezervované kapacity

Azure cache for Redis nyní pomáhá ušetřit peníze tím, že se předplatí pro výpočetní prostředky v porovnání s cenami průběžných plateb. Díky službě Azure cache pro Redis rezervovanou kapacitu uděláte předem závazek na mezipaměť po dobu jednoho nebo tří let, abyste dosáhli výrazné slevy za výpočetní náklady. Pokud si chcete koupit mezipaměť Azure pro Redis rezervovanou kapacitu, musíte zadat oblast Azure, úroveň služby a termín.

Nemusíte přiřazovat rezervaci ke konkrétní službě Azure cache pro instance Redis. Už se spuštěná mezipaměť Azure pro Redis nebo nově nasazená, automaticky získá výhody rezervovaných cen až do rezervované velikosti mezipaměti. Když si koupíte rezervaci, platíte za výpočetní náklady po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, účtují se poplatky za výpočetní služby Azure cache pro Redis, které odpovídají atributům rezervace, už nebudou účtovány podle tarifů průběžných plateb. Rezervace nepokrývá sítě ani poplatky za úložiště spojené s mezipamětí. Na konci rezervovaného období vyprší platnost fakturačního přínosu a mezipaměť Azure pro Redis se účtuje podle ceny za průběžné platby. Rezervace se neobnoví automaticky. Informace o cenách najdete v tématu [Nabídka Azure cache pro Redis rezervovanou kapacitu](https://azure.microsoft.com/pricing/details/cache).

Mezipaměť Azure můžete koupit pro Redis rezervovanou kapacitu v [Azure Portal](https://portal.azure.com/). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
* U předplatných se smlouvou Enterprise musí být na webu **EA Portal** povolená možnost [Přidat rezervované instance](https://ea.azure.com/). Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* Pro program CSP (Cloud Solution Provider) si můžou koupit mezipaměť Azure pro rezervovanou kapacitu Redis jenom agenti správce nebo prodejní agenti.

Podrobnosti o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématech [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a [pochopení využití rezervací Azure pro vaše předplatné](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)s průběžnými platbami.


## <a name="determine-the-right-cache-size-before-purchase"></a>Určení správné velikosti mezipaměti před nákupem

Velikost rezervace by měla být založená na celkovém počtu výpočtů používaných stávající nebo již nasazenou mezipamětí v konkrétní oblasti a pomocí stejné úrovně služeb.

Předpokládejme například, že máte spuštěný jeden pro obecné účely, Gen5 – 32 vCore cache a dvě paměťově optimalizované mezipaměti Gen5 – 16 vCore mezipaměti. V dalším měsíci byste si měli naplánovat nasazení za další měsíc, což je další obecné účely, Gen5 – 32 vCore databázového serveru a jedna paměťově optimalizovaná, Gen5 – 16 vCore databázový server. Dejme tomu, že víte, že tyto prostředky budete potřebovat aspoň 1 rok. V takovém případě byste si měli koupit 64 (2x32) virtuální jádra, rezervaci 1 roku pro izolovanou databázi pro obecné účely – Gen5 a 48 (2x16 + 16) pro paměť s izolovanými databázemi – Vcore.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Koupit mezipaměť Azure pro Redis rezervovanou kapacitu

1. Přihlaste se k webu [Portál Azure](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a pak v podokně rezervace nákupu vyberte **Azure cache pro Redis** , abyste si mohli koupit novou rezervaci pro vaše mezipaměti.
4. Vyplňte požadovaná pole. Stávající nebo nové databáze, které odpovídají atributům, které vyberete, mají nárok na získání rezervované slevy kapacity. Skutečný počet instancí Azure cache pro instance Redis, které obdrží slevu, závisí na zvoleném rozsahu a množství.


![Přehled rezervovaných cen](media/cache-reserved-pricing/cache-reserved-price.png)


V následující tabulce jsou popsána povinná pole.

| Pole | Popis |
| :------------ | :------- |
| Předplatné   | Předplatné použité pro platbu za mezipaměť Azure pro rezervaci rezervované kapacity Redis Platební metodou předplatného se účtují náklady na front-end v mezipaměti Azure pro rezervaci rezervované kapacity Redis. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.
| Obor | Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: </br></br> **Shared**, na službu Azure cache pro instance Redis spuštěné v rámci vašeho fakturačního kontextu se použije sleva rezervace. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.</br></br> **Jedno předplatné**– pro instance Redis v tomto předplatném se pro instance služby Azure Cache používá sleva rezervace. </br></br> **Jedna skupina prostředků**– pro instance Redis ve vybraném předplatném a vybrané skupině prostředků v rámci tohoto předplatného se použije sleva rezervované pro Azure cache.
| Oblast | Oblast Azure, která je pokrytá mezipamětí Azure pro rezervaci rezervované kapacity Redis
| Cenová úroveň | Úroveň služby pro servery Azure cache pro Redis.
| Období | Jeden rok nebo tři roky
| Množství | Množství výpočetních prostředků zakoupených v mezipaměti Azure pro rezervaci rezervované kapacity Redis. Množství je počet mezipamětí ve vybrané oblasti Azure a úrovni služby, které jsou rezervované, a obdrží fakturační slevu. Pokud například používáte nebo plánujete spustit mezipaměť Azure pro servery Redis s celkovou kapacitou mezipaměti 26 GB v oblasti Východní USA, pak byste zadali počet 26 pro maximalizaci výhod všech mezipamětí.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flexibilita velikosti mezipaměti

Flexibilita velikosti mezipaměti usnadňuje horizontální navýšení nebo snížení kapacity v rámci úrovně služeb a oblasti, aniž by došlo ke ztrátě zvýhodněné vyhrazené kapacity.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervace se automaticky aplikuje do mezipaměti Azure pro instance Redis, které odpovídají oboru rezervace a atributům. Rozsah rezervace můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo rozhraní API.

*  Informace o tom, jak se na Azure cache pro Redis aplikují zlevněné slevy, najdete v tématu [vysvětlení slevy za rezervované Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) .

* Další informace o rezervacích Azure najdete v následujících článcích:

    * [Co jsou rezervace Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Vysvětlení slev za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Vysvětlení využití rezervací u předplatného s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Vysvětlení využití rezervací u smlouvy Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)


---
title: Ušetřete náklady
description: Zjistěte, jak zakoupit rezervovanou kapacitu Azure SQL Database, abyste ušetřili na výpočetních nákladech.
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
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979992"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Ušetřete náklady na výpočetní prostředky databáze SQL s rezervovanou kapacitou databáze Azure SQL

Ušetřete peníze s Azure SQL Database tím, že se zavážete k rezervaci výpočetních prostředků ve srovnání s průběžnými platbami. S rezervovanou kapacitou Azure SQL Database se zavazujete k použití sql database po dobu jednoho nebo tří let, abyste získali výraznou slevu na výpočetní náklady. Chcete-li zakoupit rezervovanou kapacitu databáze SQL, musíte zadat oblast Azure, typ nasazení, úroveň výkonu a termín.


Není nutné přiřadit rezervaci konkrétní instance databáze SQL (jednotlivé databáze, elastické fondy nebo spravované instance). Odpovídající instance databáze SQL, které jsou již spuštěny, nebo ty, které jsou nově nasazeny, automaticky získají výhodu. Zakoupením rezervace se zavazujete k využití výpočetních nákladů po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, sql database výpočetní poplatky, které odpovídají atributy rezervace již nejsou účtovány na průběžných průběžných sazeb. Rezervace se nevztahuje na poplatky za software, sítě nebo úložiště spojené s instancí databáze SQL. Na konci období rezervace vyprší platnost fakturační výhody a sql databáze se účtují za průběžnou platbu. Rezervace se neobnovují automaticky. Informace o cenách naleznete v tématu [sql database rezervovaná kapacita nabízí](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Rezervovanou kapacitu Azure SQL Database můžete zakoupit na [webu Azure Portal](https://portal.azure.com). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../cost-management-billing/reservations/monthly-payments-reservations.md). Zakoupení rezervované kapacity databáze SQL:

- Musíte být v roli vlastníka alespoň pro jedno předplatné Enterprise nebo individuální předplatné s průběžnými platbami.
- U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com) povolená možnost **Přidat rezervované instance**. Nebo pokud je toto nastavení zakázané, musíte být správcem EA v předplatném.
- Pro program Zprostředkovatel cloudových řešení (CSP) mohou pouze agenti správce nebo prodejci zakoupit rezervovanou kapacitu databáze SQL.

Podrobnosti o tom, jak se podnikovým zákazníkům a zákazníkům s průběžným implacem účtují poplatky za nákupy rezervací, najdete [v tématu vysvětlení využití rezervací Azure pro registraci enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) a [vysvětlení využití rezervací Azure pro vaše předplatné s průběžným platbou](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Určení správné velikosti SQL před nákupem

Velikost rezervace by měla být založena na celkovém množství výpočetních prostředků používaných existujícími nebo brzy nasazenými jednotlivými databázemi, elastické fondy nebo spravované instance v rámci určité oblasti a pomocí stejné úrovně výkonu a generování hardwaru.

Předpokládejme například, že používáte jeden obecný účel, gen5 – 16 virtuálních jader elastického fondu a dvě důležité obchodní, Gen5 – 4 virtuální jádra jediné databáze. Dále předpokládáme, že plánujete nasadit během příštího měsíce další obecný účel, Gen5 – 16 virtuálních jader elastického fondu a jednoho důležitého obchodního, gen5 – 32 virtuálních jader elastického fondu. Také předpokládejme, že víte, že budete potřebovat tyto zdroje po dobu nejméně 1 roku. V takovém případě byste si měli zakoupit 32 (2x16) virtuálních jader, 1 rok rezervace pro jednu databázi/elastický fond obecnéúčely - Gen5 a 40 (2x4 + 32) vCore 1 rok rezervace pro jednu databázi / elastický fond podnikání kritické - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Nákup záložní kapacity služby Azure SQL Database

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a potom v podokně Nákupní rezervace vyberte **SQL Database** a zakupte novou rezervaci pro databázi SQL.
4. Vyplňte požadovaná pole. Existující nebo nové jednotlivé databáze, elastické fondy nebo spravované instance, které odpovídají vybraným atributům, mají nárok na získání slevy na rezervovanou kapacitu. Skutečný počet instancí databáze SQL, které získají slevu, závisí na vybraném oboru a množství.
    ![Snímek obrazovky před odesláním nákupu rezervované kapacity databáze SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

Následující tabulka popisuje povinná pole.

| Pole      | Popis|
|------------|--------------|
|Předplatné|Předplatné slouží k platbě za rezervaci rezervované kapacity databáze SQL. Způsob platby na předplatné se účtuje předem náklady na rezervaci rezervované kapacity SQL Database. Typ předplatného musí být smlouva enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo individuální smlouva s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U individuálního předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury v rámci předplatného.|
|Rozsah       |Obor rezervace virtuálních jader může zahrnovat jedno předplatné nebo více předplatných (sdílený obor). Pokud vyberete: <br/><br/>**Sdílená**sleva na rezervaci virtuálních jader se použije na instance databáze SQL spuštěné ve všech předplatných v rámci fakturačního kontextu. Pro podnikové zákazníky je sdílený obor registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu.<br/><br/>**Jedno předplatné**, sleva rezervace virtuálních jader se použije na instance databáze SQL v tomto předplatném. <br/><br/>**Jedna skupina prostředků**, sleva rezervace se použije na instance databáze SQL ve vybraném předplatném a vybrané skupině prostředků v rámci tohoto předplatného.|
|Region (Oblast)      |Oblast Azure, která je pokryta rezervací rezervované kapacity databáze SQL.|
|Typ nasazení|Typ prostředku SQL, pro který chcete rezervovat.|
|Úroveň výkonu|Úroveň služby pro instance databáze SQL.
|Označení        |Jeden rok nebo tři roky.|
|Množství    |Množství výpočetních prostředků nakupovaných v rámci rezervace rezervované kapacity databáze SQL. Množství je počet virtuálních jader ve vybrané oblasti Azure a úroveň výkonu, které jsou rezervované a získají fakturační slevu. Například pokud spouštěte nebo plánujete spustit instance databáze SQL s celkovou výpočetní kapacitou virtuálních jader Gen5 16 v oblasti USA – východ, určíte množství jako 16, abyste maximalizovali přínos pro všechny instance. |

1. Zkontrolujte náklady na rezervaci rezervované kapacity databáze SQL v části **Náklady.**
1. Vyberte **Koupit**.
1. Chcete-li zobrazit stav nákupu, vyberte možnost **Zobrazit tuto rezervaci.**

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti virtuálních jader

Flexibilita velikosti virtuálních jader vám pomůže vertikálně navýšit nebo snížit kapacitu v rámci úrovně výkonu a oblasti, aniž byste ztratili výhodu rezervované kapacity. Rezervovaná kapacita sql database také poskytuje flexibilitu pro dočasné přesunutí horkých databází mezi fondy a jednotlivými databázemi jako součást běžných operací (v rámci stejné oblasti a úrovně výkonu) bez ztráty rezervované kapacity Prospěch. Udržováním nepoužité vyrovnávací paměti v rezervaci můžete efektivně spravovat špičky výkonu bez překročení rozpočtu.

## <a name="limitation"></a>Omezení

Nelze rezervovat databáze SQL založené na DTU (základní, standardní nebo prémiové).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervace virtuálního jádra se automaticky použije na počet instancí databáze SQL Database, které odpovídají oboru rezervace rezervované kapacity sql database a atributům. Rozsah rezervace rezervované kapacity databáze SQL můžete aktualizovat prostřednictvím [portálu Azure](https://portal.azure.com)Portal , PowerShellu, rozhraní PŘÍKAZOVÉHO PŘÍKAZOVÉHO PŘÍKAZU nebo prostřednictvím rozhraní API.

Informace o tom, jak spravovat rezervaci rezervované kapacity databáze SQL, naleznete v [tématu správa rezervované kapacity databáze SQL](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Správa rezervací Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Vysvětlení slev za rezervace Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

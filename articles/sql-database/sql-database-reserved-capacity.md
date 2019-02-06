---
title: Předplatíte virtuálních jader pro Azure SQL Database ještě ušetříte peníze | Dokumentace Microsoftu
description: Zjistěte, jak zakoupit kapacitu Azure SQL Database, která je vyhrazena a Ušetřete na svých výpočetních nákladech.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 8f8884f903108deae673d030f8fd2ee1d0d9f982
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745448"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Předem za výpočetní prostředky, SQL Database s Azure SQL Database vyhrazené kapacity

Ušetřete peníze s Azure SQL Database pomocí se rozhodnete pro výpočetní prostředky Azure SQL Database ve srovnání s cenami průběžných plateb. Azure SQL Database vyhrazené kapacitě provedete počátečního závazku využívání služeb v databázi SQL po dobu jeden nebo tři roky získat výraznou slevu na náklady na výpočetní výkon. Pokud chcete zakoupit kapacitu databáze SQL vyhrazený, budete muset určit oblast Azure, typ nasazení, úroveň výkonu a délku smlouvy. 

Nemusíte přiřazovat rezervace instancí SQL Database. Odpovídající instancí SQL Database, které už jsou spuštěné nebo ty, které jsou nově nasazené, bude automaticky budete mít k dispozici. Koupit rezervaci, jste platíte předem za náklady na výpočty pro SQL Database instance po dobu jeden nebo tři roky. Poté, co můžete koupit rezervaci, SQL Database, poplatky za výpočetní kapacitu, které odpovídají atributy rezervace už se účtují platit jako budete přejít sazby. Rezervace nezahrnuje poplatky za software, sítě nebo úložiště přidružené k instanci služby SQL Database. Na konci období rezervace vyprší platnost fakturační výhoda a SQL Database se účtují cenu platit jako můžete přejít. Rezervace není automatického obnovení. Informace o cenách najdete v článku [SQL Database vyhrazené kapacity nabídky](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Můžete si koupit Azure SQL Database vyhrazené kapacitě [webu Azure portal](https://portal.azure.com). K nákupu SQL Database rezervované kapacity:

- Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise **přidat Reserved Instances** musí být povolená v [portál EA](https://ea.azure.com). Nebo, pokud je toto nastavení zakázané, musíte být správce EA na předplatné.
- Programu Cloud Solution Provider (CSP) můžou nakupovat jenom správce agentů nebo prodejní agentů SQL Database vyhrazené kapacity.

Viz podrobnosti o jak podnikovým zákazníkům a Zákazníci s průběžnými platbami se vám účtovat nákup rezervace [využití rezervace pochopit Azure u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md) a [rezervaci pochopit Azure použití předplatného s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Určení správné velikosti SQL před nákupem

Velikost rezervace mají zakládat na celkový objem výpočtů používá existující nebo brzy do--nasazen jedné databáze a elastických fondů v rámci určité oblasti a pomocí stejného výkonu vrstvy a hardware generování. 

Například předpokládejme, že používáte jeden obecné účely Gen5 – 16 elastický fond – VCORE úrovně a dvě důležité obchodní informace, Gen5 – 4 vCore izolované databáze. Dále můžeme předpokládaný, že máte v úmyslu nasadit v rámci příští měsíc další obecné účely Gen5 – 16 elastický fond – vCore a jeden pro důležité obchodní informace, Gen5 – 32 elastický fond – vCore. Také Předpokládejme, že máte jistotu, že budete potřebovat tyto prostředky alespoň 1 rok. V tomto případě byste si zakoupit 32 virtuálních jader (2 × 16), rezervace na 1 rok pro SQL Database samostatnou/elastického fondu pro obecné účely – Compute Gen5 a 40 (2 × 4 + 32) – VCORE úrovně 1 rok rezervace pro samostatné databáze SQL/elastické fondu pro důležité obchodní informace – Compute Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Nákup kapacity databáze SQL vyhrazený

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte **přidat** a potom vyberte v podokně Vybrat typ produktu **SQL Database** koupit novou rezervaci pro službu SQL Database.
4. Vyplňte požadovaná pole. Existující nebo nové izolované databáze nebo elastického fondu, které odpovídají atributy, které jste vybrali oprávněni získat slevu záložní kapacitu. Skutečný počet instancí SQL Database, které získávají slevy závisí na rozsah a množství vybranou.

   ![Snímek obrazovky před odesláním databáze SQL vyhrazený nákup kapacity](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název této rezervace.| 
    |Předplatné|Předplatné použité k úhradě rezervaci kapacity databáze SQL vyhrazený. Způsob platby v rámci předplatného účtuje pořizovací náklady pro rezervaci kapacity databáze SQL vyhrazený. Tento typ předplatného musí být smlouvu enterprise agreement (nabízejí čísla: MS-AZR-0017P nebo MS-AZR - 0148 P) nebo s průběžnými platbami (nabízejí čísla: MS-AZR-0003P nebo MS-AZR - 0023 P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.|    
    |Rozsah       |Obor rezervace – VCORE úrovně mohou vztahovat na jedno předplatné jeden nebo více odběrů (sdíleném oboru). Pokud vyberete: <ul><li>Jedno předplatné – sleva za rezervaci vCore se použije pro instance databáze SQL v tomto předplatném. </li><li>Na úrovni Shared – sleva za rezervaci vCore platí pro instance SQL Database běžící v rámci kontextu vaší fakturace žádné předplatné. Pro podnikové zákazníky sdílený obor je registrace a obsahuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.</li></ul>|
    |Oblast      |Oblast Azure, která je předmětem databáze SQL vyhrazený rezervaci kapacity.|    
    |Typ nasazení|Typ prostředku SQL, který chcete koupit rezervaci pro.|
    |Úroveň výkonu|Pro instance SQL Database na úrovni služby.
    |Označení        |Jeden nebo tři roky.|
    |Množství    |Počet instancí se zakoupených v rámci databáze SQL vyhrazený rezervaci kapacity. Počet spuštěných instancí SQL Database, které můžete získat slevu fakturace je množství. Například pokud spustíte 10 instancí SQL Database v oblasti východní USA, potom zadáte množství jako 10 a maximalizovat pro všechny počítače spuštěný. |

5. Kontrola náklady na SQL Database vyhrazené kapacity rezervace v **náklady** oddílu.
6. Vyberte **Koupit**.
7. Vyberte **zobrazit tuto rezervaci** chcete podívat na stav vašeho nákupu.

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Potřebujete-li zrušit databáze SQL vyhrazený rezervaci kapacity, může se jednat 12 % poplatek za předčasné ukončení. Výše vrácených peněz vychází z vaší kupní ceny nebo aktuální ceny rezervace podle toho, která hodnota je nižší. Výše vrácených peněz je omezená na 50 000 USD za rok. Výše vrácených peněz, které obdržíte, je zbývající poměrný zůstatek minus poplatek za předčasné ukončení ve výši 12 %. Pokud chcete požádat o zrušení, přejděte na rezervaci v Azure portal a vyberte **náhrada** vytvořit žádost o podporu.

Pokud potřebujete u své rezervace záložní kapacity SQL Database změnit oblast, typ nasazení, úroveň výkonu nebo období, můžete ji vyměnit za jinou rezervaci stejné nebo vyšší hodnoty. Počáteční datum období nové rezervace se z vyměněné rezervace nepřenáší. 1 nebo 3leté období začne okamžikem vytvoření nové rezervace. Žádost o exchange, přejděte na rezervaci na webu Azure Portal a vyberte **Exchange** vytvořit žádost o podporu.

## <a name="vcore-size-flexibility"></a>vCore velikost flexibilitu

vCore velikost flexibilitu umožňuje škálovat nahoru nebo dolů v rámci úroveň výkonu a oblasti, bez ztráty výhody plynoucí záložní kapacitu. SQL Database vyhrazené kapacity také poskytuje flexibilitu dočasně přesunout databáze z horké mezi fondy a izolované databáze v rámci běžných operací (v rámci stejné oblasti a výkonu vrstvy) bez ztráty záložní kapacitu Využijte výhod. Udržováním vyrovnávací paměť zrušením použité ve vaší rezervace můžete efektivně spravovat provozní špičky výkonu bez překročení vašemu rozpočtu.

## <a name="next-steps"></a>Další postup

Sleva za rezervaci – VCORE úrovně se automaticky použije na počet instancí SQL Database, které odpovídají obor rezervace SQL Database vyhrazené kapacity a atributy. Můžete aktualizovat rozsah rezervace kapacitu databáze SQL vyhrazený prostřednictvím [webu Azure portal](https://portal.azure.com), prostředí PowerShell, rozhraní příkazového řádku nebo prostřednictvím rozhraní API. 

Další informace o pro správu databáze SQL vyhrazený rezervaci kapacity najdete v tématu [Správa služby SQL Database rezervované kapacity](../billing/billing-manage-reserved-vm-instance.md).

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](../billing/billing-save-compute-costs-reservations.md)
- [Správa rezervací Azure](../billing/billing-manage-reserved-vm-instance.md)
- [Vysvětlení, že slevách na využití platformy Azure rezervace](../billing/billing-understand-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).


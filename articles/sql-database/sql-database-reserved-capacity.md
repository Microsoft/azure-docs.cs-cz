---
title: Předplatíte virtuálních jader pro Azure SQL Database ještě ušetříte peníze | Dokumentace Microsoftu
description: Zjistěte, jak zakoupit kapacitu Azure SQL Database, která je vyhrazena a Ušetřete na svých výpočetních nákladech.
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: carlrab
ms.openlocfilehash: 59ec4b109c01a027ce440d3f7749d69af3351571
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037707"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Předem za výpočetní prostředky, SQL Database s Azure SQL Database vyhrazené kapacity

Ušetřete peníze s Azure SQL Database pomocí se rozhodnete pro výpočetní prostředky Azure SQL Database ve srovnání s cenami průběžných plateb. Azure SQL Database vyhrazené kapacitě provedete počátečního závazku využívání služeb v databázi SQL po dobu jeden nebo tři roky získat výraznou slevu na náklady na výpočetní výkon. Pokud chcete zakoupit kapacitu databáze SQL vyhrazený, budete muset určit oblast Azure, typ prostředku, úroveň služby a délku smlouvy. 

Nemusíte přiřazovat rezervace instancí SQL Database. Odpovídající instancí SQL Database, které už jsou spuštěné nebo ty, které jsou nově nasazené, bude automaticky budete mít k dispozici. Koupit rezervaci, jste platíte předem za náklady na výpočty pro SQL Database instance po dobu jeden nebo tři roky. Poté, co můžete koupit rezervaci, SQL Database, poplatky za výpočetní kapacitu, které odpovídají atributy rezervace už se účtují platit jako budete přejít sazby. Rezervace nezahrnuje poplatky za software, sítě nebo úložiště přidružené k instanci služby SQL Database. Na konci období rezervace vyprší platnost fakturační výhoda a SQL Database se účtují cenu platit jako můžete přejít. Rezervace není automatického obnovení. Informace o cenách najdete v článku [SQL Database vyhrazené kapacity nabídky](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Můžete si koupit Azure SQL Database vyhrazené kapacitě [webu Azure portal](https://portal.azure.com). K nákupu SQL Database rezervované kapacity:
- Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise, musí být povoleno nákupy na Azure rezervace v [portál EA](https://ea.azure.com).
-  Programu Cloud Solution Provider (CSP) můžou nakupovat jenom správce agentů nebo prodejní agentů SQL Database vyhrazené kapacity.

Viz podrobnosti o jak podnikovým zákazníkům a Zákazníci s průběžnými platbami se vám účtovat nákup rezervace [využití rezervace pochopit Azure u prováděcí smlouvy Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md) a [rezervaci pochopit Azure použití předplatného s průběžnými platbami](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Určení správné velikosti SQL před nákupem

Velikost rezervace by měla být založena na celkový objem výpočtů používá SQL existující nebo brzy na--nasadit jeden databází nebo elastických fondů v rámci určité oblasti a pomocí stejného vytvoření vrstvy a hardwaru služby. 

Například předpokládejme, že používáte jeden obecné účely Gen5 – 16 elastický fond – VCORE úrovně a dvě důležité obchodní informace, Gen5 – 4 vCore izolované databáze. Dále můžeme předpokládaný, že máte v úmyslu nasadit v rámci příští měsíc další obecné účely Gen5 – 16 elastický fond – vCore a jeden pro důležité obchodní informace, Gen5 – 32 elastický fond – vCore. Také Předpokládejme, že máte jistotu, že budete potřebovat tyto prostředky alespoň 1 rok. V tomto případě byste si zakoupit 32 virtuálních jader (2 × 16), rezervace na 1 rok pro SQL Database jedním/elastického fondu pro obecné účely – Compute Gen5 a 40 (2 × 4 + 32) vCore rezervaci 1 rok pro SQL databáze Single/elastického fondu pro důležité obchodní informace – Compute Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Nákup kapacity databáze SQL vyhrazený

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte **přidat** a potom vyberte v podokně Vybrat typ produktu **SQL Database** koupit novou rezervaci pro službu SQL Database.
4. Vyplňte požadovaná pole. Existující nebo nové izolované databáze nebo elastického fondu, které odpovídají atributy, které jste vybrali oprávněni získat slevu záložní kapacitu. Skutečný počet instancí SQL Database, které získávají slevy závisí na rozsah a množství vybranou.

   ![Snímek obrazovky před odesláním databáze SQL vyhrazený nákup kapacity](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název této rezervace.| 
    |Předplatné|Předplatné použité k úhradě rezervaci kapacity databáze SQL vyhrazený. Způsob platby v rámci předplatného účtuje pořizovací náklady pro rezervaci kapacity databáze SQL vyhrazený. Musí se jednat o předplatné se smlouvou Enterprise (číslo nabídky: MS-AZR-0017P) nebo předplatné s průběžnými platbami (číslo nabídky: MS-AZR-0003P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.|    
    |Rozsah       |Obor rezervace – VCORE úrovně mohou vztahovat na jedno předplatné jeden nebo více odběrů (sdíleném oboru). Pokud vyberete: <ul><li>Jedno předplatné – sleva za rezervaci vCore se použije pro instance databáze SQL v tomto předplatném. </li><li>Na úrovni Shared – sleva za rezervaci vCore platí pro instance SQL Database běžící v rámci kontextu vaší fakturace žádné předplatné. Pro podnikové zákazníky sdílený obor je registraci a zahrnuje všechna předplatná (s výjimkou předplatná pro vývoj/testování) během registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.</li></ul>|
    |Oblast      |Oblast Azure, která je předmětem databáze SQL vyhrazený rezervaci kapacity.|    
    |Typ prostředku|Typ prostředku SQL, který chcete koupit rezervaci pro.|
    |Úroveň služby|Pro instance SQL Database na úrovni služby.
    |Označení        |Jeden nebo tři roky.|
    |Množství    |Počet instancí se zakoupených v rámci databáze SQL vyhrazený rezervaci kapacity. Počet spuštěných instancí SQL Database, které můžete získat slevu fakturace je množství. Například pokud spustíte 10 instancí SQL Database v oblasti východní USA, potom zadáte množství jako 10 a maximalizovat pro všechny počítače spuštěný. |

5. Kontrola náklady na SQL Database vyhrazené kapacity rezervace v **náklady** oddílu.
6. Vyberte **Koupit**.
7. Vyberte **zobrazit tuto rezervaci** chcete podívat na stav vašeho nákupu.

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Potřebujete-li zrušit databáze SQL vyhrazený rezervaci kapacity, může se jednat 12 % poplatek za předčasné ukončení. Náhrady jsou založeny na nejnižší cenu ceny nákupu nebo aktuální cena rezervace. Náhrady jsou omezené na 50 000 dolarů za rok. Náhrady, který jste dostali se zbývající poměrný zůstatek minus poplatek za předčasné ukončení 12 %. Pokud chcete požádat o zrušení, přejděte na rezervaci v Azure portal a vyberte **náhrada** vytvořit žádost o podporu.

Pokud potřebujete změnit vaší rezervace SQL Database vyhrazené kapacity do jiné oblasti, typ prostředku, úroveň služby nebo termín, můžete ji exchange pro jiné rezervace, který je roven nebo větší hodnotu. Počáteční datum termín pro novou rezervaci nepřenáší z výměně rezervace. Začíná při vytváření nového rezervace 1 nebo 3 roky. Žádost o exchange, přejděte na rezervaci na webu Azure Portal a vyberte **Exchange** vytvořit žádost o podporu.

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

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.


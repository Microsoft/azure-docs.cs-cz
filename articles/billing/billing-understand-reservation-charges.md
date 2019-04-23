---
title: Vysvětlení rezervace slevy pro Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak sleva za rezervaci se použije ke spouštění databází Azure SQL Database.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 4b4c6b390e9b3a0cf764f998523fe3c1cdc66026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370283"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Jak se sleva za rezervaci použije k Azure SQL Database.

Po nákupu Azure SQL Database vyhrazené kapacity sleva za rezervaci se automaticky využije na databáze SQL, které odpovídají atributy a množství rezervace. Rezervace pokrývá náklady na výpočty pro SQL Database. Software, úložiště a sítě vám budeme účtovat za běžné sazby. Může zahrnovat náklady na licencování pro SQL Database s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Rezervované instance virtuálních počítačů najdete v části [slevy pochopit Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva ve výši rezervace

Sleva za rezervaci se "*použití – it nebo ztratit – it*". Ano Pokud nemáte odpovídající prostředky pro hodinu, potom ztratíte množství rezervací pro určitou hodinu. Nelze provést vpřed nevyužité vyhrazené hodin.

Při vypínání prostředek sleva za rezervaci automaticky použije další odpovídající prostředek v zadaném oboru. Pokud systém nenašel žádné odpovídající prostředky v zadaném oboru, pak jsou vyhrazené hodin *ztráty*.

## <a name="discount-applied-to-sql-databases"></a>Slevy u databází SQL

 Sleva za rezervované kapacity databáze SQL se využije ke spouštění databází SQL po hodinách. Rezervace, které můžete zakoupit odpovídá využití služby compute, protože ho vygeneroval spuštěné databáze SQL. V případě služeb SQL Database, které neběží celou hodinu, se rezervace automaticky využije pro ostatní služby SQL Database odpovídající atributům rezervace. Sleva můžete použít SQL Database. ty jsou spuštěny souběžně. Pokud nemáte k dispozici SQL Database. ty spustit pro celou hodinu odpovídající atributy rezervace, neobdržíte všech výhod sleva za rezervaci pro určitou hodinu.

Následující příklady ukazují, jak platí sleva záložní kapacitu databáze SQL v závislosti na počtu jader jste koupili, a pokud máte spuštěnou.

- Scénář 1: Můžete si koupit kapacitu databáze SQL vyhrazený pro 8 jader SQL Database. Spuštění 16 jader SQL Database, která odpovídají rest atributy rezervace. Platíte průběžnými platbami cena za 8jádrový využití služby compute SQL Database. Získáte slevy na rezervaci za jednu hodinu využití služby compute databáze SQL 8 jader.

Zbytek těchto příkladech se předpokládá, že je databáze SQL vyhrazený kapacitu, kterou si koupit za 16 jader SQL Database a zbytek atributy rezervace shodovat s provozní databází SQL.

- Scénář 2: Spustíte dvě databáze SQL s 8 jádry je jedna hodina. Sleva za rezervaci 16 jader se použije k výpočtu využití pro obě 8 jader databází SQL.
- Scénář 3: Spusťte jeden 16jádrový procesor, SQL Database od 13: 00 do 1:30 odp. Při spouštění další 16 jader SQL Database z 1:30 do 2 hodin. Obě jsou zahrnuté do sleva za rezervaci.
- Scénář 4: Spusťte jeden 16jádrový procesor, SQL Database od 13: 00 do 1:45 pm. Při spouštění další 16 jader SQL Database z 1:30 do 2 hodin. Vám budou účtovány průběžných platbách za překryv 15 minut. Sleva za rezervaci se vztahuje na výpočetní využití pro zbytek času.

K pochopení a zobrazení aplikace vaše rezervace Azure na fakturaci využití sestav, naleznete v tématu [pochopit Azure rezervace využití](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervace pro předplatná CSP](/partner-center/azure-reservations)

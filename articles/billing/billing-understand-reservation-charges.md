---
title: Vysvětlení sleva za rezervaci Azure | Dokumentace Microsoftu
description: Zjistěte, jak sleva za rezervaci se použije ke spuštění databáze SQL.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 3a5339200309a50eace5b48dfbd9127b7eb1dae6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39634075"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Vysvětlení, používání sleva za rezervaci Azure do databáze SQL

Po nákupu Azure SQL Database vyhrazené kapacity sleva za rezervaci se automaticky využije na databáze SQL, které odpovídají atributy a množství rezervace. Rezervace pokrývá náklady na výpočty pro SQL Database. Software, úložiště a sítě vám budeme účtovat za běžné sazby. Může zahrnovat náklady na licencování pro SQL Database s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Rezervované instance virtuálních počítačů najdete v části [slevy pochopit Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Sleva za rezervaci u databází SQL

 Sleva za rezervované kapacity databáze SQL se využije ke spouštění databází SQL po hodinách. Rezervace, které můžete zakoupit odpovídá využití služby compute, protože ho vygeneroval spuštěné databáze SQL. Pro SQL Database. ty nespouštět celé hodiny se má rezervace použít automaticky s jinými databázemi SQL shodné atributy rezervace. Sleva můžete použít SQL Database. ty jsou spuštěny souběžně. Pokud nemáte k dispozici SQL Database. ty spustit pro celou hodinu odpovídající atributy rezervace, neobdržíte všech výhod sleva za rezervaci pro určitou hodinu.

Následující příklady ukazují, jak platí sleva záložní kapacitu databáze SQL v závislosti na počtu jader jste koupili, a pokud máte spuštěnou.

- Scénář 1: Zakoupit kapacitu databáze SQL vyhrazený pro 8 jader SQL Database. Spuštění 16 jader SQL Database, která odpovídají rest atributy rezervace. Platíte průběžnými platbami cena za 8jádrový využití služby compute SQL Database. Získáte slevy na rezervaci za jednu hodinu využití služby compute databáze SQL 8 jader.

Zbytek těchto příkladech se předpokládá, že je databáze SQL vyhrazený kapacitu, kterou si koupit za 16 jader SQL Database a zbytek atributy rezervace shodovat s provozní databází SQL.

- Scénář 2: Spuštění dvou databází SQL s 8 jádry je jedna hodina. Sleva za rezervaci 16 jader se použije k výpočtu využití pro obě 8 jader databází SQL.
- Scénář 3: Spuštění 16jádrový procesor, SQL Database od 13: 00 do 1:30 odp. Při spouštění další 16 jader SQL Database z 1:30 do 2 hodin. Obě jsou zahrnuté do sleva za rezervaci.
- Scénář 4: Spuštění 16jádrový procesor, SQL Database od 13: 00 do 1:45 pm. Při spouštění další 16 jader SQL Database z 1:30 do 2 hodin. Vám budou účtovány průběžných platbách za překryv 15 minut. Sleva za rezervaci se vztahuje na výpočetní využití pro zbytek času.

K pochopení a zobrazení aplikace Azure rezervace na fakturaci využití sestav, naleznete v tématu [pochopit Azure rezervace využití](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Další postup

Další informace o Azure rezervace, naleznete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplatit si virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předem za výpočetní prostředky, SQL Database s Azure SQL Database vyhrazené kapacity](../sql-database/sql-database-reserved-capacity.md)
- [Správa Azure rezervace](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervace pro předplatná CSP](https://docs.microsoft.com/partner-center/azure-reservations)


## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

---
title: Chyby přechodného připojení – Azure Database for MariaDB
description: Naučte se řešit chyby přechodného připojení pro Azure Database for MariaDB.
keywords: připojení MySQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2a651d87411654f1a52c4a097f115ba848ce569c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541670"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Manipulace s přechodnými chybami připojení pro Azure Database for MariaDB

Tento článek popisuje, jak zpracovat přechodné chyby při připojování k Azure Database for MariaDB.

## <a name="transient-errors"></a>Přechodné chyby

Přechodná chyba, označovaná také jako přechodná chyba, je chyba, která se vyřeší sám. Nejčastěji tyto chyby se manifestují jako připojení k databázovému serveru, který se vynechává. Nová připojení k serveru se taky nedají otevřít. K přechodným chybám může dojít například v případě, že dojde k selhání hardwaru nebo sítě. Dalším důvodem může být nová verze služby PaaS, která se zavádí. Většina těchto událostí je systémem automaticky snížena za méně než 60 sekund. Osvědčeným postupem pro navrhování a vývoj aplikací v cloudu je očekávat přechodné chyby. Předpokládejte, že se k nim může kdykoli docházet v každé komponentě a mít k dispozici odpovídající logiku pro zpracování těchto situací.

## <a name="handling-transient-errors"></a>Zpracování přechodných chyb

Přechodné chyby by se měly zpracovat pomocí logiky opakování. Situace, které je třeba vzít v úvahu:

* Při pokusu o otevření připojení dojde k chybě.
* Nečinné připojení je na straně serveru vyřazeno. Když se pokusíte o vydání příkazu, nejde ho spustit.
* Aktivní připojení, které aktuálně provádí příkaz, je vyřazeno.

První a druhý případ jsou poměrně přímo předávány na popisovač. Pokuste se znovu otevřít připojení. Po úspěšném provedení přechodné chyby systém sníží. Azure Database for MariaDB můžete použít znovu. Doporučujeme, abyste počkali před opakováním pokusu o připojení. Pokud nedojde k selhání počátečních pokusů, je záložní. Tímto způsobem systém může použít všechny prostředky, které jsou k dispozici k překonání chybové situace. Dobrý vzor, který je potřeba provést:

* Počkejte 5 sekund před prvním opakováním.
* Pro každou z následujících možností zkuste zvětšit počkat exponenciálně až 60 sekund.
* Nastavte maximální počet opakovaných pokusů, na kterých by vaše aplikace pomohly operaci považovat za neúspěšnou.

V případě, že připojení k aktivní transakci dojde k chybě, je obtížné správně zpracovat obnovení. Existují dva případy: Pokud byla transakce určena jen pro čtení, je bezpečné znovu otevřít připojení a opakovat transakci. Pokud je však transakce také zapsána do databáze, je nutné určit, zda byla transakce vrácena zpět nebo zda byla úspěšná, než došlo k přechodné chybě. V takovém případě jste pravděpodobně neobdrželi potvrzení potvrzení z databázového serveru.

Jedním ze způsobů, jak to provést, je vygenerovat v klientovi jedinečné ID, které se používá pro všechny opakované pokusy. Toto jedinečné ID předáte jako součást transakce serveru a uložíte ji do sloupce s jedinečným omezením. Tímto způsobem lze transakci bezpečně opakovat. V případě, že předchozí transakce byla vrácena zpět a jedinečné ID generované klientem v systému ještě neexistuje, bude úspěšné. V případě, že se jedinečné ID dříve uložilo z důvodu úspěšného dokončení předchozí transakce, selže oznámení, že dojde k porušení duplicitního klíče.

Když váš program komunikuje s Azure Database for MariaDB prostřednictvím middlewaru třetí strany, požádejte dodavatele, zda middleware obsahuje logiku opakování pro přechodné chyby.

Nezapomeňte otestovat logiku opakování. Například zkuste spustit kód při vertikálním navýšení nebo snížení kapacity výpočetních prostředků Azure Database for MariaDB serveru. Vaše aplikace by měla zpracovávat krátké výpadky zjištěné během této operace bez jakýchkoli problémů.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením ke službě Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)

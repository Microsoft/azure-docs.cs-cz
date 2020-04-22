---
title: Přechodné chyby připojení – Azure Database for MariaDB
description: Zjistěte, jak zpracovat přechodné chyby připojení pro Azure Database pro MariaDB.
keywords: připojení mysql,připojovací řetězec,problémy s připojením,přechodná chyba,chyba připojení
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2d162b5123cdaabe17859863c148f6483175d1e1
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770234"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Zpracování přechodných chyb připojení pro Azure Database pro MariaDB

Tento článek popisuje, jak zpracovat přechodné chyby připojení k Azure Database pro MariaDB.

## <a name="transient-errors"></a>Přechodné chyby

Přechodná chyba, označovaná také jako přechodná chyba, je chyba, která se vyřeší sama. Většina obvykle tyto chyby manifest jako připojení k databázovému serveru je vynechána. Také nelze otevřít nová připojení k serveru. K přechodným chybám může dojít například při selhání hardwaru nebo sítě. Dalším důvodem může být nová verze služby PaaS, která je právě zaváděna. Většina těchto událostí jsou automaticky zmírněny systémem za méně než 60 sekund. Osvědčeným postupem pro navrhování a vývoj aplikací v cloudu je očekávat přechodné chyby. Předpokládejme, že může dojít v libovolné součásti kdykoli a mít příslušnou logiku na místě pro zpracování těchto situací.

## <a name="handling-transient-errors"></a>Zpracování přechodných chyb

Přechodné chyby by měly být zpracovány pomocí logiky opakování. Situace, které je třeba vzít v úvahu:

* Při pokusu o otevření připojení dojde k chybě.
* Na straně serveru je přerušeno nečinné připojení. Při pokusu o vydání příkazu nelze provést
* Aktivní připojení, které právě provádí příkaz, je vynecháno.

První a druhý případ jsou poměrně přímočaré zvládnout. Zkuste připojení otevřít znovu. Když uspějete, přechodná chyba byla zmírněna systémem. Můžete znovu použít databázi Azure pro MariaDB. Doporučujeme mít čeká před opakováním připojení. Pokud se počáteční opakování nezdaří, ustoupíte. Tímto způsobem může systém použít všechny prostředky, které jsou k dispozici k překonání chybové situace. Dobrý vzor následovat, je:

* Před prvním opakováním počkejte 5 sekund.
* Pro každý následující opakování zvýšit čekání exponenciálně, až 60 sekund.
* Nastavte maximální počet opakování, ve kterém okamžiku aplikace považuje operaci za neúspěšnou.

Pokud se nezdaří připojení s aktivní transakcí, je obtížnější správně zpracovat obnovení. Existují dva případy: Pokud transakce byla jen pro čtení v přírodě, je bezpečné znovu otevřít připojení a opakovat transakci. Pokud však transakce byla také zápis do databáze, je nutné určit, pokud transakce byla vrácena zpět, nebo pokud byla úspěšná před přechodnou chybu došlo. V takovém případě jste potvrzení potvrzení neobdrželi z databázového serveru.

Jedním ze způsobů, jak toho dosáhnout, je generovat jedinečné ID na straně klienta, který se používá pro všechny opakování. Toto jedinečné ID předáte jako součást transakce serveru a uložíte ho do sloupce s jedinečným omezením. Tímto způsobem můžete bezpečně opakovat transakci. Bude úspěšná, pokud předchozí transakce byla vrácena zpět a klient vygeneroval jedinečné ID ještě neexistuje v systému. Selhat označující duplicitní narušení klíče, pokud bylo jedinečné ID dříve uloženo, protože předchozí transakce byla úspěšně dokončena.

Když váš program komunikuje s Azure Database pro MariaDB prostřednictvím middleware jiného výrobce, zeptejte se dodavatele, zda middleware obsahuje logiku opakování pro přechodné chyby.

Ujistěte se, že test logiku opakování. Například zkuste spustit kód při škálování nahoru nebo dolů výpočetní prostředky z vás Azure Database pro MariaDB server. Aplikace by měla zpracovat krátké prostoje, ke kterým došlo během této operace bez problémů.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením ke službě Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)

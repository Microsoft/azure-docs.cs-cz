---
title: Zpracování chyb přechodného připojení – Azure Database for PostgreSQL – jeden server
description: Naučte se zpracovávat chyby přechodného připojení pro Azure Database for PostgreSQL jeden server.
keywords: připojení PostgreSQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7c70c82615df111f265604ff0984aa452d68565f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91704346"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Zpracování přechodných chyb připojení ke službě Azure Database for PostgreSQL – Jeden server

Tento článek popisuje, jak zpracovat přechodné chyby při připojování k Azure Database for PostgreSQL.

## <a name="transient-errors"></a>Přechodné chyby

Přechodná chyba, označovaná také jako přechodná chyba, je chyba, která se vyřeší sám. Nejčastěji tyto chyby se manifestují jako připojení k databázovému serveru, který se vynechává. Nová připojení k serveru se taky nedají otevřít. K přechodným chybám může dojít například v případě, že dojde k selhání hardwaru nebo sítě. Dalším důvodem může být nová verze služby PaaS, která se zavádí. Většina těchto událostí je systémem automaticky snížena za méně než 60 sekund. Osvědčeným postupem pro navrhování a vývoj aplikací v cloudu je očekávat přechodné chyby. Předpokládejte, že se k nim může kdykoli docházet v každé komponentě a mít k dispozici odpovídající logiku pro zpracování těchto situací.

## <a name="handling-transient-errors"></a>Zpracování přechodných chyb

Přechodné chyby by se měly zpracovat pomocí logiky opakování. Situace, které je třeba vzít v úvahu:

* Při pokusu o otevření připojení dojde k chybě.
* Nečinné připojení je na straně serveru vyřazeno. Když se pokusíte o vydání příkazu, nejde ho spustit.
* Aktivní připojení, které aktuálně provádí příkaz, je vyřazeno.

První a druhý případ jsou poměrně přímo předávány na popisovač. Pokuste se znovu otevřít připojení. Po úspěšném provedení přechodné chyby systém sníží. Azure Database for PostgreSQL můžete použít znovu. Doporučujeme, abyste počkali před opakováním pokusu o připojení. Pokud nedojde k selhání počátečních pokusů, je záložní. Tímto způsobem systém může použít všechny prostředky, které jsou k dispozici k překonání chybové situace. Dobrý vzor, který je potřeba provést:

* Počkejte 5 sekund před prvním opakováním.
* Pro každou z následujících možností zkuste zvětšit počkat exponenciálně až 60 sekund.
* Nastavte maximální počet opakovaných pokusů, na kterých by vaše aplikace pomohly operaci považovat za neúspěšnou.

V případě, že připojení k aktivní transakci dojde k chybě, je obtížné správně zpracovat obnovení. Existují dva případy: Pokud byla transakce určena jen pro čtení, je bezpečné znovu otevřít připojení a opakovat transakci. Pokud je však transakce také zapsána do databáze, je nutné určit, zda byla transakce vrácena zpět nebo zda byla úspěšná, než došlo k přechodné chybě. V takovém případě je možné, že jste neobdrželi potvrzení potvrzení z databázového serveru.

Jedním ze způsobů, jak to provést, je vygenerovat v klientovi jedinečné ID, které se používá pro všechny opakované pokusy. Toto jedinečné ID předáte jako součást transakce serveru a uložíte ji do sloupce s jedinečným omezením. Tímto způsobem lze transakci bezpečně opakovat. V případě, že předchozí transakce byla vrácena zpět a jedinečné ID generované klientem v systému ještě neexistuje, bude úspěšné. V případě, že se jedinečné ID dříve uložilo z důvodu úspěšného dokončení předchozí transakce, selže oznámení, že dojde k porušení duplicitního klíče.

Když váš program komunikuje s Azure Database for PostgreSQL prostřednictvím middlewaru třetí strany, požádejte dodavatele, zda middleware obsahuje logiku opakování pro přechodné chyby.

Nezapomeňte otestovat logiku opakování. Například zkuste spustit kód při vertikálním navýšení nebo snížení kapacity výpočetních prostředků Azure Database for PostgreSQL serveru. Vaše aplikace by měla zpracovávat krátké výpadky zjištěné během této operace bez jakýchkoli problémů.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením ke službě Azure Database for PostgreSQL](howto-troubleshoot-common-connection-issues.md)

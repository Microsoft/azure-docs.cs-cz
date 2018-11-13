---
title: Zpracování chyb přechodné připojení pro službu Azure Database pro MariaDB | Dokumentace Microsoftu
description: Zjistěte, jak zpracovávat chyby přechodné připojení pro službu Azure Database pro MariaDB.
keywords: připojení k MySQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení
services: mariadb
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 203401e3842912169371f315048f6930c8dc80eb
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568093"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Řešení chyb přechodné připojení pro službu Azure Database pro MariaDB

Tento článek popisuje způsob zpracování přechodných chyb připojení k Azure Database pro MariaDB.

## <a name="transient-errors"></a>Přechodné chyby

O přechodnou chybu, označované také jako přechodných chyb, se o chybu, která vyřeší se sám. Nejčastěji tyto chyby manifestu jako připojení k serveru databáze vyřazována. Také nelze otevřít nové připojení k serveru. Přechodným chybám může dojít například, když dojde k selhání hardwaru nebo sítě. Dalším důvodem může být nová verze služby PaaS, která se nasazuje. Většinu těchto událostí jsou automaticky omezeny systém za míň než 60 sekund. Osvědčeným postupem pro návrh a vývoj aplikací v cloudu je očekávat přechodné chyby. Předpokládejme, že může dojít v libovolné součásti kdykoli a mít odpovídající logiku ke zpracování těchto situacích.

## <a name="handling-transient-errors"></a>Zpracování přechodných chyb

Zpracování přechodných chyb pomocí logika opakovaných pokusů. Situace, které musíte vzít v úvahu:

* Dojde k chybě při pokusu o otevření připojení
* Nečinná připojení se ukončí na straně serveru. Při pokusu o příkaz nelze provést
* Aktivní připojení, který aktuálně spouští příkaz je vyřazeno.

První a druhý případ jsou poměrně jasně ke zpracování. Došlo k pokusu o otevření připojení znovu. Při úspěchu, přechodná chyba má byly omezeny systému. Azure Database pro MariaDB můžete znovu. Doporučujeme mít čekat před opakováním připojení. Vraťte se zpět, pokud selžou i počáteční pokusy. Tímto způsobem systému můžete použít všechny prostředky, které jsou k dispozici k překonání chybového stavu. Dobrý vzorek dodržovat je:

* Počkejte po dobu 5 sekund před prvním opakováním.
* Pro každé následující opakované pokusy zvýšení čekání exponenciálně, až 60 sekund.
* Nastavte maximální počet opakování v tomto okamžiku vaše aplikace bude považovat za operace se nezdařila.

Při připojení k aktivní transakce selže, je obtížné správně zpracovat obnovení. Existují dva možné případy: Pokud byla transakce ve své podstatě jen pro čtení, je bezpečné, pokud chcete znovu otevřít připojení a spusťte transakci znovu. Pokud ale pokud transakce se taky zapisuje do databáze, musíte určit, pokud transakce byla vrácena zpět nebo uspěla před došlo k přechodné chybě. V takovém případě nestačí jste dostali potvrzení potvrzení z databázového serveru.

Jeden způsob, jak to provést, se má generovat jedinečný Identifikátor na straně klienta, který se používá pro všechny opakované pokusy. Předejte tento jedinečný Identifikátor transakce v rámci k serveru a jeho uložení do sloupce s jedinečné omezení. Tímto způsobem můžete bez obav opakovat transakce. Pokud předchozí transakce se zrušila a klient vygeneruje jedinečné ID v systému ještě neexistuje, proběhne úspěšně. Dojde k selhání označující narušení duplicitní klíče, jestli jedinečné ID byly dříve uloženy, protože předchozí transakce byla úspěšně dokončena.

Když váš program komunikuje s využitím Azure Database pro MariaDB prostřednictvím middlewaru třetích stran, požádejte dodavatele, určuje, zda middleware obsahuje logiku opakování pro přechodné chyby.

Ujistěte se, že můžete testovat Logika opakování. Zkuste například ke spuštění kódu při škálování navýšit nebo snížit kapacitu výpočetních prostředků z vás Azure Database pro MariaDB server. Aplikace by měl zpracovávat krátký výpadek, který je zjištěn během této operace bez problémů.

## <a name="next-steps"></a>Další postup

* [Řešení problémů s připojením ke službě Azure Database pro MariaDB](howto-troubleshoot-common-connection-issues.md)

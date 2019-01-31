---
title: Urychlení obnovení databáze – Azure SQL Database | Dokumentace Microsoftu
description: Azure SQL Database obsahuje novou funkci, která poskytuje obnovení databáze rychlé a konzistentní vzhledem k aplikacím, okamžité transakce vrácení zpět a zkrácení agresivní protokolu pro izolované databáze a databáze ve fondu ve službě Azure SQL Database a databází v Azure SQL Data Sklad.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6d962a40fe0e1a7658c0d5ac30c7fd04bfb7fb0f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475444"
---
# <a name="accelerated-database-recovery-preview"></a>Zrychlené obnovení databáze (preview)

**Obnovení databáze (ADR) Accelerated** novou funkci modul databáze SQL, která výrazně zlepšuje dostupnost databáze, zejména za přítomnosti dlouho běží transakce, realizace proces obnovení modulu databáze SQL. Pravidla automatického nasazení je aktuálně dostupné pro izolované databáze a databáze ve fondu ve službě Azure SQL Database a databáze ve službě Azure SQL Data Warehouse. Hlavní výhody pravidla automatického nasazení jsou:

- **Obnovení databáze rychlé a konzistentní vzhledem k aplikacím**

  Pomocí pravidla automatického nasazení dlouhotrvající transakce vliv na celkový čas obnovení, povolení obnovení databáze rychlé a konzistentní bez ohledu na počet aktivních transakcí v systému nebo jejich velikosti.

- **Odvolání okamžité transakce**

  Pomocí pravidla automatického nasazení je bez ohledu na čas, který byl aktivní transakce nebo počet aktualizací, který se má provést okamžité vrácení transakce.

- **Zkrácení agresivní protokolu**

  Pomocí pravidla automatického nasazení transakční protokol agresivně zkrácen, i v případě výskytu aktivní dlouhotrvající transakce, což zabraňuje narůstat mimo ovládací prvek.

## <a name="the-current-database-recovery-process"></a>Aktuální proces obnovení databáze

Následuje obnovení databáze na SQL serveru [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) model obnovení se skládá ze tří fází, které jsou znázorněné v následujícím diagramu a je vysvětleno podrobněji následující diagram.

![aktuální proces obnovení](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analytická fáze**

  Předání kontroly transakčního protokolu od začátku posledního úspěšného kontrolního bodu (nebo stránce nejstarší LSN) až do konce, k určení stavu každou transakci v době, kdy SQL Server se zastavil.

- **Znovu fáze**

  Dopředného prohledávání jazyka transakční protokol z nejstarší nepotvrzené transakce až do konce, uveďte databázi do stavu, ve kterém se v době selhání, podle opakovaného všechny operace.

- **Vrátit zpět fáze**

  Pro každou transakci, který byl aktivní k datu selhání prochází přes protokol zpětně, vrácení operace, které provádí této transakce.

Je založená na tento návrh, čas potřebný k zotavení z neočekávaného restartování databázového stroje SQL (přibližně) přímo úměrná velikosti nejdelší aktivní transakce v systému v době selhání. Obnovení vyžaduje vrácení zpět všechny neúplné transakcí. Délka čas potřebný k práci, která se má provádět transakce přímo úměrná a čas byl aktivní. Proces obnovení SQL serveru, může trvat dlouhou dobu za přítomnosti dlouhotrvající transakce (například velké hromadné vložení, operace nebo operace sestavení indexu s velkou tabulku).

Také zrušení/vrácení zpět velkou transakci podle tohoto návrhu můžete taky využít dlouhou dobu jako používá stejné obnovení fáze vrácení zpět, jak je popsáno výše.

Kromě toho databázový stroj SQL nelze zkrátit transakčního protokolu, pokud existuje dlouhý spouštění transakce, protože jejich odpovídající záznamy protokolu jsou potřebné pro obnovení a vrácení zpět procesy. V důsledku tohoto návrhu databázový stroj SQL někteří zákazníci čelí problém, že velikost protokolu transakcí roste velmi velké a spotřebuje obrovské množství místa protokolu.

## <a name="the-accelerated-database-recovery-process"></a>Proces zapnuto zrychlené obnovení databáze

Pravidla automatického nasazení od základu řeší výše uvedené ve zcela realizace proces obnovení modulu databáze SQL na:

- Zkontrolujte konstantní čas/rychlých zabráněním museli prohledávání protokolů z/do začátku nejstarší aktivní transakce. Pomocí pravidla automatického nasazení transakční protokol je zpracována pouze z posledního úspěšného kontrolního bodu (nebo nejstarší změny stránky Number(LSN) pořadí protokolu. V důsledku toho není čas obnovení vliv dlouho spuštěná transakce.
- Omezit místa protokolu požadovanou transakci, protože již není nutné ke zpracování protokolu pro celou transakci. V důsledku toho transakční protokol může být zkrácená agresivně jako kontrolní body a zálohování.

Na vysoké úrovni pravidla automatického nasazení dosahuje obnovení rychlé databáze správy verzí všechny úpravy fyzická databáze a pouze vrácení logické operace, které jsou omezené a můžete se vrátit zpátky prakticky okamžitě. Všechny transakce, který byl aktivní k datu zhroucení jsou označeny jako přerušena, a proto můžete ignorovat jakékoli verze generovaná tyto transakcemi souběžných uživatelských dotazy.

Proces automatického obnovení má stejné tři fáze jako aktuální proces obnovení. Jak se tyto fáze pracovat pomocí pravidla automatického nasazení je znázorněno v následujícím diagramu a vysvětlené podrobněji následující diagram.

![Proces obnovení pravidla automatického nasazení](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analytická fáze**

  Proces zůstává stejná jako dnes a uveďte rekonstrukce sLog a zkopírování záznamů protokolu pro ops bez správy verzí.
- **Znovu** fáze

  Rozdělit na dvě fáze (P)
  - Fáze 1

      Opakovat akci z sLog (nejstarší nepotvrzené transakce až do posledního kontrolního bodu). Opakování je rychlá operace, dokud jej potřebuje jenom o pár záznamů z sLog zpracovat.
  - Fáze 2

     Znovu z protokolu transakcí začíná od posledního kontrolního bodu (namísto nejstarší nepotvrzené transakce)
- **Vrátit zpět fáze**

   Fáze vrácení změn pomocí pravidla automatického nasazení dokončí téměř okamžitě pomocí sLog ke zrušení operací bez správy verzí a trvalé verze Store (systémy současné hodnoty) s logickou vrátit k provedení řádku úrovně zpět na základě verze.

## <a name="adr-recovery-components"></a>Obnovení součástí pravidla automatického nasazení

Čtyř klíčových komponent pravidla automatického nasazení jsou:

- **Trvalé verze Store (systémy současné hodnoty)**

  Úložiště trvalých verze je nový mechanismus modul databáze SQL pro zachování verzí řádku vygenerované v samotné databázi místo tradiční `tempdb` úložiště verzí. Systémy současné hodnoty umožňuje izolaci prostředků a také zlepšuje dostupnost čitelné sekundární databáze.

- **Logické vrátit**

  Vrátit logických zodpovídá asynchronní proces pro provádění úrovni verze řádku na základě vrátit zpět – poskytuje pro všechny verze operace odvolání okamžité transakce a vrácení zpět.

  - Uchovává informace o všech přerušené transakce
  - Provede vrácení zpět pomocí systémy současné hodnoty pro všechny uživatelské transakce
  - Přerušení všech zámků ihned po transakce vydané verze

- **sLog**

  sLog je datový proud protokolu sekundární v paměti, že úložiště protokolování záznamů pro operace bez správy verzí (jako je například zneplatnění mezipaměti metadat, zámků a tak dále). SLog je:

  - Malé množství a v paměti
  - Trvale uloženého na disku podle serializována během procesu kontrolního bodu
  - Pravidelně zkrácen jako potvrzení transakce
  - Díky svému a zpracováním pouze bez správy verzí operace vrátit zpět  
  - Zachování pouze záznamy protokolu požadované umožňuje zkrácení protokolu agresivní transakce

- **Čisticího modulu**

  Čisticí je asynchronní proces, který probudí pravidelně a vyčistí verze stránek, které nejsou potřebné.

## <a name="who-should-consider-accelerated-database-recovery"></a>Kdo by měl zvážit zapnuto zrychlené obnovení databáze

Následující typy zákazníci měli zvážit povolení pravidla automatického nasazení:

- Zákazníci, kteří mají úlohy s dlouhé spuštěné transakce.
- Zákazníci, které jste viděli případy, kdy aktivních transakcí způsobují protokol transakce a výrazně zvýší.  
- Zákazníci, kteří došlo delší období nedostupnosti databáze z důvodu dlouho trvající obnovení (například neočekávané systému SQL Server restartování nebo ruční odvolání transakce) systému SQL Server.

## <a name="to-enable-adr-during-this-preview-period"></a>Povolit pravidla automatického nasazení během tohoto období preview

Během období preview pro tuto funkci, odešlete e-mail na [ adr@microsoft.com ](mailto:adr@microsoft.com) chcete dozvědět víc a zkuste si Accelerated obnovení databáze (ADR). V e-mailu obsahují název vaší databáze SQL serveru (pro izolované databáze a databáze ve fondu ve službě SQL Database a databáze ve službě Azure Data Warehouse). Protože se jedná o funkci preview, váš testovací server by měl být mimo provozní server.

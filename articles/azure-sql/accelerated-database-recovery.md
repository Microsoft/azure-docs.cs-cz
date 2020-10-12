---
title: Zrychlené obnovení databáze
titleSuffix: Azure SQL
description: Urychlené obnovení databáze poskytuje rychlé a konzistentní obnovení databáze, okamžité vrácení transakcí a agresivní zkracování protokolů pro databáze v portfoliu SQL Azure.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 4c679b6bb0f5645ea7a972be03ba3621b824a501
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327608"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Urychlené obnovení databáze v Azure SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

**Accelerated Database Recovery (ADR)** je funkce databázového stroje SQL Server, která významně vylepšuje dostupnost databáze, zejména v případě existence dlouhotrvajících transakcí, a to tak, že přenavrhování SQL Serverho procesu obnovení databázového stroje. 

Pravidlo automatického nasazení je aktuálně dostupné pro Azure SQL Database, Azure SQL Managed instance, databáze ve službě Azure synapse Analytics (aktuálně ve verzi Preview) a SQL Server na virtuálních počítačích Azure počínaje SQL Server 2019. 

> [!NOTE] 
> V Azure SQL Database a Azure SQL Managed instance je ve výchozím nastavení povolená možnost ADR a zakázání pravidla automatického nasazení pro některý z těchto produktů se nepodporuje. 

## <a name="overview"></a>Přehled

Hlavní výhody ADR jsou:

- **Rychlá a konzistentní obnova databáze**

  Při použití pravidla automatického nasazení neovlivní dlouho běžící transakce celkovou dobu obnovení a umožní rychlé a konzistentní obnovení databáze bez ohledu na počet aktivních transakcí v systému nebo jejich velikosti.

- **Okamžitý návrat transakce**

  Pomocí pravidla automatického nasazení je vrácení transakce okamžitě, bez ohledu na čas, kdy byla transakce aktivní, nebo počet aktualizací, které byly provedeny.

- **Agresivní zkracování protokolů**

  V případě pravidla automatického nasazení je transakční protokol agresivní a dokonce i v přítomnosti aktivních dlouhotrvajících transakcí, což brání v rozšiřování řízení.

## <a name="standard-database-recovery-process"></a>Proces obnovení databáze úrovně Standard

Obnovení databáze sleduje model obnovení [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) a skládá se ze tří fází, které jsou znázorněny v následujícím diagramu a jsou podrobněji vysvětleny v následujícím diagramu.

![aktuální proces obnovení](./media/accelerated-database-recovery/current-recovery-process.png)

- **Fáze analýzy**

  Projde kontrolu transakčního protokolu od začátku posledního úspěšného kontrolního bodu (nebo nejstarší nekonečná hodnota LSN stránky) až do konce, abyste zjistili stav každé transakce v době, kdy se databáze zastavila.

- **Fáze opakování**

  Projde kontrolu transakčního protokolu z nejstarší nepotvrzené transakce do konce, aby se databáze přenesla do stavu, ve kterém byla v době selhání, a to opětovným provedením všech svěřených operací.

- **Fáze vrácení zpět**

  Pro každou transakci, která byla aktivní v době selhání, projde protokol zpět a vrátí operace, které tato transakce provedla.

Na základě tohoto návrhu je čas potřebný k zotavení z neočekávaného restartu SQL Server databázový stroj (přibližně) úměrný velikosti nejdelší aktivní transakce v systému v době selhání. Obnovení vyžaduje vrácení zpět všech neúplných transakcí. Požadovaná doba je úměrná práci, kterou transakce provedla, a času, kdy byla transakce aktivní. Proto může proces obnovení trvat dlouhou dobu v přítomnosti dlouhotrvajících transakcí (například rozsáhlé operace hromadného vložení nebo operace sestavení indexu s velkou tabulkou).

I zrušení nebo vrácení velkých transakcí na základě tohoto návrhu může také trvat dlouhou dobu, protože používá stejnou fázi vrácení zpět, jak je popsáno výše.

Kromě toho databázový stroj SQL Server nemůže zkrátit transakční protokol, pokud existují dlouhotrvající transakce, protože odpovídající záznamy protokolu jsou nutné pro procesy obnovení a vrácení zpět. V důsledku tohoto návrhu SQL Server databázového stroje se někteří zákazníci využili k problému s tím, že velikost transakčního protokolu roste velmi velká a spotřebovává velké množství místa na disku.

## <a name="the-accelerated-database-recovery-process"></a>Proces urychleného obnovení databáze

ADR řeší výše uvedené problémy tím, že zcela přenavrhování SQL Serverho procesu obnovení databázového stroje na:

- Zajistěte si neustálou dobu a okamžitou nemusíte kontrolovat protokol od/po začátek nejstarší aktivní transakce. V případě pravidla automatického nasazení se transakční protokol zpracovává jenom z posledního úspěšného kontrolního bodu (nebo nejstarší stránky pořadového čísla LSN). V důsledku toho čas obnovení není ovlivněn dlouhodobě běžícími transakcemi.
- Minimalizujte požadovaný prostor protokolu transakcí, protože již není nutné zpracovávat protokol pro celou transakci. V důsledku toho může být transakční protokol zkráceně, protože došlo k kontrolním bodům a zálohování.

V rámci vysoké úrovně dosahuje ADR rychlé obnovení databáze tím, že provádí správu verzí všech změn fyzické databáze a jenom zrušení logických operací, které jsou omezené a dají se prakticky okamžitě vrátit. Všechny transakce, které byly aktivní v době selhání, jsou označeny jako přerušené a proto mohou být všechny verze vygenerované těmito transakcemi ignorovány souběžnými uživatelskými dotazy.

Proces obnovení ADR má stejné tři fáze jako aktuální proces obnovení. Jak tyto fáze pracují s pravidlem ADR, jsou znázorněné v následujícím diagramu a podrobněji se podrobněji vysvětluje v diagramu.

![Proces obnovení ADR](./media/accelerated-database-recovery/adr-recovery-process.png)

- **Fáze analýzy**

  Proces zůstane stejný jako před přidáním rekonstrukci sLog a zkopírováním záznamů protokolu pro operace, které nejsou ve verzi.
  
- Fáze **opakování**

  Rozdělené do dvou fází (P)
  - Fáze 1

      Znovu z sLog (nejstarší nepotvrzená transakce až do posledního kontrolního bodu). Opakování je rychlá operace, protože potřebuje pouze zpracovat několik záznamů z sLog.

  - Fáze 2

     Znovu z protokolu transakce začíná od posledního kontrolního bodu (namísto nejstarší nepotvrzené transakce)

- **Fáze vrácení zpět**

   Fáze vrácení zpět se serverem SLA se téměř okamžitě dokončí pomocí sLog k vrácení neverzí operací a trvalého úložiště verzí (PVS) s logickým návratem pro vrácení zpět na základě verze na úrovni řádku.

## <a name="adr-recovery-components"></a>Součásti pro obnovení ADR

Mezi čtyři klíčové součásti pravidla automatického nasazení patří:

- **Trvalé úložiště verzí (PVS)**

  Trvalé úložiště verzí je nový mechanismus SQL Server databázového stroje pro uchování verzí řádků vygenerovaných v samotné databázi namísto tradičního `tempdb` úložiště verzí. PVS umožňuje izolaci prostředků a také zlepšuje dostupnost čitelných sekundárních.

- **Logické vrácení**

  Logické vrácení je asynchronní proces, který je zodpovědný za provádění vrácení zpět na úrovni řádku – poskytuje vrácení zpět a vrácení zpět pro všechny operace s verzí. Logické vrácení je provedeno pomocí:

  - Udržování přehledu o všech přerušených transakcích a jejich označení jako neviditelná pro jiné transakce. 
  - Vrácení zpět pomocí PVS pro všechny uživatelské transakce místo fyzického prohledávání transakčního protokolu a vrácení změn v jednom okamžiku.
  - Uvolnění všech zámků ihned po přerušení transakce. Vzhledem k tomu, že přerušení zahrnuje jednoduše označení změn v paměti, proces je velmi efektivní a proto se zámky nemusí uchovávat po dlouhou dobu.

- **sLog**

  sLog je sekundární datový proud protokolu v paměti, který ukládá záznamy protokolu pro operace, které nejsou ve verzi (jako je třeba neplatnost v mezipaměti metadat, zámky zámku atd.). SLog je:

  - Nízká hlasitost a v paměti
  - Trvalá na disku při serializaci během procesu kontrolního bodu
  - Pravidelně zkrácené jako potvrzení transakcí
  - Zrychluje akci a vrátí se zpět zpracováním jenom operací bez verzí.  
  - Zachovává agresivní zkracování transakčního protokolu tím, že zachová jenom požadované záznamy protokolu.

- **Vysavač**

  Čistič je asynchronní proces, který se probudí a vyčistí nepotřebné verze stránek.

## <a name="accelerated-database-recovery-patterns"></a>Urychlené vzorce obnovení databáze

Následující typy úloh mají největší užitek z pravidla automatického nasazení:

- Úlohy s dlouhodobě běžícími transakcemi.
- Úlohy, které viděli případy, kdy aktivní transakce způsobují významně rostoucí transakční protokol.  
- Úlohy, u kterých došlo k dlouhé době nedostupnosti databáze z důvodu dlouhotrvajícího obnovení (například neočekávaného restartování služby nebo ruční vrácení transakce).

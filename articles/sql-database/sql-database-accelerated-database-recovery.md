---
title: Urychlené obnovení databáze – Azure SQL Database | Microsoft Docs
description: Azure SQL Database má novou funkci, která poskytuje rychlé a konzistentní obnovení databáze, okamžité vrácení transakce a agresivní zkracování protokolů pro izolované databáze a databáze ve fondu v Azure SQL Database a databáze ve službě Azure SQL data. Skladu.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: d516dc51a25cbef92ff9fa22012773507b528a99
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569623"
---
# <a name="accelerated-database-recovery"></a>Urychlené obnovení databáze

**Accelerated Database Recovery (ADR)** je nová funkce stroje SQL Database, která významně vylepšuje dostupnost databáze, zejména v případě existence dlouhotrvajících transakcí, přenavrhováním procesu obnovení SQL Database Engine. ADR je aktuálně k dispozici pro izolované databáze a databáze ve fondu v Azure SQL Database a databáze v Azure SQL Data Warehouse (aktuálně ve verzi Public Preview). Hlavní výhody ADR jsou:

- **Rychlá a konzistentní obnova databáze**

  Při použití pravidla automatického nasazení neovlivní dlouho běžící transakce celkovou dobu obnovení a umožní rychlé a konzistentní obnovení databáze bez ohledu na počet aktivních transakcí v systému nebo jejich velikosti.

- **Okamžitý návrat transakce**

  Pomocí pravidla automatického nasazení je vrácení transakce okamžitě, bez ohledu na čas, kdy byla transakce aktivní, nebo počet aktualizací, které byly provedeny.

- **Agresivní zkracování protokolů**

  V případě pravidla automatického nasazení se transakční protokol výrazně zkrátí, dokonce i v přítomnosti aktivních transakcí s dlouhou dobou provozu, což brání jeho většímu růstu.

## <a name="the-current-database-recovery-process"></a>Aktuální proces obnovení databáze

Obnovení databáze v SQL Server řídí model obnovení [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) a skládá se ze tří fází, které jsou znázorněny v následujícím diagramu a jsou podrobněji vysvětleny v následujícím diagramu.

![aktuální proces obnovení](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fáze analýzy**

  Prohledání transakčního protokolu od začátku posledního úspěšného kontrolního bodu (nebo nejstarší nekonečná hodnota LSN stránky) až do konce, abyste zjistili stav každé transakce v době SQL Server zastavili.

- **Fáze opakování**

  Projde kontrolu transakčního protokolu z nejstarší nepotvrzené transakce do konce, aby se databáze přenesla do stavu, ve kterém byla v době selhání, a to opětovným provedením všech svěřených operací.

- **Fáze vrácení zpět**

  Pro každou transakci, která byla aktivní v době selhání, projde protokol zpět a vrátí operace, které tato transakce provedla.

V závislosti na tomto návrhu je čas, který modul SQL Database potřebuje k obnovení z neočekávaného restartování (přibližně) úměrný velikosti nejdelší aktivní transakce v systému v době selhání. Obnovení vyžaduje vrácení zpět všech neúplných transakcí. Požadovaná doba je úměrná práci, kterou transakce provedla, a času, kdy byla transakce aktivní. Proto může proces obnovení SQL Server trvat dlouhou dobu v přítomnosti dlouhotrvajících transakcí (například rozsáhlé operace hromadného vložení nebo operace sestavení indexu s velkou tabulkou).

I zrušení nebo vrácení velkých transakcí na základě tohoto návrhu může také trvat dlouhou dobu, protože používá stejnou fázi vrácení zpět, jak je popsáno výše.

Kromě toho stroj SQL Database nemůže zkrátit transakční protokol, pokud existují dlouho běžící transakce, protože pro procesy obnovení a vrácení zpět je nutný jejich odpovídající záznam protokolu. Výsledkem tohoto návrhu stroje SQL Database je, že někteří zákazníci čelí problému, že velikost transakčního protokolu roste velmi velká a spotřebovává velké množství místa na disku.

## <a name="the-accelerated-database-recovery-process"></a>Proces urychleného obnovení databáze

ADR řeší výše uvedené problémy tím, že zcela přenavrhuje proces obnovení SQL Database Engine na:

- Zajistěte si neustálou dobu a okamžitou nemusíte kontrolovat protokol od/po začátek nejstarší aktivní transakce. V případě pravidla automatického nasazení se transakční protokol zpracovává jenom z posledního úspěšného kontrolního bodu (nebo nejstarší stránky pořadového čísla LSN). V důsledku toho čas obnovení není ovlivněn dlouhodobě běžícími transakcemi.
- Minimalizujte požadovaný prostor protokolu transakcí, protože již není nutné zpracovávat protokol pro celou transakci. V důsledku toho může být transakční protokol zkráceně, protože došlo k kontrolním bodům a zálohování.

V rámci vysoké úrovně dosahuje ADR rychlé obnovení databáze tím, že provádí správu verzí všech změn fyzické databáze a jenom zrušení logických operací, které jsou omezené a dají se prakticky okamžitě vrátit. Všechny transakce, které byly aktivní v době selhání, jsou označeny jako přerušené a proto mohou být všechny verze vygenerované těmito transakcemi ignorovány souběžnými uživatelskými dotazy.

Proces obnovení ADR má stejné tři fáze jako aktuální proces obnovení. Jak tyto fáze pracují s pravidlem ADR, jsou znázorněné v následujícím diagramu a podrobněji se podrobněji vysvětluje v diagramu.

![Proces obnovení ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fáze analýzy**

  Proces zůstane stejný jako v dnešní době s přidáním rekonstrukce sLog a zkopírováním záznamů protokolu pro operace, které nejsou ve verzi.
  
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

  Trvalé úložiště verzí je nový mechanismus SQL Database Engine pro zachování verzí řádků vygenerovaných v samotné databázi místo z tradičního `tempdb` úložiště verzí. PVS umožňuje izolaci prostředků a také zlepšuje dostupnost čitelných sekundárních.

- **Logické vrácení**

  Logické vrácení je asynchronní proces, který je zodpovědný za provádění vrácení zpět na úrovni řádku – poskytuje vrácení zpět a vrácení zpět pro všechny operace s verzí.

  - Sleduje všechny přerušené transakce.
  - Provede vrácení zpět pomocí PVS pro všechny uživatelské transakce.
  - Uvolní všechny zámky hned po přerušení transakce.

- **sLog**

  sLog je sekundární datový proud protokolu v paměti, který ukládá záznamy protokolu pro operace, které nejsou ve verzi (jako je třeba neplatnost v mezipaměti metadat, zámky zámku atd.). SLog je:

  - Nízká hlasitost a v paměti
  - Trvalá na disku při serializaci během procesu kontrolního bodu
  - Pravidelně zkrácené jako potvrzení transakcí
  - Zrychluje akci a vrátí se zpět zpracováním jenom operací bez verzí.  
  - Zachovává agresivní zkracování transakčního protokolu tím, že zachová jenom požadované záznamy protokolu.

- **Vysavač**

  Čistič je asynchronní proces, který se probudí a vyčistí nepotřebné verze stránek.

## <a name="who-should-consider-accelerated-database-recovery"></a>Kdo by měl zvážit urychlené obnovení databáze

Následující typy zákazníků by měly zvážit povolení pravidla automatického nasazení:

- Zákazníci, kteří mají úlohy s dlouho běžícími transakcemi.
- Zákazníci, kteří si viděli případy, kdy aktivní transakce způsobují významně rostoucí transakční protokol.  
- Zákazníci, kteří mají dlouhou dobu nedostupnosti databáze kvůli SQL Server dlouho běžícímu obnovení (například neočekávané SQL Server restart nebo ruční vrácení transakcí).


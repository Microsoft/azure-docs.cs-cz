---
title: Zrychlené obnovení databáze
description: Azure SQL Database má novou funkci, která poskytuje rychlé a konzistentní obnovení databáze, okamžité vrácení transakcí a agresivní zkrácení protokolů pro jednotlivé databáze a sdružené databáze v Azure SQL Database a databáze v Azure SQL Data Skladu.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238658"
---
# <a name="accelerated-database-recovery"></a>Zrychlené obnovení databáze

**Accelerated Database Recovery (ADR)** je funkce databázového stroje SQL, která výrazně zlepšuje dostupnost databáze, zejména v přítomnosti dlouhotrvajících transakcí, a to přepracovat proces obnovení databázového stroje SQL. ADR je momentálně dostupná pro jednu databázi Azure SQL Database, elastický fond a spravovanou instanci a databáze v Azure SQL Data Warehouse (aktuálně ve verzi Preview). Hlavní výhody Alternativního řešení sporů jsou:

- **Rychlé a konzistentní obnovení databáze**

  S ADR, dlouho běžící transakce nemají vliv na celkovou dobu obnovení, což umožňuje rychlé a konzistentní obnovení databáze bez ohledu na počet aktivních transakcí v systému nebo jejich velikosti.

- **Okamžité vrácení transakce**

  S ADR vrácení transakce je okamžitá, bez ohledu na čas, kdy byla transakce aktivní nebo počet provedených aktualizací.

- **Agresivní zkrácení protokolu**

  S ADR transakční protokol je agresivně zkrácen, a to i v přítomnosti aktivních dlouhotrvající transakce, které brání tomu, aby se vymkla kontrole.

## <a name="the-current-database-recovery-process"></a>Aktuální proces obnovení databáze

Obnovení databáze v SQL Server následuje model obnovení [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) a skládá se ze tří fází, které jsou znázorněny v následujícím diagramu a podrobněji vysvětleny v následujícím diagramu.

![aktuální proces obnovy](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fáze analýzy**

  Předávání prohledávač transakční protokol od začátku poslední úspěšné kontrolní bod (nebo nejstarší dirty stránky LSN) až do konce, k určení stavu každé transakce v době, kdy SQL Server zastavil.

- **Fáze opakování**

  Předávání prohledávač transakční protokol od nejstarší nepotvrzené transakce až do konce, aby databáze do stavu, který byl v době selhání předěláním všech potvrzených operací.

- **Fáze vrátit se k odvádění**

  Pro každou transakci, která byla aktivní k době selhání, prochází protokolu zpět, vrátit zpět operace, které tato transakce provedena.

Na základě tohoto návrhu čas potřebný k obnovení databázového stroje SQL z neočekávanérestartování je (zhruba) úměrná velikosti nejdelší aktivní transakce v systému v době selhání. Obnovení vyžaduje vrácení všech neúplných transakcí. Požadovaná doba je úměrná práci, kterou transakce provedla, a době, po kterou byla transakce aktivní. Proces obnovení serveru SQL Server proto může trvat dlouhou dobu v přítomnosti dlouhotrvající transakce (například velké operace hromadné vložení nebo operace sestavení indexu proti velké tabulce).

Také zrušení/vrácení velké transakce na základě tohoto návrhu může také trvat dlouhou dobu, protože používá stejnou fázi obnovení zpět, jak je popsáno výše.

Kromě toho databázový stroj SQL nelze zkrátit transakční protokol, pokud existují dlouho běžící transakce, protože jejich odpovídající záznamy protokolu jsou potřebné pro procesy obnovení a vrácení zpět. V důsledku tohoto návrhu databázového stroje SQL, někteří zákazníci slouží k problému, že velikost transakční protokol roste velmi velké a spotřebovává obrovské množství místa na disku.

## <a name="the-accelerated-database-recovery-process"></a>Proces zrychleného obnovení databáze

ADR řeší výše uvedené problémy úplným přepracovat proces obnovení databázového stroje SQL takto:

- Ať je konstantní čas / okamžitý tím, že se vyhnete nutnosti skenovat protokol z / na začátek nejstarší aktivní transakce. S ADR transakční protokol je zpracována pouze z poslední úspěšný kontrolní bod (nebo nejstarší dirty stránky číslo sekvence protokolu (LSN)). V důsledku toho není doba obnovení ovlivněna dlouhotrvající transakce.
- Minimalizujte požadované místo protokolu transakcí, protože již není nutné protokol zpracovávat pro celou transakci. V důsledku toho transakční protokol lze agresivně zkráceny jako kontrolní body a zálohy.

Na vysoké úrovni ADR dosahuje rychlé obnovení databáze verzí všech fyzických databází modifikace a pouze vrácení logické operace, které jsou omezené a lze vrátit zpět téměř okamžitě. Všechny transakce, která byla aktivní k době selhání jsou označeny jako přerušené, a proto všechny verze generované těmito transakcemi mohou být ignorovány souběžných uživatelských dotazů.

Proces obnovení Alternativního řešení sporů má stejné tři fáze jako aktuální proces obnovení. Jak tyto fáze fungují s ADR, je znázorněno na následujícím diagramu a podrobněji vysvětleno v následujícím diagramu.

![Proces obnovení alternativního řešení sporů](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fáze analýzy**

  Proces zůstává stejný jako dříve s přidáním rekonstrukce sLog a kopírování záznamů protokolu pro operace bez verze.
  
- **Fáze opakování**

  Rozděleno do dvou fází (P)
  - Fáze 1

      Znovu z sLog (nejstarší nepotvrzené transakce až do posledního kontrolního bodu). Znovu je rychlá operace, protože potřebuje pouze zpracovat několik záznamů z sLog.
      
  - Fáze 2

     Znovu z transakčního protokolu začíná z posledního kontrolního bodu (namísto nejstarší nepotvrzené transakce)
     
- **Fáze vrátit se k odvádění**

   Fáze vrácení zpět s ADR dokončí téměř okamžitě pomocí sLog vrátit zpět operace bez verze a trvalé úložiště verzí (PVS) s logické vrátit k provedení verze řádku zpět.

## <a name="adr-recovery-components"></a>Součásti obnovení ADR

Čtyři klíčové složky alternativního řešení sporů jsou:

- **Trvalé úložiště verzí (PVS)**

  Úložiště trvalých verzí je nový mechanismus databázového stroje SQL pro uchování verzí řádků `tempdb` generovaných v samotné databázi namísto úložiště tradičních verzí. PVS umožňuje izolaci prostředků a zlepšuje dostupnost čitelných sekundárních.

- **Logický návrat**

  Logický návrat je asynchronní proces zodpovědný za provádění verze řádku na základě Zpět - poskytuje okamžité vrácení transakce zpět a zpět pro všechny operace s verzí. Logický návrat se provádí pomocí:

  - Sledování všech přerušených transakcí a jejich označení neviditelné pro jiné transakce. 
  - Provádění vrácení zpět pomocí PVS pro všechny transakce uživatele, spíše než fyzicky skenování transakční protokol a vrácení změny jeden po druhém.
  - Uvolnění všech zámků ihned po přerušení transakce. Vzhledem k tomu, přerušení zahrnuje jednoduše označení změny v paměti, proces je velmi efektivní, a proto zámky nemusí být drženy po dlouhou dobu.

- **Dřina**

  sLog je sekundární datový proud protokolu v paměti, který ukládá záznamy protokolu pro operace bez verzí (například zneplatnění mezipaměti metadat, pořízení zámků a tak dále). SLog je:

  - Nízká hlasitost a paměť
  - Trvalé na disku serializace během procesu kontrolního bodu
  - Pravidelně zkráceno jako potvrzení transakcí
  - Urychluje opakování a vrácení se zpracovat zpracováním pouze bez verze operace  
  - Umožňuje agresivní zkrácení transakčního protokolu zachováním pouze požadovaných záznamů protokolu.

- **Čistší**

  Čistič je asynchronní proces, který se pravidelně probouzí a čistí verze stránek, které nejsou potřeba.

## <a name="accelerated-database-recovery-patterns"></a>Zrychlené vzory obnovení databáze

ADR mají největší prospěch z následujících typů úloh:

- Úlohy s dlouhotrvající transakce.
- Úlohy, které zaznamenaly případy, kdy aktivní transakce způsobují výrazně růst transakční protokol.  
- Úlohy, které došlo k dlouhé období nedostupnosti databáze z důvodu SQL Server dlouho běžící obnovení (například neočekávané restartování serveru SQL Server nebo ruční vrácení transakce).


---
title: Co je nástroj pro snímky konzistentní vzhledem k aplikacím Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje Úvod do nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure, který můžete použít s Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632682"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Co je nástroj pro snímek konzistentní vzhledem k aplikacím Azure (Preview)

Nástroj pro vytváření snímků konzistentního vzhledem k aplikacím Azure (AzAcSnap) je nástroj příkazového řádku, který umožňuje zjednodušit ochranu dat pro databáze třetích stran (SAP HANA) v prostředích Linux (například SUSE a RHEL).  

## <a name="benefits-of-using-azacsnap"></a>Výhody použití AzAcSnap

AzAcSnap využívá snímky svazku a funkce replikace v Azure NetApp Files a velké instanci Azure.  Přináší následující výhody:

- **Ochrana dat konzistentní vzhledem k aplikacím** AzAcSnap je centralizované řešení pro zálohování důležitých souborů databáze. Před provedením snímku svazku úložiště zajišťuje konzistenci databáze. V důsledku toho zajistí, že se snímek svazku úložiště dá použít pro obnovení databáze.
- **Správa katalogu databází** Pokud používáte AzAcSnap s databází, která má integrovaný katalog služby Backup, záznamy v katalogu budou uchovány jako aktuální pomocí snímků úložiště.  Tato funkce umožňuje správci databáze zobrazit aktivitu zálohování.
- **Ochrana svazků ad hoc** Tato funkce je užitečná pro svazky nevyužívající databázi, které nepotřebují nepotřebnou práci s aplikacemi, než začnete vytvářet snímky úložiště.  Mezi příklady patří SAP HANA svazky pro zálohování protokolů nebo SAPTRANS svazky.
- **Klonování svazků úložiště** Tato funkce poskytuje prostorově efektivní klony svazků úložiště pro účely vývoje a testování.
- **Podpora pro zotavení po havárii** AzAcSnap využívá replikaci svazku úložiště k poskytnutí možností pro obnovu replikovaných snímků konzistentních vzhledem k aplikacím ve vzdálené lokalitě.

AzAcSnap je jeden binární soubor.  Nepotřebuje, aby další agenti nebo moduly plug-in komunikovali s databází nebo úložištěm (Azure NetApp Files přes Azure Resource Manager a velké instance Azure přes SSH).  AzAcSnap musí být nainstalovaný v systému, který má připojení k databázi a úložišti.  Flexibilita instalace a konfigurace ale umožňuje buď jednu centralizovanou instalaci, nebo úplnou distribuovanou instalaci s kopiemi nainstalovanými při každé instalaci databáze.

## <a name="architecture-overview"></a>Přehled architektury

AzAcSnap je možné nainstalovat na stejném hostitele jako databázi (SAP HANA), nebo ji můžete nainstalovat do centralizovaného systému.  Musí ale být síťové připojení k databázovým serverům a back-end úložiště (Azure Resource Manager pro Azure NetApp Files nebo SSH pro rozsáhlou instanci Azure).

AzAcSnap je zjednodušená aplikace, která se obvykle spouští z externího plánovače.  Ve většině systémů Linux Tato operace je `cron` , což znamená, že se dokumentace soustředí na.  Plánovač ale může být alternativním nástrojem, pokud může importovat `azacsnap` profil uživatelského prostředí.  Import nastavení prostředí uživatele zajišťuje správné inicializaci cest a oprávnění k souborům.

## <a name="command-synopsis"></a>Souhrn příkazů

Obecný formát příkazů je následující: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Možnosti příkazu

Možnosti příkazu jsou tyto příkazy v podobě hlavních odrážek a přidružených dílčích příkazů jako odrážek:

- **`-h`** poskytuje rozšířenou nápovědu příkazového řádku s příklady používání AzAcSnap.
- **`-c configure`** Tento příkaz poskytuje interaktivní Q&rozhraní stylu pro vytvoření nebo úpravu `azacsnap` konfiguračního souboru (výchozí = `azacsnap.json` ).
  - **`--configuration new`** Vytvoří nový konfigurační soubor.
  - **`--configuration edit`** provede úpravu stávajícího konfiguračního souboru.
  - informace najdete v tématu [konfigurace odkazů na příkazy](azacsnap-cmd-ref-configure.md).
- **`-c test`** ověří konfigurační soubor a otestuje připojení.
  - **`--test hana`**  Otestuje připojení k instanci SAP HANA.
  - **`--test storage`** testuje komunikaci s podkladovým rozhraním úložiště vytvořením dočasného snímku úložiště na všech nakonfigurovaných `data` svazcích a jejich odebráním.
  - **`--test all`** provede `hana` `storage` testy a v pořadí.
  - viz [Reference k testovacím příkazům](azacsnap-cmd-ref-test.md).
- **`-c backup`** je primárním příkazem pro spuštění konzistentních snímků úložiště databáze pro data (SAP HANA datových svazků) & jiných (například sdílených, protokolových nebo spouštěcích) svazcích.
  - **`--volume data`** pro vytvoření snímku všech svazků v `dataVolume` Stanza konfiguračního souboru.
  - **`--volume other`** pro vytvoření snímku všech svazků v `otherVolume` Stanza konfiguračního souboru.
  - informace najdete v tématu [Reference k příkazům zálohování](azacsnap-cmd-ref-backup.md).
- **`-c details`** poskytuje informace o snímcích nebo replikaci.
  - **`--details snapshots`** Obsahuje seznam základních podrobností o snímcích pro každý svazek, který byl nakonfigurován.
  - **`--details replication`** Poskytuje základní podrobnosti o stavu replikace z produkční lokality do lokality pro zotavení po havárii.
  - odkaz na [příkazy podrobností najdete v podrobnostech](azacsnap-cmd-ref-details.md).
- **`-c delete`** Tento příkaz odstraní snímek úložiště nebo sadu snímků. Můžete použít buď ID zálohy SAP HANA, jak se nachází v HANA Studio, nebo v názvu snímku úložiště. ID zálohy je vázané jenom na `hana` snímky, které jsou vytvořené pro data a sdílené svazky. V opačném případě se při zadání názvu snímku vyhledá všechny snímky, které odpovídají zadanému názvu snímku.
  - Podívejte se na [odstranění](azacsnap-cmd-ref-delete.md).
- **`-c restore`** v této části najdete dvě metody obnovení snímku na svazek. buď vytvořením nového svazku na základě snímku nebo vrácením svazku do stavu verze Preview.
  - **`--restore snaptovol`** Vytvoří nový svazek na základě posledního snímku na cílovém svazku.
  - **`-c restore --restore revertvolume`** Obnoví cílový svazek na předchozí stav na základě nejnovějšího snímku.
  - Další informace najdete v tématu věnovaném [obnovení odkazů na příkazy](azacsnap-cmd-ref-restore.md).
- **`[--configfile <configfilename>]`** Volitelný parametr příkazového řádku, který poskytne jiný název konfiguračního souboru JSON.  To je zvlášť užitečné při vytváření samostatného konfiguračního souboru na SID (např `--configfile H80.json` .).

## <a name="next-steps"></a>Další kroky

- [Začínáme s nástrojem pro vytváření snímků konzistentním s aplikacemi Azure](azacsnap-get-started.md)

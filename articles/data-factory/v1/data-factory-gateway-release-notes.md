---
title: Poznámky k verzi pro Brána pro správu dat | Microsoft Docs
description: Brána pro správu dat, text poznámky k verzi
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4530b11db7691f631f636021634cc5e9c8be8c86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620557"
---
# <a name="release-notes-for-data-management-gateway"></a>Poznámky k verzi pro Bránu pro správu dat
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [hostovanou na vlastním integrace runtime v V2](../create-self-hosted-integration-runtime.md).

Jedním z problémů pro integraci moderní dat je přesun dat do a z místního do cloudu. Objekt pro vytváření dat umožňuje integraci se Brána pro správu dat, což je místní povolit hybridní přesun dat můžete nainstalovat agenta.

Najdete podrobné informace o Brána pro správu dat a způsobu jeho použití v následujících článcích:

*  [Brána správy dat](data-factory-data-management-gateway.md)
*  [Přesun dat mezi místními a cloudu pomocí Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUÁLNÍ VERZE 
Žádné další uchováváme poznámky k verzi v tomto poli. Získat nejnovější poznámky k verzi [sem](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Starší verze
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Vylepšení-
- Přidáním položky DNS do seznamu povolených IP adres služby service bus, místo vytvoření seznamu povolených všechny Azure IP adresy z brány firewall (v případě potřeby). Odpovídající položka DNS můžete najít na portálu Azure ('Vytvořit a nasadit' -> objekt pro vytváření dat -> 'Brány' -> "serviceUrls" (ve formátu JSON)
- Konektor HDFS nyní podporuje veřejný certifikát podepsaný svým držitelem tím, že umožňuje přeskočit ověřování SSL.
- Opravené: Problém s bránou offline během aktualizace (z důvodu posun hodin)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Vylepšení-
-   Můžete přidat záznamy DNS na seznam povolených adres služby Service Bus, místo vytvoření seznamu povolených všechny Azure IP adresy z brány firewall (v případě potřeby). Další informace v tomto poli.
-   Můžete teď kopírování dat z jeden blok objektů blob až 4.75 TB, což je maximální podporovaná velikost objektu blob bloku. (dřívější limit byl 195 GB).
-   Opravené: Nedostatek paměti problém při rozbalení několik malých souborů během aktivity kopírování.
-   Opravené: Index je mimo rozsah problém při kopírování z databáze dokumentu aplikace idempotenci funkce na místní server SQL.
-   Opravené: Skript pro vyčištění SQL nefunguje s místní SQL Server z Průvodce kopírováním.
-   Opravené: Název sloupce s místem na konci nefunguje v aktivitě kopírování.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Vylepšení-
- Opravené: Problém s chybějícími přihlašovací údaje u restartování počítače brány.
- Opravené: Problém s registrací během brány obnovit záložního souboru.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Vylepšení-
- Opravené: Nesprávné čtení desetinnou hodnotu null z databáze Oracle jako zdroj.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co je nového
- Zákazníci můžete poskytnout zpětnou vazbu na bránu zaregistrujete činnost.
- Podpora nový formát komprese: ZIP (Deflate)

### <a name="enhancements-"></a>Vylepšení-
- Zlepšení výkonu pro Oracle jímky, HDFS zdroje.
- Oprava chyby pro brány automatické aktualizace, gateway paralelní zpracování kapacity.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Vylepšení
- Vylepšené a robustnější brány registrace zkušeností – teď můžete sledovat průběh během procesu registrace brány, který umožňuje registraci prostředí rychleji reagovat.
- Zlepšení brány obnovit proces - je stále možné obnovit bránu i v případě, že nemáte brány záložní soubor s touto aktualizací. To bude vyžadovat jste se resetovat přihlašovací údaje propojené služby v portálu.
- Oprava chyby.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co je nového

- Nyní můžete ukládat přihlašovací údaje zdroje dat místně. Přihlašovací údaje jsou šifrované. Pověření ke zdroji dat lze obnovit a obnovit pomocí záložní soubor, který se dá vyexportovat ze existující bránu, všechny místní.

### <a name="enhancements-"></a>Vylepšení-

- Vylepšené a robustnější brány registrace prostředí.
- Podpora automatického zjišťování QuoteChar konfigurace formátu textu v Průvodce kopírováním a zlepšit celkové přesnost detekce formátu.

## <a name="2361002"></a>2.3.6100.2

- Podpora firstRowAsHeader a SkipLineCount automatické zjišťování v Průvodce kopírováním textových souborů v systému souborů na místě a HDFS.
- Zvýšení stability síťové připojení mezi bránou a Service Bus
- Několik oprav chyb


## <a name="2260721"></a>2.2.6072.1

*  Podporuje nastavení proxy serveru HTTP pro bránu pomocí Správce konfigurace brány. Pokud nakonfigurovaný, objektů Blob v Azure, Azure Table, Azure Data Lake a Documentdb jsou přístupné prostřednictvím proxy serveru HTTP.
*  Místní systém souborů podporuje hlavička zpracování TextFormat při kopírování dat z/do objektu Blob Azure, Azure Data Lake Store a místní HDFS.
*  Podporuje kopírování dat z objektu Blob připojit a objektů Blob stránky spolu s již podporované objekt Blob bloku.
*  Zavádí nový stav brány **Online (s omezením)**, což naznačuje, že hlavní funkce brány funguje s výjimkou interaktivní operace podporu pro Průvodce kopírováním.
*  Zvyšuje odolnost registrace brány pomocí registračního klíče.

## <a name="216040"></a>2.1.6040.

*  Ovladač DB2 je nyní obsažena v instalační balíček brány. Není nutné instalovat samostatně.
*  Teď podporuje DB2 ovladače z/OS a DB2 pro i (AS / 400) společně s již podporované platformy (Linux, Unix a Windows).
*  Podporuje používání Azure Cosmos DB jako zdrojový nebo cílový pro místní úložiště dat
*  Podporuje kopírování dat z/do za provozu nebo studený objekt blob úložiště společně s účet již podporované úložiště pro obecné účely.
*  Umožňuje připojení k místnímu SQL serveru prostřednictvím brány s oprávněními vzdálené přihlášení.  

## <a name="2060131"></a>2.0.6013.1

*  Můžete vybrat jazyk nebo jazykovou verzi, která během ruční instalace použít bránu.

*  Pokud brána nebude fungovat podle očekávání, můžete odeslat protokoly gateway posledních sedmi dnů společnosti Microsoft, které usnadňují řešení potíží s problému. Pokud brána není připojená ke cloudové službě, můžete uložit a archivovat protokoly gateway.  

*  Vylepšení uživatelského rozhraní pro správce konfigurace brány:

    *  Nastavit stav brány. na kartě Domů více viditelné.

    *  Ovládací prvky reorganizovány všechny a jednodušší.

    *  Může kopírovat data z úložiště pomocí [nástroj preview bez kódu kopírování](data-factory-copy-data-wizard-tutorial.md). V tématu [připravený kopie](data-factory-copy-activity-performance.md#staged-copy) obecné podrobnosti o této funkci.
*  Brána pro správu dat na vstupní data přímo z místní databáze systému SQL Server můžete do Azure Machine Learning.

*  Vylepšení výkonu

    * Zlepšení výkonu na zobrazení schématu nebo Preview proti systému SQL Server v nástroji preview bez kódu kopírování.

## <a name="11259531"></a>1.12.5953.1

*  Opravy chyb

## <a name="11159181"></a>1.11.5918.1

*  Maximální velikost protokolu událostí brány byla zvýšena z 1 MB na 40 MB.

*  V případě, že během automatickou aktualizaci brány je zapotřebí restartování, zobrazí se dialog s upozorněním. Je možné restartovat právo pak nebo novější.

*  V případě, že automatické aktualizace nezdaří, instalační program brány opakuje, automatické aktualizace třikrát za maximální.

*  Vylepšení výkonu

    * Zlepšení výkonu pro načítání velké tabulky z místního serveru ve scénáři bez kódu kopírování.

*  Opravy chyb

## <a name="11058921"></a>1.10.5892.1

*  Vylepšení výkonu

*  Opravy chyb

## <a name="1958652"></a>1.9.5865.2

*  Nulové funkcí touch automatické aktualizace
*  Ikona nový panelu s indikátory stavu brány
*  Možnost "Teď Update" z klienta
*  Možnost nastavit čas plánu aktualizace
*  Skript prostředí PowerShell pro přepnutím zapnout nebo vypnout automatickou aktualizaci
*  Podpora formátu JSON  
*  Vylepšení výkonu
*  Opravy chyb

## <a name="1858221"></a>1.8.5822.1

*  Lepší řešení potíží
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1757951"></a>1.7.5795.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1757641"></a>1.7.5764.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1657351"></a>1.6.5735.1

*  Podpora místní HDFS zdroj/jímka
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656961"></a>1.6.5696.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656761"></a>1.6.5676.1

*  Diagnostické nástroje podpory na Configuration Manager
*  Podpora sloupců tabulky pro tabulkové zdroje dat pro Azure Data Factory
*  Podpora pro vytváření dat Azure SQL DW
*  Podpora pro vytváření dat Azure Reclusive v BlobSource a FileSource
*  Podpora CopyBehavior – MergeFiles, PreserveHierarchy a FlattenHierarchy v BlobSink a FileSink s binární kopie pro datovou továrnu Azure
*  Podpora aktivity kopírování hlášení průběhu pro Azure Data Factory
*  Ověření připojení zdroje dat podporu pro vytváření dat Azure
*  Opravy chyb

### <a name="1656721"></a>1.6.5672.1

*  Podpora pro Azure Data Factory název tabulky zdroje dat ODBC
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656581"></a>1.6.5658.1

*  Podpora souboru jímky pro datovou továrnu Azure
*  Podpora pro Azure Data Factory zachování hierarchie v binární kopie
*  Podpora idempotenci aktivity kopírování pro vytváření dat Azure
*  Opravy chyb

### <a name="1656401"></a>1.6.5640.1

*  Podpora 3 Další zdroje dat pro Azure Data Factory (rozhraní ODBC, OData, HDFS)
*  Podpora znak uvozovky csv analyzátoru pro Azure Data Factory
*  Podpora komprese (BZip2)
*  Opravy chyb

### <a name="1556121"></a>1.5.5612.1

*  Podpora pět relačních databází pro Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata a Sybase)
*  Podpora komprese (Gzip a Deflate)
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1455491"></a>1.4.5549.1

*  Přidání podpory zdroje dat Oracle pro Azure Data Factory
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1454921"></a>1.4.5492.1

*  Jednotná binární soubor, který podporuje služby Microsoft Azure Data Factory a Office 365 Power BI
*  Upřesnit proces uživatelské rozhraní konfigurace a registrace
*  Azure Data Factory – Azure příchozí a odchozí podporu pro zdroj dat systému SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Opravte problém časový limit pro podporu více připojení ke zdroji dat časově náročná.

### <a name="1155268"></a>1.1.5526.8

*  Vyžaduje rozhraní .NET Framework 4.5.1 předpokladem je během instalace.

### <a name="1051442"></a>1.0.5144.2

*  Žádné změny, které ovlivňují scénáře Azure Data Factory.

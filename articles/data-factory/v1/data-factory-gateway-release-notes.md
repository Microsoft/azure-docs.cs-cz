---
title: Poznámky k verzi pro bránu správy dat | Dokumentace Microsoftu
description: Brána pro správu dat, text poznámky k verzi
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 865bfdae199bca7ebee888be527db239d34511d1
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017612"
---
# <a name="release-notes-for-data-management-gateway"></a>Poznámky k verzi pro Bránu pro správu dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [v místním prostředí integration runtime ve V2](../create-self-hosted-integration-runtime.md).

Jedním z problémů pro integraci dat moderních je k přesunu dat do a z místní do cloudu. Objekt pro vytváření dat díky integraci se Brána pro správu dat, což je místní umožňuje přesun hybridních dat můžete nainstalovat agenta.

Naleznete v následujících článcích pro podrobné informace o bráně pro správu dat a způsobu jeho použití:

*  [Brána správy dat](data-factory-data-management-gateway.md)
*  [Přesun dat mezi místním a cloudovým pomocí Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUÁLNÍ VERZE 
Udržujeme žádné další poznámky k verzi tady. Získat nejnovější poznámky k verzi [zde](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Starší verze
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Vylepšení-
- Můžete přidat položky DNS do seznamu povolených IP adres služby Service bus, nikoli na seznam povolených všechny Azure IP adresy z brány firewall (v případě potřeby). Příslušné položky DNS najdete na webu Azure portal ("Vytvořit a nasadit" -> Data Factory -> "Brány" -> "serviceUrls" (ve formátu JSON)
- HDFS konektor teď podporuje veřejného certifikátu podepsaného svým držitelem tím, že umožňuje přeskočit ověřování SSL.
- Opraveno: Problém s brána offline během aktualizace (z důvodu posun hodin)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Vylepšení-
-   Můžete přidat záznamy DNS na seznam povolených služby Service Bus, spíše než přidávání na seznam povolených všechny Azure IP adresy z brány firewall (v případě potřeby). Další podrobnosti tady.
-   Můžete teď kopírování dat z jednoho bloku blob až 4,75 TB, což je maximální podporovanou velikost objektu blob bloku. (dřívější omezení se 195 GB).
-   Opraveno: Nedostatek paměti problém při rozbalování několik malých souborů při aktivitě kopírování.
-   Opraveno: Index je mimo rozsah problém při kopírování z Document DB na místní SQL serveru s funkcí idempotence.
-   Opraveno: Skript pro vyčištění SQL nefunguje s místním SQL serverem z Průvodce kopírováním.
-   Opraveno: Název sloupce s mezerou na konci nefunguje v aktivitě kopírování.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Vylepšení-
- Opraveno: Problém s nebylo nalezeno pověření. při restartování počítače brány.
- Opraveno: Problém s registrací během brány obnovení ze záložního souboru.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Vylepšení-
- Opraveno: Nesprávný čtení desetinné hodnoty null z Oraclu jako zdroj.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co je nového
- Zákazníci můžou poskytnout zpětnou vazbu na bráně registrace prostředí.
- Podpora nový formát komprese: ZIP (Deflate)

### <a name="enhancements-"></a>Vylepšení-
- Vylepšení výkonu pro Oracle jímky, HDFS zdroje.
- Oprava chyby pro bránu automaticky aktualizovat, paralelní zpracování kapacitu brány.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Vylepšení
- Vylepšené a výkonnější brány registrace prostředí – teď můžete sledovat průběh během procesu registrace brány, které urychluje odezvu registraci prostředí.
- Zlepšení bránu obnovit proces - je stále možné obnovit brány i v případě, že nemáte záložního souboru brány s touto aktualizací. Bude nutné resetovat přihlašovací údaje propojené služby na portálu.
- Oprava chyby.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co je nového

- Nyní můžete ukládat přihlašovací údaje zdroje dat místně. Přihlašovací údaje jsou šifrované. Pověření ke zdroji dat je možné obnovit a obnovit pomocí záložní soubor, který je možné exportovat z existující bránu, všechny místní.

### <a name="enhancements-"></a>Vylepšení-

- Vylepšené a výkonnější brány způsobu registrace.
- Podpora automatického zjišťování quotechar současně konfigurace formátu textu v Průvodce kopírováním a zlepšovat přesnost detekce celkový formát.

## <a name="2361002"></a>2.3.6100.2

- Podpora firstRowAsHeader a SkipLineCount Automatická detekce v Průvodci kopírováním pro textových souborů v místním systému souborů a HDFS.
- Vylepšení stability síťové připojení mezi bránou a Service Bus
- Několik oprav chyb


## <a name="2260721"></a>2.2.6072.1

*  Podporuje nastavení proxy serveru HTTP pro bránu pomocí Správce konfigurace brány. Pokud nakonfigurované, objektů Blob v Azure, Azure Data Lake, tabulky Azure a Documentdb se přistupuje prostřednictvím proxy serveru HTTP.
*  Záhlaví podporuje zpracování TextFormat při kopírování dat z/do objektů Blob v Azure, Azure Data Lake Store, v místním systému souborů a místní HDFS.
*  Podporuje kopírování dat z doplňovací objekt Blob a objekty Blob stránky spolu s již podporovaných objektů Blob bloku.
*  Zavádí nový stav brány **Online (s omezením)**, což znamená, že hlavní funkce brány funguje s výjimkou interaktivní operace podporu pro Průvodce kopírováním.
*  Zvyšuje odolnost registrace brány pomocí registračního klíče.

## <a name="216040"></a>2.1.6040.

*  Ovladač DB2 je součástí instalačního balíčku brány nyní. Nemusíte ho instalovat samostatně.
*  Ovladač DB2 teď podporuje z/OS a DB2 pro mi (AS / 400) společně s už podporované platformy (Linux, Unix a Windows).
*  Podporuje používání služby Azure Cosmos DB jako zdroj nebo cíl pro místní úložiště dat
*  Podporuje kopírování dat z/do horké nebo studené úložiště spolu se účet úložiště pro obecné účely již podporuje objektů blob.
*  Umožňuje připojení k místnímu SQL serveru přes bránu s oprávněními pro vzdálené přihlášení.  

## <a name="2060131"></a>2.0.6013.1

*  Můžete vybrat jazykovou verzi brány používané při ruční instalaci.

*  Pokud brána nebude fungovat podle očekávání, můžete odeslat protokoly brány z posledních sedmi dnů společnosti Microsoft pro usnadnění Poradce při potížích pro problém. Pokud brána není připojená ke cloudové službě, můžete uložit a archivovat protokoly brány.  

*  Vylepšení uživatelského rozhraní pro správce konfigurace brány:

    *  Zkontrolujte stav brány na kartě Domů viditelnější.

    *  Ovládací prvky přeorganizovaným a jednodušší.

    *  Může kopírovat data z úložiště pomocí [nástroj pro kopírování bez použití kódu](data-factory-copy-data-wizard-tutorial.md). Zobrazit [připravené kopírování](data-factory-copy-activity-performance.md#staged-copy) obecné podrobnosti o této funkci.
*  Brána správy dat pro příchozí přenos dat přímo z databáze SQL serveru v místním můžete do Azure Machine Learning.

*  Vylepšení výkonu

    * Zlepšení výkonu na zobrazení schématu/Preview pro SQL Server v nástroj pro kopírování bez použití kódu.

## <a name="11259531"></a>1.12.5953.1

*  Opravy chyb

## <a name="11159181"></a>1.11.5918.1

*  Maximální velikost protokolu událostí brány se zvýšil z 1 MB na 40 MB.

*  V případě, že během automatické aktualizace brány je zapotřebí restartování, zobrazí se dialogové okno upozornění. Je možné restartovat hned potom nebo novější.

*  Automatické aktualizace nezdaří, instalační program brány zopakuje pokus o automatické aktualizace třikrát na maximum.

*  Vylepšení výkonu

    * Zvýšení výkonu pro načítání velkých tabulek z místního serveru ve scénáři kopie bez použití kódu.

*  Opravy chyb

## <a name="11058921"></a>1.10.5892.1

*  Vylepšení výkonu

*  Opravy chyb

## <a name="1958652"></a>1.9.5865.2

*  Nula funkce Automatické aktualizace dotykové ovládání
*  Novou ikonu na hlavním panelu s indikátory stavu brány
*  Možnost "Aktualizace nyní" z klienta
*  Možnost nastavit čas plánované aktualizace
*  Skript prostředí PowerShell pro přepínání automatické aktualizace zapnuto/vypnuto
*  Podpora formátu JSON  
*  Vylepšení výkonu
*  Opravy chyb

## <a name="1858221"></a>1.8.5822.1

*  Vylepšení prostředí pro řešení potíží
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1757951"></a>1.7.5795.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1757641"></a>1.7.5764.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1657351"></a>1.6.5735.1

*  Podpora místních HDFS zdroj/jímka
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656961"></a>1.6.5696.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656761"></a>1.6.5676.1

*  Podpora diagnostické nástroje v Configuration Manageru
*  Podpora sloupců tabulky pro tabulkové zdroje dat pro Azure Data Factory
*  Podpora pro vytváření dat Azure SQL data Warehouse
*  Podpora Reclusive v BlobSource a v tuto chvíli uzamyká pro službu Azure Data Factory
*  Podpora CopyBehavior – MergeFiles, PreserveHierarchy a FlattenHierarchy BlobSink a FileSink binárních kopií pro službu Azure Data Factory
*  Podpora vytváření sestav průběhu pro Azure Data Factory aktivity kopírování
*  Ověření připojení zdroje dat podporu pro Azure Data Factory
*  Opravy chyb

### <a name="1656721"></a>1.6.5672.1

*  Název tabulky podporu pro zdroje dat ODBC pro Azure Data Factory
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656581"></a>1.6.5658.1

*  Pomocný soubor jímky pro službu Azure Data Factory
*  Podpora pro Azure Data Factory zachování hierarchie v binární kopie
*  Podpora služby Azure Data Factory idempotence aktivity kopírování
*  Opravy chyb

### <a name="1656401"></a>1.6.5640.1

*  Podpora 3 Další zdroje dat pro Azure Data Factory (rozhraní ODBC, OData, HDFS)
*  Podpora znak uvozovky v analyzátoru csv pro Azure Data Factory
*  Podpory komprese (BZip2)
*  Opravy chyb

### <a name="1556121"></a>1.5.5612.1

*  Podpora pěti relační databáze pro službu Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata a Sybase)
*  Podpory komprese (Gzip a Deflate)
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1455491"></a>1.4.5549.1

*  Přidat podporu pro zdroje dat Oracle pro Azure Data Factory
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1454921"></a>1.4.5492.1

*  Sjednocené binární soubor, který podporuje služby Microsoft Azure Data Factory a Office 365 Power BI
*  Upřesnění zpracování uživatelského rozhraní konfigurace a registrace
*  Azure Data Factory – Azure příchozí a odchozí podporu pro zdroj dat SQL serveru

### <a name="1253031"></a>1.2.5303.1

*  Oprava potíží časový limit pro podporu další připojení zdrojů dat časově náročné.

### <a name="1155268"></a>1.1.5526.8

*  Během instalace vyžaduje rozhraní .NET Framework 4.5.1 jako předpoklad.

### <a name="1051442"></a>1.0.5144.2

*  Žádné změny, které ovlivňují scénáře služby Azure Data Factory.

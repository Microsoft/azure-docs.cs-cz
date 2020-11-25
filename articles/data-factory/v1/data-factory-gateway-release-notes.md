---
title: Poznámky k verzi pro Bránu pro správu dat
description: Zpráva k vydání verze služby Správa dat Gateway tory
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0538777cadf7935a6684932a17fadc10849518d8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001622"
---
# <a name="release-notes-for-data-management-gateway"></a>Poznámky k verzi pro Bránu pro správu dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Integration runtime v místním prostředí v v2](../create-self-hosted-integration-runtime.md).

Jedním z problémů pro moderní integraci dat je přesun dat do cloudu a z místního prostředí do cloudu. Data Factory provádí tuto integraci s Správa dat bránou, což je agent, který můžete nainstalovat místně a umožnit tak přesun hybridních dat.

Podrobné informace o Správa dat bráně a způsobu jejich použití najdete v následujících článcích:

*  [Brána správy dat](data-factory-data-management-gateway.md)
*  [Přesun dat mezi místním prostředím a cloudem pomocí Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUÁLNÍ VERZE 
Poznámky k verzi tady neuchováváme. [Sem](https://go.microsoft.com/fwlink/?linkid=853077) získáte nejnovější poznámky k verzi.




## <a name="earlier-versions"></a>Starší verze
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Prvky
- Můžete přidat záznamy DNS a povolit tak službě Service Bus místo povolení všech IP adres Azure z brány firewall (Pokud je potřeba). Odpovídající položku DNS najdete na Azure Portal (Data Factory-> ' Author a deploy '-> ' – > "serviceUrls" (ve formátu JSON)
- Služba HDFS Connector teď podporuje veřejný certifikát podepsaný svým držitelem, protože umožňuje přeskočit ověřování TLS.
- Opraveno: problém s bránou v režimu offline během aktualizace (kvůli posunu hodin)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Prvky
-   Můžete přidat položky DNS, které povolí Service Bus místo povolení všech IP adres Azure z brány firewall (Pokud je potřeba). Další podrobnosti najdete tady.
-   Nyní můžete kopírovat data do nebo z jednoho objektu blob bloku až do 4,75 TB, což je maximální podporovaná velikost objektu blob bloku. (předchozí omezení bylo 195 GB).
-   Opraveno: nedostatek paměti při rozzipováváí několika malých souborů během aktivity kopírování.
-   Opraveno: index je mimo rozsah problému při kopírování z databáze dokumentů do SQL Server s funkcí idempotence.
-   Opraveno: skript SQL Cleanup nefunguje s SQL Server z Průvodce kopírováním.
-   Opraveno: název sloupce s mezerou na konci nefunguje v aktivitě kopírování.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Prvky
- Opraveno: problém s chybějícími přihlašovacími údaji při restartování počítače brány.
- Opraveno: problém s registrací při obnovení brány pomocí záložního souboru.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Prvky
- Opraveno: nesprávný čtení desítkové hodnoty null od Oracle jako zdroje.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co je nového
- Zákazníci můžou poskytnout zpětnou vazbu na možnosti registrace brány.
- Podpora nového formátu komprese: ZIP (uprostřed)

### <a name="enhancements-"></a>Prvky
- Vylepšení výkonu pro jímku Oracle, zdroj HDFS.
- Oprava chyby pro automatickou aktualizaci brány, kapacitu paralelního zpracování brány.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Prvky
- Vylepšené a robustnější prostředí pro registraci bran – nyní můžete sledovat stav průběhu během procesu registrace brány. díky tomu bude registrace rychlejší.
- Vylepšení procesu obnovení brány – můžete i nadále obnovit bránu i v případě, že nemáte záložní soubor brány s touto aktualizací. To by vyžadovalo resetovat přihlašovací údaje propojených služeb na portálu.
- Oprava chyby.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co je nového

- Přihlašovací údaje zdroje dat teď můžete ukládat lokálně. Přihlašovací údaje jsou šifrované. Přihlašovací údaje ke zdroji dat se dají obnovit a obnovit pomocí záložního souboru, který se dá exportovat ze stávající brány, a to všechno místně.

### <a name="enhancements-"></a>Prvky

- Vylepšené a robustnější možnosti registrace brány.
- Podpora automatického zjišťování konfigurace QuoteChar současně pro textový formát v průvodci kopírováním a zlepšení celkové přesnosti detekce formátu.

## <a name="2361002"></a>2.3.6100.2

- Podpora automatického zjišťování firstRowAsHeader a SkipLineCount v průvodci kopírováním pro textové soubory v místním systému souborů a HDFS.
- Vylepšení stability síťového připojení mezi bránou a Service Bus
- Několik oprav chyb


## <a name="2260721"></a>2.2.6072.1

*  Podporuje nastavení proxy serveru HTTP pro bránu pomocí Configuration Manager brány. Pokud se nakonfigurují, Azure Blob, tabulka Azure, Azure Data Lake a dokument DB se přistupují prostřednictvím proxy serveru HTTP.
*  Podporuje zpracování hlaviček pro TextFormat při kopírování dat z/do objektů BLOB v Azure, Azure Data Lake Store, místního systému souborů a místního HDFS.
*  Podporuje kopírování dat z připojeného objektu BLOB a objektu blob stránky společně s již podporovaným objektem blob bloku.
*  Zavádí nový stav brány v **režimu online (s omezením)**, což znamená, že hlavní funkce brány funguje s výjimkou podpory interaktivních operací pro Průvodce kopírováním.
*  Vylepšuje odolnost registrace brány pomocí registračního klíče.

## <a name="216040"></a>2.1.6040.

*  Ovladač DB2 je teď součástí instalačního balíčku brány. Nemusíte ho instalovat samostatně.
*  Ovladač DB2 teď podporuje z/OS a DB2 pro i (jako/400) i s již podporovanými platformami (Linux, UNIX a Windows).
*  Podporuje použití Azure Cosmos DB jako zdroje nebo cíle místních úložišť dat.
*  Podporuje kopírování dat z/do studeného a horkého úložiště objektů BLOB spolu s již podporovaným účtem úložiště pro obecné účely.
*  Umožňuje připojení k SQL Server prostřednictvím brány s oprávněními vzdáleného přihlášení.  

## <a name="2060131"></a>2.0.6013.1

*  Můžete vybrat jazyk nebo jazykovou verzi, které brána používá během ruční instalace.

*  Pokud brána nefunguje podle očekávání, můžete se rozhodnout odeslat protokoly brány za posledních sedm dní společnosti Microsoft, aby se usnadnilo řešení problému. Pokud brána není připojená ke cloudové službě, můžete se rozhodnout ukládat a archivovat protokoly brány.  

*  Vylepšení uživatelského rozhraní pro Správce konfigurace brány:

    *  Zviditelnit stav brány na kartě Domů.

    *  Přeorganizováné a zjednodušené ovládací prvky.

    *  Data z úložiště můžete kopírovat pomocí [Nástroje pro kopírování bez kódu](data-factory-copy-data-wizard-tutorial.md). Podrobné informace o této funkci najdete obecně v části [připravené kopírování](data-factory-copy-activity-performance.md#staged-copy) .
*  Bránu Správa dat můžete použít k příchozímu přenosu dat přímo z databáze SQL Server do Azure Machine Learning.

*  Vylepšení výkonu

    * Zvyšte výkon zobrazení schématu nebo verze Preview proti SQL Server v nástroji pro kopírování bez kódu.

## <a name="11259531"></a>1.12.5953.1

*  Opravy chyb

## <a name="11159181"></a>1.11.5918.1

*  Maximální velikost protokolu událostí brány se zvýšila z 1 MB na 40 MB.

*  Zobrazí se dialogové okno s upozorněním v případě, že během automatické aktualizace brány budete potřebovat restartování. Můžete zvolit, že se má restartovat hned a později.

*  V případě, že se automatické aktualizace nezdařily, instalační program brány se automaticky aktualizuje třikrát.

*  Vylepšení výkonu

    * Zvyšte výkon pro načítání velkých tabulek z místního serveru ve scénáři kopírování bez kódu.

*  Opravy chyb

## <a name="11058921"></a>1.10.5892.1

*  Vylepšení výkonu

*  Opravy chyb

## <a name="1958652"></a>1.9.5865.2

*  Možnost Automatické aktualizace nulového dotykového ovládání
*  Ikona nového zásobníku s indikátory stavu brány
*  Možnost "aktualizovat hned" z klienta
*  Možnost nastavit čas plánu aktualizace
*  PowerShellový skript pro automatické aktualizace automatických aktualizací
*  Podpora formátu JSON  
*  Vylepšení výkonu
*  Opravy chyb

## <a name="1858221"></a>1.8.5822.1

*  Zlepšení prostředí pro odstraňování potíží
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1757951"></a>1.7.5795.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1757641"></a>1.7.5764.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1657351"></a>1.6.5735.1

*  Podpora místního zdroje/jímky HDFS
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656961"></a>1.6.5696.1

*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656761"></a>1.6.5676.1

*  Podpora diagnostických nástrojů na Configuration Manager
*  Podpora sloupců tabulek pro tabelární zdroje dat pro Azure Data Factory
*  Podpora Azure synapse Analytics pro Azure Data Factory
*  Podpora Reclusive v BlobSource a zdroji dat pro Azure Data Factory
*  Podpora CopyBehavior – MergeFiles, PreserveHierarchy a FlattenHierarchy v BlobSink a jímka souborů s binární kopií pro Azure Data Factory
*  Podpora průběhu kopírování sestav aktivit pro Azure Data Factory
*  Podpora ověření připojení ke zdroji dat pro Azure Data Factory
*  Opravy chyb

### <a name="1656721"></a>1.6.5672.1

*  Podpora názvu tabulky pro zdroj dat ODBC pro Azure Data Factory
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1656581"></a>1.6.5658.1

*  Podpora jímky souborů pro Azure Data Factory
*  Podpora zachování hierarchie v binární kopii pro Azure Data Factory
*  Podpora aktivity kopírování Idempotence pro Azure Data Factory
*  Opravy chyb

### <a name="1656401"></a>1.6.5640.1

*  Podpora 3 dalších zdrojů dat pro Azure Data Factory (ODBC, OData, HDFS)
*  Podpora znaku citace v analyzátoru sdílených svazků clusteru pro Azure Data Factory
*  Podpora komprese (BZip2)
*  Opravy chyb

### <a name="1556121"></a>1.5.5612.1

*  Podpora pěti relačních databází pro Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata a Sybase)
*  Podpora komprese (gzip a Deflate)
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1455491"></a>1.4.5549.1

*  Přidání podpory zdroje dat Oracle pro Azure Data Factory
*  Vylepšení výkonu
*  Opravy chyb

### <a name="1454921"></a>1.4.5492.1

*  Sjednocený binární soubor, který podporuje Microsoft Azure služby Data Factory a sady Office 365 Power BI
*  Upřesnění uživatelského rozhraní konfigurace a procesu registrace
*  Azure Data Factory – podpora Azure pro příchozí a odchozí přenosy pro SQL Server zdroj dat

### <a name="1253031"></a>1.2.5303.1

*  Oprava potíží s časovým limitem pro podporu více časově náročných připojení ke zdroji dat.

### <a name="1155268"></a>1.1.5526.8

*  Vyžaduje .NET Framework 4.5.1 jako součást při instalaci.

### <a name="1051442"></a>1.0.5144.2

*  Žádné změny, které by ovlivnily Azure Data Factory scénáře.

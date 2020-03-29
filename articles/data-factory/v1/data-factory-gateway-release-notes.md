---
title: Poznámky k verzi pro Bránu pro správu dat
description: Poznámky k verzi brány pro správu dat
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
ms.openlocfilehash: 838e523f74a21c44958ddb6dc88e4dab3526d81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064998"
---
# <a name="release-notes-for-data-management-gateway"></a>Poznámky k verzi pro Bránu pro správu dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, podívejte se na prostředí runtime integrace s [vlastním hostitelem ve Verzi 2](../create-self-hosted-integration-runtime.md).

Jednou z výzev pro moderní integraci dat je přesun dat do a z místního prostředí do cloudu. Data Factory umožňuje tuto integraci s bránou pro správu dat, což je agent, který můžete nainstalovat místně a povolit hybridní přesun dat.

Podrobné informace o bráně pro správu dat a o tom, jak ji používat, naleznete v následujících článcích:

*  [Brána správy dat](data-factory-data-management-gateway.md)
*  [Přesouvání dat mezi místním a cloudem pomocí Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUÁLNÍ VERZE 
My už neudržovat poznámky k verzi zde. Získejte nejnovější poznámky k verzi [zde](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Dřívější verze
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Vylepšení-
- Můžete přidat položky DNS do whitelist service bus spíše než whitelisting všechny IP adresy Azure z brány firewall (v případě potřeby). Příslušnou položku DNS najdete na webu Azure Portal (Data Factory -> "Autor a nasazení" -> 'Brány' -> "serviceUrls" (v JSON)
- Konektor HDFS nyní podporuje veřejný certifikát podepsaný svým držitelem tím, že umožňuje přeskočit ověření TLS.
- Opraveno: Problém s bránou offline během aktualizace (kvůli zkosení hodin)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Vylepšení-
-   Můžete přidat položky DNS do whitelist Service Bus spíše než whitelisting všechny IP adresy Azure z brány firewall (v případě potřeby). Další podrobnosti najdete tady.
-   Nyní můžete kopírovat data do nebo z jednoho bloku blob až 4,75 TB, což je maximální podporovaná velikost objektu blob bloku. (dřívější limit byl 195 GB).
-   Opraveno: Problém s nepaměti při rozpisu několika malých souborů během kopírování.
-   Oprava: Index mimo rozsah problém při kopírování z databáze dokumentů na místní SQL Server s idempotence funkce.
-   Opraveno: Skript vyčištění SQL nefunguje s místním SQL Serverem z Průvodce kopírováním.
-   Opraveno: Název sloupce s mezerou na konci nefunguje v aktivitě kopírování.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Vylepšení-
- Opraveno: Problém s chybějícími přihlašovacími údaji při restartování počítače brány.
- Oprava: Problém s registrací během obnovení brány pomocí záložního souboru.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Vylepšení-
- Oprava: Nesprávné čtení desetinné hodnoty null od společnosti Oracle jako zdroje.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co je nového
- Zákazníci mohou poskytnout zpětnou vazbu o možnostech registrace brány.
- Podpora nového kompresního formátu: ZIP (Deflate)

### <a name="enhancements-"></a>Vylepšení-
- Zlepšení výkonu pro Oracle Sink, zdroj HDFS.
- Oprava chyb pro automatickou aktualizaci brány, paralelní zpracování brány.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Vylepšení
- Vylepšené a robustnější zkušenosti s registrací brány - Nyní můžete sledovat stav průběhu během procesu registrace brány, což činí registrační prostředí citlivější.
- Zlepšení procesu obnovení brány- Stále můžete obnovit bránu i v případě, že nemáte záložní soubor brány s touto aktualizací. To by vyžadovalo obnovení přihlašovacích údajů propojené služby na portálu.
- Oprava chyby.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co je nového

- Nyní můžete ukládat pověření zdroje dat místně. Pověření jsou zašifrována. Pověření zdroje dat lze obnovit a obnovit pomocí záložního souboru, který lze exportovat z existující brány, všechny místní.

### <a name="enhancements-"></a>Vylepšení-

- Vylepšené a robustnější zkušenosti s registrací brány.
- Podporujte automatickou detekci konfigurace QuoteChar pro formát Textu v průvodci kopírováním a zlepšete celkovou přesnost detekce formátu.

## <a name="2361002"></a>2.3.6100.2

- Podpora firstRowAsHeader a SkipLineCount automatické detekce v průvodci kopírováním pro textové soubory v místním systému souborů a HDFS.
- Zvýšení stability síťového připojení mezi bránou a sběrnicí Service Bus
- Několik oprav chyb


## <a name="2260721"></a>2.2.6072.1

*  Podporuje nastavení http proxy pro bránu pomocí Správce konfigurace brány. Pokud je nakonfigurovaný, azure blob, Azure Table, Azure Data Lake a Document DB se přistupují přes http proxy.
*  Podporuje zpracování záhlaví pro TextFormat při kopírování dat z/do objektu Blob Azure, Azure Data Lake Store, místního systému souborů a místního HDFS.
*  Podporuje kopírování dat z objektu blob aplikace Append a objektblob stránky spolu s již podporovaným objektem blob bloku.
*  Zavádí nový stav brány **Online (Limited)**, což znamená, že hlavní funkce brány funguje s výjimkou interaktivní operace podpory pro Copy Wizard.
*  Zvyšuje robustnost registrace brány pomocí registračního klíče.

## <a name="216040"></a>2.1.6040.

*  Ovladač DB2 je nyní součástí instalačního balíčku brány. Nemusíte ho instalovat samostatně.
*  Db2 ovladač nyní podporuje z/OS a DB2 pro i (AS/400) spolu s již podporovanými platformami (Linux, Unix a Windows).
*  Podporuje použití Azure Cosmos DB jako zdroje nebo cíle pro místní úložiště dat
*  Podporuje kopírování dat z úložiště objektů blob za studena/do horkého zařízení spolu s již podporovaným účtem úložiště pro obecné účely.
*  Umožňuje připojení k místnímu serveru SQL Server prostřednictvím brány s oprávněními ke vzdálenému přihlášení.  

## <a name="2060131"></a>2.0.6013.1

*  Můžete vybrat jazyk/jazykovou verzi, kterou má brána používat při ruční instalaci.

*  Pokud brána nefunguje podle očekávání, můžete odeslat protokoly brány za posledních sedm dní společnosti Microsoft, abyste usnadnili řešení potíží s problémem. Pokud brána není připojena ke cloudové službě, můžete uložit a archivovat protokoly brány.  

*  Vylepšení uživatelského rozhraní pro správce konfigurace brány:

    *  Zviditelnění stavu brány na kartě Domů

    *  Reorganizované a zjednodušené ovládací prvky.

    *  Data z úložiště můžete kopírovat pomocí [nástroje pro kopírování bez kódu](data-factory-copy-data-wizard-tutorial.md). Podrobnosti o této funkci obecně naleznete v [tématu Fázovaná kopie.](data-factory-copy-activity-performance.md#staged-copy)
*  Bránu pro správu dat můžete použít k přenosu dat přímo z místní databáze SQL Serveru do Azure Machine Learning.

*  Zlepšení výkonu

    * Zlepšit výkon při zobrazení schématu/náhledu proti SQL Server v nástroji pro kopírování bez kódu.

## <a name="11259531"></a>1.12.5953.1

*  Opravy chyb

## <a name="11159181"></a>1.11.5918.1

*  Maximální velikost protokolu událostí brány byla zvýšena z 1 MB na 40 MB.

*  V případě, že je při automatické aktualizaci brány potřeba restartovat počítač, zobrazí se dialogové okno s upozorněním. Můžete se rozhodnout restartovat hned potom nebo později.

*  V případě, že se automatická aktualizace nezdaří, instalační program brány provede automatickou aktualizaci maximálně třikrát.

*  Zlepšení výkonu

    * Zlepšete výkon pro načítání velkých tabulek z místního serveru ve scénáři kopírování bez kódu.

*  Opravy chyb

## <a name="11058921"></a>1.10.5892.1

*  Zlepšení výkonu

*  Opravy chyb

## <a name="1958652"></a>1.9.5865.2

*  Možnost automatické aktualizace s nulovým dotykem
*  Nová ikona na hlavním panelu s indikátory stavu brány
*  Možnost "Aktualizovat nyní" od klienta
*  Možnost nastavit čas plánu aktualizace
*  Skript prostředí PowerShell pro přepínání automatické aktualizace zapnutí/vypnutí
*  Podpora formátu JSON  
*  Zlepšení výkonu
*  Opravy chyb

## <a name="1858221"></a>1.8.5822.1

*  Zlepšení potíží s odstraňováním potíží
*  Zlepšení výkonu
*  Opravy chyb

### <a name="1757951"></a>1.7.5795.1

*  Zlepšení výkonu
*  Opravy chyb

### <a name="1757641"></a>1.7.5764.1

*  Zlepšení výkonu
*  Opravy chyb

### <a name="1657351"></a>1.6.5735.1

*  Podpora místního zdroje/propadu HDFS
*  Zlepšení výkonu
*  Opravy chyb

### <a name="1656961"></a>1.6.5696.1

*  Zlepšení výkonu
*  Opravy chyb

### <a name="1656761"></a>1.6.5676.1

*  Podpora diagnostických nástrojů ve správci konfigurace
*  Sloupce tabulky podpory pro tabulkové zdroje dat pro Azure Data Factory
*  Podpora SQL DW pro Azure Data Factory
*  Podpora samotáře v blobsource a FileSource pro Azure Data Factory
*  Podpora CopyBehavior – mergefiles, preserveHierarchy a Sloučení hierarchie v BlobSink a FileSink s binární kopírování pro Azure Data Factory
*  Podpora vytváření zpráv o aktivitách kopírování pro Azure Data Factory
*  Podpora ověřování připojení zdroje dat pro Azure Data Factory
*  Opravy chyb

### <a name="1656721"></a>1.6.5672.1

*  Název tabulky podpory pro zdroj dat ODBC pro Azure Data Factory
*  Zlepšení výkonu
*  Opravy chyb

### <a name="1656581"></a>1.6.5658.1

*  Podpora jímky souborů pro Azure Data Factory
*  Podpora zachování hierarchie v binární kopii pro Azure Data Factory
*  Podpora idempotence aktivity kopírování pro Azure Data Factory
*  Opravy chyb

### <a name="1656401"></a>1.6.5640.1

*  Podpora dalších 3 zdrojů dat pro Azure Data Factory (ODBC, OData, HDFS)
*  Znak nabídky podpory v analyzátoru csv pro Azure Data Factory
*  Podpora komprese (BZip2)
*  Opravy chyb

### <a name="1556121"></a>1.5.5612.1

*  Podpora pěti relačních databází pro Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata a Sybase)
*  Podpora komprese (Gzip a Deflate)
*  Zlepšení výkonu
*  Opravy chyb

### <a name="1455491"></a>1.4.5549.1

*  Přidání podpory zdroje dat Oracle pro Azure Data Factory
*  Zlepšení výkonu
*  Opravy chyb

### <a name="1454921"></a>1.4.5492.1

*  Sjednocený binární soubor, který podporuje microsoft azure data factory i služby Office 365 Power BI
*  Upřesnění konfiguračního a registračního procesu
*  Azure Data Factory – podpora Azure Ingress a Egress pro zdroj dat SQL Serveru

### <a name="1253031"></a>1.2.5303.1

*  Opravte problém s časovým časem pro podporu časově náročnějších připojení ke zdrojům dat.

### <a name="1155268"></a>1.1.5526.8

*  Vyžaduje rozhraní .NET Framework 4.5.1 jako předpoklad během instalace.

### <a name="1051442"></a>1.0.5144.2

*  Žádné změny, které ovlivňují scénáře Azure Data Factory.

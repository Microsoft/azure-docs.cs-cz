---
title: Nasazení služby dělení a slučování
description: Použijte také rozdělení sloučení přesunout data mezi rozdělené databáze.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 50dbca0b3a761b72134eaa6cfed57e231be4ef13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421027"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Nasazení služby rozdělení sloučení pro přesun dat mezi rozdělenými databázemi

Nástroj rozdělení sloučení umožňuje přesouvat data mezi rozdělenými databázemi. Viz [Přesouvání dat mezi škálovatenatými cloudovými databázemi](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Stažení balíčků split-merge

1. Stáhněte si nejnovější verzi NuGet z [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

1. Otevřete příkazový řádek a přejděte do adresáře, do kterého jste stáhli soubor nuget.exe. Stahování obsahuje příkazy prostředí PowerShell.

1. Stáhněte si nejnovější balíček Split-Merge do aktuálního adresáře pomocí níže uvedeného příkazu:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Soubory jsou umístěny v adresáři s názvem **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x,** kde *x.x.xxx.x* odráží číslo verze. Vyhledejte soubory služby rozdělení sloučení v podadresáři **content\splitmerge\service** a skripty prostředí Split-Merge PowerShell (a požadované klientské knihovny) v podadresáři **content\splitmerge\powershell.**

## <a name="prerequisites"></a>Požadavky

1. Vytvořte databázi Azure SQL DB, která se použije jako databáze stavu rozdělení sloučení. Přejděte na [portál Azure](https://portal.azure.com). Vytvořte novou **databázi SQL**. Pojmenujte databázi a vytvořte nového správce a heslo. Nezapomeňte zaznamenat jméno a heslo pro pozdější použití.

1. Ujistěte se, že váš server Azure SQL DB umožňuje služby Azure připojení k němu. Na portálu v **nastavení brány firewall**zkontrolujte, zda je nastavení **Povolit přístup ke službám Azure** nastaveno na **zapnuto**. Klikněte na ikonu "uložit".

1. Vytvořte účet úložiště Azure pro výstup diagnostiky.

1. Vytvořte cloudovou službu Azure pro vaši službu split-merge.

## <a name="configure-your-split-merge-service"></a>Konfigurace služby Split-Merge

### <a name="split-merge-service-configuration"></a>Konfigurace služby Split-Merge

1. Ve složce, do které jste stáhli sestavení rozdělené sloučit, vytvořte kopii souboru *ServiceConfiguration.Template.cscfg,* který byl dodán společně s *souborem SplitMergeService.cspkg,* a přejmenujte jej *ServiceConfiguration.cscfg*.

1. Schylujte *ServiceConfiguration.cscfg* v textovém editoru, jako je Visual Studio, který ověřuje vstupy, jako je například formát kryptografických otisků certifikátu.

1. Vytvořte novou databázi nebo zvolte existující databázi, která bude sloužit jako stavová databáze pro operace split-merge a načte se připojovací řetězec této databáze.

   > [!IMPORTANT]
   > V tomto okamžiku musí databáze stavu používat řazení\_latinky (SQL Latin1\_General\_CP1\_CI\_AS). Další informace naleznete v tématu [Název řazení systému Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).

   V azure SQL DB je připojovací řetězec obvykle ve formuláři:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Zadejte tento připojovací řetězec do souboru *.cscfg* v oddílech rolí **SplitMergeWeb** i **SplitMergeWorker** v nastavení ElasticScaleMetadata.

1. Pro roli **SplitMergeWorker** zadejte platný připojovací řetězec do úložiště Azure pro nastavení **WorkerRoleSynchronizationStorageAccountConnectionString.**

### <a name="configure-security"></a>Konfigurace zabezpečení

Podrobné pokyny ke konfiguraci zabezpečení služby naleznete v [konfiguraci zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md).

Pro účely jednoduchého testovacího nasazení pro tento kurz bude provedena minimální sada kroků konfigurace, aby byla služba spuštěna. Tyto kroky umožňují pouze jeden počítač/účet jejich provádění ke komunikaci se službou.

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Vytvořte nový adresář a z tohoto adresáře proveďte následující příkaz pomocí [příkazového řádku pro vývojáře pro aplikaci Visual Studio:](https://msdn.microsoft.com/library/ms229859.aspx)

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Budete požádáni o heslo k ochraně soukromého klíče. Zadejte silné heslo a potvrďte ho. Poté budete vyzváni k použití hesla ještě jednou. Klepnutím na tlačítko **Ano** na konci ji importujete do kořenového úložiště důvěryhodných certifikačních úřadů.

### <a name="create-a-pfx-file"></a>Vytvoření souboru PFX

Proveďte následující příkaz ze stejného okna, kde byl makecert proveden; použijte stejné heslo, které jste použili k vytvoření certifikátu:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Import klientského certifikátu do osobního úložiště

1. V Průzkumníkovi Windows poklepejte na *soubor MyCert.pfx*.
2. V **Průvodci importem certifikátu** vyberte **aktuálního uživatele** a klepněte na tlačítko **Další**.
3. Potvrďte cestu k souboru a klepněte na tlačítko **Další**.
4. Zadejte heslo, nechte **zkontrolovat zahrnout všechny rozšířené vlastnosti** a klepněte na tlačítko **Další**.
5. Ponechat **Automaticky vybrat úložiště certifikátů [...]** zaškrtnuto a klepněte na tlačítko **Další**.
6. Klepněte na **tlačítko Dokončit** a **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Nahrání souboru PFX do cloudové služby

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Vyberte **Možnost Cloudové služby**.
3. Vyberte cloudovou službu, kterou jste vytvořili výše pro službu Rozdělit/sloučit.
4. V horní nabídce klikněte na **Certifikáty.**
5. V dolní liště klikněte na **Nahrát.**
6. Vyberte soubor PFX a zadejte stejné heslo jako výše.
7. Po dokončení zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

### <a name="update-the-service-configuration-file"></a>Aktualizace konfiguračního souboru služby

Vložte kryptografický otisk certifikátu zkopírovaný výše do atributu thumbprint/value těchto nastavení.
Pro roli pracovníka:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Pro webovou roli:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Vezměte prosím na vědomí, že pro produkční nasazení by měly být pro certifikační autoritu použity samostatné certifikáty, pro šifrování, certifikát y serveru a klientské certifikáty. Podrobné pokyny naleznete v tématu [Konfigurace zabezpečení](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Nasazení služby

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Vyberte cloudovou službu, kterou jste vytvořili dříve.
3. Klikněte na **Přehled**.
4. Zvolte pracovní prostředí a klikněte na **Nahrát**.
5. V dialogovém okně zadejte popisek nasazení. Pro oba 'Balíček' a 'Konfigurace', klikněte na 'Z místní' a zvolte *SplitMergeService.cspkg* soubor a cscfg soubor, který jste nakonfigurovali dříve.
6. Ujistěte se, že zaškrtávací políčko s popiskem Nasazení i v případě, že je zaškrtnuto **jednu nebo více rolí, které obsahují jednu instanci.**
7. Stiskněte tlačítko pro zaškrtnutí v pravém dolním rohu a začněte s nasazením. Očekávejte, že to bude trvat několik minut.

## <a name="troubleshoot-the-deployment"></a>Poradce při potížích s nasazením

Pokud se vaše webová role nepodaří přepnout do režimu online, je pravděpodobně problém s konfigurací zabezpečení. Zkontrolujte, zda je ssl nakonfigurován, jak je popsáno výše.

Pokud se role pracovního procesu nepodaří připojit do režimu online, ale vaše webová role je úspěšná, je to s největší pravděpodobností problém s připojením k databázi stavu, kterou jste vytvořili dříve.

- Ujistěte se, že připojovací řetězec ve vašem cscfg je přesný.
- Zkontrolujte, zda existuje server a databáze a zda jsou id uživatele a heslo správné.
- Pro Azure SQL DB by měl být připojovací řetězec ve formuláři:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Ujistěte se, že název serveru nezačíná **https://**.
- Ujistěte se, že váš server Azure SQL DB umožňuje služby Azure připojení k němu. Chcete-li to provést, otevřete databázi na portálu a ujistěte se, že **nastavení Povolit přístup ke službám Azure** je nastaveno na **On****.

## <a name="test-the-service-deployment"></a>Testování nasazení služby

### <a name="connect-with-a-web-browser"></a>Připojení pomocí webového prohlížeče

Určete koncový bod webu služby Split-Merge. Najdete to na portálu tak, že přejdete na **přehled** cloudové služby a podíváte se do **adresy URL webu** na pravé straně. Nahraďte **http://** **https://** protože výchozí nastavení zabezpečení zakáže koncový bod HTTP. Načtěte stránku této adresy URL do prohlížeče.

### <a name="test-with-powershell-scripts"></a>Testování pomocí skriptů prostředí PowerShell

Nasazení a vaše prostředí lze otestovat spuštěním zahrnuté skripty prostředí PowerShell.

Zahrnuté soubory skriptů jsou:

1. *SetupSampleSplitMergeEnvironment.ps1* - nastaví testovací datovou vrstvu pro rozdělení/sloučení (podrobný popis naleznete v tabulce níže).
2. *ExecuteSampleSplitMerge.ps1* - provádí testovací operace na testovací datové vrstvě (podrobný popis naleznete v tabulce níže)
3. *GetMappings.ps1* - ukázkový skript nejvyšší úrovně, který vytiskne aktuální stav mapování šimejdů.
4. *ShardManagement.psm1* - pomocný skript, který zabalí rozhraní API ShardManagement
5. *SqlDatabaseHelpers.psm1* - pomocný skript pro vytváření a správu databází SQL
   
   <table style="width:100%">
     <tr>
       <th>Soubor powershellu</th>
       <th>Kroky</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Vytvoří databázi správce map síchu.</td>
     </tr>
     <tr>
       <td>2. Vytvoří 2 databáze střepů.
     </tr>
     <tr>
       <td>3. Vytvoří mapování střepu pro tyto databáze (odstraní všechny existující mapy úlomků v těchto databázích). </td>
     </tr>
     <tr>
       <td>4. Vytvoří malou ukázkovou tabulku v obou štrůdk a naplní tabulku v jednom z štrůdků.</td>
     </tr>
     <tr>
       <td>5. Deklaruje SchemaInfo pro tabulku se stelivek.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Soubor powershellu</th>
       <th>Kroky</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Odešle požadavek rozdělení do webového front-endu služby Split-Merge Service, který rozdělí polovinu dat z prvního oddílu na druhý oddíl.</td>
     </tr>
     <tr>
       <td>2. Dotazuje webový front-end pro stav požadavku rozdělení a čeká na dokončení požadavku.</td>
     </tr>
     <tr>
       <td>3. Odešle požadavek na sloučení do webového front-endu služby Split-Merge Service, který přesune data z druhého oddílu zpět do prvního oddílu.</td>
     </tr>
     <tr>
       <td>4. Dotazuje webový front-end pro stav požadavku na sloučení a čeká na dokončení požadavku.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Ověření nasazení pomocí PowerShellu

1. Otevřete nové okno PowerShellu a přejděte do adresáře, do kterého jste stáhli balíček Split-Merge, a přejděte do adresáře powershellu.

2. Vytvořte server Azure SQL Database (nebo zvolte existující server), kde se vytvoří správce mapy svižní a oddíly.

   > [!NOTE]
   > Skript *SetupSampleSampleSplitMergeEnvironment.ps1* ve výchozím nastavení vytvoří všechny tyto databáze na stejném serveru, aby byl skript jednoduchý. Nejedná se o omezení samotné služby split-merge.

   Pro službu Split-Merge bude potřeba přihlášení ověřování SQL s přístupem pro čtení a zápis do dbs k přesunutí dat a aktualizaci mapy svižného modulu. Vzhledem k tomu, že služba Split-Merge běží v cloudu, aktuálně nepodporuje integrované ověřování.

   Ujistěte se, že je server Azure SQL nakonfigurovaný tak, aby umožňoval přístup z IP adresy počítače se spuštěnou těmito skripty. Toto nastavení najdete v části Azure SQL server / konfigurace / povolené IP adresy.

3. Spusťte skript *SetupSampleSplitMergeEnvironment.ps1* a vytvořte ukázkové prostředí.

   Spuštění tohoto skriptu vyhladí všechny existující struktury dat správy map sypů v databázi správce map skřípků a úlomků. Může být užitečné znovu spustit skript, pokud chcete znovu inicializovat mapu šifrovacího oddílu nebo štřepů.

   Ukázkový příkazový řádek:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Spusťte skript Getmappings.ps1 a zobrazte mapování, která aktuálně existují v ukázkovém prostředí.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Spusťte skript *ExecuteSampleSplitMerge.ps1* k provedení operace rozdělení (přesunutí poloviny dat na první úlomek do druhého střepu) a potom operace sloučení (přesunutí dat zpět na první úlomek). Pokud jste nakonfigurovali protokol SSL a ponechali koncový bod http zakázaný, ujistěte se, že místo toho použijete koncový bod https://.

   Ukázkový příkazový řádek:

   ```cmd
   .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' 
    -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```

   Pokud se zobrazí níže uvedená chyba, je to s největší pravděpodobností problém s certifikátem koncového bodu webu. Zkuste se připojit k koncovému webovému bodu pomocí oblíbeného webového prohlížeče a zkontrolujte, zda nedošlo k chybě certifikátu.

     `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

   Pokud se to podařilo, výstup by měl vypadat takto:

   ```output
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```

6. Experimentujte s jinými datovými typy! Všechny tyto skripty trvat volitelný parametr -ShardKeyType, který umožňuje zadat typ klíče. Výchozí hodnota je Int32, ale můžete také zadat Int64, Guid nebo Binary.

## <a name="create-requests"></a>Vytváření požadavků

Službu lze použít buď pomocí webového uživatelského rozhraní nebo importem a použitím modulu SplitMerge.psm1 PowerShell, který odešle vaše požadavky prostřednictvím webové role.

Služba může přesunout data v tabulce s oddíly a referenční tabulky. Tabulka s oddíly je vybavena klíčem pro seser a má na každém oddílu různá data řádků. Referenční tabulka není oddílová, takže obsahuje stejná data řádků na každém úlovku. Referenční tabulky jsou užitečné pro data, která se nemění často a slouží ke spojení s tabulkami s oddíly v dotazech.

Chcete-li provést operaci rozdělení sloučení, musíte deklarovat rozdělení tabulky a referenční tabulky, které chcete přesunout. To se provádí pomocí rozhraní **SchemaInfo** API. Toto rozhraní API je v oboru názvů **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema.**

1. Pro každou rozdělenou tabulku vytvořte objekt **ShardedTableInfo** popisující název nadřazeného schématu tabulky (volitelné, výchozí hodnota "dbo"), název tabulky a název sloupce v této tabulce, který obsahuje klíč pro šiřidlo.
2. Pro každou referenční tabulku vytvořte objekt **ReferenceTableInfo** popisující název nadřazeného schématu tabulky (volitelné, výchozí hodnoty "dbo") a název tabulky.
3. Přidejte výše uvedené objekty TableInfo do nového objektu **SchemaInfo.**
4. Získejte odkaz na objekt **ShardMapManager** a zavolejte **GetSchemaInfoCollection**.
5. Přidejte **SchemaInfo** do **schemaInfoCollection**, poskytuje název mapy oddílu.

Příkladem toho lze vidět ve skriptu SetupSampleSplitMergeEnvironment.ps1.

Služba Split-Merge nevytvoří cílovou databázi (nebo schéma pro všechny tabulky v databázi) pro vás. Musí být předem vytvořeny před odesláním požadavku do služby.

## <a name="troubleshooting"></a>Řešení potíží

Při spuštění ukázkových skriptů powershellu se může zobrazit následující zpráva:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Tato chyba znamená, že certifikát SSL není správně nakonfigurován. Postupujte podle pokynů v části "Připojení k webovému prohlížeči".

Pokud nemůžete odeslat žádosti, můžete vidět toto:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

V takovém případě zkontrolujte konfigurační soubor, zejména nastavení **WorkerRoleSynchronizationStorageAccountConnectionString**. Tato chyba obvykle označuje, že role pracovního procesu nelze úspěšně inicializovat databázi metadat při prvním použití.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

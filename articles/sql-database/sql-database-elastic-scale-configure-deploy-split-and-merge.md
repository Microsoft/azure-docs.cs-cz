---
title: Nasazení služby dělení a slučování | Dokumentace Microsoftu
description: Použití dělení a slučování příliš k přesouvání dat mezi horizontálně dělené databáze.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 253a50f50e94888a5d764e51a4ff49cc0c1424c6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253529"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Nasazení služby dělení a slučování pro přesun dat mezi horizontálně dělené databáze

Nástroj split-merge umožňuje přesun dat mezi horizontálně dělené databáze. Zobrazit [přesouvá data mezi databázemi s horizontálním navýšením kapacity](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Stáhnout balíčky dělení a slučování
1. Stažení nejnovější verze NuGet z [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Otevřete příkazový řádek a přejděte do adresáře, kam jste stáhli nuget.exe. Soubor ke stažení obsahuje příkazy prostředí PowerShell.
3. Stáhněte si nejnovější balíček dělení a slučování do aktuálního adresáře se následující příkaz:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Soubory jsou umístěny v adresáři s názvem **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** kde *x.x.xxx.x* zobrazuje číslo verze. Najít soubory služby dělení a slučování v **content\splitmerge\service** podadresáře a skripty Powershellu pro dělení a slučování (a klienta knihovny DLL) v **content\splitmerge\powershell** podadresáře.

## <a name="prerequisites"></a>Požadavky
1. Vytvoření databáze Azure SQL DB, který se použije jako dělení a slučování stav databáze. Přejděte na [Azure Portal](https://portal.azure.com). Vytvořte nový **SQL Database**. Pojmenujte databázi a vytvoření nového správce a hesla. Nezapomeňte někam jméno a heslo pro pozdější použití.
2. Ujistěte se, že váš server Azure SQL DB umožňuje služby Azure se k němu připojit. Na portálu v **nastavení brány Firewall**, zkontrolujte **povolit přístup ke službám Azure** nastavená na **na**. Klikněte na ikonu "save".
3. Vytvoření účtu služby Azure Storage pro diagnostický výstup.
4. Vytvoření cloudové služby Azure pro vaši službu dělení a slučování.

## <a name="configure-your-split-merge-service"></a>Konfigurace služby dělení a slučování
### <a name="split-merge-service-configuration"></a>Konfigurace služby dělení a slučování
1. Ve složce, do které jste si stáhli dělení a slučování sestavení, vytvořte kopii **ServiceConfiguration.Template.cscfg** souboru, dodávané spolu s **SplitMergeService.cspkg** a přejmenujte jej na **Souboru ServiceConfiguration.cscfg**.
2. Otevřít **souboru ServiceConfiguration.cscfg** v textovém editoru, jako je Visual Studio, která ověřuje vstupů, jako je formát kryptografické otisky certifikátu.
3. Vytvořit novou databázi nebo vyberte existující databázi a slouží jako stav database pro dělení a slučování operace načtení připojovacího řetězce databáze. 
   
   > [!IMPORTANT]
   > V tuto chvíli stav databáze musí používat kolaci latinky (SQL\_Latin1\_Obecné\_CP1\_CI\_AS). Další informace najdete v tématu [název kolace Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Pomocí služby Azure SQL DB připojovací řetězec je obvykle ve formátu:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Zadejte tento připojovací řetězec v souboru cscfg v obou **SplitMergeWeb** a **SplitMergeWorker** oddílů role v nastavení ElasticScaleMetadata.
5. Pro **SplitMergeWorker** role, zadejte platný připojovací řetězec do úložiště Azure pro **WorkerRoleSynchronizationStorageAccountConnectionString** nastavení.

### <a name="configure-security"></a>Konfigurace zabezpečení
Podrobné pokyny ke konfiguraci zabezpečení služby, najdete [konfigurace zabezpečení dělení a slučování](sql-database-elastic-scale-split-merge-security-configuration.md).

Pro účely jednoduchá testovací nasazení pro účely tohoto kurzu minimální sadu kroků konfigurace bude provedena zprovoznit službu a spuštěné. Tyto kroky aktivují pouze na jeden počítač/účet spouštějící, aby mohla komunikovat se službou.

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Vytvořte nový adresář a z tohoto adresáře spusťte následující příkaz using [Developer Command Prompt pro sadu Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) okno:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Zobrazí se výzva k zadání hesla k ochraně soukromého klíče. Zadejte silné heslo a potvrďte ho. Zobrazí se výzva k zadání hesla pro použití po tomto ještě jednou. Klikněte na tlačítko **Ano** na konci a importovat ho do úložiště Důvěryhodné kořenové certifikační autority.

### <a name="create-a-pfx-file"></a>Vytvoření souboru PFX
Spusťte následující příkaz z téhož okna, ve kterém byl spuštěn nástroj makecert; Použijte stejné heslo, které jste použili k vytvoření certifikátu:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importujte klientský certifikát do osobního úložiště
1. V Průzkumníku Windows poklepejte na **MyCert.pfx**.
2. V **Průvodce importem certifikátu** vyberte **aktuálního uživatele** a klikněte na tlačítko **Další**.
3. Zkontrolujte cestu k souboru a klikněte na tlačítko **Další**.
4. Zadejte heslo, nechte **zahrnout všechny rozšířené vlastnosti** zaškrtnuto a klikněte na tlačítko **Další**.
5. Ponechte **automaticky vybrat úložiště certifikátů [...]**  zaškrtnuto a klikněte na tlačítko **Další**.
6. Klikněte na tlačítko **Dokončit** a **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Nahrajte soubor PFX do cloudové služby
1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte **cloudových služeb**.
3. Vyberte cloudovou službu, kterou jste vytvořili výše pro služby dělení a slučování.
4. Klikněte na tlačítko **certifikáty** v horní nabídce.
5. Klikněte na tlačítko **nahrát** na dolním panelu.
6. Vyberte soubor PFX a zadejte stejné heslo, jak je uvedeno výše.
7. Po dokončení, zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

### <a name="update-the-service-configuration-file"></a>Aktualizovat konfigurační soubor služby
Vložte kryptografický otisk certifikátu nad zkopírována kryptografický otisk nebo hodnotu atributu z těchto nastavení.
Pro roli pracovního procesu:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Pro webovou roli:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Všimněte si, že pro produkční nasazení samostatné certifikáty by měla sloužit prosím pro certifikační Autoritu pro šifrování, certifikát serveru a klientské certifikáty. Podrobné pokyny najdete v části [konfigurace zabezpečení](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Nasazení služby
1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Vyberte cloudovou službu, kterou jste vytvořili dříve.
3. Klikněte na **Přehled**.
4. Zvolte testovací prostředí a potom klikněte na tlačítko **nahrát**.
5. V dialogovém okně zadejte označení nasazení. "Balíček" a "Konfigurace", klikněte na tlačítko "Z místního" a zvolte **SplitMergeService.cspkg** souboru a souboru cscfg, který jste nakonfigurovali v předchozích krocích.
6. Ujistěte se, že zaškrtávací políčko **nasadit, i když jedna nebo víc rolí obsahuje jednu instanci** je zaškrtnuté políčko.
7. Klikněte na tlačítko značek v pravém dolním spusťte nasazení. Očekávají, že bude trvat několik minut.


## <a name="troubleshoot-the-deployment"></a>Řešení potíží s nasazení
Pokud vaše webová role selže do režimu online, je pravděpodobně problém s konfigurací zabezpečení. Zkontrolujte, jestli je nakonfigurovaný protokol SSL, jak je popsáno výše.

Pokud své role pracovního procesu selže do režimu online, ale webová role proběhne úspěšně, je pravděpodobně potíže s připojením k databázi stav, který jste vytvořili dříve.

* Ujistěte se, že je připojovací řetězec vaší cscfg přesné.
* Zkontrolujte, jestli server a databáze existuje, a id uživatele a heslo jsou správné.
* Pro službu Azure SQL DB připojovací řetězec by měl být ve tvaru:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Ujistěte se, že název serveru nezačíná **https://**.
* Ujistěte se, že váš server Azure SQL DB umožňuje služby Azure se k němu připojit. Chcete-li to provést, otevřete databázi na portálu a ověřte, že **povolit přístup ke službám Azure** nastavená na ** na ***.

## <a name="test-the-service-deployment"></a>Testování nasazení služby
### <a name="connect-with-a-web-browser"></a>Připojit pomocí webového prohlížeče
Zjistěte koncový bod webové služby dělení a slučování. Zjistíte to v portálu tak, že přejdete **přehled** cloudové služby a podíváte se na **adresa URL webu** na pravé straně. Nahraďte **http://** s **https://** vzhledem k tomu, že výchozí nastavení zabezpečení zakázat koncový bod HTTP. Načtení stránky pro tuto adresu URL do prohlížeče.

### <a name="test-with-powershell-scripts"></a>Testování pomocí skriptů prostředí PowerShell
Nasazení a prostředí můžete otestovat spuštěním zahrnuté ukázkové skripty Powershellu.

Zahrnuté soubory skriptů jsou:

1. **SetupSampleSplitMergeEnvironment.ps1** – nastaví testovací datovou vrstvu pro dělení a slučování (viz níže uvedené tabulce podrobný popis)
2. **ExecuteSampleSplitMerge.ps1** – provádí test operace na testovací datové vrstvy (viz níže uvedené tabulce podrobný popis)
3. **GetMappings.ps1** – nejvyšší úrovně ukázkový skript, který vytiskne aktuální stav mapování horizontálních oddílů.
4. **ShardManagement.psm1** – pomocné rutiny skriptů, které zabaluje rozhraní API ShardManagement
5. **SqlDatabaseHelpers.psm1** – pomocné rutiny skript pro vytvoření a Správa databází SQL Database
   
   <table style="width:100%">
     <tr>
       <th>Soubor prostředí PowerShell</th>
       <th>Kroky</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Vytvoří databáze správce mapování horizontálních oddílů</td>
     </tr>
     <tr>
       <td>2.    Vytvoří 2 databázemi s horizontálními oddíly.
     </tr>
     <tr>
       <td>3.    Vytvoří mapy horizontálních oddílů pro tyto databáze (odstraní všechny existující horizontálního oddílu mapování nad těmito databázemi). </td>
     </tr>
     <tr>
       <td>4.    Vytvoří malý ukázkovou tabulku v obou horizontální oddíly a naplňuje tabulka v jednom z horizontální oddíly.</td>
     </tr>
     <tr>
       <td>5.    Deklaruje položka SchemaInfo pro horizontálně dělenou tabulku.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Soubor prostředí PowerShell</th>
       <th>Kroky</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Odešle žádost rozdělit na webový front-end služby dělení a slučování, které rozdělí poloviční data z první horizontálních oddílů do druhého horizontálního oddílu.</td>
     </tr>
     <tr>
       <td>2.    Webový front-end pro dělené stav žádosti o dotazování a počká na dokončení žádosti.</td>
     </tr>
     <tr>
       <td>3.    Odešle požadavek sloučení služby dělení a slučování webový front-end, který přesouvá data z druhé horizontálních oddílů zpět do první horizontálního oddílu.</td>
     </tr>
     <tr>
       <td>4.    Dotazuje webového front-endu pro stav žádosti o sloučení a počká na dokončení žádosti.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Použití Powershellu k ověření nasazení
1. Otevřete nové okno Powershellu a přejděte do adresáře, kam jste stáhli balíček dělení a slučování a pak přejděte do adresáře "powershell".
2. Vytvoření serveru Azure SQL database (nebo zvolte existující server) kde bude vytvořen správce mapování horizontálních oddílů a horizontálních oddílů.
   
   > [!NOTE]
   > Ve výchozím nastavení pro zjednodušení tento skript vytvoří skript SetupSampleSplitMergeEnvironment.ps1 těchto databází na stejném serveru. Toto není omezení služby dělení a slučování samotný.
   >
   
   Přihlašovací jméno ověřování SQL s přístupem pro čtení a zápis k databáze bude potřebovat služby dělení a slučování, přesun dat a aktualizovat mapy horizontálních oddílů. Služby dělení a slučování běží v cloudu, nepodporuje aktuálně integrované ověřování.
   
   Ujistěte se, že server Azure SQL je nakonfigurována pro povolení přístupu z IP adresy v daném počítači používají tyto skripty. Tato nastavení v rámci serveru Azure SQL najdete / configuration / povolené IP adresy.
3. Spusťte skript SetupSampleSplitMergeEnvironment.ps1 k vytvoření ukázkové prostředí.
   
   Spuštěním tohoto skriptu bude vymazat všechny existující data správy mapování horizontálních oddílů struktury databáze správce mapování horizontálních oddílů a horizontální oddíly. Může být užitečné skript spustit znovu, pokud chcete znovu inicializovat mapy horizontálních oddílů nebo horizontálních oddílů.
   
   Ukázka příkazového řádku:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Spusťte skript Getmappings.ps1, chcete-li zobrazit mapování, které momentálně existují v ukázkové prostředí.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Spusťte skript ExecuteSampleSplitMerge.ps1 spustit operaci dělení (přesunutí poloviční dat na první horizontálních oddílů do druhé horizontální oddíl) a pak operaci sloučení (přesunutí dat zpět do první horizontální oddíl). Pokud jste nakonfigurovali SSL a left koncový bod http, které jsou zakázané, ujistěte se, že je použít koncový bod https://.
   
   Ukázka příkazového řádku:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Pokud se zobrazí následující chyba, je pravděpodobně problém s certifikátem váš koncový bod webové. Zkuste se připojit ke koncovému bodu webové s oblíbený webový prohlížeč a zkontrolujte, jestli je chyba certifikátu.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Pokud byla úspěšná, by měla vypadat podobně jako následující:
   
   ```
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
6. Experimentujte s jinými datovými typy! Provést všechny tyto skripty volitelného parametru - ShardKeyType, který vám umožní určit typ klíče. Výchozí hodnota je datový typ Int32, ale můžete také určit, Int64, Guid nebo binárního souboru.

## <a name="create-requests"></a>Požadavky na vytvoření
Službu je možné pomocí webového uživatelského rozhraní nebo importováním a pomocí modulu SplitMerge.psm1 PowerShell, které se odesílají vaše žádosti přes webové role.

Služby můžou přesouvat data v horizontálně dělené tabulky a referenční tabulky. Horizontálně dělenou tabulku má sloupec klíče horizontálního dělení a má odlišný řádek dat v jednotlivých horizontálních oddílů. Referenční tabulky není horizontálně dělené tak, aby obsahovala stejné dat řádek na každý horizontální oddíl. Referenční tabulky jsou užitečné pro data, která se nemění často a slouží ke spojení s horizontálně dělené tabulky v dotazech.

Aby bylo možné provést operaci dělení a slučování, je třeba deklarovat horizontálně dělené tabulky a referenční tabulky, které mají být přesunuty. Toho se dosahuje pomocí **položka SchemaInfo** rozhraní API. Toto rozhraní API je v **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** oboru názvů.

1. Pro každou horizontálně dělenou tabulku vytvořte **ShardedTableInfo** objekt popisující název schématu nadřazené tabulky (volitelné, výchozí hodnota je "dbo"), název tabulky a název sloupce v tabulce, který obsahuje klíč horizontálního dělení.
2. Pro každý odkaz na tabulku vytvořte **ReferenceTableInfo** objekt popisující název schématu nadřazené tabulky (volitelné, výchozí hodnota je "dbo") a název tabulky.
3. Přidání výše uvedených objektů TableInfo do nového **položka SchemaInfo** objektu.
4. Získání odkazu na **ShardMapManager** objektu a volání **GetSchemaInfoCollection**.
5. Přidat **položka SchemaInfo** k **SchemaInfoCollection**, že zadáte název mapy horizontálních oddílů.

Příklad si můžete prohlédnout ve skriptu SetupSampleSplitMergeEnvironment.ps1.

Služby dělení a slučování nevytváří cílová databáze (nebo schéma pro všechny tabulky v databázi) za vás. Musí být předem vytvořené před odesláním požadavku do služby.

## <a name="troubleshooting"></a>Řešení potíží
Může se zobrazit pod zprávu při spuštění ukázkové skripty powershellu:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Tato chyba znamená, že svůj certifikát SSL není správně nakonfigurován. Postupujte podle pokynů v části "Připojení s webovým prohlížečem,".

Pokud nelze odeslat žádosti se může zobrazit toto:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Zkontrolujte v tomto případě konfiguračním souboru, zejména nastavení **WorkerRoleSynchronizationStorageAccountConnectionString**. Tato chyba obvykle značí, že role pracovního procesu nebyl správně spuštěn metadata databáze při prvním použití. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png


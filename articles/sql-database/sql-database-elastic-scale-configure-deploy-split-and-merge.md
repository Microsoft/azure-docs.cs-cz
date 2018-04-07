---
title: Nasazení služby rozdělení sloučení | Microsoft Docs
description: Pomocí rozdělení sloučení příliš pro přesun dat mezi horizontálně dělené databázemi.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 90f758bf5bc979dc4bc173b08dadaceeaa077317
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-a-split-merge-service"></a>Nasazení služby dělení a slučování
Nástroj pro rozdělení sloučení umožňuje přesun dat mezi horizontálně dělené databáze. V tématu [přesouvání dat mezi instancemi cloudu databáze](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Stáhnout balíčky rozdělení sloučení
1. Stažení nejnovější verze NuGet z [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Otevřete příkazový řádek a přejděte do adresáře, kam jste stáhli nuget.exe. Stahování obsahuje příkazy prostředí PowerShell.
3. Stáhněte si nejnovější balíček sloučení rozdělení do aktuální adresář se následující příkaz:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Soubory jsou umístěny v adresáři s názvem **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** kde *x.x.xxx.x* zobrazuje číslo verze. Hledání souborů služby rozdělení sloučení v **content\splitmerge\service** podadresář a skriptů prostředí PowerShell rozdělení sloučení (a knihovny DLL klienta) v **content\splitmerge\powershell** podadresář.

## <a name="prerequisites"></a>Požadavky
1. Vytvořte databázi Azure SQL DB, který se použije jako rozdělení sloučení stavu databáze. Přejděte na [portál Azure](https://portal.azure.com). Vytvořte novou **SQL Database**. Zadejte název databáze a vytvořte nový správce a heslo. Ujistěte se, že záznam jméno a heslo pro pozdější použití.
2. Zajistěte, aby váš server Azure SQL DB povolovalo Azure Services pro připojení k němu. Na portálu v **nastavení brány Firewall**, zkontrolujte **povolit přístup ke službám Azure** nastavení je **na**. Klikněte na ikonu "uložit".
   
   ![Povolené služby][1]
3. Vytvoření účtu úložiště Azure, který se použije pro výstup diagnostiky. Přejděte na portálu Azure. V levém panelu klikněte na **vytvořit prostředek**, klikněte na tlačítko **Data + úložiště**, pak **úložiště**.
4. Vytvoření cloudové služby Azure, která bude obsahovat služby rozdělení sloučení.  Přejděte na portálu Azure. V levém panelu klikněte na **vytvořit prostředek**, pak **výpočetní**, **Cloudová služba**, a **vytvořit**. 

## <a name="configure-your-split-merge-service"></a>Konfigurace služby rozdělení sloučení
### <a name="split-merge-service-configuration"></a>Konfigurace služby rozdělení sloučení
1. Ve složce, do které jste stáhli sestavení rozdělení sloučení, vytvořit kopii **ServiceConfiguration.Template.cscfg** soubor, který se dodává spolu s **SplitMergeService.cspkg** a přejmenujte ji **Souboru ServiceConfiguration.cscfg**.
2. Otevřete **souboru ServiceConfiguration.cscfg** v textovém editoru, jako je například Visual Studio, která ověřuje vstupy například formát kryptografické otisky certifikátu.
3. Vytvořit novou databázi nebo vyberte existující databázi sloužit jako databázi stavu pro operace sloučení rozdělení a načtení připojovacího řetězce databáze. 
   
   > [!IMPORTANT]
   > V tomto okamžiku stav databáze musí používat Latin kolace (SQL\_Latin1\_Obecné\_CP1\_CI\_AS). Další informace najdete v tématu [název kolace systému Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   S Azure SQL DB připojovací řetězec je obvykle ve formátu:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Zadejte tento připojovací řetězec v soubor .cscfg v obou **SplitMergeWeb** a **SplitMergeWorker** role části ElasticScaleMetadata nastavení.
5. Pro **SplitMergeWorker** role, zadejte platný připojovací řetězec do úložiště Azure pro **WorkerRoleSynchronizationStorageAccountConnectionString** nastavení.

### <a name="configure-security"></a>Konfigurace zabezpečení
Podrobné pokyny ke konfiguraci zabezpečení služby, najdete v části [konfigurace zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md).

Pro účely jednoduchá testovací nasazení pro tento kurz minimální sadu konfiguračních kroků bude provádět ke zprovoznění služby a spuštěná. Tyto kroky aktivují jenom jeden nebo účet počítače, aby mohla komunikovat se službou provádění.

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem
Vytvořte nový adresář a z tohoto adresáře spusťte následující příkaz pomocí [příkazový řádek vývojáře pro sadu Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) okno:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Zobrazí se výzva k zadání hesla k ochraně soukromého klíče. Zadejte silné heslo a potvrďte ho. Zobrazí se výzva k zadání hesla pro použití jednou po který. Klikněte na tlačítko **Ano** na konci ho importovat do úložiště Důvěryhodné kořenové certifikační autority.

### <a name="create-a-pfx-file"></a>Vytvořte soubor PFX
Spusťte následující příkaz z téhož okna, kde byl proveden makecert; Použijte stejné heslo, které jste použili k vytvoření certifikátu:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importujte certifikát klienta do osobního úložiště
1. V Průzkumníku Windows, klikněte dvakrát na **MyCert.pfx**.
2. V **Průvodce importem certifikátu** vyberte **aktuální uživatel** a klikněte na tlačítko **Další**.
3. Zkontrolujte cestu k souboru a klikněte na **Další**.
4. Zadejte heslo, nechte **obsahovat všechny rozšířené vlastnosti** zaškrtnutí a klikněte na tlačítko **Další**.
5. Nechte **automaticky vybrat úložiště certifikátů [...]**  zaškrtnutí a klikněte na tlačítko **Další**.
6. Klikněte na tlačítko **Dokončit** a **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Nahrát soubor PFX do cloudové služby
1. Přejděte na [portál Azure](https://portal.azure.com).
2. Vyberte **cloudových služeb**.
3. Vyberte cloudovou službu, kterou jste vytvořili výše pro službu rozdělení či sloučení.
4. Klikněte na tlačítko **certifikáty** v horní nabídce.
5. Klikněte na tlačítko **nahrát** v dolním panelu.
6. Vyberte soubor PFX a zadejte stejné heslo, jak je uvedeno výše.
7. Po dokončení, zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

### <a name="update-the-service-configuration-file"></a>Aktualizovat konfigurační soubor služby
Vložte kryptografický otisk certifikátu výše zkopírují do atribut kryptografický otisk nebo hodnotu z těchto nastavení.
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

Všimněte si, že pro produkční nasazení samostatné certifikáty prosím by měl použít pro certifikační Autoritu pro šifrování, certifikát serveru a klientské certifikáty. Podrobné pokyny v tomto najdete v tématu [konfigurace zabezpečení](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Nasazení služby
1. Přejděte na [portál Azure](https://manage.windowsazure.com).
2. Klikněte **cloudové služby** na levé straně a vyberte cloudovou službu, kterou jste vytvořili dříve.
3. Klikněte na tlačítko **řídicí panel**.
4. Vyberte pracovní prostředí a potom klikněte na **nahrát nový pracovní nasazení**.
   
   ![Fázování][3]
5. V dialogovém okně zadejte označení nasazení. Pro 'Balíček' i 'konfiguraci, klikněte na tlačítko 'Z Local' a vyberte **SplitMergeService.cspkg** soubor a váš soubor .cscfg, který jste nakonfigurovali dříve.
6. Ujistěte se, že na zaškrtávací políčko s názvem bez přípony **nasadit, i když jedna nebo víc rolí obsahuje jednu instanci** je zaškrtnuté.
7. Klikněte na tlačítko značek v pravém dolním zahájíte nasazení. Jeho trvat několik minut na dokončení očekávejte.

   ![Odeslat][4]

## <a name="troubleshoot-the-deployment"></a>Řešení potíží s nasazení
Pokud vaši webovou roli selže do režimu online, je pravděpodobně problém s konfigurací zabezpečení. Zkontrolujte, jestli je nakonfigurované SSL, jak je popsáno výše.

Pokud své role pracovního procesu selže do režimu online, ale vaši webovou roli úspěšné, je pravděpodobně problém s připojením k databázi stav, který jste vytvořili dříve.

* Ujistěte se, že je připojovací řetězec do vaší cscfg přesná.
* Zkontrolujte, zda server a databázi existují, a zda jsou správné id uživatele a heslo.
* Pro databáze SQL Azure musí mít připojovací řetězec ve tvaru:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Ujistěte se, že název serveru nemá na začátku **https://**.
* Zajistěte, aby váš server Azure SQL DB povolovalo Azure Services pro připojení k němu. Chcete-li to provést, otevřete https://manage.windowsazure.comna levé straně klikněte na tlačítko "Databází SQL", klikněte na tlačítko "Servery" v horní části a vyberte svůj server. Klikněte na tlačítko **konfigurace** v horní části a ujistěte se, že **služeb Azure** nastavení je "Ano". (Viz část požadavky v horní části v tomto článku).

## <a name="test-the-service-deployment"></a>Testovací nasazení služby
### <a name="connect-with-a-web-browser"></a>Připojení s webovým prohlížečem
Zjistěte koncový bod webové služby rozdělení sloučení. Tento nástroj naleznete na portálu Azure classic přechodem na **řídicí panel** cloudové služby a vyhledávání v části **adresa URL webu** na pravé straně. Nahraďte **http://** s **https://** vzhledem k tomu, že výchozí nastavení zabezpečení zakázat koncový bod HTTP. Načtení stránky pro tuto adresu URL do prohlížeče.

### <a name="test-with-powershell-scripts"></a>Testování pomocí skriptů prostředí PowerShell
Nasazení a prostředí může být testována spuštěním zahrnuty vzorové skripty prostředí PowerShell.

Zahrnuté soubory skriptů jsou:

1. **SetupSampleSplitMergeEnvironment.ps1** -nastaví datovou vrstvu testu pro rozdělení/Merge (v tabulce níže najdete podrobný popis)
2. **ExecuteSampleSplitMerge.ps1** -provádí operace test na testovací datové vrstvy (v tabulce níže najdete podrobný popis)
3. **GetMappings.ps1** – nejvyšší úrovně ukázkový skript, který vytiskne aktuální stav mapování horizontálního oddílu.
4. **ShardManagement.psm1** -skriptu pomocné rutiny, které zabaluje rozhraní API ShardManagement
5. **SqlDatabaseHelpers.psm1** – Pomocník skriptu pro vytváření a správu databází SQL
   
   <table style="width:100%">
     <tr>
       <th>Soubor PowerShell</th>
       <th>Kroky</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Vytvoří databázi správce horizontálního oddílu mapy</td>
     </tr>
     <tr>
       <td>2.    Vytvoří 2 horizontálního oddílu databáze.
     </tr>
     <tr>
       <td>3.    Vytvoří mapu horizontálního oddílu pro tyto databáze (odstraní všechny existující horizontálního oddílu mapování na tyto databáze). </td>
     </tr>
     <tr>
       <td>4.    Vytvoří tabulku malé ukázkové v obou horizontálních oddílů a naplní tabulky v jednom z horizontálních oddílů.</td>
     </tr>
     <tr>
       <td>5.    Deklaruje SchemaInfo pro horizontálně dělenou tabulku.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Soubor PowerShell</th>
       <th>Kroky</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Odešle požadavek rozdělení front-end webové rozdělení sloučení služby, který rozdělí poloviční dat z první horizontálního oddílu, do druhé horizontálního oddílu.</td>
     </tr>
     <tr>
       <td>2.    Dotazování front-endu webové pro stav žádosti o rozdělení a čeká na dokončení žádosti.</td>
     </tr>
     <tr>
       <td>3.    Odešle požadavek sloučení pro front-end webové rozdělení sloučení služby, který přesouvá data z druhé horizontálního oddílu zpět na první horizontálního oddílu.</td>
     </tr>
     <tr>
       <td>4.    Dotazování front-endu webové pro stav žádosti o sloučení a čeká na dokončení žádosti.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Ověření nasazení pomocí prostředí PowerShell
1. Otevřete nové okno prostředí PowerShell a přejděte do adresáře, kam jste stáhli balíček rozdělení sloučení a pak přejděte do adresáře "powershell".
2. Vytvoření serveru Azure SQL database (nebo vyberte existující server) kde bude vytvořen správce mapy horizontálního oddílu a horizontálních oddílů.
   
   > [!NOTE]
   > Ve výchozím nastavení do skriptu zjednodušení vytvoří skript SetupSampleSplitMergeEnvironment.ps1 těchto databází na stejném serveru. Toto není omezení služby rozdělení sloučení sám sebe.
   >
   
   Přihlašovací jméno ověřování SQL s přístupem pro čtení a zápis do Operations Manager bude potřeba pro službu rozdělení sloučení pro přesun dat a aktualizaci mapy horizontálního oddílu. Vzhledem k tomu, že služba rozdělení sloučení běží v cloudu, nepodporuje aktuálně integrované ověřování.
   
   Ujistěte se, že server Azure SQL je nakonfigurována pro povolení přístupu z IP adresy počítače spuštěného tyto skripty. Toto nastavení v rámci serveru Azure SQL můžete najít / configuration / povolené IP adresy.
3. Spusťte skript SetupSampleSplitMergeEnvironment.ps1 k vytvoření ukázkové prostředí.
   
   Spuštěním tohoto skriptu budou vymazat všechny existující data správy mapy horizontálního oddílu struktury na databázi horizontálního oddílu mapa správce a horizontálních oddílů. Může být užitečné skript znovu spustit, pokud chcete znovu inicializovat horizontálního oddílu mapy nebo horizontálních oddílů.
   
   Ukázka příkazového řádku:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Spusťte skript Getmappings.ps1 zobrazení mapování, která momentálně existují v ukázkové prostředí.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Spusťte skript ExecuteSampleSplitMerge.ps1 provést operaci rozdělení (přesunutí poloviční dat na první horizontálního oddílu druhý horizontálního oddílu) a pak operace sloučení (přesunutí dat zpět na první horizontálního oddílu). Pokud jste nakonfigurovali SSL a zbývajících koncový bod http zakázaná, ujistěte se, že je použít koncový bod https://.
   
   Ukázka příkazového řádku:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Pokud se zobrazí níže chyba, je pravděpodobně k potížím s certifikátem váš koncový bod webové. Pokuste se připojit ke koncovému bodu webové pomocí Oblíbené webový prohlížeč a zkontrolujte, jestli Chyba certifikátu.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Pokud byly úspěšné, výstup by měl vypadat jako níže:
   
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
6. Experimentujte s jiné datové typy! Všechny tyto skripty trvat volitelný parametr - ShardKeyType, který vám umožní určit typ klíče. Výchozí hodnota je Int32, ale můžete také určit Int64, identifikátor Guid nebo Binary.

## <a name="create-requests"></a>Požadavky na vytvoření
Službu lze pomocí webového uživatelského rozhraní nebo importováním a pomocí modulu SplitMerge.psm1 PowerShell, které se budou odesílat své žádosti prostřednictvím webové role.

Službu můžete přesouvat data v horizontálně dělené tabulky a referenční tabulky. Horizontálně dělenou tabulku má klíčový sloupec horizontálního dělení a má jiný řádek dat v každém horizontálního oddílu. Referenční tabulka není horizontálně dělené tak, aby obsahovala stejná data řádků na každé horizontálního oddílu. Referenční tabulky jsou užitečné pro data, která se nemění často a slouží k připojení k s horizontálně dělené tabulky v dotazech.

Aby bylo možné provést operaci merge rozdělení, je potřeba deklarovat horizontálně dělené tabulky a referenční tabulky, které chcete mít přesunout. To je provedeno pomocí **SchemaInfo** rozhraní API. Toto rozhraní API je v **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** oboru názvů.

1. Pro každou horizontálně dělenou tabulku, vytvořte **ShardedTableInfo** objekt popisující název schématu nadřazené tabulky (volitelné, výchozí hodnota je "dbo"), název tabulky a název sloupce v této tabulce, který obsahuje klíč horizontálního dělení.
2. Pro každý odkaz na tabulku, vytvoření **ReferenceTableInfo** objekt popisující název schématu nadřazené tabulky (volitelné, výchozí hodnota je "dbo") a název tabulky.
3. Výše uvedené objekty TableInfo přidávat do nové **SchemaInfo** objektu.
4. Získat odkaz na **ShardMapManager** objekt a volání **GetSchemaInfoCollection**.
5. Přidat **SchemaInfo** k **SchemaInfoCollection**, poskytuje název pro mapování horizontálního oddílu.

Příklad toho si můžete prohlédnout ve skriptu SetupSampleSplitMergeEnvironment.ps1.

Službu rozdělení sloučení nevytvoří cílová databáze (nebo schéma pro všechny tabulky v databázi) pro vás. Musí být předem vytvořené před odesláním požadavku službě.

## <a name="troubleshooting"></a>Řešení potíží
Může se zobrazit pod zpráv při spuštění ukázkové skripty prostředí powershell:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Tato chyba znamená, že svůj certifikát SSL není správně nakonfigurován. Postupujte podle pokynů v části 'Připojení s webovým prohlížečem'.

Pokud nelze odeslat požadavky mohou se zobrazit tato:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Zkontrolujte v takovém případě konfiguračního souboru na konkrétní nastavení pro **WorkerRoleSynchronizationStorageAccountConnectionString**. Tato chyba obvykle značí, že role pracovního procesu nelze úspěšně spustit databázi metadat při prvním použití. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png


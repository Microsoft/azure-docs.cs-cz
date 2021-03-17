---
title: Nasazení služby dělení a slučování
description: K přesouvání dat mezi databázemi horizontálně dělené slouží také rozdělené sloučení.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 71aad7699c5af6ce2a1b9d82a340138200cfb5e1
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792068"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Nasazení služby dělení a slučování pro přesun dat mezi databázemi horizontálně dělené
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nástroj pro dělení a slučování umožňuje přesouvat data mezi databázemi horizontálně dělené. Viz [přesouvání dat mezi cloudové databáze s horizontálním](elastic-scale-overview-split-and-merge.md) navýšení kapacity

## <a name="download-the-split-merge-packages"></a>Stažení balíčků Split-Merge

1. Stáhněte si nejnovější verzi NuGet z [nugetu](https://docs.nuget.org/docs/start-here/installing-nuget).

1. Otevřete příkazový řádek a přejděte do adresáře, kam jste stáhli nuget.exe. Soubor ke stažení obsahuje příkazy prostředí PowerShell.

1. Stáhněte si nejnovější balíček Split-Merge do aktuálního adresáře pomocí příkazu níže:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Soubory jsou umístěny v adresáři s názvem **Microsoft. Azure. SqlDatabase. ElasticScale. Service. SplitMerge. x. x. xxx. x** , kde *x. x. xxx. x* odráží číslo verze. V podadresáři **content\splitmerge\service** vyhledejte soubory služby pro dělení a slučování a v podadresáři **Content\splitmerge\powershell** Split-Merge skripty PowerShellu (a požadované klientské knihovny DLL).

## <a name="prerequisites"></a>Předpoklady

1. Vytvořte databázi Azure SQL Database, která bude použita jako databáze stavu rozdělení a sloučení. Přejděte na web [Azure Portal](https://portal.azure.com). Vytvoří nový **SQL Database** . Zadejte název databáze a vytvořte nového správce a heslo. Nezapomeňte si název a heslo zaznamenat pro pozdější použití.

1. Ujistěte se, že váš server umožňuje připojení služeb Azure. Na portálu v **nastavení brány firewall** ověřte, že nastavení **povolený přístup ke službám Azure** je nastavené na **zapnuto** . Klikněte na ikonu Uložit.

1. Vytvořte účet Azure Storage pro výstup diagnostiky.

1. Vytvořte cloudovou službu Azure pro službu Split-Merge.

## <a name="configure-your-split-merge-service"></a>Konfigurace služby Split-Merge

### <a name="split-merge-service-configuration"></a>Konfigurace služby Split-Merge

1. Ve složce, do které jste stáhli Split-Merge sestavení, vytvořte kopii souboru *ServiceConfiguration. template. cscfg* , který byl dodán společně s *SplitMergeService. cspkg* a přejmenujte ho *ServiceConfiguration. cscfg* .

1. Otevřete *ServiceConfiguration. cscfg* v textovém editoru, jako je například Visual Studio, který ověřuje vstupy, jako je například formát kryptografických otisků certifikátů.

1. Vytvořte novou databázi nebo vyberte existující databázi, která bude sloužit jako stavová databáze pro operace Split-Merge a načtení připojovacího řetězce této databáze.

   > [!IMPORTANT]
   > V tuto chvíli musí databáze stavu používat kolaci s latinkou (SQL \_ latin1 \_ General \_ CP1 \_ CI \_ as). Další informace najdete v tématu [název řazení Windows (Transact-SQL)](/sql/t-sql/statements/windows-collation-name-transact-sql).

   V případě Azure SQL Database je připojovací řetězec obvykle ve tvaru:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Tento připojovací řetězec zadejte v souboru *. cscfg* v částech role **SplitMergeWeb** a **SplitMergeWorker** v nastavení ElasticScaleMetadata.

1. Pro roli **SplitMergeWorker** zadejte platný připojovací řetězec do služby Azure Storage pro nastavení **WorkerRoleSynchronizationStorageAccountConnectionString** .

### <a name="configure-security"></a>Konfigurace zabezpečení

Podrobné pokyny ke konfiguraci zabezpečení služby najdete v tématu [Konfigurace zabezpečení dělení](elastic-scale-split-merge-security-configuration.md).

Pro účely jednoduchého testovacího nasazení pro účely tohoto kurzu se provede minimální sada kroků konfigurace, která zajistí zprovoznění služby. Tyto kroky umožňují, aby se ke službě komunikovaly jenom jeden počítač nebo účet, který je spouští.

### <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

Vytvořte nový adresář a z tohoto adresáře spusťte pomocí okna [Developer Command Prompt pro aplikaci Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) následující příkaz:

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Zobrazí se výzva k zadání hesla k ochraně privátního klíče. Zadejte silné heslo a potvrďte ho. Pak se zobrazí výzva k zadání hesla, které se bude používat ještě jednou. Kliknutím na **Ano** na konci ho naimportujete do kořenového úložiště důvěryhodné certifikační autority.

### <a name="create-a-pfx-file"></a>Vytvořit soubor PFX

Spusťte následující příkaz ze stejného okna, ve kterém bylo provedeno Makecert; použijte stejné heslo, které jste použili k vytvoření certifikátu:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importovat klientský certifikát do osobního úložiště

1. V Průzkumníku Windows poklikejte na *mycert. pfx* .
2. V **Průvodci importem certifikátu** vyberte **aktuální uživatel** a klikněte na **Další** .
3. Potvrďte cestu k souboru a klikněte na tlačítko **Další** .
4. Zadejte heslo, nechte zaškrtnuté políčko **Zahrnout všechny rozšířené vlastnosti** a klikněte na **Další** .
5. Ponechte **automaticky vybrat úložiště certifikátů [...]** a klikněte na **Další** .
6. Klikněte na **Dokončit** a pak na **OK** .

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Nahrání souboru PFX do cloudové služby

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Vyberte **Cloud Services** .
3. Vyberte cloudovou službu, kterou jste vytvořili výše pro službu dělení a sloučení.
4. V horní nabídce klikněte na **certifikáty** .
5. Klikněte na **nahrát** na dolním panelu.
6. Vyberte soubor PFX a zadejte stejné heslo jako výše.
7. Po dokončení zkopírujte kryptografický otisk certifikátu z nové položky v seznamu.

### <a name="update-the-service-configuration-file"></a>Aktualizace konfiguračního souboru služby

Vložte miniaturu certifikátu zkopírovanou výše do atributu kryptografický otisk nebo hodnota tohoto nastavení.
Pro roli pracovního procesu:

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

Upozorňujeme, že pro produkční nasazení by se měly pro certifikační autoritu použít samostatné certifikáty pro šifrování, certifikát serveru a klientské certifikáty. Podrobné pokyny k tomuto postupu najdete v tématu [Konfigurace zabezpečení](elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Nasazení služby

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Vyberte cloudovou službu, kterou jste vytvořili dříve.
3. Klikněte na **Přehled** .
4. Zvolte testovací prostředí a pak klikněte na **nahrát** .
5. V dialogovém okně zadejte popisek nasazení. Pro "balíček" i "konfigurace" klikněte na "z místního" a vyberte soubor *SplitMergeService. cspkg* a soubor. cscfg, který jste nakonfigurovali dříve.
6. Zajistěte, aby se zaškrtávací políčko s názvem nasadit i v případě, že je zaškrtnuta **jedna nebo více rolí, které obsahují jednu instanci** .
7. Spusťte nasazení kliknutím na tlačítko se značkou dole vpravo. Očekává se, že dokončení může trvat několik minut.

## <a name="troubleshoot-the-deployment"></a>Řešení potíží s nasazením

Pokud se webová role nepovede přejít do režimu online, je pravděpodobný problém s konfigurací zabezpečení. Ověřte, že je protokol TLS/SSL nakonfigurovaný, jak je popsáno výše.

Pokud se vaše role pracovního procesu nepodaří přejít do režimu online, ale vaše webová role bude úspěšná, pravděpodobně se jedná o problém s připojením k databázi stavu, kterou jste vytvořili dříve.

- Ujistěte se, že je připojovací řetězec v poli cscfg přesný.
- Ověřte, zda server a databáze existují a zda je správné ID uživatele a heslo.
- V případě Azure SQL Database by měl být připojovací řetězec ve tvaru:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Zajistěte, aby název serveru nezačínal na **https://** .
- Ujistěte se, že váš server umožňuje připojení služeb Azure. Pokud to chcete provést, otevřete databázi na portálu a ujistěte se, že nastavení **Povolit přístup ke službám Azure** je nastaveno na * * zapnuto * * * *.

## <a name="test-the-service-deployment"></a>Testování nasazení služby

### <a name="connect-with-a-web-browser"></a>Připojení pomocí webového prohlížeče

Určete webový koncový bod vaší Split-Merge služby. Můžete to najít na portálu tak, že kliknete na **Přehled** cloudové služby a na pravé straně na **adrese URL webu** . Nahraďte **http://** hodnotou **https://** , protože výchozí nastavení zabezpečení zakáže koncový bod HTTP. Načte stránku pro tuto adresu URL do prohlížeče.

### <a name="test-with-powershell-scripts"></a>Testování pomocí skriptů PowerShellu

Nasazení a vaše prostředí je možné otestovat spuštěním zahrnutých ukázkových skriptů PowerShellu.

> [!IMPORTANT]
> Ukázkové skripty se spouští v prostředí PowerShell 5,1. Aktuálně se nespouštějí v PowerShellu 6 nebo novějším.

K dispozici jsou tyto soubory skriptu:

1. *SetupSampleSplitMergeEnvironment.ps1* – nastaví testovací datovou vrstvu pro rozdělení a sloučení (podrobný popis najdete v tabulce níže).
2. *ExecuteSampleSplitMerge.ps1* – provede operace testu na úrovni testovacích dat (podrobný popis najdete v tabulce níže).
3. *GetMappings.ps1* – vzorový skript nejvyšší úrovně, který tiskne aktuální stav mapování horizontálních oddílů.
4. Skript *ShardManagement. psm1* -Helper, který zabalí rozhraní ShardManagement API
5. *SqlDatabaseHelpers. psm1* – Pomocný skript pro vytváření a správu databází v SQL Database

   <table style="width:100%">
     <tr>
       <th>Soubor PowerShellu</th>
       <th>Postup</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Vytvoří databázi správce mapy horizontálních oddílů.</td>
     </tr>
     <tr>
       <td>2. Vytvoří 2 databáze horizontálních oddílů.
     </tr>
     <tr>
       <td>3. Vytvoří mapu horizontálních oddílů pro tyto databáze (odstraní všechny existující mapy horizontálních oddílů na těchto databázích). </td>
     </tr>
     <tr>
       <td>4. Vytvoří malou ukázkovou tabulku v horizontálních oddílů a naplní tabulku v jednom z horizontálních oddílů.</td>
     </tr>
     <tr>
       <td>5. Deklaruje SchemaInfo pro tabulku horizontálně dělené.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Soubor PowerShellu</th>
       <th>Postup</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Odešle požadavek na rozdělení na webový frontu Split-Merge služby, který rozdělí polovinu dat z prvního horizontálních oddílů do druhé horizontálních oddílů.</td>
     </tr>
     <tr>
       <td>2. Provede dotaz na webový front-end pro stav žádosti o rozdělení a počká, až se žádost dokončí.</td>
     </tr>
     <tr>
       <td>3. Odešle požadavek sloučení na front-end web služby Split-Merge, který přesune data z druhého horizontálních oddílů zpět na první horizontálních oddílů.</td>
     </tr>
     <tr>
       <td>4. Provede dotaz na webový front-end pro stav žádosti o sloučení a počká, až se žádost dokončí.</td>
     </tr>
   </table>

## <a name="use-powershell-to-verify-your-deployment"></a>Použití PowerShellu k ověření nasazení

1. Otevřete nové okno PowerShellu a přejděte do adresáře, do kterého jste stáhli balíček Split-Merge, a pak přejděte do adresáře "PowerShell".

2. Vytvořte Server (nebo vyberte existující server), kde se vytvoří správce mapy horizontálních oddílů a horizontálních oddílů.

   > [!NOTE]
   > Skript *SetupSampleSplitMergeEnvironment.ps1* ve výchozím nastavení vytvoří všechny tyto databáze na stejném serveru, aby byl skript jednoduchý. Nejedná se o omezení samotné služby Split-Merge.

   Přihlašovací jméno SQL ověřování s přístupem pro čtení i zápis pro databáze bude potřeba k tomu, aby služba Split-Merge přesunula data a aktualizovala horizontálních oddílů mapu. Vzhledem k tomu, že služba Split-Merge běží v cloudu, aktuálně nepodporuje integrované ověřování.

   Ujistěte se, že je server nakonfigurovaný tak, aby povoloval přístup z IP adresy počítače, na kterém běží tyto skripty. Toto nastavení můžete najít v části SQL Server/brány firewall a virtuální sítě/IP adresy klientů.

3. Spusťte skript *SetupSampleSplitMergeEnvironment.ps1* pro vytvoření ukázkového prostředí.

   Spuštěním tohoto skriptu dojde k vymazání všech stávajících datových struktur správy mapy horizontálních oddílů v databázi správce map horizontálních oddílů a v horizontálních oddílů. Pokud chcete znovu inicializovat mapu horizontálních oddílů nebo horizontálních oddílů, může být užitečné skript spustit znovu.

   Vzorový příkazový řádek:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Spusťte skript Getmappings.ps1 pro zobrazení mapování, která aktuálně existují ve vzorovém prostředí.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Spusťte skript *ExecuteSampleSplitMerge.ps1* , který spustí operaci rozdělení (přesunou polovinu dat na první horizontálních oddílů do druhé horizontálních oddílů) a potom operaci sloučení (přesun dat zpět na první horizontálních oddílů). Pokud jste nakonfigurovali protokol TLS a opustili jste koncový bod HTTP zakázaný, ujistěte se, že místo toho použijete koncový bod https://.

    Vzorový příkazový řádek:

    ```cmd
    .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd'
    -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net'
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
    ```

    Pokud se zobrazí následující chyba, pravděpodobně došlo k potížím s certifikátem vašeho webového koncového bodu. Zkuste se připojit ke koncovému bodu webu pomocí oblíbeného webového prohlížeče a podívejte se, jestli došlo k chybě certifikátu.

    `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

    Pokud bylo úspěšné, výstup by měl vypadat jako v následujícím příkladu:

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

6. Experimentujte s jinými datovými typy! Všechny tyto skripty pobírají volitelný parametr-ShardKeyType, který umožňuje zadat typ klíče. Výchozí hodnota je Int32, ale můžete také zadat Int64, GUID nebo Binary.

## <a name="create-requests"></a>Žádosti o vytvoření

Službu lze použít buď pomocí webového uživatelského rozhraní, nebo importováním a použitím modulu PowerShellu SplitMerge. psm1, který odešle vaše požadavky prostřednictvím webové role.

Služba může přesouvat data v tabulkách horizontálně dělené a referenčních tabulkách. Tabulka horizontálně dělené má klíčový sloupec horizontálního dělení a má v každém horizontálních oddílů odlišná data řádků. Referenční tabulka není horizontálně dělené, takže obsahuje stejná data řádku pro každé horizontálních oddílů. Referenční tabulky jsou užitečné pro data, která se nemění často a slouží k připojení k horizontálně dělené tabulkám v dotazech.

Aby bylo možné provést operaci dělení na sloučení, je nutné deklarovat tabulky horizontálně dělené a referenční tabulky, které chcete přesunout. Toho se dá dosáhnout pomocí rozhraní **SchemaInfo** API. Toto rozhraní API je v oboru názvů **Microsoft. Azure. SqlDatabase. ElasticScale. ShardManagement. Schema** .

1. Pro každou tabulku horizontálně dělené vytvořte objekt **ShardedTableInfo** popisující název nadřazeného schématu tabulky (volitelné, výchozí nastavení je "dbo"), název tabulky a název sloupce v tabulce, která obsahuje klíč horizontálního dělení.
2. Pro každou referenční tabulku vytvořte objekt **ReferenceTableInfo** popisující název nadřazeného schématu tabulky (volitelné, výchozí nastavení je "dbo") a název tabulky.
3. Přidejte výše uvedené objekty TableInfo do nového objektu **SchemaInfo** .
4. Získejte odkaz na objekt **ShardMapManager** a zavolejte **GetSchemaInfoCollection** .
5. Přidejte **SchemaInfo** do **SchemaInfoCollection** a poskytněte název mapy horizontálních oddílů.

Příkladem toho lze zobrazit ve skriptu SetupSampleSplitMergeEnvironment.ps1.

Služba Split-Merge nevytváří cílovou databázi (nebo schéma pro žádné tabulky v databázi) za vás. Před odesláním žádosti službě je nutné je předem vytvořit.

## <a name="troubleshooting"></a>Řešení potíží

Při spouštění ukázkových skriptů PowerShellu se může zobrazit následující zpráva:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Tato chyba znamená, že váš certifikát TLS/SSL není správně nakonfigurovaný. Postupujte prosím podle pokynů v části připojení k webovému prohlížeči.

Pokud nemůžete odesílat žádosti, může se zobrazit tato:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

V takovém případě Ověřte konfigurační soubor, konkrétně nastavení pro **WorkerRoleSynchronizationStorageAccountConnectionString** . Tato chyba obvykle indikuje, že role pracovního procesu nemohla úspěšně inicializovat databázi metadat při prvním použití.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
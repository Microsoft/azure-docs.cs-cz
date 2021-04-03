---
title: Distribuované transakce v cloudových databázích (Preview)
description: Přehled transakcí Elastic Database s Azure SQL Database a spravovanou instancí Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793343"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Distribuované transakce v cloudových databázích (Preview)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Transakce elastické databáze pro Azure SQL Database a Azure SQL Managed instance umožňují spouštět transakce, které přesahují několik databází. Transakce elastické databáze jsou k dispozici pro aplikace .NET využívající ADO.NET a integrují se se známým programovacím prostředím pomocí tříd [System. Transactions](/dotnet/api/system.transactions) . Pokud chcete získat knihovnu, přečtěte si téma [.NET Framework 4.6.1 (Webová instalační služba)](https://www.microsoft.com/download/details.aspx?id=49981).
Pro distribuované transakce spravované instance jsou navíc k dispozici v [jazyce Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql).

V místním prostředí takový scénář obvykle vyžaduje spuštění Microsoft DTC (Distributed Transaction Coordinator) (MSDTC). Vzhledem k tomu, že Služba MSDTC není k dispozici pro aplikaci typu platforma jako služba v Azure, možnost koordinace distribuovaných transakcí je teď přímo integrovaná do SQL Database nebo spravované instance. Aplikace se mohou připojit k libovolné databázi a spustit distribuované transakce a jedna z databází nebo serverů bude transparentně koordinovat distribuovanou transakci, jak je znázorněno na následujícím obrázku.

V tomto dokumentu se termínem "distribuované transakce" a "transakce elastické databáze" považují synonyma a budou se používat zaměnitelné.

  ![Distribuované transakce s Azure SQL Database s využitím transakcí elastické databáze ][1]

## <a name="common-scenarios"></a>Obvyklé scénáře

Transakce elastické databáze umožňují aplikacím provádět atomické změny dat uložených v několika různých databázích. Verze Preview se zaměřuje na prostředí vývoje na straně klienta v jazycích C# a .NET. Prostředí na straně serveru (kód napsaný v uložených procedurách nebo skriptech na straně serveru) pomocí [jazyka Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) je k dispozici pouze pro spravovanou instanci.
> [!IMPORTANT]
> Ve verzi Preview se v současnosti nepodporuje spouštění transakcí elastické databáze mezi Azure SQL Database a spravovanou instancí Azure SQL. Transakce elastické databáze může být rozložená jenom na celou sadu databází SQL nebo sadu spravovaných instancí.

Transakce elastické databáze cílí na následující scénáře:

* Aplikace s více databázemi v Azure: v tomto scénáři jsou data vertikálně rozdělená mezi několik databází v SQL Database nebo spravované instance tak, aby se různé druhy dat nacházely v různých databázích. Některé operace vyžadují změny dat, které jsou uchovávány ve dvou nebo více databázích. Aplikace používá transakce elastické databáze k koordinaci změn napříč databázemi a zajištění jejich nedělitelnost.
* Horizontálně dělené databázové aplikace v Azure: v tomto scénáři Datová vrstva používá [elastic Database klientské knihovny](elastic-database-client-library.md) nebo horizontálního dělení k horizontálnímu rozdělení dat mezi mnoho databází v SQL Database nebo ve spravované instanci. Jeden z nejvýraznějšího případu použití je nutnost provádět atomické změny pro horizontálně dělené aplikaci pro více tenantů při změně klientů s rozsahem. Zamyslete se nad tím, jak se instance přenosů z jednoho tenanta do druhého nacházejí v různých databázích. Druhý případ je jemně odstupňované horizontálního dělení, aby se vešly požadavky na kapacitu pro velký tenant, což zase obvykle znamená, že některé atomické operace musí roztáhnout mezi několik databází používaných pro stejného tenanta. Třetí případ je atomické aktualizace pro referenční data, která se replikují napříč databázemi. Atomické, transakční, operace na těchto řádcích se teď dají koordinovat napříč několika databázemi pomocí verze Preview.
  Transakce elastické databáze používají dva fáze potvrzení k zajištění atomické transakce napříč databázemi. Je vhodný pro transakce, které zahrnují méně než 100 databází v rámci jedné transakce v jednom okamžiku. Tato omezení nejsou vynutila, ale při překročení těchto limitů by měla očekávat míry výkonu a úspěšnosti pro transakce elastické databáze.

## <a name="installation-and-migration"></a>Instalace a migrace

Funkce pro transakce elastické databáze jsou k dispozici prostřednictvím aktualizací knihoven .NET System.Data.dll a System.Transactions.dll. Knihovny DLL zajišťují, že se v případě potřeby použije dvoufázové potvrzení, aby se zajistila nedělitelnost. Chcete-li začít vyvíjet aplikace pomocí transakcí elastické databáze, nainstalujte [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) nebo novější verzi. Při spuštění v dřívější verzi rozhraní .NET Framework se transakce nezdaří povýšit na distribuovanou transakci a vyvolá se výjimka.

Po instalaci můžete použít rozhraní API distribuovaných transakcí v System. Transactions s připojeními SQL Database a spravované instance. Pokud máte pomocí těchto rozhraní API existující aplikace MSDTC, po instalaci rozhraní 4.6.1 Framework znovu sestavte stávající aplikace pro .NET 4,6. Pokud vaše projekty cílí na .NET 4,6, budou automaticky používat aktualizované knihovny DLL z nové verze rozhraní a volání rozhraní API distribuované transakce v kombinaci s připojením k SQL Database nebo spravovaná instance bude nyní úspěšná.

Pamatujte, že transakce elastické databáze nevyžadují instalaci MSDTC. Místo toho jsou transakce elastické databáze přímo spravovány službou a v rámci služby. To významně zjednodušuje cloudové scénáře, protože nasazení MSDTC není nutné k použití distribuovaných transakcí s SQL Database nebo spravovanou instancí. Oddíl 4 podrobněji vysvětluje, jak nasadit transakce elastické databáze a požadované rozhraní .NET Framework spolu s vašimi cloudových aplikací do Azure.

## <a name="net-installation-for-azure-cloud-services"></a>Instalace rozhraní .NET pro Azure Cloud Services

Azure poskytuje několik nabídek pro hostování aplikací .NET. Porovnání různých nabídek je k dispozici v [Azure App Service, Cloud Services a Virtual Machines porovnání](/azure/architecture/guide/technology-choices/compute-decision-tree). Pokud je hostovaný operační systém nabídky menší než .NET 4.6.1 vyžadované pro elastické transakce, musíte upgradovat hostovaný operační systém na 4.6.1.

Pro Azure App Service se v současné době nepodporují upgrady hostovaného operačního systému. V případě Azure Virtual Machines se jednoduše přihlaste k virtuálnímu počítači a spusťte instalační program pro nejnovější verzi rozhraní .NET Framework. V případě Azure Cloud Services musíte do úloh po spuštění nasazení zahrnout instalaci novější verze rozhraní .NET. Koncepty a kroky jsou popsané v části [instalace .NET v roli cloudové služby](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Všimněte si, že instalační program pro .NET 4.6.1 může vyžadovat další dočasné úložiště během zaváděcího procesu ve službě Azure Cloud Services, než je instalační program pro .NET 4,6. Aby se zajistila úspěšná instalace, musíte pro cloudovou službu Azure zvětšit dočasné úložiště v souboru ServiceDefinition. csdef v části LocalResources a nastavení prostředí úlohy po spuštění, jak je znázorněno v následující ukázce:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>Prostředí pro vývoj v rozhraní .NET

### <a name="multi-database-applications"></a>Aplikace s více databázemi

Následující vzorový kód používá známé programovací prostředí s .NET System. Transactions. Třída TransactionScope vytváří okolí transakce v .NET. ("Ambientní transakce" je ta, která žije v aktuálním vlákně.) Všechna připojení otevřená v rámci objektu TransactionScope se účastní transakce. Pokud se v různých databázích účastní, transakce se automaticky nazvyšuje na distribuovanou transakci. Výsledek transakce je řízen nastavením rozsahu, který bude dokončen k označení potvrzení.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Databázové aplikace horizontálně dělené

Transakce elastické databáze pro SQL Database a spravovaná instance také podporují koordinaci distribuovaných transakcí, kde pomocí metody OpenConnectionForKey klientské knihovny elastické databáze otevírá připojení pro datovou vrstvu s možností horizontálního rozšíření kapacity. Zvažte případy, kdy potřebujete zaručit konzistenci transakcí pro změny v několika různých hodnotách horizontálního dělení klíče. Připojení k horizontálních oddílů, které hostují různé hodnoty klíčů horizontálního dělení, se účtují pomocí OpenConnectionForKey. V obecném případě může být připojení k různým horizontálních oddílůům, aby bylo zajištěno, že transakční záruky vyžadují distribuovanou transakci.
Tento přístup je znázorněn v následujícím příkladu kódu. Předpokládá, že proměnná s názvem shardmap se používá k reprezentaci mapy horizontálních oddílů z klientské knihovny elastické databáze:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Prostředí pro vývoj v jazyce Transact-SQL

Distribuované transakce na straně serveru pomocí jazyka Transact-SQL jsou k dispozici pouze pro spravovanou instanci Azure SQL. Distribuované transakce se dají provádět jenom mezi spravovanými instancemi, které patří do stejné [skupiny důvěryhodných serverů](../managed-instance/server-trust-group-overview.md). V tomto scénáři musí spravované instance pomocí [odkazovaného serveru](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) odkazovat na sebe navzájem.

Následující vzorový kód Transact-SQL používá ke spuštění distribuované transakce [počáteční distribuovanou transakci](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) .

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Kombinování prostředí pro vývoj v .NET a Transact-SQL

Aplikace .NET, které používají třídy System. Transactions, mohou kombinovat třídu TransactionScope s příkazem jazyka Transact-SQL a zahájit DISTRIBUOVANou transakci. V rámci objektu TransactionScope se vnitřní transakce, která spustí transakci BEGIN DITRIBUTED, bude explicitně povýšena na distribuovanou transakci. Kromě toho, když je v objektu TransactionScope otevřeno druhé SqlConnecton, bude implicitně povýšen na distribuovanou transakci. Po spuštění distribuované transakce se všechny následné žádosti o transakce, ať už pocházejí z .NET nebo Transact-SQL, připojí k nadřazené distribuované transakci. Vzhledem k tomu, že všechny rozsahy vnořených transakcí iniciované příkazem BEGIN budou ukončeny ve stejné transakci a příkazy COMMIT/ROLLBACK budou mít vliv na celkový výsledek:
 * Příkaz COMMIT nebude mít žádný vliv na obor transakce inicializovaný příkazem BEGIN, to znamená, že žádné výsledky nebudou potvrzeny před vyvoláním metody Complete () v objektu TransactionScope. Pokud je objekt TransactionScope před dokončením zničen, všechny změny provedené v rámci tohoto oboru jsou vráceny zpět.
 * Příkaz ROLLBACK způsobí návrat celého objektu TransactionScope zpět. Jakékoli pokusy o zařazení nových transakcí v rámci objektu TransactionScope selžou poté, co se pokusí vyvolat Complete () na objekt TransactionScope.

Zde je příklad, kde transakce je explicitně povýšena na distribuovanou transakci pomocí jazyka Transact-SQL.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

Následující příklad ukazuje transakci, která je implicitně povýšena na distribuovanou transakci poté, co byl v objektu TransactionScope spuštěn druhý SqlConnecton.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Transakce napříč několika servery pro Azure SQL Database

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Transakce elastické databáze jsou podporovány napříč různými servery v Azure SQL Database. Když transakce přesahující hranice serveru, je potřeba, aby se zúčastněné servery nejdřív zadaly do vzájemně se komunikačních vztahů. Po navázání komunikačního vztahu se všechny databáze na všech dvou serverech můžou účastnit elastických transakcí s databázemi z druhého serveru. S transakcemi, které jsou na více než dvou serverech, je nutné, aby se pro všechny dvojice serverů nauskutečnil komunikační vztah.

Pomocí následujících rutin PowerShellu můžete spravovat vztahy komunikace mezi servery pro transakce elastické databáze:

* **New-AzSqlServerCommunicationLink**: pomocí této rutiny můžete vytvořit nový vztah komunikace mezi dvěma servery v Azure SQL Database. Vztah je symetrický, což znamená, že oba servery mohou iniciovat transakce s jiným serverem.
* **Get-AzSqlServerCommunicationLink**: tuto rutinu použijte k načtení existujících komunikačních vztahů a jejich vlastností.
* **Remove-AzSqlServerCommunicationLink**: pomocí této rutiny můžete odebrat existující komunikační vztah.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Transakce napříč několika servery pro spravovanou instanci Azure SQL

Distribuované transakce jsou podporované napříč různými servery ve spravované instanci Azure SQL. Při transakcích, které překračují hranice spravované instance, je nutné, aby se zúčastněné instance nacházely do vzájemného vztahu zabezpečení a komunikace. K tomu je potřeba vytvořit [skupinu důvěryhodných serverů](../managed-instance/server-trust-group-overview.md), kterou je možné provést Azure Portal. Pokud spravované instance nejsou ve stejné virtuální síti, je potřeba nastavit [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md) a příchozí a odchozí pravidla skupiny zabezpečení sítě musí povolit porty 5024 a 11000-12000 ve všech zúčastněných virtuálních sítích.

  ![Skupiny důvěryhodných serverů na webu Azure Portal][3]

Následující diagram znázorňuje skupinu důvěryhodných serverů se spravovanými instancemi, které mohou spouštět distribuované transakce pomocí .NET nebo Transact-SQL.

  ![Distribuované transakce se spravovanou instancí Azure SQL pomocí elastických transakcí][2]

## <a name="monitoring-transaction-status"></a>Stav transakce monitorování

Pomocí zobrazení dynamické správy (zobrazení dynamické správy) můžete monitorovat stav a průběh probíhajících transakcí elastické databáze. Všechny zobrazení dynamické správy vztahující se k transakcím jsou relevantní pro distribuované transakce v SQL Database a ve spravované instanci. Odpovídající seznam zobrazení dynamické správy můžete najít tady: [zobrazení a funkce dynamické správy související s transakcemi (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Tyto zobrazení dynamické správy jsou zvláště užitečné:

* **Sys.DM \_ Tran \_ Active \_ Transactions**: vypíše aktuálně aktivní transakce a jejich stav. Sloupec UOW (jednotka práce) může identifikovat různé podřízené transakce, které patří do stejné distribuované transakce. Všechny transakce v rámci stejné distribuované transakce mají stejnou hodnotu UOW. Další informace najdete v [dokumentaci k DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql).
* **\_ \_ \_ transakce sys.DM Tran Database**: poskytuje další informace o transakcích, jako je umístění transakce v protokolu. Další informace najdete v [dokumentaci k DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql).
* **Sys.DM \_ Tran \_ zámky**: poskytuje informace o zámkech, které jsou aktuálně uchovávány v probíhajících transakcích. Další informace najdete v [dokumentaci k DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

## <a name="limitations"></a>Omezení

Následující omezení se aktuálně vztahují na transakce elastické databáze v SQL Database:

* Podporují se jenom transakce napříč databázemi v SQL Database. Jiní poskytovatelé prostředků a databáze [XA](https://en.wikipedia.org/wiki/X/Open_XA) , kteří nejsou SQL Database, se nemůžou zúčastnit transakcí elastické databáze. To znamená, že transakce elastické databáze se nemůžou roztahovat mezi místními SQL Server a Azure SQL Database. U distribuovaných transakcí v místním prostředí používejte MSDTC.
* Jsou podporovány pouze transakce, které jsou koordinovány klientem z aplikace .NET. Podpora na straně serveru pro T-SQL, jako je BEGIN DISTRIBUTed TRANSACTIONed, je plánovaná, ale ještě není dostupná.
* Transakce napříč službami WCF se nepodporují. Například máte metodu služby WCF, která provádí transakci. Uzavření volání do oboru transakce se nezdaří jako [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

Následující omezení se aktuálně vztahují na distribuované transakce ve spravované instanci:

* Podporují se jenom transakce napříč databázemi ve spravované instanci. Jiní zprostředkovatelé prostředků [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) a databáze mimo SPRAVOVANOU instanci SQL Azure se nemůžou účastnit distribuovaných transakcí. To znamená, že distribuované transakce se nemůžou roztahovat mezi místními SQL Server a Azure SQL Managed instance. U distribuovaných transakcí v místním prostředí používejte MSDTC.
* Transakce napříč službami WCF se nepodporují. Například máte metodu služby WCF, která provádí transakci. Uzavření volání do oboru transakce se nezdaří jako [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* Spravovaná instance Azure SQL musí být součástí [skupiny důvěryhodných serverů](../managed-instance/server-trust-group-overview.md) , aby se mohla účastnit distribuované transakce.
* Omezení [skupin vztahů důvěryhodnosti serveru](../managed-instance/server-trust-group-overview.md) mají vliv na distribuované transakce.
* Spravované instance, které se účastní distribuovaných transakcí, musí mít připojení prostřednictvím privátních koncových bodů (pomocí privátní IP adresy z virtuální sítě, ve které jsou nasazené) a musí se vzájemně odkazovat pomocí privátních plně kvalifikovaných názvů domén. Klientské aplikace mohou používat distribuované transakce u privátních koncových bodů. V případech, kdy Transact-SQL využívá propojené servery odkazující na soukromé koncové body, mohou navíc klientské aplikace používat také distribuované transakce ve veřejných koncových bodech. Toto omezení je vysvětleno v následujícím diagramu.
  ![Omezení připojení privátního koncového bodu][4]
## <a name="next-steps"></a>Další kroky

* V případě otázek nás na [stránce s dotazem na Microsoft Q&SQL Database](/answers/topics/azure-sql-database.html).
* V případě žádostí o funkce je přidejte do [fóra SQL Database Fórum](https://feedback.azure.com/forums/217321-sql-database/) nebo [spravované instance](https://feedback.azure.com/forums/915676-sql-managed-instance).



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png

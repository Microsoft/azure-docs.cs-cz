---
title: Distribuované transakce v cloudových databázích (Preview)
description: Přehled transakcí Elastic Database s Azure SQL Database
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
ms.openlocfilehash: 60f6863bbe051338308c30e22c6969d84670dc64
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409727"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Distribuované transakce v cloudových databázích (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Transakce elastické databáze pro Azure SQL Database umožňují spouštět transakce, které jsou rozloženy na více databází v SQL Database. Transakce elastické databáze pro SQL Database jsou k dispozici pro aplikace .NET s využitím ADO.NET a integrují se se známým programovacím prostředím pomocí tříd [System. Transactions](https://msdn.microsoft.com/library/system.transactions.aspx) . Pokud chcete získat knihovnu, přečtěte si téma [.NET Framework 4.6.1 (Webová instalační služba)](https://www.microsoft.com/download/details.aspx?id=49981).

V místním prostředí takový scénář obvykle vyžaduje spuštění Microsoft DTC (Distributed Transaction Coordinator) (MSDTC). Vzhledem k tomu, že Služba MSDTC není k dispozici pro aplikaci typu platforma jako služba v Azure, možnost koordinace distribuovaných transakcí je teď přímo integrovaná do SQL Database. Aplikace se mohou připojit k libovolné databázi v SQL Database spustit distribuované transakce a jedna z databází bude transparentně koordinovat distribuovanou transakci, jak je znázorněno na následujícím obrázku.

  ![Distribuované transakce s Azure SQL Database s využitím transakcí elastické databáze ][1]

## <a name="common-scenarios"></a>Typické scénáře

Transakce elastické databáze pro SQL Database umožňují aplikacím provádět atomické změny dat uložených v různých databázích v SQL Database. Verze Preview se zaměřuje na prostředí vývoje na straně klienta v jazycích C# a .NET. Prostředí na straně serveru využívající T-SQL se plánuje na pozdější dobu.  
Transakce elastické databáze cílí na následující scénáře:

* Aplikace s více databázemi v Azure: v tomto scénáři jsou data vertikálně rozdělená mezi několik databází v SQL Database tak, aby se různé druhy dat nacházely v různých databázích. Některé operace vyžadují změny dat, které jsou uchovávány ve dvou nebo více databázích. Aplikace používá transakce elastické databáze k koordinaci změn napříč databázemi a zajištění jejich nedělitelnost.
* Horizontálně dělené databázové aplikace v Azure: v tomto scénáři Datová vrstva používá [elastic Database klientské knihovny](elastic-database-client-library.md) nebo horizontálního dělení k horizontálnímu rozdělení dat mezi mnoho databází v SQL Database. Jeden z nejvýraznějšího případu použití je nutnost provádět atomické změny pro horizontálně dělené aplikaci pro více tenantů při změně klientů s rozsahem. Zamyslete se nad tím, jak se instance přenosů z jednoho tenanta do druhého nacházejí v různých databázích. Druhý případ je jemně odstupňované horizontálního dělení, aby se vešly požadavky na kapacitu pro velký tenant, což zase obvykle znamená, že některé atomické operace musí roztáhnout mezi několik databází používaných pro stejného tenanta. Třetí případ je atomické aktualizace pro referenční data, která se replikují napříč databázemi. Atomické, transakční, operace na těchto řádcích se teď dají koordinovat napříč několika databázemi pomocí verze Preview.
  Transakce elastické databáze používají dvoufázové potvrzení k zajištění atomické transakce napříč databázemi. Je vhodný pro transakce, které zahrnují méně než 100 databází v rámci jedné transakce v jednom okamžiku. Tato omezení nejsou vynutila, ale při překročení těchto limitů by měla očekávat míry výkonu a úspěšnosti pro transakce elastické databáze.

## <a name="installation-and-migration"></a>Instalace a migrace

Funkce pro transakce elastické databáze v SQL Database jsou k dispozici prostřednictvím aktualizací knihoven .NET System.Data.dll a System.Transactions.dll. Knihovny DLL zajišťují, že se v případě potřeby použije dvoufázové potvrzení, aby se zajistila nedělitelnost. Chcete-li začít vyvíjet aplikace pomocí transakcí elastické databáze, nainstalujte [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) nebo novější verzi. Při spuštění v dřívější verzi rozhraní .NET Framework se transakce nezdaří povýšit na distribuovanou transakci a vyvolá se výjimka.

Po instalaci můžete použít rozhraní API distribuovaných transakcí v System. Transactions s připojeními k SQL Database. Pokud máte pomocí těchto rozhraní API existující aplikace MSDTC, jednoduše po instalaci rozhraní 4.6.1 Framework znovu sestavte své stávající aplikace pro .NET 4,6. Pokud vaše projekty cílí na .NET 4,6, budou automaticky používat aktualizované knihovny DLL z nové verze rozhraní a volání rozhraní API distribuované transakce v kombinaci s připojením k SQL Database budou nyní úspěšná.

Pamatujte, že transakce elastické databáze nevyžadují instalaci MSDTC. Místo toho jsou transakce elastické databáze přímo spravovány nástrojem a v rámci SQL Database. To významně zjednodušuje cloudové scénáře, protože nasazení MSDTC není nutné k použití distribuovaných transakcí s SQL Database. Oddíl 4 podrobněji vysvětluje, jak nasadit transakce elastické databáze a požadované rozhraní .NET Framework spolu s vašimi cloudových aplikací do Azure.

## <a name="development-experience"></a>Vývojové prostředí

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

Transakce elastické databáze pro SQL Database také podporují koordinaci distribuovaných transakcí, kde použijete metodu OpenConnectionForKey klientské knihovny elastické databáze k otevření připojení pro datovou vrstvu s možností horizontálního rozšíření kapacity. Zvažte případy, kdy potřebujete zaručit konzistenci transakcí pro změny v několika různých hodnotách horizontálního dělení klíče. Připojení k horizontálních oddílů, které hostují různé hodnoty klíčů horizontálního dělení, se účtují pomocí OpenConnectionForKey. V obecném případě může být připojení k různým horizontálních oddílůům, aby bylo zajištěno, že transakční záruky vyžadují distribuovanou transakci.
Tento přístup je znázorněn v následujícím příkladu kódu. Předpokládá, že proměnná s názvem shardmap se používá k reprezentaci mapy horizontálních oddílů z klientské knihovny elastické databáze:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

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

## <a name="transactions-across-multiple-servers"></a>Transakce napříč několika servery

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Transakce elastické databáze jsou podporovány napříč různými servery v Azure SQL Database. Když transakce přesahující hranice serveru, je potřeba, aby se zúčastněné servery nejdřív zadaly do vzájemně se komunikačních vztahů. Po navázání komunikačního vztahu se všechny databáze na všech dvou serverech můžou účastnit elastických transakcí s databázemi z druhého serveru. S transakcemi, které jsou na více než dvou serverech, je nutné, aby se pro všechny dvojice serverů nauskutečnil komunikační vztah.

Pomocí následujících rutin PowerShellu můžete spravovat vztahy komunikace mezi servery pro transakce elastické databáze:

* **New-AzSqlServerCommunicationLink**: pomocí této rutiny můžete vytvořit nový vztah komunikace mezi dvěma servery v Azure SQL Database. Vztah je symetrický, což znamená, že oba servery mohou iniciovat transakce s jiným serverem.
* **Get-AzSqlServerCommunicationLink**: tuto rutinu použijte k načtení existujících komunikačních vztahů a jejich vlastností.
* **Remove-AzSqlServerCommunicationLink**: pomocí této rutiny můžete odebrat existující komunikační vztah.

## <a name="monitoring-transaction-status"></a>Stav transakce monitorování

Pomocí zobrazení dynamické správy (zobrazení dynamické správy) v SQL Database můžete monitorovat stav a průběh probíhajících transakcí elastické databáze. Všechny zobrazení dynamické správy vztahující se k transakcím jsou relevantní pro distribuované transakce v SQL Database. Odpovídající seznam zobrazení dynamické správy můžete najít tady: [zobrazení a funkce dynamické správy související s transakcemi (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Tyto zobrazení dynamické správy jsou zvláště užitečné:

* **Sys.DM \_ Tran \_ Active \_ Transactions**: vypíše aktuálně aktivní transakce a jejich stav. Sloupec UOW (jednotka práce) může identifikovat různé podřízené transakce, které patří do stejné distribuované transakce. Všechny transakce v rámci stejné distribuované transakce mají stejnou hodnotu UOW. Další informace najdete v [dokumentaci k DMV](https://msdn.microsoft.com/library/ms174302.aspx) .
* ** \_ \_ \_ transakce sys.DM Tran Database**: poskytuje další informace o transakcích, jako je umístění transakce v protokolu. Další informace najdete v [dokumentaci k DMV](https://msdn.microsoft.com/library/ms186957.aspx) .
* **Sys.DM \_ Tran \_ zámky**: poskytuje informace o zámkech, které jsou aktuálně uchovávány v probíhajících transakcích. Další informace najdete v [dokumentaci k DMV](https://msdn.microsoft.com/library/ms190345.aspx) .

## <a name="limitations"></a>Omezení

Následující omezení se aktuálně vztahují na transakce elastické databáze v SQL Database:

* Podporují se jenom transakce napříč databázemi v SQL Database. Jiní poskytovatelé prostředků a databáze [XA](https://en.wikipedia.org/wiki/X/Open_XA) , kteří nejsou SQL Database, se nemůžou zúčastnit transakcí elastické databáze. To znamená, že transakce elastické databáze se nemůžou roztahovat mezi místními SQL Server a Azure SQL Database. U distribuovaných transakcí v místním prostředí používejte MSDTC.
* Jsou podporovány pouze transakce, které jsou koordinovány klientem z aplikace .NET. Podpora na straně serveru pro T-SQL, jako je BEGIN DISTRIBUTed TRANSACTIONed, je plánovaná, ale ještě není dostupná.
* Transakce napříč službami WCF se nepodporují. Například máte metodu služby WCF, která provádí transakci. Uzavření volání do oboru transakce se nezdaří jako [System. ServiceModel. ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Další kroky

Pokud máte otázky, obraťte se na nás na [stránce s dotazem na Microsoft Q&SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html). V případě žádostí o funkce je prosím přidejte do [fóra SQL Database Feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
 
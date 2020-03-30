---
title: Distribuované transakce v cloudových databázích
description: Přehled transakcí elastické databáze s databází Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 3ca3e9074f28d66068d49b80915e98600759d9be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68568291"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Distribuované transakce v cloudových databázích

Elastické databázové transakce pro Azure SQL Database (SQL DB) umožňují spouštět transakce, které pokrývají několik databází v DATABÁZI SQL. Elastické databázové transakce pro SQL DB jsou k dispozici pro aplikace .NET pomocí rozhraní ADO .NET a integrují se známým prostředím programování pomocí tříd [System.Transaction.](https://msdn.microsoft.com/library/system.transactions.aspx) Chcete-li získat knihovnu, naleznete [v tématu .NET Framework 4.6.1 (Web Installer)](https://www.microsoft.com/download/details.aspx?id=49981).

V místním prostředí takový scénář obvykle vyžaduje spuštění koordinátoru distribuovaných transakcí společnosti Microsoft (MSDTC). Vzhledem k tomu, že koordinátor MSDTC není k dispozici pro aplikaci platformy jako služba v Azure, schopnost koordinovat distribuované transakce je teď přímo integrovaná do databáze SQL DB. Aplikace se mohou připojit k libovolné databázi SQL a spouštět distribuované transakce a jedna z databází bude transparentně koordinovat distribuovanou transakci, jak je znázorněno na následujícím obrázku. 

  ![Distribuované transakce s Databází Azure SQL pomocí elastických databázových transakcí ][1]

## <a name="common-scenarios"></a>Obvyklé scénáře

Elastické databázové transakce pro SQL DB umožňují aplikacím provádět atomické změny dat uložených v několika různých databázích SQL. Náhled se zaměřuje na prostředí vývoje na straně klienta v c# a .NET. Prostředí na straně serveru pomocí T-SQL je plánováno na pozdější dobu.  
Transakce elastické databáze cílí na následující scénáře:

* Vícedatabázové aplikace v Azure: V tomto scénáři jsou data vertikálně rozdělena napříč několika databázemi v DATABÁZI SQL, takže různé druhy dat jsou umístěny v různých databázích. Některé operace vyžadují změny dat, které jsou uchovávány ve dvou nebo více databázích. Aplikace používá transakce elastické databáze koordinovat změny napříč databázemi a zajistit nedělitost.
* Horizontálně rozdělené databázové aplikace v Azure: V tomto scénáři datová vrstva používá [klientskou knihovnu elastické databáze](sql-database-elastic-database-client-library.md) nebo vlastní horizontální dělení k horizontálnímu rozdělení dat mezi mnoho databází v DATABÁZI. Jeden prominentní případ použití je potřeba provádět atomické změny pro víceklientské aplikace s oddíly při změnách span tenantů. Představte si například přenos z jednoho klienta do druhého, oba s bydlištěm v různých databázích. Druhý případ je jemně odstupňované řídké řídké řídké nálože pro potřeby kapacity pro velkého klienta, což obvykle znamená, že některé atomické operace musí roztáhnout několik databází používaných pro stejného klienta. Třetím případem je atomické aktualizace referenčních dat, které jsou replikovány napříč databázemi. Atomic, transakční, operace podél těchto řádků lze nyní koordinovat v několika databázích pomocí náhledu.
  Elastické databázové transakce používají dvoufázové potvrzení k zajištění nedělitelnosti transakcí napříč databázemi. Je vhodné pro transakce, které zahrnují méně než 100 databází najednou v rámci jedné transakce. Tyto limity nejsou vynuceny, ale je třeba očekávat, že výkon a úspěšnost pro transakce elastické databáze trpět při překročení těchto limitů.

## <a name="installation-and-migration"></a>Instalace a migrace

Možnosti pro transakce elastické databáze v databázi SQL DB jsou poskytovány prostřednictvím aktualizací knihoven .NET System.Data.dll a System.Transactions.dll. Knihovny DLL zajišťují, že dvoufázové potvrzení se používá v případě potřeby k zajištění nedělitelnosti. Chcete-li začít vyvíjet aplikace pomocí elastických databázových transakcí, nainstalujte [rozhraní .NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) nebo novější verzi. Při spuštění v dřívější verzi rozhraní .NET framework, transakce se nezdaří povýšení na distribuované transakce a výjimka bude vyvolána.

Po instalaci můžete použít úložiště API distribuovaných transakcí v System.Transactions s připojením k SQL DB. Pokud máte existující aplikace MSDTC pomocí těchto rozhraní API, jednoduše znovu sestavit existující aplikace pro .NET 4.6 po instalaci 4.6.1 Framework. Pokud vaše projekty cíl .NET 4.6, budou automaticky používat aktualizované knihovny DLL z nové verze rozhraní Framework a volání rozhraní API distribuované transakce v kombinaci s připojením k DATABÁZI SQL bude nyní úspěšné.

Nezapomeňte, že transakce elastické databáze nevyžadují instalaci nástroje MSDTC. Místo toho elastické databázové transakce jsou přímo spravovány a v rámci SQL DB. To výrazně zjednodušuje cloudové scénáře, protože nasazení koordinátoru MSDTC není nutné používat distribuované transakce s DATABÁZÍ SQL DB. Část 4 podrobněji vysvětluje, jak nasadit transakce elastické databáze a požadované rozhraní .NET framework společně s cloudovými aplikacemi do Azure.

## <a name="development-experience"></a>Vývojové zkušenosti

### <a name="multi-database-applications"></a>Aplikace s více databázemi

Následující ukázkový kód používá známé zkušenosti s programováním s rozhraním .NET System.Transactions. Třída TransactionScope vytvoří okolní transakci v rozhraní .NET. ("okolní transakce" je ten, který žije v aktuálním vlákně.) Všechny připojení otevřené v rámci TransactionScope účastnit transakce. Pokud se účastní různé databáze, transakce je automaticky zvýšena na distribuovanou transakci. Výsledek transakce je řízen nastavením oboru k dokončení k označení potvrzení.

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

### <a name="sharded-database-applications"></a>Aplikace s pevnými jako uhlazené databáze

Elastické databázové transakce pro SQL DB také podporují koordinaci distribuovaných transakcí, kde použijete metodu OpenConnectionForKey klientské knihovny elastické databáze k otevření připojení pro škálovanou datovou vrstvu. Zvažte případy, kdy je třeba zaručit transakční konzistenci pro změny v několika různých hodnotách klíče s ráždí. Připojení k úlomky hostování různých hodnot klíče srážlivost jsou zprostředkovány pomocí OpenConnectionForKey. V obecném případě připojení může být různé horizontálních oddílů, takže zajištění transakční záruky vyžaduje distribuované transakce. Následující ukázka kódu ilustruje tento přístup. Předpokládá, že proměnná s názvem shardmap se používá k reprezentaci mapy střepů z klientské knihovny elastické databáze:

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


## <a name="net-installation-for-azure-cloud-services"></a>Instalace rozhraní .NET pro cloudové služby Azure

Azure poskytuje několik nabídek pro hostování aplikací .NET. Porovnání různých nabídek je k dispozici ve [službě Azure App Service, cloudových službách a porovnání virtuálních počítačů](/azure/architecture/guide/technology-choices/compute-decision-tree). Pokud je hostovaný operační systém nabídky menší než .NET 4.6.1 požadovaný pro elastické transakce, je třeba upgradovat hostovaný operační systém na 4.6.1. 

Pro Azure App Services upgrady na hostovaný operační systém nejsou aktuálně podporovány. Pro virtuální počítače Azure jednoduše přihlaste se k virtuálnímu počítači a spusťte instalační program pro nejnovější rozhraní .NET Framework. Pro Cloudové služby Azure je potřeba zahrnout instalaci novější verze .NET do úloh spuštění vašeho nasazení. Koncepty a kroky jsou popsány v [části Instalace rozhraní .NET na roli cloudové služby](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Všimněte si, že instalační program pro rozhraní .NET 4.6.1 může vyžadovat více dočasného úložiště během procesu zavádění v cloudových službách Azure než instalační program pro rozhraní .NET 4.6. Chcete-li zajistit úspěšnou instalaci, je třeba zvýšit dočasné úložiště pro cloudovou službu Azure v souboru ServiceDefinition.csdef v části LocalResources a nastavení prostředí úlohy při spuštění, jak je znázorněno v následující ukázce:

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

## <a name="transactions-across-multiple-servers"></a>Transakce na více serverech

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Elastické databázové transakce jsou podporované na různých serverech databáze SQL v Azure SQL Database. Když transakce překročí hranice serveru SQL Database, zúčastněné servery musí být nejprve zadány do vztahu vzájemné komunikace. Po vytvoření komunikačního vztahu se jakákoli databáze na libovolném ze dvou serverů může účastnit elastických transakcí s databázemi z jiného serveru. S transakcemi zahrnujícími více než dva servery databáze SQL musí být pro libovolný pár serverů SQL Database zaveden komunikační vztah.

Ke správě vztahů komunikace mezi servery pro transakce elastické databáze použijte následující rutiny prostředí PowerShell:

* **New-AzSqlServerCommunicationLink**: Pomocí této rutiny vytvořte nový komunikační vztah mezi dvěma servery SQL Database v Azure SQL Database. Vztah je symetrický, což znamená, že oba servery mohou iniciovat transakce s jiným serverem.
* **Get-AzSqlServerCommunicationLink**: Pomocí této rutiny načíst existující komunikační vztahy a jejich vlastnosti.
* **Remove-AzSqlServerCommunicationLink**: Pomocí této rutiny odeberte existující komunikační vztah. 

## <a name="monitoring-transaction-status"></a>Sledování stavu transakce

Pomocí zobrazení dynamické správy (DMVs) v databázi SQL sledovat stav a průběh probíhajících transakcí elastické databáze. Všechny DMVs související s transakcemi jsou relevantní pro distribuované transakce v databázi SQL. Odpovídající seznam dmvnas najdete zde: [Zobrazení a funkce dynamické správy související s transakcemi (Transact-SQL).](https://msdn.microsoft.com/library/ms178621.aspx)

Tyto DMV s a jsou obzvláště užitečné:

* **sys.dm\_\_aktivní\_transakce tran**: Seznamy aktuálně aktivních transakcí a jejich stav. Sloupec UOW (Jednotka práce) můžete identifikovat různé podřízené transakce, které patří do stejné distribuované transakce. Všechny transakce v rámci stejné distribuované transakce nesou stejnou hodnotu UOW. Další informace naleznete v [dokumentaci k dmv.](https://msdn.microsoft.com/library/ms174302.aspx)
* **sys.dm\_\_transakce\_tran databáze**: Poskytuje další informace o transakcích, jako je například umístění transakce v protokolu. Další informace naleznete v [dokumentaci k dmv.](https://msdn.microsoft.com/library/ms186957.aspx)
* **sys.dm\_\_uzamknout**: Poskytuje informace o zámky, které jsou aktuálně v držení probíhající transakce. Další informace naleznete v [dokumentaci k dmv.](https://msdn.microsoft.com/library/ms190345.aspx)

## <a name="limitations"></a>Omezení

Následující omezení se aktuálně vztahují na transakce elastické databáze v databázi SQL DB:

* Podporovány jsou pouze transakce mezi databázemi v databázi SQL DB. Ostatní zprostředkovatelé prostředků [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) a databáze mimo SQL DB se nemohou účastnit transakcí elastické databáze. To znamená, že transakce elastické databáze nelze roztáhnout napříč místně SQL Server a Azure SQL Database. Pro distribuované transakce v místním prostředí nadále používat Koordinátor MSDTC. 
* Podporovány jsou pouze transakce koordinované klientem z aplikace .NET. Podpora na straně serveru pro T-SQL, jako je begin distributed transaction je plánována, ale ještě není k dispozici. 
* Transakce napříč službami WCF nejsou podporovány. Například máte metodu služby WCF, která provádí transakci. Ohraničující volání v rámci oboru transakce se nezdaří jako [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Další kroky

Máte-li dotazy, obraťte se na nás na [fóru DATABÁZE SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) a pro žádosti o funkce, prosím, přidejte je do [fóra pro zpětnou vazbu SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png

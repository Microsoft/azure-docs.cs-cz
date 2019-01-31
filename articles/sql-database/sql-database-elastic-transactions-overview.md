---
title: Distribuované transakce v cloudových databázích
description: Přehled transakcí elastické databáze s využitím Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: ae9f4d1ebcb84748b665579104f63dab3ee6f076
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463867"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Distribuované transakce v cloudových databázích

Transakcí elastické databáze pro službu Azure SQL Database (databáze SQL) umožňují provozovat transakcí, které jsou rozmístěny v několika databází v SQL DB. U transakcí elastické databáze pro SQL DB jsou k dispozici pro aplikace .NET pomocí rozhraní ADO .NET a integrace s známé programování pomocí prostředí [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) třídy. Získat knihovny, naleznete v tématu [rozhraní .NET Framework 4.6.1 (Webová instalační služba)](https://www.microsoft.com/download/details.aspx?id=49981).

V místním prostředí takový scénář obvykle vyžaduje spuštění Microsoft distribuované transakce koordinátor (MSDTC). Protože příkaz MSDTC není k dispozici pro aplikaci platformy jako služby v Azure, umožňuje koordinovat distribuované transakce teď přímo integroval do databáze SQL. Aplikace můžou připojit k libovolné službě SQL Database ke spuštění distribuovaných transakcí a jedna z databází se transparentně koordinovat distribuovaných transakcí, jak je znázorněno na následujícím obrázku. 

  ![Distribuovaná transakce s Azure SQL Database s použitím transakcí elastické databáze ][1]

## <a name="common-scenarios"></a>Obvyklé scénáře

Transakcí elastické databáze SQL Database umožňují aplikacím atomic měnit data uložená v několika různých databázích SQL. Verzi preview se zaměřuje na vývoj klientské strany prostředí v jazyce C# a .NET. Na straně serveru prostředí pomocí jazyka T-SQL je naplánovaná na pozdější dobu.  
Transakcí elastické databáze, zaměřuje následující scénáře:

* Více databázových aplikací v Azure: V tomto scénáři je dat vertikálně dělené napříč několika databází v SQL DB tak, aby různé druhy dat jsou umístěny na různých databázích. Některé operace vyžadují změny dat, který je uložen ve dvou nebo více databází. Aplikace používá ke koordinaci změn napříč databázemi a zajištění atomicitu transakcí elastické databáze.
* Horizontálně dělené databáze aplikace v Azure: V tomto scénáři Datová vrstva používá [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md) nebo self horizontální dělení horizontálně dělit data v rozsáhlé skupině databází v SQL DB. Jeden případ použití viditelného je nutné provádět atomická změny pro horizontálně dělenou aplikaci s více tenanty, když změny span tenantů. Představte si třeba přenos z jednoho tenanta na jiný, obě umístěných v různých databázích. Druhou možností je podrobných horizontálního dělení s ohledem na kapacitu pro velké tenanta, která zase obvykle znamená, že některé atomických operací by měl Roztáhnout přes několik databází používaných pro stejného tenanta. Třetí případ má atomické aktualizace pro referenční data, která se replikují napříč databázemi. Atomické, podporou transakcí, operace spolu tyto řádky může nyní koordinovat napříč několika databází pomocí verze preview.
  Transakcí elastické databáze pomocí dvoufázového potvrzení zajistit transakce nedělitelnost napříč databázemi. Je vhodné pro transakce, které se týkají kratší než 100 databáze v době v rámci jedné transakce. Tato omezení se vynucují, ale jeden byste očekávat, že výkon a míra úspěšnosti pro elastické databáze transakcí, které trpí při překročení těchto omezení.

## <a name="installation-and-migration"></a>Instalace a migrace

Možnosti pro transakcí elastické databáze v SQL DB jsou k dispozici prostřednictvím aktualizace knihoven .NET System.Data.dll a System.Transactions.dll. Knihovny DLL Ujistěte se, že tento dvoufázového potvrzení se používá v případě potřeby zajistit atomicitu. Chcete-li začít s vývojem aplikací pomocí transakcí elastické databáze, nainstalovat [rozhraní .NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) nebo novější. Při spuštění na starší verzi rozhraní .NET framework, transakce se nepodaří povýšit na distribuovanou transakci a bude vyvolána výjimka.

Po dokončení instalace můžete použít distribuovaných transakcí rozhraní API System.Transactions s připojením k databázi SQL. Pokud máte existující aplikace MSDTC pomocí těchto rozhraní API, jednoduše znovu vytvořit existující aplikace pro .NET 4.6 po instalaci 4.6.1 Framework. Pokud vaše projekty jsou určené pro rozhraní .NET 4.6, budou automaticky používat aktualizované knihovny DLL z novou verzi rozhraní Framework a distribuovaných transakcí, které volání rozhraní API v kombinaci s připojením k SQL DB nyní úspěšné.

Mějte na paměti, transakcí elastické databáze nevyžadují instalaci MSDTC. Místo toho transakcí elastické databáze jsou spravováni přímo a v databázi SQL. To výrazně zjednodušuje scénáře v cloudu, protože nasazení MSDTC není nutné použít distribuované transakce s využitím SQL DB. Oddíl 4 podrobně vysvětluje, jak nasadit transakcí elastické databáze a vyžaduje rozhraní .NET framework společně s vašich cloudových aplikací do Azure.

## <a name="development-experience"></a>Vývojové prostředí

### <a name="multi-database-applications"></a>Více databázových aplikací

Následující vzorový kód používá známé programovací prostředí s .NET System.Transactions. Třídy TransactionScope vytváří okolí transakce v rozhraní .NET. ("Okolí transakce" je ten, který se nachází v aktuálním vlákně.) Všechna připojení otevřené v rámci objektu TransactionScope účasti v transakci. Pokud se účastnit různých databázích, transakce automaticky převedou na distribuovanou transakci. Výsledek transakce je řízen pomocí nastavení oboru dokončete označující potvrzení.

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

### <a name="sharded-database-applications"></a>Aplikace horizontálně dělené databáze

Transakcí elastické databáze SQL Database také podporují koordinace distribuované transakce, kde použít metodu OpenConnectionForKey Klientská knihovna elastic database k otevření připojení pro horizontálně datové vrstvy. Vezměte v úvahu případy, kdy potřebujete zajistit konzistenci transakcí změn napříč několika různých horizontálního dělení hodnoty klíče. Připojení k hostování hodnoty klíče horizontálního dělení různých horizontální oddíly jsou zprostředkovaných zpráv pomocí OpenConnectionForKey. V tomto obecném případě může být připojení do různých horizontálních oddílů tak, aby zajištění transakční záruky vyžaduje distribuovanou transakci. Tento postup znázorňuje následující ukázka kódu. Předpokládá, že proměnnou s názvem shardmap se používá k reprezentaci mapy horizontálních oddílů z Klientská knihovna elastic database:

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


## <a name="net-installation-for-azure-cloud-services"></a>Instalace rozhraní .NET pro Azure Cloud Services

Azure poskytuje několik nabídek k hostování aplikací .NET. Porovnání různých nabídky je k dispozici v [srovnání Azure App Service, Cloud Services a Virtual Machines](../app-service/overview-compare.md). Pokud hostovaný operační systém nabídky je menší než .NET 4.6.1 vyžaduje pro elastické transakce, budete muset upgradovat hostovaný operační systém na 4.6.1. 

Pro službu Azure App Services se momentálně nepodporuje upgrade na hostovaném operačním systému. Pro virtuální počítače Azure, jednoduše se přihlaste k virtuálnímu počítači a spusťte instalační program pro nejnovější rozhraní .NET framework. Pro Azure Cloud Services je potřeba se jednat o instalaci na novější verzi rozhraní .NET do úlohy po spuštění vašeho nasazení. Koncepty a kroky jsou popsané v [instalaci .NET v roli cloudové služby](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Všimněte si, že instalační program pro verzi .NET 4.6.1 může vyžadovat další dočasného úložiště během procesu samozaváděcí v Azure cloud services, než instalační program pro rozhraní .NET 4.6. Aby se zajistilo úspěšné instalaci, potřebujete zvýšit dočasné úložiště pro cloudové služby Azure v souboru ServiceDefinition.csdef v části LocalResources a nastavení prostředí při spuštění úlohy, jak je znázorněno v následujícím příkladu:

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

## <a name="transactions-across-multiple-servers"></a>Transakce napříč několika servery

Transakcí elastické databáze jsou podporovány na různé servery SQL Database ve službě Azure SQL Database. Když transakce překračují hranice databáze SQL serveru, zúčastněných serverů nejdřív zadat do vztahu vzájemnou komunikaci. Jakmile je navázaný vztah komunikace, všechny databáze v libovolné ze dvou serverů mohou účastnit elastické transakce s databází z jiného serveru. Vztahu komunikace s transakcemi pokrývající více než dva servery SQL Database, musí být nastavené pro jakýkoli pár servery SQL Database.

Správa relací mezi komunikaci mezi serverem pro transakcí elastické databáze pomocí následujících rutin Powershellu:

* **New-AzureRmSqlServerCommunicationLink**: Tuto rutinu použijte k vytvoření nové relace komunikace mezi dvěma servery SQL Database ve službě Azure SQL Database. Relace je symetrický, což znamená, že oba servery můžete zahájit transakce s jiným serverem.
* **Get-AzureRmSqlServerCommunicationLink**: Tuto rutinu použijte k načtení existujících relací komunikace a jejich vlastnosti.
* **Remove-AzureRmSqlServerCommunicationLink**: Tuto rutinu použijte k odebrání existujícího vztahu komunikace. 

## <a name="monitoring-transaction-status"></a>Monitorování stavu transakce

Monitorování stavu a průběhu zpracování transakcí probíhající elastické databáze pomocí zobrazení dynamické správy (DMV) v databázi SQL. Všechna zobrazení dynamické správy související s transakce jsou relevantní pro distribuované transakce v databázi SQL. Můžete najít odpovídající seznam zobrazení dynamické správy zde: [Transakce související s funkcí (Transact-SQL) a zobrazení dynamické správy](https://msdn.microsoft.com/library/ms178621.aspx).

Tato zobrazení dynamické správy jsou obzvláště užitečná:

* **sys.dm\_tran\_active\_transactions**: Obsahuje seznam aktuálně aktivních transakcí a jejich stav. Sloupec UOW (jednotka práce) můžete určit různé podřízené transakcí, které patří do stejné distribuované transakce. Všechny transakce v rámci jedné distribuované transakce mají stejnou hodnotu parametru UOW. Zobrazit [dokumentace k zobrazení dynamické správy](https://msdn.microsoft.com/library/ms174302.aspx) Další informace.
* **sys.dm\_tran\_database\_transactions**: Poskytuje další informace o transakcích, jako je například umístění transakce v protokolu. Zobrazit [dokumentace k zobrazení dynamické správy](https://msdn.microsoft.com/library/ms186957.aspx) Další informace.
* **sys.dm\_tran\_locks**: Poskytuje informace o zámky, které jsou aktuálně probíhající transakce. Zobrazit [dokumentace k zobrazení dynamické správy](https://msdn.microsoft.com/library/ms190345.aspx) Další informace.

## <a name="limitations"></a>Omezení

Transakcí elastické databáze SQL DB aktuálně platí následující omezení:

* Jsou podporovány pouze transakce v databázích v databázi SQL. Další [X / otevře XA](https://en.wikipedia.org/wiki/X/Open_XA) poskytovatelů prostředků a databází mimo SQL DB nemůže být součástí transakcí elastické databáze. To znamená, že transakcí elastické databáze nejde roztáhnout na místní SQL Server a Azure SQL Database. Pro distribuované transakce v místním prostředí i nadále pomocí příkaz MSDTC. 
* Jsou podporovány pouze klient koordinované transakce z aplikace .NET. Na straně serveru podpora T-SQL, jako je například začít DISTRIBUOVANÉ transakce je plánovaná, ale zatím není k dispozici. 
* Transakce ve službách WCF nejsou podporovány. Například máte metodu služby WCF, které provádí transakce. Zachytávají v rámci oboru transakce selže, jako [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Další postup

Máte dotazy, kontaktujte nás na [fórum SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) a pro žádosti o funkce, přidejte je do [fóru pro zpětnou vazbu SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png

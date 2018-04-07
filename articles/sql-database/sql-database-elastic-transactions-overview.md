---
title: Distribuované transakce v cloudových databázích
description: Přehled transakcí elastické databáze s databází Azure SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.topic: article
ms.custom: scale out apps
ms.workload: On Demand
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 37de2a6918012707ae171415ac8f043d6f9cb1ae
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="distributed-transactions-across-cloud-databases"></a>Distribuované transakce v cloudových databázích
Transakcí elastické databáze pro Azure SQL Database (databáze SQL) umožňují spustit transakce, které jsou rozmístěny v několika databází v databázi SQL. Jsou k dispozici pro aplikace .NET pomocí rozhraní ADO .NET transakcí elastické databáze pro databázi SQL a integrovat známé programování pomocí prostředí [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) třídy. Pokud chcete získat knihovny, přečtěte si téma [rozhraní .NET Framework 4.6.1 (Webová instalační služba)](https://www.microsoft.com/download/details.aspx?id=49981).

Místní takové situaci většinou vyžaduje systémem Microsoft Distributed Transaction Coordinator služba MSDTC (). Vzhledem k tomu, že služby MS DTC není k dispozici pro aplikaci platforma jako služba v Azure, možnost koordinovat distribuované transakce, nyní přímo integrovány do databáze SQL. Aplikace se můžou připojit k jakékoli databázi SQL ke spuštění distribuovaných transakcí a jedna z databází se transparentně koordinovat distribuované transakce, jak je znázorněno na následujícím obrázku. 

  ![Distribuovaná transakce s Azure SQL Database pomocí transakcí elastické databáze ][1]

## <a name="common-scenarios"></a>Obvyklé scénáře
Transakcí elastické databáze pro databázi SQL umožňuje aplikacím atomic měnit data uložená v několika různých databází SQL. Ve verzi preview se zaměřuje na straně klienta vývoj prostředí v C# a rozhraní .NET. Na straně serveru zkušenosti s používáním T-SQL je naplánován na pozdější dobu.  
Transakcí elastické databáze cílem následující scénáře:

* Více databázových aplikací v Azure: Tento scénář je dat svisle rozděleného mezi několika databází v databázi SQL tak, že různé druhy dat jsou umístěné na různých databází. Některé operace vyžadují změny dat, který je uložen ve dvou nebo více databází. Aplikace používá pro koordinaci změny napříč databáze a ujistěte se, nedělitelnost transakcí elastické databáze.
* Horizontálně dělené databázové aplikace v Azure: Tento scénář Datová vrstva používá [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md) nebo samoobslužné-horizontálního dělení do vodorovně oddílu data mezi mnoha databázemi v databázi SQL. Jeden případ použití viditelného je potřeba provést atomic změny pro horizontálně dělenou víceklientské aplikace v případě, že změny span klientů. Představte si například přenos z jednoho klienta do jiné, oba umístěný na různých databází. Druhém případě je podrobných horizontálního dělení zohlednit požadavků na kapacitu pro velké klienta, který naopak obvykle znamená, že některé atomická operace se musí Roztáhnout přes několik databází použitých pro stejné klienta. Třetí případem je atomic aktualizace referenční data, která se replikují mezi databázemi. Operace Atomic, zpracovaných, podle těchto zásad můžete nyní koordinované napříč několika databází pomocí verze preview.
  Transakcí elastické databáze pomocí dvojfázového zápisu transakce nedělitelnost mezi databázemi. Je vhodné pro transakce zahrnující méně než 100 databáze v době v rámci jedné transakce. Tato omezení nejsou vynucena, ale jeden očekávat výkon a úspěšnost pro elastické databáze transakce snížení při překročení těchto mezních hodnot.

## <a name="installation-and-migration"></a>Instalace a migrace
Možnosti pro elastické databáze transakce v databázi SQL je zajišťována prostřednictvím aktualizace na knihovny .NET System.Data.dll a System.Transactions.dll. Knihovny DLL Ujistěte se, že tento dvojfázového zápisu se používá tam, kde je to nezbytné pro zajištění Nedělitelnost. Chcete-li začít vyvíjet aplikace, které používají transakcí elastické databáze, nainstalovat [rozhraní .NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) nebo novější. Při spuštění ve starší verzi rozhraní .NET Framework, transakce se nepodaří povýšit na distribuovanou transakci a k výjimce.

Po instalaci můžete distribuované transakce rozhraní API v System.Transactions – s připojením k databázi SQL. Pokud máte existující služby MSDTC aplikací pomocí těchto rozhraní API, po instalaci 4.6.1 jednoduše sestavit vaší stávající aplikace pro 4.6 rozhraní .NET Framework. Pokud projekty cílí na .NET 4.6, automaticky použijí aktualizované knihovny DLL z nové verze Framework a nyní úspěšné distribuované transakce, které volání rozhraní API v kombinaci s připojení k databázi SQL.

Mějte na paměti, že transakcí elastické databáze nevyžadují instalaci služby MSDTC. Místo toho transakcí elastické databáze jsou spravovány přímo a v databázi SQL. To výrazně zjednodušuje scénářích cloudu, protože nasazení služby MS DTC není potřeba pomocí distribuovaných transakcí v databázi SQL. Oddíl 4 podrobněji vysvětluje, jak nasadit transakcí elastické databáze a vyžaduje rozhraní .NET framework společně s cloudové aplikace do Azure.

## <a name="development-experience"></a>Vývojové prostředí
### <a name="multi-database-applications"></a>Více databázové aplikace
Následující vzorový kód používá známé programovací prostředí s .NET System.Transactions. Třída TransactionScope vytváří ambientní transakce v rozhraní .NET. ("Vedlejším transakcí" je ten, který je umístěn v aktuální vlákno.) Všechna připojení otevřené v rámci v objektu TransactionScope účastnit transakce. Jestliže se účastní různých databází, transakce se automaticky se zvýšenými oprávněními na distribuovanou transakci. Výsledek transakce je řízena nastavením oboru k dokončení znamenat potvrzení.

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

### <a name="sharded-database-applications"></a>Horizontálně dělené databázové aplikace
Transakcí elastické databáze pro databázi SQL také podporují koordinace distribuované transakce, kde používáte metodu OpenConnectionForKey Klientská knihovna pro elastické databáze k otevření připojení pro škálovaný dat vrstvy. Vezměte v úvahu případy, kdy potřebujete zaručit transakční konzistence na změny napříč několika různých horizontálního dělení hodnoty klíče. Připojení k horizontálních oddílů hostování různých horizontálního dělení hodnoty klíče jsou zprostředkované pomocí OpenConnectionForKey. V případě Obecné může být připojení do různých horizontálních oddílů tak, že zajistíte transakční záruky vyžaduje distribuované transakce. Následující příklad kódu ukazuje tento přístup. Předpokládá, že proměnné s názvem shardmap se používá k reprezentování mapu horizontálního oddílu z klientské knihovny pro elastické databáze:

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
Azure poskytuje několik nabídky k hostování aplikací založených na rozhraní .NET. Porovnání různé nabídky jsou k dispozici v [porovnání služby Azure App Service, cloudové služby a virtuální počítače](../app-service/choose-web-site-cloud-service-vm.md). Pokud hostovaný operační systém nabídky je menší než .NET 4.6.1 vyžaduje pro elastické transakce, budete muset upgradovat hostovaný operační systém na 4.6.1. 

Pro aplikační služby Azure nejsou aktuálně podporované upgrady hostovaný operační systém. Pro virtuální počítače Azure, jednoduše Přihlaste se k virtuálnímu počítači a spusťte instalační program pro nejnovější rozhraní .NET framework. Azure Cloud Services musíte zahrnout instalaci novější verze rozhraní .NET do spuštění úlohy nasazení. Principy a postupy popsané v [nainstalovat rozhraní .NET v roli služby Cloud](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Všimněte si, že instalační program pro .NET 4.6.1 může vyžadovat další dočasné úložiště během procesu samozaváděcí na cloudové služby Azure než instalační program pro .NET 4.6. Chcete-li zajistit úspěšnou instalaci, je potřeba zvýšit dočasné úložiště pro cloudové služby Azure v souboru ServiceDefinition.csdef v části LocalResources a nastavení prostředí spuštění úkolu, jak znázorňuje následující ukázka:

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
Transakce elastické databáze jsou podporovány na různé servery logické ve službě Azure SQL Database. Pokud transakce kříží hranice logického serveru, zúčastněných serverů nejprve je třeba zadat do vztahu vzájemné komunikaci. Po vytvoření relace komunikace, všechny databáze v některém ze dvou serverů účastnit elastické transakcí s databází z druhý server. Vztah komunikace s transakcemi pokrývání uzlů více než dva logické servery, musí být nastavené pro jakýkoli pár logické servery.

Správa vztahů komunikaci mezi servery pro transakcí elastické databáze pomocí následující rutiny prostředí PowerShell:

* **Nové AzureRmSqlServerCommunicationLink**: tuto rutinu použijte k vytvoření nového vztahu komunikace mezi dvěma logické servery v databázi SQL Azure. Relace je symetrický, což znamená, že oba servery můžete zahájit transakce s druhý server.
* **Get-AzureRmSqlServerCommunicationLink**: tuto rutinu použijte k načtení existující komunikace vztahy a jejich vlastnosti.
* **Odebrat AzureRmSqlServerCommunicationLink**: pomocí této rutiny odeberte existující relaci komunikace. 

## <a name="monitoring-transaction-status"></a>Monitorování stavu transakce
Monitorování stavu a průběhu transakcí probíhající elastické databáze pomocí zobrazení dynamické správy (zobrazení dynamické správy) v databázi SQL. Všechny zobrazení dynamické správy související s transakce jsou relevantní pro distribuované transakce v databázi SQL. Můžete najít odpovídající seznamu zobrazení dynamické správy zde: [transakce související dynamické správy zobrazení a funkce (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Tato zobrazení dynamické správy jsou obzvláště užitečná:

* **Sys.DM\_tran\_active\_transakce**: uvádí aktuálně aktivních transakcí a jejich stav. Sloupec UOW (jednotka práce) můžete určit jiné podřízené transakcí, které patří do stejné distribuované transakce. Všechny transakce v rámci stejné distribuované transakce provádění stejnou hodnotu parametru UOW. Najdete v článku [DMV dokumentaci](https://msdn.microsoft.com/library/ms174302.aspx) Další informace.
* **Sys.DM\_tran\_databáze\_transakce**: poskytuje další informace o transakcích, jako je například umístění transakce v protokolu. Najdete v článku [DMV dokumentaci](https://msdn.microsoft.com/library/ms186957.aspx) Další informace.
* **Sys.DM\_tran\_zámky**: poskytuje informace o zámky, které jsou aktuálně uložené probíhající transakce. Najdete v článku [DMV dokumentaci](https://msdn.microsoft.com/library/ms190345.aspx) Další informace.

## <a name="limitations"></a>Omezení
Pro elastické databáze transakce v databázi SQL aktuálně platí následující omezení:

* Jsou podporovány pouze transakce napříč databázemi v databázi SQL. Další [X / otevřete XA](https://en.wikipedia.org/wiki/X/Open_XA) zprostředkovatelé prostředků a databáze mimo SQL DB nemůže se podílet na transakcí elastické databáze. To znamená, že transakcí elastické databáze nelze roztáhnout na místní systém SQL Server a databáze SQL Azure. Pro distribuované transakce místně nadále používat služby MS DTC. 
* Jsou podporovány pouze klienta koordinované transakce z aplikace .NET. Podpora serverové T-SQL, jako je například začít DISTRIBUOVANÝCH transakcí je plánované, ale ještě není k dispozici. 
* Transakce ve službách WCF nejsou podporovány. Například máte metodu služby WCF, které provádí transakce. Jako zachytávají v oboru transakce se nezdaří [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Další postup
Máte dotazy, kontaktujte nás na [fórum SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) a pro žádosti o funkce, přidejte je do [fóru pro zpětnou vazbu SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png




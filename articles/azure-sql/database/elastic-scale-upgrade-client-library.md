---
title: Upgrade na nejnovější klientskou knihovnu elastické databáze
description: Pomocí NuGetu upgradujte klientskou knihovnu elastické databáze.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793411"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Upgrade aplikace na použití nejnovější klientské knihovny pro elastickou databázi
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nové verze [elastic Database klientské knihovny](elastic-database-client-library.md) jsou k dispozici prostřednictvím NuGet a rozhraní Správce balíčků NuGet v aplikaci Visual Studio. Upgrady obsahují opravy chyb a podporu pro nové funkce klientské knihovny.

**Pro nejnovější verzi:** Přejít na [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Znovu sestavte aplikaci s novou knihovnou a také změňte existující metadata správce mapy horizontálních oddílů uložené v databázích v Azure SQL Database pro podporu nových funkcí.

Provedení těchto kroků v pořadí zajistí, že starší verze klientské knihovny nebudou ve vašem prostředí nadále přítomny, když jsou aktualizovány objekty metadat, což znamená, že objekty metadat staré verze nebudou vytvořeny po upgradu.

## <a name="upgrade-steps"></a>Postup upgradu

**1. Upgradujte své aplikace.** V aplikaci Visual Studio Stáhněte a odkazujte na nejnovější verzi klientské knihovny do všech vývojových projektů, které knihovnu používají. pak znovu sestavte a nasaďte.

* V řešení sady Visual Studio vyberte **nástroje**  -->  **Správce balíčků NuGet**  -->   **Spravovat balíčky NuGet pro řešení**.
* (Visual Studio 2013) Na levém panelu vyberte **aktualizace** a potom vyberte tlačítko **aktualizovat** v balíčku **Azure SQL Database klientské knihovny elastického škálování** , které se zobrazí v okně.
* (Visual Studio 2015) Nastavte pole filtru na **Upgrade k dispozici**. Vyberte balíček, který chcete aktualizovat, a klikněte na tlačítko **aktualizovat** .
* (Visual Studio 2017) V horní části dialogového okna vyberte **aktualizace**. Vyberte balíček, který chcete aktualizovat, a klikněte na tlačítko **aktualizovat** .
* Sestavování a nasazování.

**2. upgradujte skripty.** Pokud ke správě horizontálních oddílů používáte skripty **prostředí PowerShell** , [Stáhněte si novou verzi knihovny](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) a zkopírujte ji do adresáře, ze kterého spouštíte skripty.

**3. upgradujte službu pro dělení a slučování.** Pokud k reorganizaci dat horizontálně dělené používáte nástroj pro dělení elastické databáze, [Stáhněte a nasaďte nejnovější verzi nástroje](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Podrobný postup upgradu pro službu najdete [tady](elastic-scale-overview-split-and-merge.md).

**4. upgradujte svoje databáze správce mapy horizontálních oddílů**. Upgradujte metadata podporující mapy horizontálních oddílů v Azure SQL Database.  To můžete provést dvěma způsoby pomocí PowerShellu nebo C#. Níže jsou uvedeny obě možnosti.

***Možnost 1: upgrade metadat pomocí PowerShellu***

1. Stáhněte si nejnovější nástroj příkazového řádku pro NuGet z [tohoto místa](https://nuget.org/nuget.exe) a uložte ho do složky.
2. Otevřete příkazový řádek, přejděte do stejné složky a vydejte příkaz: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Přejděte do podsložky obsahující novou verzi klientské knihovny DLL, kterou jste právě stáhli, například: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Stáhněte si skript upgradu klienta elastické databáze z [centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)a uložte ho do stejné složky, která obsahuje knihovnu DLL.
5. Z této složky spusťte z příkazového řádku PowerShell .\upgrade.ps1 a postupujte podle zobrazených výzev.

***Možnost 2: upgrade metadat pomocí jazyka C #***

Případně můžete vytvořit aplikaci Visual Studio, která otevře vaši ShardMapManager, iterovat všechny horizontálních oddílů a provede upgrade metadat voláním metod [UpgradeLocalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) a [UpgradeGlobalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) jako v tomto příkladu:

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

Tyto techniky pro upgrady metadat lze použít několikrát bez poškození. Pokud například starší verze klienta neúmyslně vytvoří horizontálních oddílů po aktualizaci, můžete znovu spustit upgrade napříč všemi horizontálních oddílů a zajistit, aby byla v celé infrastruktuře k dispozici nejnovější verze metadat.

**Poznámka:**  Nové verze klientských knihoven, které jsou publikovány k datu, fungují s předchozími verzemi metadat správce map horizontálních oddílů na Azure SQL Database a naopak.   Pokud ale chcete využít výhod některých nových funkcí v nejnovějším klientovi, je potřeba upgradovat metadata.   Aktualizace metadat nebudou mít vliv na data týkající se dat uživatelů nebo aplikací, pouze objekty vytvořené a používané správcem mapy horizontálních oddílů.  A aplikace pokračují v provozu prostřednictvím pořadí upgradu popsaného výše.

## <a name="elastic-database-client-version-history"></a>Historie verzí klienta elastické databáze

Pro historii verzí použijte [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) .

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
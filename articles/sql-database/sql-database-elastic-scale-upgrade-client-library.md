---
title: Upgrade na nejnovější klientskou knihovnu elastické databáze
description: Pomocí aplikace Nuget můžete upgradovat klientskou knihovnu elastické databáze.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: a1ab684f13c56698d4359f2bf74826f3dd696c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823511"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Upgrade aplikace tak, aby používala nejnovější klientskou knihovnu elastické databáze

Nové verze [klientské knihovny elastická databáze](sql-database-elastic-database-client-library.md) jsou k dispozici prostřednictvím NuGet a rozhraní NuGet Package Manager v sadě Visual Studio. Inovace obsahují opravy chyb a podporu nových funkcí klientské knihovny.

**Pro nejnovější verzi:** Přejděte na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Znovu sestavte aplikaci pomocí nové knihovny a změňte stávající metadata Správce map svižné části uložené v databázích Azure SQL tak, aby podporovala nové funkce.

Provedení těchto kroků v pořadí zajišťuje, že staré verze klientské knihovny již nejsou k dispozici ve vašem prostředí při aktualizaci objektů metadat, což znamená, že objekty metadat staré verze nebudou vytvořeny po upgradu.

## <a name="upgrade-steps"></a>Kroky upgradu

**1. Inovujte své aplikace.** V sadě Visual Studio stáhněte a odkazujte na nejnovější verzi klientské knihovny do všech vývojových projektů, které knihovnu používají. pak znovu sestavit a nasadit.

* V řešení Sady Visual Studio vyberte **nástroje, které** --> **spravuje správce** -->  balíčků**NuGet, aby se podařilo spravovat balíčky nugetů pro řešení**.
* (Visual Studio 2013) V levém panelu vyberte **Aktualizace**a pak vyberte tlačítko **Aktualizovat** v balíčku **Azure SQL Database Elastic Scale client library,** který se zobrazí v okně.
* (Visual Studio 2015) Nastavte pole Filtr na **upgrade k dispozici**. Vyberte balíček, který chcete aktualizovat, a klepněte na tlačítko **Aktualizovat.**
* (Visual Studio 2017) V horní části dialogového okna vyberte **Aktualizace**. Vyberte balíček, který chcete aktualizovat, a klepněte na tlačítko **Aktualizovat.**
* Sestavení a nasazení.

**2. Inovujte skripty.** Pokud ke správě štrůdků používáte skripty **prostředí PowerShell,** [stáhněte novou verzi knihovny](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) a zkopírujte ji do adresáře, ze kterého spouštíte skripty.

**3. Inovujte službu rozdělení sloučení.** Pokud použijete nástroj pro sloučení elastické databáze k reorganizaci dělených dat, [stáhněte a nasaďte nejnovější verzi nástroje](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Podrobné kroky upgradu služby naleznete [zde](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Inovujte databáze Správce map svižnků**. Upgradujte metadata podporující mapy síní v Azure SQL Database.  Existují dva způsoby, jak toho dosáhnout pomocí Prostředí PowerShell nebo C#. Obě možnosti jsou uvedeny níže.

***Možnost 1: Upgrade metadat pomocí PowerShellu***

1. Stáhněte si nejnovější nástroj příkazového řádku pro NuGet [odtud](https://nuget.org/nuget.exe) a uložte do složky.
2. Otevřete příkazový řádek, přejděte do stejné složky a vyjezte příkaz:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Přejděte do podsložky obsahující novou klientskou verzi dll, kterou jste právě stáhli, například:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Stáhněte si skript pro upgrade klienta elastické databáze z [Centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)a uložte jej do stejné složky obsahující dll.
5. Z této složky spusťte z příkazového řádku prostředí PowerShell .\upgrade.ps1 a postupujte podle pokynů.

***Možnost 2: Upgrade metadat pomocí jazyka C #***

Případně vytvořte aplikaci Sady Visual Studio, která otevře shardMapManager, itetuje přes všechny oddíly a provede upgrade metadat voláním metody [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) a [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) jako v tomto příkladu:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Tyto techniky pro upgrady metadat lze použít vícekrát bez poškození. Například pokud starší verze klienta neúmyslně vytvoří šiřík po již aktualizovány, můžete spustit upgrade znovu přes všechny oddíly a zajistit, že nejnovější verze metadat je k dispozici v celé infrastruktuře.

**Poznámka:**  Nové verze klientské knihovny publikované k dnešnímu dni nadále pracují s předchozími verzemi metadat Správce map svižných bitových částí v Azure SQL DB a naopak.   Chcete-li však využít některé nové funkce v nejnovějším klientovi, je třeba upgradovat metadata.   Všimněte si, že upgrady metadat nebude mít vliv na všechna data uživatele nebo data specifická pro aplikaci, pouze objekty vytvořené a používané Správce map střepů.  A aplikace nadále pracovat prostřednictvím sekvence upgradu popsané výše.

## <a name="elastic-database-client-version-history"></a>Historie verzí klienta elastické databáze

Pokud jde o historii verzí, přejděte na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

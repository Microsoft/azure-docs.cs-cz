---
title: Upgrade na nejnovější klientské knihovny elastické databáze | Microsoft Docs
description: Klientská knihovna pro upgrade elastické databáze pomocí balíčku Nuget.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 56500a21822f7c7122a20cb676b9ad307de49c87
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647006"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Upgrade aplikace používat nejnovější klientské knihovny elastické databáze
Nové verze [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md) jsou k dispozici prostřednictvím NuGetand rozhraní NuGetPackage správce v sadě Visual Studio. Upgrady obsahovat oprav chyb a podporu pro nové funkce knihovny klienta.

**Nejnovější verzi:** přejít na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Znovu sestavte aplikaci s novou knihovnu, a také změnit vaše stávající správce mapy horizontálního oddílu metadata uložená v databázích SQL Azure pro podporu nových funkcí.

Provedením těchto kroků v pořadí zajistí staré verze klientské knihovny už existuje ve vašem prostředí při aktualizaci metadat objektů, což znamená, že objekty starý verze metadat nevytvoří po upgradu.   

## <a name="upgrade-steps"></a>Postup upgradu
**1. Upgrade aplikace.** V sadě Visual Studio stáhněte a odkazovat na nejnovější verzi knihovny klienta do všech vašich projektů vývoj, které používají knihovnu; poté znovu vytvořit a nasadit. 

* V řešení sady Visual Studio vyberte **nástroje** --> **Správce balíčků NuGet** -->  **spravovat balíčky NuGet pro řešení**. 
* (Visual Studio 2013) V levém panelu, vyberte **aktualizace**a pak vyberte **aktualizace** tlačítko na balíček **Azure SQL Database elastické škálování klientské knihovny** které se zobrazí v okně.
* (Visual Studio 2015) Nastavte filtr na **Upgrade k dispozici**. Vyberte balíček, který chcete aktualizovat a klikněte na **aktualizace** tlačítko.
* (Visual Studio 2017) V horní části okna vyberte **aktualizace**. Vyberte balíček, který chcete aktualizovat a klikněte na **aktualizace** tlačítko.
* Sestavení a nasazení. 

**2. Upgrade skripty.** Pokud používáte **prostředí PowerShell** skripty pro správu horizontálních oddílů, [stáhněte si novou verzi knihovny](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) a zkopírujte jej do adresáře, ze kterého můžete spustit skripty. 

**3. Upgrade služby rozdělení sloučení.** Pokud použijete nástroj rozdělení sloučení elastické databáze reorganizace horizontálně dělená data [stáhnout a nasadit nejnovější verzi nástroje](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Podrobné kroky upgradu naleznete službu [zde](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Upgrade vašich databází správce mapy horizontálního oddílu**. Upgrade metadat podpora vaší mapy horizontálního oddílu v Azure SQL Database.  Existují dva způsoby, můžete to provést, pomocí prostředí PowerShell nebo C#. Níže jsou uvedeny obě možnosti.

***Možnost 1: Upgrade metadat pomocí prostředí PowerShell***

1. Stáhněte si nejnovější nástroj příkazového řádku pro NuGet z [zde](http://nuget.org/nuget.exe) a uložte do složky. 
2. Otevřete příkazový řádek, přejděte do stejné složky a vydejte příkaz: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Přejděte do podsložky, která obsahuje nové verze knihoven DLL klienta, který jste právě stáhli, například: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Stáhnout upgradu skriptlet elastické databáze klienta z [centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)a uložte ho do stejné složky, která obsahuje knihovnu DLL.
5. Z této složky spusťte z příkazového řádku "PowerShell.\upgrade.ps1" a postupujte podle pokynů.

***Možnost 2: Upgrade metadat pomocí jazyka C#***

Můžete taky vytvořit aplikaci Visual Studio, která otevře vaše ShardMapManager iteruje nad všechny horizontálních oddílů a provede upgrade metadat voláním metody [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) a [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) jako v následujícím příkladu: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Tyto postupy pro metadata upgrady je možné použít více než jednou. bez škodu. Například pokud starší verze klienta nechtěně vytvoří horizontálního oddílu po již aktualizaci, můžete spustit upgrade znovu napříč všechny horizontálních oddílů zajistit, že je nainstalována v celé infrastruktuře nejnovější verze metadat. 

**Poznámka:** nové verze klientské knihovny publikovaná-datum pokračovat v práci s předchozím verzím metadaty správce mapy horizontálního oddílu na Azure SQL DB a naopak.   Ale pokud chcete využít výhod některé z nových funkcí v nejnovější verzi klienta, je nutno aktualizovat metadata.   Všimněte si, že metadata upgrady nebude mít vliv na všechna uživatelská data nebo data specifické pro aplikaci, pouze objekty vytvořené a pomocí správce mapy horizontálního oddílu.  A aplikace i nadále fungovat prostřednictvím pořadí upgradu popsané výše. 

## <a name="elastic-database-client-version-history"></a>Historie verzí klienta elastické databáze
Historie verzí najdete na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png


---
title: Upgrade na nejnovější Klientská knihovna elastic database | Dokumentace Microsoftu
description: Klientská knihovna elastic database upgradu pomocí Nuget.
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
ms.date: 04/01/2018
ms.openlocfilehash: e54a2cf40bb03ef573348f276b4c24e20965009f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866132"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Upgrade aplikace použít nejnovější Klientská knihovna elastic database
Nové verze [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md) jsou k dispozici prostřednictvím NuGetand rozhraní NuGetPackage správce v sadě Visual Studio. Upgrady obsahují opravy chyb a podporu pro nové funkce knihovny klienta.

**Nejnovější verze:** přejít na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Opětovné sestavení aplikace s novou knihovnu, jakož i změnit stávající správce mapování horizontálních oddílů metadata uložená v databázích Azure SQL pro podporu nové funkce.

Provedením těchto kroků v pořadí zajistí, že starší verze klientské knihovny už neexistují ve vašem prostředí při aktualizaci metadat objektů, což znamená, že objekty staré verze metadat se nevytvoří po upgradu.   

## <a name="upgrade-steps"></a>Postup upgradu
**1. Upgrade aplikace.** V sadě Visual Studio stáhněte si a odkazovat na nejnovější verzi knihovny klienta do všech vašich vývojových projektech, které používají knihovnu; potom znovu sestavit a nasadit. 

* V řešení sady Visual Studio vyberte **nástroje** --> **Správce balíčků NuGet** -->  **spravovat balíčky NuGet pro řešení**. 
* (Visual Studio 2013) Na levém panelu, vyberte **aktualizace**a pak vyberte **aktualizace** tlačítko na balíček **Azure SQL Database Elastic klientské knihovny pro škálování** , který se zobrazí v okně.
* (Visual Studio 2015) Nastavte filtr na **Upgrade k dispozici**. Vyberte balíček aktualizovat, a klikněte na tlačítko **aktualizovat** tlačítko.
* (Visual Studio 2017) V horní části dialogového okna, vyberte **aktualizace**. Vyberte balíček aktualizovat, a klikněte na tlačítko **aktualizovat** tlačítko.
* Sestavení a nasazení. 

**2. Upgradujte vaše skripty.** Pokud používáte **PowerShell** skriptů pro správu horizontálních oddílů, [stáhnout novou verzi knihovny](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) a zkopírujte ho do adresáře, ze kterého můžete spustit skripty. 

**3. Upgrade služby dělení a slučování.** Pokud používáte nástroj split-merge elastické databáze reorganizovat horizontálně dělených dat [stáhnout a nasadit nejnovější verzi nástroje](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Podrobné kroky upgradu pro službu najdete [tady](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Upgrade databáze správce mapování horizontálních oddílů**. Upgrade metadat podporuje vaše mapy horizontálních oddílů ve službě Azure SQL Database.  Existují dva způsoby, můžete to provést, pomocí Powershellu nebo C#. Obě možnosti jsou uvedeny níže.

***Možnost 1: Upgrade metadat pomocí Powershellu***

1. Stáhněte si nejnovější nástroje příkazového řádku pro NuGet z [tady](http://nuget.org/nuget.exe) a uložte do složky. 
2. Otevřete příkazový řádek, přejděte do stejné složky a vydejte příkaz: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Přejděte do podsložky obsahující na novou verzi klienta knihovny DLL, které jste si právě stáhli, například: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Stáhněte si klienta elastické databáze upgradu skriptletu z [centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)a uložte ho do stejné složky obsahující knihovnu DLL.
5. Z této složky spusťte "Powershellu.\upgrade.ps1" z příkazového řádku a postupujte podle zobrazených výzev.

***Možnost 2: Upgrade metadat pomocí jazyka C#***

Můžete také vytvořit aplikaci Visual Studio, která otevře vaše ShardMapManager Iteruje přes všechny horizontální oddíly a provede upgrade metadat voláním metody [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) a [UpgradeGlobalStore ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) jako v následujícím příkladu: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Tyto postupy pro aktualizace metadat lze použít bez poškození více než jednou. Například pokud starší verze klienta se neúmyslně vytvoří horizontálního oddílu, po aktualizaci již, můžete spustit upgrade znovu napříč všemi horizontálními oddíly na Ujistěte se, že nejnovější verze metadat se nachází v rámci vaší infrastruktury. 

**Poznámka:** k datu publikování nové verze klientské knihovny i nadále fungovat se staršími verzemi metadat Správce mapování horizontálních oddílů ve službě Azure SQL DB a naopak.   Ale využívat některé nové funkce v nejnovější verzi klienta, je nutno aktualizovat metadata.   Všimněte si, že metadata upgrady nebude mít vliv na všechna uživatelská data nebo data specifická pro aplikaci, pouze objekty vytvořené a používá správce mapování horizontálních oddílů.  A aplikace i nadále pracovat prostřednictvím pořadí upgradu je popsáno výše. 

## <a name="elastic-database-client-version-history"></a>Historie verzí klienta elastické databáze
Historie verzí, v části [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png


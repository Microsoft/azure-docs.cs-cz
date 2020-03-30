---
title: Čítače výkonu pro sledování správce map úlomků
description: Čítače výkonu směrování na třídě ShardMapManager a na datech
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823900"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Vytvoření čítačů výkonu pro sledování výkonu správce map střepů

Čítače výkonu se používají ke sledování výkonu [operací směrování závislých na datech.](sql-database-elastic-scale-data-dependent-routing.md) Tyto čítače jsou přístupné v sledování výkonu v kategorii "ElasticDatabase: Střep management".

Můžete zachytit výkon [správce mapy sypů](sql-database-elastic-scale-shard-map-management.md), zejména při použití [směrování závislého na datech](sql-database-elastic-scale-data-dependent-routing.md). Čítače jsou vytvářeny pomocí metod třídy Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Pro nejnovější verzi:** Přejděte na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Viz také [Upgrade aplikace pro použití nejnovější knihovny klienta elastické databáze](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Požadavky

* Chcete-li vytvořit kategorii výkonu a čítače, musí být uživatel součástí místní **skupiny Administrators** v počítači hostujícím aplikaci.  
* Chcete-li vytvořit instanci čítače výkonu a aktualizovat čítače, musí být uživatel členem **skupiny Administrators** nebo **Performance Monitor Users.**

## <a name="create-performance-category-and-counters"></a>Vytvoření výkonnostní kategorie a čítačů

Chcete-li vytvořit čítače, zavolejte metodu CreatePerformanceCategoryAndCounters [třídy ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Metodu může spustit pouze správce:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Tuto metodu můžete také použít pomocí [tohoto](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skriptu prostředí PowerShell.
Metoda vytvoří následující čítače výkonu:  

* **Mapování v mezipaměti**: Počet mapování uložených v mezipaměti pro mapu úlomků.
* **Operace DDR/s**: Rychlost operací směrování závislých na datech pro mapu střepu. Tento čítač je aktualizován, když volání [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) má za následek úspěšné připojení k cílovému oddílu.
* **Mapování přístupů do vyhledávací mezipaměti/s**: Rychlost úspěšných operací vyhledávání mezipaměti pro mapování v mapě šibenicí.
* **Mapování neúspěšných přístupů mezipaměť/s**: Rychlost neúspěšných operací vyhledávání mezipaměti pro mapování v mapě šibenicí.
* **Mapování přidané nebo aktualizované v mezipaměti/s**: Rychlost, jakou jsou mapování přidávána nebo aktualizována v mezipaměti pro mapu střepu.
* **Mapování odebrané z mezipaměti/s**: Rychlost, jakou jsou mapování odebírána z mezipaměti pro mapu střepu.

Čítače výkonu jsou vytvořeny pro každou mapu šitrů uložených v mezipaměti na proces.  

## <a name="notes"></a>Poznámky

Následující události aktivují vytvoření čítačů výkonu:  

* Inicializace [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) s [eager načítání](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), pokud ShardMapManager obsahuje všechny mapy střepů. Patří mezi ně [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) a [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) metody.
* Úspěšné vyhledávání mapy střepů (pomocí [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) nebo [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Úspěšné vytvoření mapy střepů pomocí CreateShardMap().

Čítače výkonu budou aktualizovány všemi operacemi mezipaměti prováděnými na mapě šibenicí a mapování. Úspěšné odebrání mapy horizontálního oddílu pomocí DeleteShardMap() má za následek odstranění instance čítačů výkonu.  

## <a name="best-practices"></a>Osvědčené postupy

* Vytvoření kategorie výkonu a čítače by měla být provedena pouze jednou před vytvořením objektu ShardMapManager. Každé spuštění příkazu CreatePerformanceCategoryAndCounters() vymaže předchozí čítače (ztráta dat hlášených všemi instancemi) a vytvoří nové.  
* Instance čítače výkonu jsou vytvářeny pro jeden proces. Jakýkoli pád aplikace nebo odebrání mapy horizontálního oddílu z mezipaměti bude mít za následek odstranění instancí čítačů výkonu.  

### <a name="see-also"></a>Viz také

[Přehled funkcí Elastic Database](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

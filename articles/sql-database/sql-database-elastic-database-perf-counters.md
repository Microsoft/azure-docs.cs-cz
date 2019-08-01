---
title: Vytváření čítačů výkonu pro sledování výkonu správce map horizontálních oddílů
description: Čítače výkonu směrování závislých na datech třídy ShardMapManager a dat
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568674"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Vytváření čítačů výkonu pro sledování výkonu správce map horizontálních oddílů

Čítače výkonu se používají ke sledování výkonu operací [Směrování závislých na datech](sql-database-elastic-scale-data-dependent-routing.md) . Tyto čítače jsou dostupné v nástroji Sledování výkonu v rámci "Elastic Database: Kategorie Správa horizontálních oddílů

Můžete zachytit výkon [správce mapy horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md), zejména při použití směrování závislého na [datech](sql-database-elastic-scale-data-dependent-routing.md). Čítače jsou vytvářeny pomocí metod třídy Microsoft. Azure. SqlDatabase. ElasticScale. Client.  


**Pro nejnovější verzi:** Přejít na [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Viz také [Upgrade aplikace, aby se použila nejnovější knihovna klienta elastické databáze](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Požadavky

* Chcete-li vytvořit kategorii a čítače výkonu, musí být uživatel členem místní skupiny **Administrators** v počítači, který je hostitelem aplikace.  
* Chcete-li vytvořit instanci čítače výkonu a aktualizovat čítače, musí být uživatel členem skupiny **Administrators** nebo **Performance Monitor Users** .

## <a name="create-performance-category-and-counters"></a>Vytvoření kategorie výkonu a čítačů

Chcete-li vytvořit čítače, zavolejte metodu CreatePerformanceCategoryAndCounters [třídy ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Pouze správce může provést tuto metodu:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

K provedení metody můžete použít také [Tento](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skript prostředí PowerShell.
Metoda vytvoří následující čítače výkonu:  

* **Mapování v mezipaměti**: Počet mapování ukládaných do mezipaměti pro mapu horizontálních oddílů
* **Operace DDR za sekundu**: Frekvence operací směrování závislých na datech pro mapu horizontálních oddílů. Tento čítač se aktualizuje, když volání [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) vede k úspěšnému připojení k cílovému horizontálních oddílů.
* **Počet přístupů do mezipaměti mapování vyhledávání za sekundu**: Frekvence úspěšných operací vyhledávání mezipaměti pro mapování v mapě horizontálních oddílů
* Počet **přístupů do mezipaměti mapování vyhledávání za sekundu**: Frekvence neúspěšných operací vyhledávání mezipaměti pro mapování v mapě horizontálních oddílů
* **Mapování přidáno nebo aktualizováno v mezipaměti/s**: Rychlost přidávání nebo aktualizace mapování v mezipaměti pro mapu horizontálních oddílů
* **Mapování odebrané z mezipaměti/s**: Rychlost odebírání mapování z mezipaměti pro mapu horizontálních oddílů

Čítače výkonu jsou vytvářeny pro každou horizontálních oddílů mapu v mezipaměti na proces.  

## <a name="notes"></a>Poznámky

Následující události aktivují vytváření čítačů výkonu:  

* Inicializace [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) s načítáním [Eager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), pokud ShardMapManager obsahuje nějaké mapování horizontálních oddílů. Mezi ně patří [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) a metody [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Úspěšné vyhledávání mapy horizontálních oddílů (pomocí [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) nebo [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Vytvoření mapy horizontálních oddílů pomocí CreateShardMap () bylo úspěšné.

Čítače výkonu budou aktualizovány všemi operacemi mezipaměti provedenými na mapě horizontálních oddílů a mapováním. Úspěšné odebrání mapy horizontálních oddílů pomocí DeleteShardMap () vede k odstranění instance čítačů výkonu.  

## <a name="best-practices"></a>Osvědčené postupy

* Vytvoření kategorie výkonu a čítačů by mělo být provedeno pouze jednou před vytvořením objektu ShardMapManager. Každé spuštění příkazu CreatePerformanceCategoryAndCounters () vymaže předchozí čítače (ztratí data nahlášené všemi instancemi) a vytvoří nové.  
* Instance čítače výkonu jsou vytvořeny pro jednotlivé procesy. Pokud dojde k selhání aplikace nebo odebrání mapy horizontálních oddílů z mezipaměti, dojde k odstranění instancí čítačů výkonu.  

### <a name="see-also"></a>Viz také:

[Přehled funkcí Elastic Database](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

---
title: Čítače výkonu ke sledování horizontálních oddílů map Manageru
description: Čítače výkonu směrování závislých na datech třídy ShardMapManager a dat
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: 3bfbf56b6e5f2be33b407945490531e6e2e8ac47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92781256"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Vytváření čítačů výkonu pro sledování výkonu správce map horizontálních oddílů
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Čítače výkonu se používají ke sledování výkonu operací [Směrování závislých na datech](elastic-scale-data-dependent-routing.md) . Tyto čítače jsou dostupné v monitorování výkonu v kategorii "Elastic Database: horizontálních oddílů Management".

Můžete zachytit výkon [správce mapy horizontálních oddílů](elastic-scale-shard-map-management.md), zejména při použití [Směrování závislého na datech](elastic-scale-data-dependent-routing.md). Čítače jsou vytvářeny pomocí metod třídy Microsoft. Azure. SqlDatabase. ElasticScale. Client.  


**Pro nejnovější verzi:** Přejít na [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Viz také [Upgrade aplikace, aby se použila nejnovější knihovna klienta elastické databáze](elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Předpoklady

* Chcete-li vytvořit kategorii a čítače výkonu, musí být uživatel členem místní skupiny **Administrators** v počítači, který je hostitelem aplikace.  
* Chcete-li vytvořit instanci čítače výkonu a aktualizovat čítače, musí být uživatel členem skupiny **Administrators** nebo **Performance Monitor Users** .

## <a name="create-performance-category-and-counters"></a>Vytvoření kategorie výkonu a čítačů

Chcete-li vytvořit čítače, zavolejte metodu CreatePerformanceCategoryAndCounters [třídy ShardMapManagementFactory](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Pouze správce může provést tuto metodu:

`ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()`

K provedení metody můžete použít také [Tento](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skript prostředí PowerShell.
Metoda vytvoří následující čítače výkonu:  

* **Mapování v mezipaměti**: počet mapování ukládaných do mezipaměti pro mapu horizontálních oddílů.
* **Operace DDR za sekundu**: rychlost operací směrování závislých na datech pro mapu horizontálních oddílů. Tento čítač se aktualizuje, když volání [OpenConnectionForKey ()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) vede k úspěšnému připojení k cílovému horizontálních oddílů.
* **Počet přístupů do mezipaměti mapování vyhledávání/s**: frekvence úspěšných operací vyhledávání mezipaměti pro mapování v mapě horizontálních oddílů
* Mapování neúspěšných **přístupů do mezipaměti vyhledávání/s**: frekvence operací vyhledávání v mezipaměti pro mapování v mapě horizontálních oddílů.
* **Mapování přidáno nebo aktualizováno v mezipaměti/s**: rychlost přidávání nebo aktualizace mapování v mezipaměti pro mapu horizontálních oddílů.
* **Mapování odebraných z mezipaměti/s**: rychlost odebírání mapování z mezipaměti pro mapu horizontálních oddílů.

Čítače výkonu jsou vytvářeny pro každou horizontálních oddílů mapu v mezipaměti na proces.  

## <a name="notes"></a>Poznámky

Následující události aktivují vytváření čítačů výkonu:  

* Inicializace [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) s [načítáním Eager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), pokud ShardMapManager obsahuje nějaké mapování horizontálních oddílů. Mezi ně patří [GetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) a metody [TryGetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Úspěšné vyhledávání mapy horizontálních oddílů (pomocí [GetShardMap ()](/previous-versions/azure/dn824215(v=azure.100)), [GetListShardMap ()](/previous-versions/azure/dn824212(v=azure.100)) nebo [GetRangeShardMap ()](/previous-versions/azure/dn824173(v=azure.100))).
* Vytvoření mapy horizontálních oddílů pomocí CreateShardMap () bylo úspěšné.

Čítače výkonu budou aktualizovány všemi operacemi mezipaměti provedenými na mapě horizontálních oddílů a mapováním. Úspěšné odebrání mapy horizontálních oddílů pomocí DeleteShardMap () vede k odstranění instance čítačů výkonu.  

## <a name="best-practices"></a>Osvědčené postupy

* Vytvoření kategorie výkonu a čítačů by mělo být provedeno pouze jednou před vytvořením objektu ShardMapManager. Každé spuštění příkazu CreatePerformanceCategoryAndCounters () vymaže předchozí čítače (ztratí data nahlášené všemi instancemi) a vytvoří nové.  
* Instance čítače výkonu jsou vytvořeny pro jednotlivé procesy. Pokud dojde k selhání aplikace nebo odebrání mapy horizontálních oddílů z mezipaměti, dojde k odstranění instancí čítačů výkonu.  

### <a name="see-also"></a>Viz také

[Přehled funkcí Elastic Database](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
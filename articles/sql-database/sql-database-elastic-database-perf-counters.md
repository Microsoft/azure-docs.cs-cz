---
title: Čítače výkonu pro správce mapování horizontálních oddílů
description: ShardMapManager třídy a dat závislé směrování čítače výkonu
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
ms.date: 01/03/2019
ms.openlocfilehash: c6a536316437ccf54cb0646603286c54d2a42f07
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733361"
---
# <a name="performance-counters-for-shard-map-manager"></a>Čítače výkonu pro správce mapování horizontálních oddílů

Můžete zaznamenávat výkon [správce mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md), zvláště při použití [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md). Čítače jsou vytvořeny metodami Microsoft.Azure.SqlDatabase.ElasticScale.Client třídy.  

Čítače se používají ke sledování výkonu [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md) operace. Tyto čítače jsou přístupné v nástroji Sledování výkonu v části "elastické databáze: Kategorie správy horizontálních oddílů".

**Nejnovější verze:** Přejděte na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Viz také [upgradu aplikace použít nejnovější Klientská knihovna elastic database](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Požadavky

* Pokud chcete vytvořit kategorie výkonu a čítače, uživatel musí být součástí místní **správci** skupina na počítači, který je hostitelem aplikace.  
* K vytvoření instance čítače výkonu a aktualizaci čítače, musí být uživatel členem **správci** nebo **Performance Monitor Users** skupiny.

## <a name="create-performance-category-and-counters"></a>Vytvoření kategorie výkonu a čítače

K vytvoření čítače, zavolejte metodu CreatePerformanceCategoryAndCounters [ShardMapManagementFactory třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Metodu lze spustit pouze správce:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Můžete také použít [to](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skript Powershellu pro provádění metody.
Metoda vytvoří následující čítače výkonu:  

* **Uložit do mezipaměti mapování**: Počet mapování pro mapy horizontálních oddílů v mezipaměti uloženy.
* **DDR operací za sekundu**: Rychlost operací závislých směrování dat pro mapy horizontálních oddílů. Tento čítač je aktualizována při volání [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) má za následek úspěšné připojení k cílové horizontálních oddílů.
* **Mapování přístupy do mezipaměti vyhledávání/s**: Rychlost operací vyhledávání úspěšné mezipaměti pro mapování horizontálních oddílů.
* **Mapování Neúspěšné přístupy do mezipaměti vyhledávání za sekundu**: Rychlost operací vyhledávání neúspěšné mezipaměti pro mapování horizontálních oddílů.
* **Mapování přidán nebo aktualizován v mezipaměti za sekundu**: Rychlost, na které mapování jsou přidáním nebo aktualizací v mezipaměti pro mapy horizontálních oddílů.
* **Mapování odebrány z mezipaměti za sekundu**: Rychlost, jakou mapování budou odebrány z mezipaměti pro mapy horizontálních oddílů.

Čítače výkonu jsou vytvořeny pro každé mapování horizontálních oddílů v mezipaměti proces.  

## <a name="notes"></a>Poznámky

Tyto události spustit vytvoření čítače výkonu:  

* Inicializace [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) s [předběžné načítání](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)v případě, ShardMapManager obsahuje všechny mapy horizontálních oddílů. Patří mezi ně [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) a [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) metody.
* Úspěšné vyhledávání mapy horizontálních oddílů (pomocí [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) nebo [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Úspěšné vytvoření mapy horizontálních oddílů pomocí CreateShardMap().

Čítače výkonu se aktualizovat všechny mezipaměti operace prováděné s mapy horizontálních oddílů a mapování. Úspěšné odebrání mapy horizontálních oddílů pomocí DeleteShardMap() za následek odstranění instance čítače výkonu.  

## <a name="best-practices"></a>Osvědčené postupy

* Vytváření kategorie výkonu a čítače se provádí jenom jednou před vytvořením objektu ShardMapManager. Každé spuštění příkazu CreatePerformanceCategoryAndCounters() vymaže předchozí čítače (ztráty data, která všechny instance) a vytvoří nové značky.  
* Proces se vytvářejí instance čítače výkonu. Jakékoli selhání aplikace nebo odebrání mapy horizontálních oddílů z mezipaměti způsobí odstranění instance čítače výkonu.  

### <a name="see-also"></a>Další informace najdete v tématech

[Přehled funkcí Elastic Database](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

---
title: Čítače výkonu pro správce mapování horizontálních oddílů
description: ShardMapManager třídy a data závislé směrování čítače výkonu
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 9c134ee96f7749529ab665df041cfc51c979acde
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647319"
---
# <a name="performance-counters-for-shard-map-manager"></a>Čítače výkonu pro správce mapování horizontálních oddílů
Můžete zaznamenat výkon [správce mapy horizontálního oddílu](sql-database-elastic-scale-shard-map-management.md), zvláště při používání [závislé směrování dat](sql-database-elastic-scale-data-dependent-routing.md). Čítače jsou vytvořeny pomocí metod třídy Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Čítače se používají ke sledování výkonu [data závislé směrování](sql-database-elastic-scale-data-dependent-routing.md) operace. Tyto čítače jsou dostupné v nástroji Sledování výkonu v kategorii "Elastické databáze: ID horizontálního oddílu Správa".

**Nejnovější verzi:** přejít na [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Viz také [upgradovat aplikaci používat nejnovější klientské knihovny elastické databáze](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Požadavky
* Pokud chcete vytvořit kategorie výkonu a čítače, uživatel musí být součástí místní **správci** skupina na počítači, který je hostitelem aplikace.  
* K vytvoření instance čítače výkonu a čítače aktualizaci, musí být uživatel členem buď **správci** nebo **Performance Monitor Users** skupiny. 

## <a name="create-performance-category-and-counters"></a>Vytvoření kategorie výkonu a čítače
Pokud chcete vytvořit čítače, zavolejte metodu CreatePeformanceCategoryAndCounters [ShardMapManagmentFactory třída](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Metodu lze spustit pouze správce: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Můžete také použít [to](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) k provedení metody skriptu prostředí PowerShell. Metoda vytvoří následující čítače výkonu:  

* **V mezipaměti mapování**: počet mapování do mezipaměti pro mapu horizontálního oddílu.
* **Operace DDR za sekundu**: rychlost závislé směrování operací dat pro mapu horizontálního oddílu. Toto počítadlo je aktualizováno při volání [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) výsledkem úspěšné připojení k cílové horizontálního oddílu. 
* **Mapování vyhledávání přístupy do mezipaměti nebo s**: rychlost úspěšných mezipaměti vyhledávání operací pro mapování v mapě horizontálního oddílu. 
* **Mapování vyhledávání sekundu Neúspěšné přístupy do mezipaměti**: počet mezipaměti se nezdařila operace vyhledávání pro mapování v mapě horizontálního oddílu.
* **Mapování přidán nebo aktualizován v mezipaměti za sekundu**: frekvence, které mapování se přidávají nebo aktualizované v mezipaměti pro mapu horizontálního oddílu. 
* **Mapování odebrány z mezipaměti za sekundu**: rychlost, jakou jsou probíhá mapování odebraných z mezipaměti pro mapu horizontálního oddílu. 

Čítače výkonu jsou vytvořeny pro každé mapování v mezipaměti horizontálních podle procesu.  

## <a name="notes"></a>Poznámky
Tyto události aktivovat vytvoření čítače výkonu:  

* Inicializace [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) s [přes načítání](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), pokud ShardMapManager obsahuje všechny mapy horizontálního oddílu. Patří mezi ně [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) a [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) metody.
* Úspěšné vyhledávání horizontálního oddílu mapy (pomocí [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) nebo [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Úspěšné vytvoření mapy horizontálního oddílu pomocí CreateShardMap().

Čítače výkonu budou aktualizovány operacemi mezipaměti u horizontálního oddílu mapování a mapování. Úspěšné odstranění mapy horizontálního oddílu pomocí reults DeleteShardMap () v odstranění instance čítače výkonu.  

## <a name="best-practices"></a>Osvědčené postupy
* Vytvoření kategorie výkonu a čítače se provádí jenom jednou před vytvořením objektu ShardMapManager. Každé provedení příkazu CreatePerformanceCategoryAndCounters() vymaže předchozí čítače (ztráty dat, které jsou uvedeny všechny instance) a vytvoří nové.  
* Podle procesu vytváření instancí čítače výkonu. Všechny havárie aplikací nebo odebrání horizontálního oddílu mapy z mezipaměti bude mít za následek odstranění instancí čítače výkonu.  

### <a name="see-also"></a>Další informace najdete v tématech
[Přehled funkcí Elastic Database](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->


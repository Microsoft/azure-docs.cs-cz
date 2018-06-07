---
title: Pomocí Správce obnovení opravit problémy mapy horizontálního oddílu | Microsoft Docs
description: Použití třídy RecoveryManager pro řešení problémů s horizontálního oddílu mapy
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 6257edbb567be3ebb3151724e7e50ca81905ad40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646231"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Oprava problémů s mapováním horizontálních oddílů pomocí třídy RecoveryManager
[RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) třída poskytuje ADO.Net aplikace umožňuje snadno zjistit a opravit nekonzistence mezi globální horizontálních mapy (GSM) a místní horizontálních mapy (LSM) v prostředí s horizontálně dělené databáze. 

GSM a LSM sledovat mapování každou databázi v horizontálně dělené prostředí. V některých případech zalomení probíhá mezi GSM a LSM. V takovém případě použijte třídu RecoveryManager zjistit a opravit rozdělení.

Třída RecoveryManager je součástí [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md). 

![Mapování horizontálních][1]

Definice podmínek, najdete v části [Glosář nástroje elastické databáze](sql-database-elastic-scale-glossary.md). Zjistit, jak **ShardMapManager** se používá k správě dat v horizontálně dělené řešení najdete v tématu [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Proč používat Správce obnovení?
V prostředí s horizontálně dělené databáze není jednoho klienta na databázi a mnoho databází na serveru. V prostředí může být také mnoho serverů. Každá databáze je namapována v mapě horizontálního oddílu, tak volání budou směrovány na správný server a databáze. Databáze se sledují podle **horizontálního dělení klíč**, a je mu přiřazená každý horizontálního oddílu **rozsah hodnot klíče**. Například klíč horizontálního dělení může představují zákazníka názvy z "D" na "F." Mapování horizontálních oddílů všechny (neboli databáze) a jejich mapování rozsahy jsou součástí **globální horizontálních mapy (GSM)**. Každá databáze také obsahuje mapu rozsahů obsažených v horizontálního oddílu, který se označuje jako **místní horizontálních mapy (LSM)**. Když se aplikace připojí k horizontálního oddílu, mapování se uloží do mezipaměti pomocí aplikace pro rychlé načítání. LSM slouží k ověření data uložená v mezipaměti. 

GSM a LSM mohou stát synchronizované z následujících důvodů:

1. Odstranění horizontálních, jejichž rozsah je pokládán již nebude používán nebo přejmenování horizontálního oddílu. Odstranění horizontálního oddílu vede **osamocené mapování horizontálních**. Podobně přejmenovat databázi, může způsobit mapování u osamocených horizontálního oddílu. V závislosti na záměr změnu ID horizontálního oddílu může třeba je odebrat nebo umístění horizontálního oddílu je třeba aktualizovat. Chcete-li obnovit odstraněnou databázi, přečtěte si téma [obnovit odstraněnou databázi](sql-database-recovery-using-backups.md).
2. Dojde k události geo-převzetí služeb při selhání. Chcete-li pokračovat, jeden musí aktualizovat název serveru a název databáze správce mapy horizontálního oddílu v aplikaci a aktualizujte podrobnosti horizontálního oddílu mapování pro všechny horizontálních oddílů v mapě horizontálního oddílu. Pokud dojde k selhání geograficky, by je možné automatizovat tuto logiku obnovení v rámci pracovního postupu převzetí služeb při selhání. Automatizace akce obnovení umožňuje hladký možnosti správy databází s povolenou geografickou a zabraňuje ruční lidského akce. Další informace o možnosti obnovení databáze, pokud je výpadku datacentra najdete v tématu [kontinuity podnikových procesů](sql-database-business-continuity.md) a [zotavení po havárii](sql-database-disaster-recovery.md).
3. Horizontálního oddílu nebo databázi ShardMapManager je obnoven dříve bodu v stav. Další informace o bodu obnovení pomocí záloh najdete v tématu [obnovení pomocí záloh](sql-database-recovery-using-backups.md).

Další informace o nástrojích elastické databáze pro databázi SQL Azure, geografická replikace a obnovení naleznete v následujících tématech: 

* [Přehled: Cloudu obchodní kontinuitu a databáze zotavení po havárii s databází SQL](sql-database-business-continuity.md) 
* [Začínáme s nástroje elastické databáze](sql-database-elastic-scale-get-started.md)  
* [ShardMap správy](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Načítání RecoveryManager z ShardMapManager
Prvním krokem je vytvoření RecoveryManager instance. [GetRecoveryManager metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) vrátí správce obnovení pro aktuální [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) instance. Chcete-li vyřešit všechny nekonzistence v mapě horizontálního oddílu, musí nejdřív získat RecoveryManager mapy konkrétní horizontálního oddílu. 

   ```
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 
   ```

V tomto příkladu je RecoveryManager inicializován ze ShardMapManager. ShardMapManager obsahující ShardMap také již byla inicializována. 

Vzhledem k tomu, že tento kód aplikace zpracovává samotná mapa horizontálního oddílu, přihlašovací údaje použité metoda factory (v předchozím příkladu smmConnectionString) by měl být přihlašovací údaje, které mají oprávnění pro čtení a zápis na databázi GSM odkazuje připojovací řetězec. Tyto přihlašovací údaje se obvykle liší od přihlašovací údaje používané k otevření připojení pro směrování závislé na data. Další informace najdete v tématu [pomocí přihlašovacích údajů v klientovi elastické databáze](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Odebráním horizontálního oddílu ShardMap po odstranění horizontálního oddílu
[DetachShard metoda](https://msdn.microsoft.com/library/azure/dn842083.aspx) odpojí dané horizontálního oddílu z mapování horizontálních a odstraní mapování přidružené horizontálního oddílu.  

* Parametr umístění je umístění horizontálního oddílu, konkrétně název serveru a název databáze, horizontálních odpojuje. 
* Parametr shardMapName je název mapy horizontálního oddílu. Toto je pouze vyžadována, je-li více mapami horizontálního oddílu spravuje správce mapu stejné ID horizontálního oddílu. Volitelné. 


> [!IMPORTANT]
> Tento postup použijte, pouze pokud jste si jisti, že rozsah pro aktualizovaný mapování je prázdný. Výše uvedené metody Neprovádět kontrolu dat pro rozsah přesouvání, proto je nejlepší zahrnout kontroly v kódu.
>

V tomto příkladu odebere mapy horizontálního oddílu horizontálních oddílů. 

   ```
   rm.DetachShard(s.Location, customerMap);
   ``` 

Mapování horizontálních odráží umístění horizontálního oddílu v GSM před odstranění horizontálního oddílu. Protože horizontálního oddílu byla odstraněna, se předpokládá, bylo záměrné, klíče rozsahu horizontálního dělení je již používán. Pokud ne, můžete provést obnovení bodu v době. Obnovit horizontálního oddílu starší bodu v čase. (V takovém případě zkontrolujte následující část ke zjištění horizontálního oddílu nekonzistence.) Pokud chcete obnovit, najdete v části [bodu v době obnovení](sql-database-recovery-using-backups.md).

Vzhledem k tomu, že se předpokládá, že byl odstraněn databáze záměrně, poslední správce čištění akcí je odstranit položku k horizontálního oddílu ve Správci mapy horizontálního oddílu. To brání aplikaci v nechtěně zápisu informací o na rozsah, který není očekáván.

## <a name="to-detect-mapping-differences"></a>Ke zjištění rozdíly mapování
[DetectMappingDifferences metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) vybere a vrátí jednu z horizontálního oddílu mapy (místní nebo globální) jako zdroj pravdivosti a sloučí mapování na obou horizontálního oddílu mapy (GSM a LSM).

   ```
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Umístění* Určuje název serveru a název databáze. 
* *ShardMapName* parametr je název mapy horizontálního oddílu. Používá se pouze požadované Pokud více mapami horizontálního oddílu spravuje správce mapu stejné ID horizontálního oddílu. Volitelné. 

## <a name="to-resolve-mapping-differences"></a>Chcete-li vyřešit rozdíly mapování
[ResolveMappingDifferences metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) vybere jeden horizontálního oddílu map (místní nebo globální) jako zdroj pravdivosti a sloučí mapování na obou horizontálního oddílu mapy (GSM a LSM).

   ```
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* parametr zobrazí rozdíly v mapování mezi GSM a LSM pro konkrétní horizontálního oddílu. 
* [MappingDifferenceResolution výčtu](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) slouží k označení metodu pro překlad rozdíl mezi mapování horizontálního oddílu. 
* **MappingDifferenceResolution.KeepShardMapping** se doporučuje, když LSM obsahuje mapování přesné, a proto by měl použít mapování v horizontálního oddílu. To je obvykle případ, pokud dojde převzetí služeb při selhání: horizontálního oddílu je umístěn na nový server. Vzhledem k tomu, že horizontálního oddílu musí být nejprve odebrány z GSM (pomocí metody RecoveryManager.DetachShard), mapování na GSM už existuje. Proto LSM použije k opětovnému zřízení mapování horizontálního oddílu.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Přiřadit horizontálního oddílu ShardMap po obnovení horizontálního oddílu
[AttachShard metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) připojí dané horizontálního oddílu mapu horizontálního oddílu. Pak zjistí nekonzistencím mapy horizontálního oddílu a aktualizuje mapování tak, aby odpovídaly horizontálního oddílu v okamžiku obnovení horizontálního oddílu. Se předpokládá, že databáze je přejmenována také tak, aby odrážela původní název databáze (dříve, než byla obnovena horizontálního oddílu), protože bod v době obnovení výchozí nastavení pro novou databázi spolu s časové razítko. 

   ```
   rm.AttachShard(location, shardMapName)
   ``` 

* *Umístění* parametr je název serveru a název databáze, horizontálních připojovaný. 
* *ShardMapName* parametr je název mapy horizontálního oddílu. Toto je pouze vyžadována, je-li více mapami horizontálního oddílu spravuje správce mapu stejné ID horizontálního oddílu. Volitelné. 

Tento příklad přidá horizontálního oddílu horizontálního oddílu mapu, která byla nedávno obnovena ze starší čas bodu v. Vzhledem k tomu, že byla obnovena horizontálního oddílu (konkrétně mapování horizontálních v LSM), je potenciálně nekonzistentní s položkou horizontálního oddílu v GSM. Mimo tento příklad kódu se horizontálního oddílu obnovit a přejmenovat na původní název databáze. Vzhledem k tomu, že ho byla obnovena, předpokládá se, že je mapování v LSM důvěryhodné mapování. 

   ```
   rm.AttachShard(s.Location, customerMap); 
   var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualizace umístění horizontálního oddílu po geo-převzetí služeb při selhání (obnovení) horizontálních oddílů
Pokud dojde k selhání geograficky, sekundární databázi se provádí zápis přístupný a že bude nové primární databáze. Název serveru a potenciálně databáze (v závislosti na konfiguraci), se může lišit od původní primární. Proto je potřeba opravit položky mapování pro horizontálního oddílu v GSM a LSM. Podobně pokud byla databáze obnovena jiný název nebo umístění nebo do dřívějšího bodu v čase, může to způsobit nekonzistence v rámci služby maps horizontálního oddílu. Správce mapy horizontálního oddílu zpracovává distribuci otevřená připojení k databázi správné. Distribuce je založena na datech v mapě horizontálního oddílu a hodnota klíče horizontálního dělení, který je cílem dané žádosti o aplikace. Po geo-převzetí služeb při selhání musí být tyto informace aktualizovány název přesné serveru, názvu databáze a mapování horizontálních obnovené databáze. 

## <a name="best-practices"></a>Osvědčené postupy
GEO-převzetí služeb při selhání a obnovení jsou operace, které jsou obvykle spravuje správce cloudu aplikace záměrně využitím jedna z funkcí kontinuity obchodních databází SQL Azure. Plánování kontinuity obchodních vyžaduje procesy, postupy a opatření, aby zajistily, že provoz firmy můžete pokračovat bez přerušení. Dostupné metody jako součást třídy RecoveryManager by měl použít v rámci tento pracovní postup k Ujistěte se, že GSM a LSM budou kopírovány podle akce obnovení. Je pět základní kroky pro zajištění správně GSM a LSM odpovídají přesných informací po události převzetí služeb při selhání. Kódu aplikace k provedení těchto kroků můžete integrovat do existující nástroje a pracovní postup. 

1. Načtení RecoveryManager z ShardMapManager. 
2. Odpojte staré horizontálního oddílu z mapy horizontálního oddílu.
3. Připojte nové horizontálního oddílu mapu horizontálního oddílu, včetně nové umístění horizontálního oddílu.
4. V mapování mezi GSM a LSM rozpoznat nekonzistence. 
5. Rozdíly mezi GSM a LSM, důvěřující LSM vyřešte. 

Tento příklad provede následující kroky:

1. Odebere horizontálních oddílů horizontálního oddílu mapu, která odráží horizontálního oddílu umístění před převzetím služeb při selhání.
2. Připojí horizontálních oddílů a mapováním horizontálního oddílu odrážející nová umístění horizontálního oddílu (parametr "Configuration.SecondaryServer" je nový název serveru, ale název stejné databáze).
3. Získá tokeny obnovení zjišťuje rozdíly mapování mezi GSM a LSM pro každý horizontálního oddílu. 
4. Přeloží nekonzistence ve důvěřující mapování z LSM z každé horizontálního oddílu. 
   
   ```
   var shards = smm.GetShards(); 
   foreach (shard s in shards) 
   { 
     if (s.Location.Server == Configuration.PrimaryServer) 
   
         { 
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database); 
   
          rm.DetachShard(s.Location); 
   
          rm.AttachShard(slNew); 
   
          var gs = rm.DetectMappingDifferences(slNew); 
   
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png


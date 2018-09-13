---
title: Oprava problémů s horizontálními oddíly mapy pomocí Správce obnovení | Dokumentace Microsoftu
description: Řešení problémů s mapováním horizontálních oddílů pomocí třídy RecoveryManager
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 3aeee7cd4c588460a16b93237b08f13d8422a72a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721294"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Oprava problémů s mapováním horizontálních oddílů pomocí třídy RecoveryManager
[RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) třída umožňuje ADO.Net aplikace snadno zjistit a opravit nekonzistence mezi mapy horizontálních oddílů globální (GSM) a mapy horizontálních oddílů místní (LSM) v prostředí s horizontálně dělené databáze. 

GSM a LSM sledovat mapování jednotlivých databází v horizontálně dělené prostředí. V některých případech dojde GSM až LSM k přerušení. V takovém případě pomocí třídy RecoveryManager k rozpoznání a opravě přerušení.

Třídy RecoveryManager je součástí [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md). 

![Mapy horizontálních oddílů][1]

Definice termínu, naleznete v tématu [Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md). Pochopit, jak **ShardMapManager** slouží ke správě dat v horizontálně dělené řešení, najdete v článku [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Proč používat Správce obnovení?
V prostředí s horizontálně dělené databáze je jednoho tenanta na databázi a velký počet databází na serveru. V prostředí může být také mnoho serverů. Každá databáze je mapována v mapy horizontálních oddílů, takže volání je možné směrovat správnému serveru a databáze. Databáze jsou sledována podle **klíč horizontálního dělení**, a je mu přiřazená každý horizontální oddíl **rozsahu hodnot klíčů**. Například klíč horizontálního dělení může představovat názvů zákazníka z hodnoty "D" k "F" Mapování všech horizontálních oddílů (označuje se také jako databáze) a jejich mapování oblastí jsou obsaženy v **mapy horizontálních oddílů globální (GSM)**. Každá databáze obsahuje také mapa rozsahů adres obsažené v horizontálním oddílu, který se označuje jako **mapy horizontálních oddílů místní (LSM)**. Když se aplikace připojí k horizontálnímu oddílu, mapování je uložit do mezipaměti s aplikací pro rychlé načítání. LSM slouží k ověření dat uložených v mezipaměti. 

GSM a LSM může přestat synchronizované z následujících důvodů:

1. Odstranění do horizontálního oddílu, jejichž rozsah je zřejmě již používán nebo přejmenování horizontálního oddílu. Odstraňuje se horizontální oddíl má za následek **osamocené mapování horizontálních oddílů**. Obdobně přejmenováno databáze může způsobit mapování horizontálních oddílů osamocený. V závislosti na záměru změnu možná bude nutné k odebrání horizontálního oddílu nebo umístění horizontálního oddílu je potřeba aktualizovat. Chcete-li obnovit odstraněnou databázi, přečtěte si téma [obnovení odstraněné databáze](sql-database-recovery-using-backups.md).
2. Dojde k události geo-převzetí služeb při selhání. Chcete-li pokračovat, jeden musíte aktualizovat název serveru a název databáze správce mapování horizontálních oddílů v aplikaci a pak aktualizovat podrobnosti mapování horizontálních oddílů pro všemi horizontálními oddíly v mapě horizontálních oddílů. Pokud při selhání geograficky tuto logiku obnovení by mělo být automatické v rámci pracovního postupu převzetí služeb při selhání. Automatizace zotavení umožňuje bezproblémové možnosti správy databází s povolenou geografickou a ručně prováděné akce lidské se vyhnete. Další informace o možnosti obnovení databáze při výpadku datového centra, naleznete v tématu [kontinuita podnikových procesů](sql-database-business-continuity.md) a [zotavení po havárii](sql-database-disaster-recovery.md).
3. Horizontální oddíl nebo ShardMapManager databáze je obnovit do dříve bodu v čase. Další informace o bodu v čase obnovení pomocí záloh najdete v tématu [obnovení pomocí záloh](sql-database-recovery-using-backups.md).

Další informace o nástrojích elastické databáze Azure SQL Database, geografickou replikaci a obnovení naleznete v následujících tématech: 

* [Přehled: Cloudová provozní kontinuita a databáze zotavení po havárii s využitím SQL Database](sql-database-business-continuity.md) 
* [Začínáme s nástroji elastic database](sql-database-elastic-scale-get-started.md)  
* [Správa ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Načítání z ShardMapManager RecoveryManager
Prvním krokem je vytvoření RecoveryManager instance. [GetRecoveryManager metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) vrátí recovery manager pro aktuální [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) instance. Chcete-li vyřešit jakékoli nekonzistence mapy horizontálních oddílů, musí nejdřív načtěte RecoveryManager pro konkrétní horizontálního dělení mapu. 

   ```
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 
   ```

V tomto příkladu je inicializován RecoveryManager z ShardMapManager. ShardMapManager obsahující ShardMap také již byl inicializován. 

Vzhledem k tomu, že tento kód aplikace zpracovává samotné mapy horizontálních oddílů, přihlašovací údaje použité pro objekt pro vytváření – metoda (v předchozím příkladu smmConnectionString) by měl být přihlašovací údaje, které mají oprávnění pro čtení i zápis v GSM databázi odkazuje připojení řetězec. Tyto přihlašovací údaje se obvykle liší od přihlašovacích údajů, které slouží k otevření připojení pro směrování závislé na datech. Další informace najdete v tématu [pomocí přihlašovacích údajů klienta elastické databáze](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Odebrání horizontálního oddílu ShardMap po odstranění horizontálního oddílu
[DetachShard metoda](https://msdn.microsoft.com/library/azure/dn842083.aspx) odpojí dané horizontálních oddílů z mapy horizontálních oddílů a odstraní mapování spojené s horizontálním oddílu.  

* Umístění parametru je umístění horizontálního oddílu, konkrétně název serveru a název databáze, horizontálních oddílů budou tímto odpojeny. 
* Parametr shardMapName je název mapy horizontálních oddílů. Toto je jenom nutné při několika map horizontálních oddílů se spravují přes stejné správce mapování horizontálních oddílů. Volitelné. 


> [!IMPORTANT]
> Tento postup použijte, pouze pokud jste si jisti, že rozsah aktualizovaná mapování je prázdný. Metod uvedených výše nezaškrtávejte políčko dat pro rozsah přesouvaných, takže je vhodné zahrnout kontroly ve vašem kódu.
>

V tomto příkladu odebere horizontálních oddílů z mapy horizontálních oddílů. 

   ```
   rm.DetachShard(s.Location, customerMap);
   ``` 

Mapy horizontálních oddílů odráží umístění horizontálních oddílů v GSM před odstraněním horizontálního oddílu. Protože se odstranil horizontálního oddílu, předpokládá se, to je to tak úmyslně, a rozsah klíčů horizontálního dělení je již používán. Pokud ne, můžete provést obnovení k určitému bodu v čase. provést obnovení horizontálních oddílů starší v daném okamžiku. (V takovém případě najdete v následující části k detekci horizontálních oddílů nekonzistence.) Pokud chcete obnovit, naleznete v tématu [bodu v čase obnovení](sql-database-recovery-using-backups.md).

Protože se předpokládá, že odstranění databáze je to tak úmyslně, poslední správce vyčištění akcí je odstranit položku do horizontálního oddílu v správce mapování horizontálních oddílů. To zabrání aplikaci od nedopatřením zápisu informací do rozsahu, který není očekáván.

## <a name="to-detect-mapping-differences"></a>Chcete-li zjistit rozdíly mapování
[DetectMappingDifferences metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) vybere a vrátí jednu z mapy horizontálních oddílů (místní nebo globální) jako zdroj pravdivých informací a sloučí mapování na obou mapy horizontálních oddílů (GSM a LSM).

   ```
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Umístění* Určuje název serveru a název databáze. 
* *ShardMapName* parametr je název mapy horizontálních oddílů. Toto je jenom nutné, pokud několika map horizontálních oddílů jsou spravovány stejné správce mapování horizontálních oddílů. Volitelné. 

## <a name="to-resolve-mapping-differences"></a>Chcete-li vyřešit rozdíly mapování
[ResolveMappingDifferences metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) vybere jeden z mapy horizontálních oddílů (místní nebo globální) jako zdroj pravdivých informací a sloučí mapování na obou mapy horizontálních oddílů (GSM a LSM).

   ```
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* parametru zobrazí rozdíly GSM a LSM pro konkrétní horizontálních oddílů v mapování. 
* [MappingDifferenceResolution výčet](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) se používá k označení metodu pro překlad rozdíl mezi mapování horizontálních oddílů. 
* **MappingDifferenceResolution.KeepShardMapping** se doporučuje, když LSM obsahuje mapování přesné, a proto by měla sloužit mapování v horizontálním oddílu. Toto je obvykle tento případ, pokud dojde k selhání: horizontálního oddílu je umístěn na nový server. Protože horizontálního oddílu musí být nejprve odebrány z GSM (pomocí metody RecoveryManager.DetachShard), mapování na GSM již existuje. Proto LSM použije pro opětovné vytvoření mapování horizontálních oddílů.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Připojení k ShardMap horizontálního oddílu, po obnovení do horizontálního oddílu
[AttachShard metoda](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) připojí dané horizontálními oddíly mapy horizontálních oddílů. Potom jakékoli nekonzistence mapy horizontálních oddílů a aktualizuje mapování tak, aby odpovídaly horizontálních oddílů Přejme během obnovení horizontálních oddílů. Předpokládá se, že databáze je také přejmenovat tak, aby odrážely původní název databáze (dříve, než se obnovila horizontální oddíl), protože výchozí obnovení bodu v čase do nové databáze s časové razítko. 

   ```
   rm.AttachShard(location, shardMapName)
   ``` 

* *Umístění* parametr je název serveru a název databáze, připojovaný horizontálního oddílu. 
* *ShardMapName* parametr je název mapy horizontálních oddílů. Toto je jenom nutné při několika map horizontálních oddílů se spravují přes stejné správce mapování horizontálních oddílů. Volitelné. 

V tomto příkladu přidá do mapy horizontálního dělení, která byla nedávno obnovena ze dřívější čas bod v horizontálním oddílu. Protože horizontálních oddílů (konkrétně mapování horizontálních oddílů v LSM) byla obnovena, není potenciálně konzistentní s položkou horizontálních oddílů v GSM. Mimo tento příklad kódu byl horizontálního oddílu obnovit a přejmenovat na původní název databáze. Vzhledem k tomu, že byla obnovena, předpokládá se, že je namapování LSM důvěryhodné mapování. 

   ```
   rm.AttachShard(s.Location, customerMap); 
   var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualizace umístění horizontálních oddílů po geo-převzetí služeb při selhání (obnovení) horizontálních oddílů
Pokud dojde při selhání geografické, sekundární databáze zpřístupněna zápisu a stane nová primární databáze. Název serveru a potenciálně databáze (v závislosti na vaší konfiguraci), se může lišit od původní databáze. Proto musí být stanovena položky mapování horizontálních oddílů v GSM a LSM. Podobně pokud obnovení databáze na jiný název nebo umístění, nebo k dřívějšímu bodu v čase, to může způsobit nekonzistence v mapy horizontálních oddílů. Správce mapování horizontálních oddílů zpracovává rozdělení otevřená připojení ke správné databázi. Distribuce podle data mapy horizontálních oddílů a hodnotu klíče horizontálního dělení, který je cílem dané žádosti o aplikace. Po geo-převzetí služeb při selhání musí být tyto informace aktualizovány název přesné serveru, název databáze a mapování horizontálních oddílů z obnovené databáze. 

## <a name="best-practices"></a>Osvědčené postupy
Geografické převzetí služeb při selhání a obnovení jsou operace obvykle spravuje správce cloudové aplikace záměrně využívají jednu z funkce provozní kontinuity obchodních databází Azure SQL. Obchodní kontinuity podnikových procesů plánování vyžaduje procesy, postupy a opatření pro zajištění, že obchodní operace mohou bez přerušení pokračovat. Metody, které jsou k dispozici jako součást třídy RecoveryManager by měl použít v rámci této pracovní postup k Ujistěte se, že GSM a LSM jsou tak pořád aktuální závisí na akci obnovení. Existuje pět základních kroků správně zajistit, že GSM a LSM odrážejí přesné informace po události převzetí služeb při selhání. Kód aplikace provádět tyto kroky je možné integrovat do existujících nástrojů a pracovních postupů. 

1. Načtěte RecoveryManager z ShardMapManager. 
2. Odpojení původního horizontálního oddílu z mapy horizontálních oddílů.
3. Připojte nový horizontální oddíl do mapy horizontálního oddílu, včetně nové umístění horizontálních oddílů.
4. V mapování mezi GSM a LSM rozpoznat nekonzistence. 
5. Rozdíly GSM a LSM, důvěřující LSM vyřešte. 

V tomto příkladu provede následující kroky:

1. Odebere horizontálních oddílů z mapy horizontálních oddílů, který umístění horizontálních oddílů před událostí převzetí služeb při selhání.
2. Připojí horizontálními oddíly mapy horizontálních oddílů odrážející nové umístění horizontálních oddílů (parametr "Configuration.SecondaryServer" je nový název serveru, ale stejný název databáze).
3. Získá tokeny obnovení nalezením mapování rozdíly GSM a LSM pro každý horizontální oddíl. 
4. Odstraňuje nekonzistence důvěryhodnou mapování z LSM každý horizontální oddíl. 
   
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


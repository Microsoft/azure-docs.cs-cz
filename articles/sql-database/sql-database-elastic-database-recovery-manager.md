---
title: Správce obnovení k opravě problémů s mapou úlomků
description: Použití třídy RecoveryManager k řešení problémů s mapami úlomků
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823864"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Oprava problémů s mapováním horizontálních oddílů pomocí třídy RecoveryManager

Třída [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) poskytuje ADO.NET aplikacím možnost snadno rozpoznat a opravit všechny nekonzistence mezi globální mapy střepů (GSM) a místní mapy střepů (LSM) v prostředí databáze s chybou.

GSM a LSM sledovat mapování každé databáze v prostředí s oddíly. V některých případě dojde k přerušení mezi GSM a LSM. V takovém případě použijte RecoveryManager třídy rozpoznat a opravit konec.

Třída RecoveryManager je součástí [klientské knihovny elastická databáze](sql-database-elastic-database-client-library.md).

![Mapa úlomků][1]

Definice termínů naleznete v [tématu Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md). Informace o tom, jak se **shardMapManager** používá ke správě dat v řešení s oddíly, naleznete v [tématu Správa map stolu](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Proč použít správce obnovení

V prostředí s oddíly 20 000 databáze je jeden klient na databázi a mnoho databází na server. V prostředí může být také mnoho serverů. Každá databáze je mapována v mapě střepů, takže volání mohou být směrována na správný server a databázi. Databáze jsou sledovány podle **klíče s ráždí**a každému úlomku je přiřazen **rozsah hodnot klíče**. Klíč s ráždí může například představovat názvy zákazníků od "D" do "F". Mapování všech úlomků (aka databází) a jejich mapování rozsahy jsou obsaženy v **globální mapě střepů (GSM)**. Každá databáze také obsahuje mapu rozsahů obsažených na úložný oddíl, který je známý jako **místní mapy střepů (LSM).** Když se aplikace připojí ke šiřiti, mapování se uloží do mezipaměti s aplikací pro rychlé načtení. LSM se používá k ověření dat uložených v mezipaměti.

GSM a LSM se mohou stát nesynchronizované z následujících důvodů:

1. Odstranění horizontálního oddílu, jehož rozsah je věřil již používat nebo přejmenování horizontálního oddílu. Odstranění šiřidla má za následek **mapování osamoceného oddílu**. Podobně přejmenovaná databáze může způsobit osamocené mapování šiřidla. V závislosti na záměru změny může být nutné odebrat úlomek nebo umístění oddílu je třeba aktualizovat. Informace o obnovení odstraněné databáze naleznete v [tématu Obnovení odstraněné databáze](sql-database-recovery-using-backups.md).
2. Dojde k události geografického převzetí služeb při selhání. Chcete-li pokračovat, je třeba aktualizovat název serveru a název databáze správce mapy střepu v aplikaci a potom aktualizovat podrobnosti mapování oddílu pro všechny oddíly v mapě střepu. Pokud existuje geografické převzetí služeb při selhání, taková logika obnovení by měla být automatizována v rámci pracovního postupu převzetí služeb při selhání. Automatizace akcí pro obnovení umožňuje bezproblémovou správu geografických databází a zabraňuje ručním lidským akcím. Informace o možnostech obnovení databáze v případě výpadku datového centra naleznete v tématu [Kontinuita provozu](sql-database-business-continuity.md) a [zotavení po havárii](sql-database-disaster-recovery.md).
3. Buď střep nebo ShardMapManager databáze je obnovena na dřívější bod-in čas. Informace o obnovení bodu v čase pomocí záloh naleznete v [tématu Obnovení pomocí záloh](sql-database-recovery-using-backups.md).

Další informace o nástrojích elastické databáze Azure SQL Database, geografické replikaci a obnovení najdete v následujících tématech:

* [Přehled: Kontinuita podnikání v cloudu a obnovení po havárii databáze pomocí databáze SQL Database](sql-database-business-continuity.md)
* [Začínáme s elastickými databázovými nástroji](sql-database-elastic-scale-get-started.md)  
* [Správa shardmap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Načítání RecoveryManager z ShardMapManager

Prvním krokem je vytvoření instance RecoveryManager. [Metoda GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) vrátí správce obnovení pro aktuální instanci [ShardMapManager.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Chcete-li vyřešit všechny nekonzistence v mapě šikmu, musíte nejprve načíst RecoveryManager pro konkrétní mapu oddílu.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

V tomto příkladu RecoveryManager je inicializován z ShardMapManager. ShardMapManager obsahující ShardMap je také již inicializován.

Vzhledem k tomu, že tento kód aplikace manipuluje s mapou střepů samotnými, pověření použitá v metodě factory (v předchozím příkladu smmConnectionString) by měla být pověření, která mají oprávnění pro čtení a zápis v databázi GSM odkazovaná připojením Řetězec. Tato pověření se obvykle liší od pověření používaných k otevření připojení pro směrování závislé na datech. Další informace naleznete [v tématu Using credentials in the elastic database client](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Odebrání šněrového oddílu z střepu po odstranění šiřidla

[Metoda DetachShard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) odpojí daný úlomek od mapy štřepu a odstraní mapování přidružené k úložnosti.  

* Parametr umístění je umístění úlomku, konkrétně název serveru a název databáze, oddílu, který je odpojen.
* Parametr shardMapName je název mapy střepů. To je vyžadováno pouze v případě, že více map skřípků je spravováno stejným správcem mapy oddílu. Nepovinný parametr.

> [!IMPORTANT]
> Tuto techniku použijte pouze v případě, že jste si jisti, že rozsah pro aktualizované mapování je prázdný. Výše uvedené metody nekontrolují data pro oblast, která je přesunuta, takže je nejlepší zahrnout kontroly ve vašem kódu.

Tento příklad odebere úlomky z mapy štřepů.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Mapa horizontálního oddílu odráží umístění horizontálního oddílu v GSM před odstraněním horizontálního oddílu. Vzhledem k tomu, že oddíl byl odstraněn, předpokládá se, že to bylo úmyslné a rozsah klíčů horizontu se již nepoužívá. Pokud ne, můžete provést obnovení bodu v čase. obnovení úlomek z dřívějšího bodu v čase. (V takovém případě zkontrolujte následující část a zjistěte nekonzistence sdílčími úlomky.) Chcete-li obnovit, naleznete [v tématu Point in time zotavení](sql-database-recovery-using-backups.md).

Vzhledem k tomu, že se předpokládá, že odstranění databáze bylo úmyslné, je konečnou akcí vyčištění správy odstranění položky horizontálního oddílu ve správci map horizontálního oddílu. Tím zabráníte aplikaci neúmyslně zápis informací do oblasti, která není očekávána.

## <a name="to-detect-mapping-differences"></a>Zjištění rozdílů v mapování

[Metoda DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) vybere a vrátí jednu z map úlomků (místní nebo globální) jako zdroj pravdy a odsouhlasí mapování na mapách střepů (GSM a LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Umístění* určuje název serveru a název databáze.
* Parametr *shardMapName* je název mapy střepů. To je vyžadováno pouze v případě, že více map skřípků jsou spravovány stejným správcem mapy oddílu. Nepovinný parametr.

## <a name="to-resolve-mapping-differences"></a>Řešení rozdílů mapování

[Metoda ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) vybere jednu z map úložného oddílu (místní nebo globální) jako zdroj pravdy a odsouhlasí mapování na mapách střepů (GSM a LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* parametr enumerates rozdíly v mapování mezi GSM a LSM pro konkrétní střep.
* [MappingDifferenceResolution výčet](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) se používá k označení metody pro řešení rozdílu mezi mapování míchací ho.
* **MappingDifferenceResolution.KeepShardMapping** se doporučuje, aby v případě, že LSM obsahuje přesné mapování a proto mapování v úlomek by měl být použit. To je obvykle případ, pokud je převzetí služeb při selhání: oddíl střižný objekt nyní sídlí na novém serveru. Vzhledem k tomu, že úlomek musí být nejprve odebrán z GSM (pomocí metody RecoveryManager.DetachShard), mapování již v GSM neexistuje. Proto lsm musí být použit k obnovení mapování oddílu.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Připojení úlomek na střep po obnovení střepu

[AttachShard metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) připojí daný šiřidlo na mapě šikříku. Potom zjistí všechny nekonzistence mapy oddílu a aktualizuje mapování tak, aby odpovídaly úlomku v okamžiku obnovení oddílu. Předpokládá se, že databáze je také přejmenován tak, aby odrážely původní název databáze (před byl obnoven oddíl), protože bod-in čas obnovení výchozí nové databáze připojené s časovým razítkem.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* Parametr *umístění* je název serveru a název databáze připojovaného oddílu.
* Parametr *shardMapName* je název mapy střepů. To je vyžadováno pouze v případě, že více map skřípků je spravováno stejným správcem mapy oddílu. Nepovinný parametr.

Tento příklad přidá šiř do mapy šibenic, která byla nedávno obnovena z dřívějšího bodu v čase. Vzhledem k tomu, že byl obnoven úlomek (konkrétně mapování pro oddíl v LSM), je potenciálně nekonzistentní s položkou úlomku v GSM. Mimo tento ukázkový kód byl oddíl oddílu obnoven a přejmenován na původní název databáze. Vzhledem k tomu, že byla obnovena, předpokládá se, že mapování v LSM je důvěryhodné mapování.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualizace umístění úlomků po geografickém převzetí služeb při selhání (obnovení) úlomků

Pokud je geograficky převzetí služeb při selhání, sekundární databáze je zpřístupnil zápis přístupné a stane se novou primární databáze. Název serveru a potenciálně databáze (v závislosti na konfiguraci) se může lišit od původní primární. Proto musí být opraveny položky mapování pro úlomek v GSM a LSM. Podobně pokud je databáze obnovena na jiný název nebo umístění nebo do dřívějšího bodu v čase, může to způsobit nekonzistence v mapách oddílu. Správce mapy svižných bitových částí zpracovává distribuci otevřených připojení ke správné databázi. Distribuce je založena na datech v mapě střepů a hodnotě klíče srážlivosti, který je cílem požadavku aplikace. Po geografickém převzetí služeb při selhání musí být tyto informace aktualizovány přesným názvem serveru, názvem databáze a mapováním oddílu oddílu obnovené databáze.

## <a name="best-practices"></a>Osvědčené postupy

Geografické převzetí služeb při selhání a obnovení jsou operace, které obvykle spravuje správce cloudu aplikace, který záměrně využívá jednu z funkcí kontinuity podnikání v databázích Azure SQL. Plánování kontinuity provozu vyžaduje procesy, postupy a opatření, aby bylo zajištěno, že obchodní operace mohou pokračovat bez přerušení. Metody, které jsou k dispozici jako součást třídy RecoveryManager, by měly být použity v rámci tohoto pracovního toku, aby bylo zajištěno, že GSM a LSM jsou průběžně aktuální na základě provedené akce obnovení. Existuje pět základních kroků, jak správně zajistit, aby GSM a LSM odrážely přesné informace po události převzetí služeb při selhání. Kód aplikace pro provedení těchto kroků lze integrovat do existujících nástrojů a pracovního postupu.

1. Načíst RecoveryManager z ShardMapManager.
2. Odpojte starý úlomek od mapy úlomku.
3. Připojte nový úlomek k mapě úlomku, včetně nového umístění šmejdu.
4. Zjistěte nekonzistence v mapování mezi GSM a LSM.
5. Vyřešte rozdíly mezi GSM a LSM a důvěřujte LSM.

Tento příklad provádí následující kroky:

1. Odebere úlomky z mapy štřepů, které odrážejí umístění štřepů před událostí převzetí služeb při selhání.
2. Připojí úlomky k mapě střepů odrážející nová umístění střepů (parametr "Configuration.SecondaryServer" je nový název serveru, ale stejný název databáze).
3. Načte tokeny pro obnovení zjišťováním rozdílů mapování mezi GSM a LSM pro každý úlomek.
4. Řeší nekonzistence tím, že důvěřuje mapování z LSM každého střepu.

   ```java
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

---
title: Recovery Manager opravit problémy s mapou horizontálních oddílů
description: Použití třídy RecoveryManager k řešení problémů s mapami horizontálních oddílů
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: fdd5f7d291d9c56361c17547628795b378091109
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443448"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Oprava problémů s mapováním horizontálních oddílů pomocí třídy RecoveryManager
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Třída [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) poskytuje aplikacím ADO.NET možnost snadno detekovat a opravovat všechny nekonzistence mezi globálním horizontálních oddílů map (GSM) a místní mapou horizontálních oddílů (LSM) v prostředí databáze horizontálně dělené.

GSM a LSM sledují mapování jednotlivých databází v prostředí horizontálně dělené. Občas dojde k přerušení mezi systémem GSM a LSM. V takovém případě k detekci a opravě přerušení použijte třídu RecoveryManager.

Třída RecoveryManager je součástí [klientské knihovny elastic Database](elastic-database-client-library.md).

![Mapa horizontálních oddílů][1]

Definice termínů najdete v článku [Glosář nástrojů pro elastic Database](elastic-scale-glossary.md). Pokud chcete pochopit, jak se **ShardMapManager** používá ke správě dat v řešení horizontálně dělené, přečtěte si téma [Správa map horizontálních oddílů](elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Proč používat Správce obnovení

V prostředí databáze horizontálně dělené je jeden tenant na databázi a mnoho databází na jeden server. V prostředí může být také mnoho serverů. Každá databáze je mapována v mapě horizontálních oddílů, takže volání lze směrovat do správného serveru a databáze. Databáze jsou sledovány podle **horizontálního dělení klíče**a každá horizontálních oddílů je přiřazena **Rozsah hodnot klíče**. Horizontálního dělení klíč může například představovat jména zákazníků z "D" do "F". Mapování všech horizontálních oddílů (označovaných také jako databáze) a jejich rozsahy mapování jsou obsaženy v **globálním mapování horizontálních oddílů (GSM)**. Každá databáze také obsahuje mapu rozsahů obsažených v horizontálních oddílů, které se označují jako **místní Mapa horizontálních oddílů (LSM)**. Když se aplikace připojí k horizontálních oddílů, mapování se uloží do mezipaměti s aplikací pro rychlé načtení. LSM se používá k ověření dat uložených v mezipaměti.

Z následujících důvodů se může stát, že se nesynchronizují LSM a GSM.

1. Odstranění horizontálních oddílů, jehož rozsah je považován za již nepoužitý, nebo přejmenování horizontálních oddílů. Výsledkem odstranění horizontálních oddílů je **osamocené mapování horizontálních oddílů**. Podobně přejmenovaná databáze může způsobit oddělené mapování horizontálních oddílů. V závislosti na záměru změny může být nutné odebrat horizontálních oddílů nebo umístění horizontálních oddílů musí být aktualizováno. Informace o obnovení odstraněné databáze najdete v tématu [Obnovení odstraněné databáze](recovery-using-backups.md).
2. Dojde k události geografické převzetí služeb při selhání. Chcete-li pokračovat, je třeba aktualizovat název serveru a název databáze správce mapy horizontálních oddílů v aplikaci a poté aktualizovat podrobnosti mapování horizontálních oddílů pro všechny horizontálních oddílů v mapě horizontálních oddílů. Pokud dojde k geografickému převzetí služeb při selhání, musí být tato logika obnovení automatizovaná v rámci pracovního postupu převzetí služeb při selhání. Automatizace akcí obnovení umožňuje bezproblémovou správu pro geograficky dostupné databáze a vyhnout se ručním lidským akcím. Další informace o možnostech obnovení databáze v případě výpadku datového centra najdete v tématu [provozní kontinuita](business-continuity-high-availability-disaster-recover-hadr-overview.md) a [zotavení po havárii](disaster-recovery-guidance.md).
3. Horizontálních oddílů nebo databáze ShardMapManager se obnovily do dřívějšího bodu v čase. Informace o obnovení bodu v čase pomocí zálohování najdete v tématu [obnovení pomocí záloh](recovery-using-backups.md).

Další informace o Azure SQL Database Elastic Databasech nástrojích, geografickou replikaci a obnovení najdete v následujících tématech:

* [Přehled: provozní kontinuita v cloudu a zotavení po havárii databáze pomocí SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Začínáme s nástroji elastické databáze](elastic-scale-get-started.md)  
* [Správa ShardMap](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Načítání RecoveryManager z ShardMapManager

Prvním krokem je vytvoření instance RecoveryManager. [Metoda GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) vrátí správce obnovení pro aktuální instanci [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) . Chcete-li vyřešit jakékoli nekonzistence v mapě horizontálních oddílů, je nutné nejprve načíst RecoveryManager pro konkrétní mapu horizontálních oddílů.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

V tomto příkladu je RecoveryManager inicializován z ShardMapManager. ShardMapManager obsahující ShardMap je také již inicializován.

Vzhledem k tomu, že tento kód aplikace pracuje přímo s mapou horizontálních oddílů, přihlašovací údaje použité v metodě Factory (v předchozím příkladu, smmConnectionString) by měly být přihlašovací údaje, které mají oprávnění ke čtení i zápisu v databázi GSM odkazované připojovacím řetězcem. Tyto přihlašovací údaje se obvykle liší od přihlašovacích údajů používaných k otevření připojení pro směrování závislé na datech. Další informace najdete v tématu [použití přihlašovacích údajů v klientovi elastické databáze](elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Odebrání horizontálních oddílů z ShardMap po odstranění horizontálních oddílů

[Metoda DetachShard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) odpojí daný horizontálních oddílů od mapy horizontálních oddílů a odstraní mapování přidružená k horizontálních oddílů.  

* Parametr Location (umístění) je umístění horizontálních oddílů, konkrétně název serveru a název databáze horizontálních oddílů, který se má odpojit.
* Parametr shardMapName je název mapy horizontálních oddílů. To je nutné pouze v případě, že je více map horizontálních oddílů spravováno stejným správcem map horizontálních oddílů. Nepovinný parametr.

> [!IMPORTANT]
> Tuto techniku použijte pouze v případě, že jste si jisti, že rozsah aktualizovaných mapování je prázdný. Výše uvedené metody nekontrolují data pro rozsah, který se přesouvá, takže je nejlepší zahrnout do kódu kontroly.

Tento příklad odebere horizontálních oddílů z mapy horizontálních oddílů.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Mapa horizontálních oddílů odráží umístění horizontálních oddílů v GSM před odstraněním horizontálních oddílů. Vzhledem k tomu, že se horizontálních oddílů odstranil, předpokládá se, že to bylo úmyslné, a rozsah klíčů horizontálního dělení se už nepoužívá. V takovém případě můžete provést obnovení v čase. obnovení horizontálních oddílů z dřívějšího bodu v čase. (V takovém případě si přečtěte následující část, kde zjistíte nekonzistence horizontálních oddílů.) Obnovení najdete v tématu [obnovení k bodu v čase](recovery-using-backups.md).

Vzhledem k tomu, že se předpokládá, že odstranění databáze bylo úmyslné, konečná akce čištění správy je odstranění záznamu do horizontálních oddílů ve Správci map horizontálních oddílů. To brání aplikaci v neúmyslném zápisu informací do rozsahu, který není očekáván.

## <a name="to-detect-mapping-differences"></a>Zjišťování rozdílů v mapování

[Metoda DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) vybere a vrátí jedno z map horizontálních oddílů (místní nebo globální) jako zdroj pravdy a sloučí mapování jak na mapě horizontálních oddílů (GSM a LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Umístění* Určuje název serveru a název databáze.
* Parametr *shardMapName* je název mapy horizontálních oddílů. Tento požadavek je vyžadován pouze v případě, že je více map horizontálních oddílů spravováno stejným správcem map horizontálních oddílů. Nepovinný parametr.

## <a name="to-resolve-mapping-differences"></a>Řešení rozdílů v mapování

[Metoda ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) vybere jedno z map horizontálních oddílů (místní nebo globální) jako zdroj pravdy a sloučí mapování jak na mapě horizontálních oddílů (GSM a LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Parametr *RecoveryToken* vyčísluje rozdíly v mapování mezi verzemi GSM a lsm pro konkrétní horizontálních oddílů.
* [Výčet MappingDifferenceResolution](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) slouží k označení metody pro vyřešení rozdílu mezi mapováním horizontálních oddílů.
* **MappingDifferenceResolution. KeepShardMapping** se doporučuje, aby v případě, že lsm obsahuje přesné mapování, a proto by mělo být použito mapování v horizontálních oddílů. To je obvykle případ, pokud dojde k převzetí služeb při selhání: horizontálních oddílů se teď nachází na novém serveru. Vzhledem k tomu, že se horizontálních oddílů musí nejdřív odebrat z GSM (pomocí metody RecoveryManager. DetachShard), mapování už v systému GSM neexistuje. Proto je nutné použít LSM k opětovnému vytvoření mapování horizontálních oddílů.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Připojit horizontálních oddílů k ShardMap po obnovení horizontálních oddílů

[Metoda AttachShard](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) připojí daný horizontálních oddílů k mapě horizontálních oddílů. Pak detekuje všechny nekonzistence map horizontálních oddílů a aktualizuje mapování tak, aby odpovídalo horizontálních oddílů v místě obnovení horizontálních oddílů. Předpokládá se, že databáze je také přejmenována tak, aby odrážela původní název databáze (před obnovením horizontálních oddílů), protože výchozí databáze připojená k časovému razítku má za následek obnovení výchozího bodu v čase.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* Parametr *Location (umístění* ) je název serveru a název databáze horizontálních oddílů, který se připojuje.
* Parametr *shardMapName* je název mapy horizontálních oddílů. To je nutné pouze v případě, že je více map horizontálních oddílů spravováno stejným správcem map horizontálních oddílů. Nepovinný parametr.

Tento příklad přidá horizontálních oddílů k mapě horizontálních oddílů, která se nedávno obnovila z dřívějšího bodu v čase. Vzhledem k tomu, že byl obnoven horizontálních oddílů (konkrétně mapování pro horizontálních oddílů v LSM), je potenciálně nekonzistentní s položkou horizontálních oddílů v GSM. Mimo tento vzorový kód byl horizontálních oddílů obnoven a přejmenován na původní název databáze. Vzhledem k tomu, že byl obnoven, se předpokládá, že mapování v LSM je důvěryhodné mapování.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualizace umístění horizontálních oddílů po geografickém převzetí služeb při selhání (obnovení) horizontálních oddílů

Pokud dojde k geografickému převzetí služeb při selhání, je sekundární databázi přístupný k zápisu a stane se novou primární databází. Název serveru a potenciálně databáze (v závislosti na vaší konfiguraci) se mohou lišit od původního primárního. Proto musí být opraveny položky mapování pro horizontálních oddílů v GSM a LSM. Podobně platí, že pokud je databáze obnovena na jiný název nebo umístění nebo k dřívějšímu bodu v čase, může to způsobit nekonzistence v mapách horizontálních oddílů. Správce map horizontálních oddílů zpracovává distribuci otevřených připojení ke správné databázi. Distribuce je založena na datech v mapě horizontálních oddílů a na hodnotě horizontálního dělení klíče, který je cílem žádosti o aplikace. Po geografickém převzetí služeb při selhání je potřeba tyto informace aktualizovat pomocí správného názvu serveru, názvu databáze a mapování horizontálních oddílů obnovené databáze.

## <a name="best-practices"></a>Osvědčené postupy

Geografické převzetí služeb při selhání a obnovení jsou operace obvykle spravované správcem cloudu aplikace, které úmyslně využívají Azure SQL Database funkcí provozní kontinuity. Plánování kontinuity podnikových procesů vyžaduje procesy, postupy a míry, aby bylo zajištěno, že obchodní operace mohou i nadále bez přerušení pokračovat v práci. Metody dostupné jako součást třídy RecoveryManager by měly být použity v rámci tohoto pracovního postupu, aby bylo zajištěno, že se soubory GSM a LSM udržují aktuální na základě provedených akcí obnovení. Existuje pět základních kroků ke správnému zajištění, aby se v GSM a LSM odrážely přesné informace po události převzetí služeb při selhání. Kód aplikace pro provedení těchto kroků lze integrovat do existujících nástrojů a pracovních postupů.

1. Načte RecoveryManager z ShardMapManager.
2. Odpojte starou horizontálních oddílů z mapy horizontálních oddílů.
3. Připojte nový horizontálních oddílů k mapě horizontálních oddílů, včetně nového umístění horizontálních oddílů.
4. Zjistí nekonzistence v mapování mezi verzemi GSM a LSM.
5. Řešení rozdílů mezi verzemi GSM a LSM, které důvěřují LSM.

Tento příklad provádí následující kroky:

1. Odebere horizontálních oddílů z mapy horizontálních oddílů, která odráží umístění horizontálních oddílů před událostí převzetí služeb při selhání.
2. Připojí horizontálních oddílů k mapě horizontálních oddílů odrážející nová umístění horizontálních oddílů (parametr Configuration. SecondaryServer je novým názvem serveru, ale stejným názvem databáze).
3. Načte tokeny obnovení zjištěním rozdílů v mapování mezi příponou GSM a LSM pro každou horizontálních oddílů.
4. Řeší nekonzistence tím, že důvěřuje mapování z LSM každého horizontálních oddílů.

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

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png

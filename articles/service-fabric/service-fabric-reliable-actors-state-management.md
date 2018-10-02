---
title: Správa stavu Reliable Actors | Dokumentace Microsoftu
description: Popisuje, jak spravovat, trvalé a replikují pro zajištění vysoké dostupnosti stavu Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: d5bc6877aa353ae37ba3ada53ee620a0230357e9
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585165"
---
# <a name="reliable-actors-state-management"></a>Správa stavu Reliable Actors
Reliable Actors jsou jednovláknových objektů zapouzdřujících logiky a stavových. Actors spustit v modelu Reliable Services, proto jejich stav udržovat spolehlivě s použitím stejného trvalosti a mechanismech replikace. Tímto způsobem actors neztratili po selhání, po aktivaci po uvolnění paměti nebo se přesouvat mezi uzly v clusteru kvůli vyrovnávání prostředků nebo upgrady jejich stav.

## <a name="state-persistence-and-replication"></a>Stav trvalost a replikaci
Všechny Reliable Actors jsou považovány za *stavová* vzhledem k tomu, že každá instance objektu actor se mapuje na jedinečné ID. To znamená, že opakovaná volání stejnému identifikátoru objektu actor se směrují do stejné instance objektu actor. V rámci bezstavové systému naopak volání klienta není zaručena bude směrovat na stejný server pokaždé, když. Z tohoto důvodu jsou služby objektu actor vždy stavové služby.

I když actors jsou považovány za stavová, neznamená, že musí spolehlivé ukládání stavu. Účastníky můžete zvolit úroveň trvalost stavu a replikace na základě svých dat požadavky na úložiště:

* **Trvalý stav**: stav ukládaný na disk a replikovaný pro tři nebo více replikami. Trvalý stav je nejvíce trvalý možnost úložiště stavu, ve kterém můžete zachovat stav prostřednictvím výpadek celého clusteru.
* **Volatile stavu**: stav je replikovat do tří nebo více replikami a uchovávají pouze v paměti. Volatile stavu poskytuje odolnost proti selhání uzlu a selhání objektu actor a během upgradu a vyrovnávání prostředků. Nicméně, není stav ukládaný na disk. Takže pokud všechny repliky byly ztraceny najednou, stav je ztraceny i.
* **Žádný trvalý stav**: stav není replikovány nebo zapisují na disk, používat jenom pro objekty actor, které není potřeba spolehlivě zachovat stav.

Každá úroveň trvalosti je jednoduše jiný *zprostředkovatele stavu* a *replikace* konfigurace vaší služby. Určuje, jestli se stav je zapsaný na disk závisí na poskytovateli stavu – součástí spolehlivé služby, který ukládá stav. Replikace závisí na tom, kolik repliky služby se nasazuje s. Stejně jako Reliable Services zprostředkovatele stavu i počet replik lze snadno nastavit ručně. Rozhraní objektu actor poskytuje atribut, který, pokud použít na prvek "actor", automaticky vybere výchozího zprostředkovatele stavu a automaticky generuje nastavení pro počet replik dosáhnout jednoho z těchto tří nastavení trvalosti. Atribut StatePersistence není zděděn z odvozené třídy, každý typ objektu Actor musíte zadat jeho StatePersistence úroveň.

### <a name="persisted-state"></a>Trvalý stav
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Toto nastavení využívá zprostředkovatele stavu, který ukládá data na disku a automaticky nastaví počet replik služby na 3.

### <a name="volatile-state"></a>Volatile stavu
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Toto nastavení používá poskytovatele v paměti – pouze pro stav a nastaví počet replik na 3.

### <a name="no-persisted-state"></a>Žádný trvalý stav
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Toto nastavení používá poskytovatele v paměti – pouze pro stav a nastaví počet replik na 1.

### <a name="defaults-and-generated-settings"></a>Vygenerované nastavení a výchozí hodnoty
Když používáte `StatePersistence` atribut zprostředkovatele stavu je automaticky vybraná za běhu při spuštění služby objektu actor. Počet replik, ale je nastavena v době kompilace pomocí nástrojů pro vytváření objektu actor sady Visual Studio. Nástroje sestavení automaticky generovat *výchozí služby* služba objektu actor v souboru ApplicationManifest.xml. Vytvoří se pro parametry **velikost sady replik min** a **velikost sady replik cílové**.

Tyto parametry můžete změnit ručně. Ale pokaždé, když `StatePersistence` změnit atribut, parametry jsou nastaveny na výchozí hodnoty velikost sady replik pro vybranou `StatePersistence` atribut přepsání jakékoli předchozí hodnoty. Jinými slovy, jsou hodnoty, které jste nastavili v souboru ServiceManifest.xml *pouze* přepsat v okamžiku sestavení při změně `StatePersistence` hodnotu atributu.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Správce stavu
Každá instance objektu actor má své vlastní správce stavu: slovníku jako datová struktura, která je spolehlivě uchová dvojic klíč/hodnota. Správce stavu představuje obálku kolem zprostředkovatele stavu. Můžete ho použít k ukládání dat bez ohledu na to, který se používá nastavení trvalosti. Neposkytuje žádné záruky, že spuštěné služby objektu actor se dá změnit nastavení volatile (v paměti – jen) stavu do trvalého stavu nastavení prostřednictvím upgradu se zajištěním provozu při zachování dat. Je však možné změnit počet replik pro spuštěnou službu.

Správce stavu klíče musejí být řetězce. Hodnoty jsou obecné a může být libovolný typ, včetně vlastních typů. Hodnoty uložené v správce stavu musí být serializovatelný kontraktu dat, protože může přenést přes síť do jiných uzlů během replikace a může být zapsaný na disk, v závislosti na nastavení trvalého stavu prvek "actor".

Správce stavu uvádí běžné metody slovníku pro správu stavu, podobné těm v spolehlivého slovníku.

Příklady správy stavu objektu actor, najdete v článku [přístup, uložení a odstranění stavu Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Osvědčené postupy
Tady jsou některé doporučené postupy a tipy pro řešení potíží pro správu stavu objektu actor.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Vytvořit detailní co nejrychleji stavu objektu actor
To je velmi důležité pro výkon a využití prostředků aplikace. Pokaždé, když se neprovádí se žádné aktualizace/zápisu "pojmenované stavu" objektu actor, je na celočíselnou hodnotu odpovídající "s názvem státu" serializovat a odeslaných v síti do sekundárních replik.  Sekundárních replik zapsat do místních disků a odpověď zpět na primární repliku. Když primární přijme potvrzení od kvora sekundárních replik, zapíše stavu na svůj místní disk. Předpokládejme například, že hodnota je třída, která má 20 členy a o velikosti 1 MB. I v případě, že můžete upravovat jenom jeden z členy třídy, které je velikost 1 KB, můžete nakonec platíte cenu za serializace a sítě a disku zápisy pro úplné 1 MB. Podobně pokud je hodnota kolekce (jako je například seznam, pole nebo slovníku), platíte cenu úplnou kolekci i v případě, že upravíte jeden z jeho členů. Úložišti StateManager rozhraní třídy objektu actor je jako slovník. Vždy by měl modelů strukturu dat stavu objektu actor nad tento slovník reprezentující.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Správně spravovat životní cyklus objektu actor
Měli byste mít jasné zásady týkající se správy velikost stavu v každém oddílu služby objektu actor. Vaše služba objektu actor musí mít pevný počet objektů actor a opakovaně používat, je velmi nejvíce. Pokud vytvoříte nepřetržitě nových objektů actor, je třeba je smazat po dokončení práce s jejich práci. Rozhraní objektu actor ukládá některá metadata o každého objektu actor, který existuje. Odstraňuje se stav prvek "actor" neodebere metadata o tomto objektu actor. Je nutné odstranit objekt actor (viz [odstranění objektů actor a jejich stav](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) odebrat všechny informace o jeho uložených v systému. Jako další kontrolu, by měl dotazování na službu objektu actor (viz [vytváření výčtu objektů actor](service-fabric-reliable-actors-enumerate.md)) v k Ujistěte se, že číslo actors jsou do očekávaného rozsahu.
 
Pokud někdy uvidíte, že se mimo očekávané velikosti zvyšuje velikost souboru databáze služby objektu Actor, ujistěte se, že postupujete výše uvedených pokynů. Pokud jsou dodržení těchto pokynů a jsou stále databáze problémům s velikostí souboru, měli byste [vytvořit lístek podpory](service-fabric-support.md) s produktovým týmem na nápovědu.

## <a name="next-steps"></a>Další postup

Stav, který je uložen v Reliable Actors musí serializovat. před jeho zapsané na disk a replikovaný pro zajištění vysoké dostupnosti. Další informace o [serializaci typu Actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

V dalším kroku Další informace o [objektu Actor Diagnostika a sledování výkonu](service-fabric-reliable-actors-diagnostics.md).

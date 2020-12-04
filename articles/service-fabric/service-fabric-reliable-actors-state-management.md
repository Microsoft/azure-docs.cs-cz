---
title: Správa stavu Reliable Actors
description: Popisuje, jak je stav Reliable Actors spravovaný, trvalý a replikovaný pro zajištění vysoké dostupnosti.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575597"
---
# <a name="reliable-actors-state-management"></a>Správa stavu Reliable Actors
Reliable Actors jsou objekty s jedním vláknem, které mohou zapouzdřit logiku i stav. Vzhledem k tomu, že objekty actor běží na Reliable Services, mohou spolehlivě udržovat stav pomocí stejných mechanismů trvalosti a replikace. Díky tomu objekty actor neztratí stav po selhání, po opětovné aktivaci po uvolnění paměti nebo při jejich přesunu mezi uzly v clusteru kvůli vyrovnávání nebo upgradu prostředků.

## <a name="state-persistence-and-replication"></a>Trvalost stavu a replikace
Všechna Reliable Actors jsou považována za *stavová* , protože každá instance objektu actor je mapována na jedinečné ID. To znamená, že opakovaná volání stejného ID objektu actor jsou směrována do stejné instance objektu actor. V bezstavovém systému na rozdíl od klientů není zaručeno směrování na stejný server pokaždé, když jsou na stejném serveru. Z tohoto důvodu jsou služby objektu actor vždy stavové služby.

I když se aktéri považují za stav, což neznamená, že musí stav ukládat spolehlivě. Objekty actor mohou zvolit úroveň trvalosti stavu a replikaci na základě jejich požadavků na úložiště dat:

* **Trvalý stav**: stav je trval na disku a je replikován do tří nebo více replik. Trvalý stav je nejodolnější možnost úložiště stavu, ve které může stav trvat i při úplném výpadku clusteru.
* **Volatile stav**: stav je replikován na tři nebo více replik a je uložen pouze v paměti. Nestálý stav zajišťuje odolnost proti selhání uzlu a selhání objektu actor a během upgradů a vyrovnávání prostředků. Stav však není trvale uložen na disku. Takže pokud dojde ke ztrátě všech replik najednou, stav se ztratí také.
* **Žádný trvalý stav**: stav není replikovaný ani zápis na disk, používá se pouze pro objekty actor, které nepotřebují udržovat stav spolehlivě.

Každá úroveň trvalosti je jednoduše jiným *poskytovatelem stavu* a konfigurací *replikace* vaší služby. Bez ohledu na to, jestli je stav zapsaný na disk, závisí na poskytovateli stavu – na komponentě spolehlivé služby, která ukládá stav. Replikace závisí na počtu replik, se kterými je služba nasazená. Stejně jako u Reliable Services může být poskytovatel stavu a počet replik snadno nastaven ručně. Rozhraní actor Framework poskytuje atribut, který při použití objektu actor automaticky vybere výchozího zprostředkovatele stavu a automaticky vygeneruje nastavení pro počet replik, aby bylo dosaženo jednoho z těchto tří nastavení trvalosti. Atribut StatePersistence není děděný odvozenou třídou, každý typ objektu actor musí poskytovat svou StatePersistence úroveň.

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
Toto nastavení používá zprostředkovatele stavu, který ukládá data na disk a automaticky nastaví počet replik služby na 3.

### <a name="volatile-state"></a>Nestálý stav
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
Toto nastavení používá zprostředkovatele stavu pouze v paměti a nastavuje počet replik na 3.

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
Toto nastavení používá zprostředkovatele stavu pouze v paměti a nastavuje počet replik na hodnotu 1.

### <a name="defaults-and-generated-settings"></a>Výchozí a vygenerovaná nastavení
Při použití `StatePersistence` atributu je poskytovatel stavu automaticky vybrán za běhu při spuštění služby objektu actor. Počet replik je však nastaven v době kompilace pomocí nástrojů sestavení actor sady Visual Studio. Nástroje pro sestavení automaticky generují *výchozí službu* pro službu objektu actor v ApplicationManifest.xml. Jsou vytvořeny parametry pro **minimální velikost sady replik** a **Velikost cílové sady replik**.

Tyto parametry můžete změnit ručně. Ale pokaždé `StatePersistence` , když je atribut změněn, jsou parametry nastaveny na výchozí hodnoty velikosti sady replik pro vybraný `StatePersistence` atribut a přepsaly všechny předchozí hodnoty. Jinými slovy hodnoty, které jste nastavili v ServiceManifest.xml jsou přepsány *pouze* při sestavení při změně `StatePersistence` hodnoty atributu.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Každá instance objektu actor má svého vlastního správce stavu: datová struktura, která spolehlivě ukládá páry klíč/hodnota. Správce stavu je Obálka kolem poskytovatele stavu. Můžete ho použít k ukládání dat bez ohledu na to, jaké nastavení trvalosti se používá. Neposkytuje žádné záruky, že běžící službu objektu actor lze změnit z nastavení stavu volatile (pouze v paměti) na nastavení trvalého stavu prostřednictvím postupného upgradu při zachování dat. Je ale možné změnit počet replik pro běžící službu.

Klíče správce stavu musí být řetězce. Hodnoty jsou obecné a můžou být libovolného typu, včetně vlastních typů. Hodnoty uložené ve Správci stavů musí být serializovatelné kontrakty dat, protože můžou být přenášeny přes síť do jiných uzlů během replikace a můžou být zapsané na disk v závislosti na nastavení trvalosti stavu objektu actor.

Správce stavů zveřejňuje běžné Slovníkové metody pro správu stavu, podobně jako u těch, které se nacházejí ve spolehlivém slovníku.

Příklady správy stavu objektu actor, přístupu pro čtení [, ukládání a odebírání Reliable Actors stavu](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Osvědčené postupy
Tady jsou některé doporučené postupy a tipy pro řešení potíží pro správu stavu objektu actor.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Uveďte stav objektu actor jako podrobnějších možností
To je důležité pro výkon a využití prostředků vaší aplikace. Pokaždé, když je v objektu actor k dispozici nějaký zápis nebo aktualizace, celá hodnota odpovídající tomuto "pojmenovanému stavu" je serializována a odeslána prostřednictvím sítě do sekundárních replik.  Sekundární replika ji zapíše na místní disk a odpoví zpátky na primární repliku. Když primární obdrží potvrzení z kvora sekundárních replik, zapíše stav na svůj místní disk. Předpokládejme například, že hodnota je třída, která má 20 členů a velikost 1 MB. I v případě, že jste změnili pouze jednoho člena třídy, který má velikost 1 KB, platíte náklady na serializaci a zápisy ze sítě a disku na plný 1 MB. Podobně platí, že pokud je hodnota kolekcí (například seznam, pole nebo slovník), platíte náklady za celou kolekci i v případě, že změníte jednu z jejích členů. Rozhraní úložišti StateManager třídy Actor je jako slovník. Vždy byste měli modelovat strukturu dat představující stav objektu actor nad tímto slovníkem.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Správně spravovat životní cyklus objektu actor
Měli byste mít jasné zásady týkající se správy velikosti stavu v jednotlivých oddílech služby objektu actor. Vaše služba objektu actor by měla mít pevný počet objektů actor a znovu je použít co nejvíce. Pokud průběžně vytváříte nové objekty actor, musíte je po dokončení práce s jejich prací odstranit. Rozhraní actor Framework uchovává některá metadata o každém objektu actor, který existuje. Odstraněním všech stavů objektu actor se neodstraňují metadata tohoto objektu actor. Chcete-li odebrat všechny informace, které jsou uloženy v systému, je nutné objekt actor odstranit (viz téma [odstranění objektů actor a jejich stav](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)). Jako další kontrolu byste měli zadat dotaz na službu objektu actor (Další informace naleznete v tématu [vytváření výčtu objektů](service-fabric-reliable-actors-enumerate.md)Actors), abyste měli jistotu, že počet objektů actor je v očekávaném rozsahu.
 
Pokud se někdy zobrazuje, že velikost souboru databáze objektu actor je větší než očekávaná velikost, ujistěte se, že jste provedli předchozí pokyny. Pokud tyto pokyny dodržujete a stále dochází k problémům s velikostí souborů databáze, měli byste [otevřít lístek podpory](service-fabric-support.md) s produktovým týmem, abyste mohli získat pomoc.

## <a name="next-steps"></a>Další kroky

Stav, který je uložen v Reliable Actors musí být serializován před zapsáním na disk a replikován pro zajištění vysoké dostupnosti. Přečtěte si další informace o [serializaci typu objektu actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

V dalším kroku se dozvíte další informace o [diagnostice objektu actor a monitorování výkonu](service-fabric-reliable-actors-diagnostics.md).

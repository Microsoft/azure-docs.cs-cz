---
title: Spolehlivá správa stavu Actors
description: Popisuje, jak spolehlivé objekty actor stav je spravován, trvalé a replikovány pro vysokou dostupnost.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9962d4333e458243670d1005ad2ccfbc0bb7c92a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348920"
---
# <a name="reliable-actors-state-management"></a>Spolehlivá správa stavu Actors
Reliable Actors jsou objekty s jedním podprocesem, které mohou zapouzdřit logiku i stav. Vzhledem k tomu, že objekty actor spustit spolehlivé služby, mohou udržovat stav spolehlivě pomocí stejné perzistence a replikace mechanismy. Tímto způsobem objekty actor neztrácejí svůj stav po selhání, po opětovné aktivaci po uvolnění paměti nebo při jejich přesunutí mezi uzly v clusteru z důvodu vyrovnávání prostředků nebo upgrady.

## <a name="state-persistence-and-replication"></a>Trvalosti a replikace stavu
Všechny spolehlivé objekty actor jsou považovány za *stavové,* protože každá instance objektu actor mapuje na jedinečné ID. To znamená, že opakované volání stejného ID objektu actor jsou směrovány do stejné instance objektu actor. V bezstavovém systému naopak není zaručeno, že volání klientů budou pokaždé směrována na stejný server. Z tohoto důvodu actor služby jsou vždy stavové služby.

I když jsou objekty actor považovány za stavové, neznamená to, že musí spolehlivě ukládat stav. Objekty actor mohou zvolit úroveň trvalost stavu a replikace na základě jejich požadavků na ukládání dat:

* **Trvalý stav**: Stav je trvalý na disk a je replikován na tři nebo více replik. Trvalý stav je nejodolnější možnost úložiště stavu, kde stav může přetrvávat až do úplného výpadku clusteru.
* **Nestálý stav**: Stav je replikován na tři nebo více replik a uchovává pouze v paměti. Nestálý stav poskytuje odolnost proti selhání uzlu a selhání objektu actor a během upgradů a vyrovnávání prostředků. Stav však není trvalý na disk. Takže pokud jsou všechny repliky ztraceny najednou, stav je ztracen také.
* **Žádný trvalý stav**: Stav není replikován nebo zapsán na disk, používá se pouze pro objekty actor, které nepotřebují spolehlivě udržovat stav.

Každá úroveň trvalosti je jednoduše jiný *poskytovatel stavu* a *replikace* konfigurace služby. Zda je stav zapsán na disk, závisí na zprostředkovateli stavu -- součásti ve spolehlivé službě, která ukládá stav. Replikace závisí na tom, kolik replik je služba nasazena. Stejně jako u spolehlivých služeb lze počet zprostředkovatelů stavu i replik snadno nastavit ručně. Rozhraní actor poskytuje atribut, který při použití v objektu actor automaticky vybere výchozího zprostředkovatele stavu a automaticky generuje nastavení pro počet replik k dosažení jednoho z těchto tří nastavení trvalosti. StatePersistence atribut není zděděn odvozené třídy, každý typ Actor musí poskytnout jeho statePersistence úroveň.

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

### <a name="volatile-state"></a>Stav těkavých
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
Toto nastavení používá zprostředkovatele stavu pouze v paměti a nastaví počet replik na 3.

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
Toto nastavení používá zprostředkovatele stavu pouze v paměti a nastaví počet replik na 1.

### <a name="defaults-and-generated-settings"></a>Výchozí a vygenerovaná nastavení
Při použití atributu `StatePersistence` je automaticky vybrán zprostředkovatel stavu za běhu při spuštění služby objektu actor. Počet replik je však nastaven v době kompilace nástroji pro sestavení objektu actor sady Visual Studio. Nástroje sestavení automaticky generují *výchozí službu* pro službu objektu actor v applicationManifest.xml. Parametry jsou vytvořeny pro **velikost min repliky sady** a **cílová velikost sady replik**.

Tyto parametry můžete změnit ručně. Při každé `StatePersistence` změně atributu jsou však parametry nastaveny na výchozí `StatePersistence` hodnoty velikosti sady replik pro vybraný atribut, které převažují nad všemi předchozími hodnotami. Jinými slovy hodnoty, které nastavíte v ServiceManifest.xml jsou přepsány *pouze* v době sestavení při změně hodnoty atributu. `StatePersistence`

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

## <a name="state-manager"></a>Správce státu
Každá instance objektu actor má vlastní správce stavu: strukturu dat podobný slovníku, která spolehlivě ukládá dvojice klíč/hodnota. Správce stavu je obálka kolem zprostředkovatele stavu. Můžete ji použít k ukládání dat bez ohledu na to, které nastavení trvalosti se používá. Neposkytuje žádné záruky, že spuštěná služba objektu actor lze změnit z nastavení stavu nestálého (pouze v paměti) na trvalé nastavení stavu prostřednictvím postupné inovace při zachování dat. Je však možné změnit počet replik pro spuštěnou službu.

Klíče správce stavu musí být řetězce. Hodnoty jsou obecné a mohou být libovolný mač, včetně vlastních typů. Hodnoty uložené ve správci stavu musí být data smlouvy serializovatelné, protože mohou být přenášeny po síti do jiných uzlů během replikace a mohou být zapsány na disk, v závislosti na nastavení trvalého stavu objektu actor.

Správce stavu zveřejňuje běžné slovníkové metody pro správu stavu, podobné těm, které se nacházejí v spolehlivém slovníku.

Příklady správy stavu objektu actor, číst [přístup, uložit a odebrat spolehlivé objekty actor stavu](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Osvědčené postupy
Tady jsou některé navrhované postupy a tipy pro řešení potíží pro správu stavu objektu actor.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Aby byl stav objektu actor co nejpodrobnější
To je důležité pro výkon a využití prostředků vaší aplikace. Vždy, když je jakýkoli zápis nebo aktualizace "pojmenovaný stav" objektu actor, celá hodnota odpovídající tomuto "pojmenovaného stavu" je serializována a odeslána po síti sekundární repliky.  Sekundární zapisují na místní disk a odpoví zpět na primární repliku. Když primární obdrží potvrzení z kvora sekundární repliky, zapíše stav na jeho místní disk. Předpokládejme například, že hodnota je třída, která má 20 členů a velikost 1 MB. I v případě, že jste pouze upravili jeden z členů třídy, který je velikost 1 KB, nakonec zaplatíte náklady na serializaci a síťové a diskové zápisy za celý chod 1 MB. Podobně pokud je hodnota kolekce (například seznam, pole nebo slovník), zaplatíte náklady na celou kolekci i v případě, že změníte jeden z členů. StateManager rozhraní třídy actor je jako slovník. Vždy byste měli modelovat strukturu dat představující stav objektu actor nad tímto slovníkem.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Správná správa životního cyklu herce
Měli byste mít jasné zásady týkající se správy stavu v každém oddílu služby objektu actor. Vaše služba objektu actor by měla mít pevný počet aktérů a co nejvíce je znovu použít. Pokud neustále vytváříte nové herce, musíte je odstranit, jakmile budou hotovi se svou prací. Rozhraní actor ukládá některá metadata o každý objekt actor, který existuje. Odstraněním celého stavu objektu actor neodeberete metadata o tomto objektu actor. Chcete-li odebrat všechny informace o něm uložené v systému, je nutné odstranit objekt actor (viz [odstranění aktérů a jejich stavu).](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state) Jako další kontrolu byste měli dotaz na službu objektu actor (viz [výčet objekty actor)](service-fabric-reliable-actors-enumerate.md)jednou za čas, abyste se ujistili, že počet aktérů je v očekávaném rozsahu.
 
Pokud někdy uvidíte, že velikost souboru databáze služby actor se zvyšuje nad očekávanou velikost, ujistěte se, že jste podle předchozích pokynů. Pokud dodržujete tyto pokyny a stále máte problémy s velikostí souboru databáze, měli byste [otevřít lístek podpory](service-fabric-support.md) s produktovým týmem, abyste získali nápovědu.

## <a name="next-steps"></a>Další kroky

Stav, který je uložen v reliable actors musí být serializovánpřed jeho zapsány na disk a replikovány pro vysokou dostupnost. Další informace o [serializaci typu actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Další informace o [diagnostice a sledování výkonu objektu Actor](service-fabric-reliable-actors-diagnostics.md).

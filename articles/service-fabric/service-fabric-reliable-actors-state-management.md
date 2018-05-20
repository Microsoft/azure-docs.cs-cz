---
title: Stav správy Reliable Actors | Microsoft Docs
description: Popisuje způsob správy, jako trvalý a pro zajištění vysoké dostupnosti replikují Reliable Actors stavu.
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
ms.openlocfilehash: 3cab4d87eacc7bce17da64cda213086c262179a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-actors-state-management"></a>Spolehlivé aktéři řízení stavu
Reliable Actors jsou jedním podprocesem objekty, které může zapouzdřit logiku a stavu. Protože aktéři spustit na spolehlivé služby, jejich stavu udržovat spolehlivé pomocí stejné trvalosti a mechanismech replikace. Tímto způsobem Neztraťte aktéři jejich stav po selhání při opětovné aktivaci po uvolnění paměti, nebo při jejich přesunu mezi uzly v clusteru s podporou kvůli vyrovnávání prostředků nebo upgradu.

## <a name="state-persistence-and-replication"></a>Trvalost stavu a replikace
Jsou považovány za všechny Reliable Actors *stateful* vzhledem k tomu, že každá instance objektu actor se mapuje na jedinečný identifikátor. To znamená, že opakovaná volání stejné ID objektu actor jsou směrované na stejnou instanci objektu actor. V systému bezstavové naopak volání klienta není zaručena bezpečnost pro směrované na stejný server pokaždé, když. Z tohoto důvodu služby objektu actor jsou vždy stavové služby.

I když aktéři jsou považovány za stateful, který neznamená, že musí spolehlivě uložení stavu. Aktéři můžete vybrat úroveň trvalost stavu a replikace na základě jejich dat požadavky na úložiště:

* **Trvalý stav**: je uložen stav disku a se replikují do tři nebo více replik. Trvalého stavu je možnost nejvíce odolná úložiště stavu, kde můžete zachovat stav prostřednictvím clusterů výpadku.
* **Volatile stavu**: stav se replikovat na tři nebo více replik a pouze uchovává se v paměti. Volatile stavu poskytuje odolnost proti selhání uzlu a selhání objektu actor a během upgradu a vyrovnávání prostředků. Však není trvalý stav na disk. Proto všechny repliky byly ztraceny najednou, stav dojde ke ztrátě také.
* **Žádné trvalého stavu**: stav není replikovat nebo zapsaných na disk, používají jenom pro objekty actor nemusíte spolehlivě Udržovat stav.

Každou úroveň trvalost je jednoduše jiné *zprostředkovatele stavu* a *replikace* konfigurace služby. Zda je stav zapsán do disku závisí na poskytovateli stavu--součástí spolehlivá služba, která ukládá stav. Replikace závisí na tom, kolik repliky může služba nasazený s. Stejně jako u spolehlivé služby zprostředkovatele stavu i počet replik jde snadno nastavit ručně. Rozhraní objektu actor poskytuje atribut, který, když použije na objekt actor, automaticky vybere výchozí poskytovatel stavu a automaticky vygeneruje nastavení pro repliky počet dosáhnout jednoho z těchto tří nastavení trvalosti. Atribut StatePersistence není zdědí odvozené třídy, každý typ objektu Actor musíte zadat jeho StatePersistence úroveň.

### <a name="persisted-state"></a>Trvalého stavu
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
Toto nastavení využívá zprostředkovatele stavu, která ukládá data na disku a automaticky nastaví počet replik služby 3.

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
Toto nastavení používá poskytovatele v paměti jen stavu a nastaví počet replik na 3.

### <a name="no-persisted-state"></a>Žádné trvalého stavu
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
Toto nastavení používá poskytovatele v paměti jen stavu a nastaví počet replik na 1.

### <a name="defaults-and-generated-settings"></a>Výchozí hodnoty a vygenerované nastavení
Pokud používáte `StatePersistence` atribut zprostředkovatele stavu je automaticky vybrána pro za běhu při spuštění služby objektu actor. Počet replik, ale je nastavena v době kompilace pomocí nástroje sestavení sady Visual Studio objektu actor. Nástroje pro sestavení automaticky generovat *výchozí služba* služby objektu actor v ApplicationManifest.xml. Vytvoří se pro parametry **sady replik minimální velikost** a **cíl repliky nastavit velikost**.

Tyto parametry můžete ručně změnit. Ale pokaždé, když `StatePersistence` změně atributu, parametry jsou nastaveny na výchozí hodnoty velikosti sady replik pro vybranou `StatePersistence` atribut přepsání žádné předchozí hodnoty. Jinými slovy, jsou hodnoty, které se nastavují v ServiceManifest.xml *pouze* přepsat v čase vytvoření buildu, když změníte `StatePersistence` hodnota atributu.

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
Všechny instance objektu actor má svou vlastní správce stavu: jako slovník datová struktura, která spolehlivě uchová dvojice klíč/hodnota. Správce stavu je obálku kolem zprostředkovatele stavu. Můžete ho použít k ukládání dat bez ohledu na to, který se používá nastavení trvalosti. Neposkytuje žádné záruky, spuštěné služby objektu actor můžete změnit z nastavení volatile (v paměti jen) stavu tak, aby nastavení trvalého stavu prostřednictvím postupného upgradu při zachování dat. Je však možné změnit počet replik pro spuštěnou službu.

Správce stavu klíče musí být řetězce. Hodnoty jsou obecné a mohou být jakéhokoli typu, včetně vlastních typů. Hodnotami uloženými v správce stavu musí být serializovatelný kontrakt dat, protože může přenést přes síť do dalších uzlů během replikace a může zapsat na disk, v závislosti na nastavení trvalost stavu objektu actor.

Správce stavu zpřístupní běžné metody slovník pro správu stavu, podobné těm, které jsou obsaženy ve slovníku pro spolehlivé.

Správa stavu objektu actor příkladů, najdete v tématu [přístup, uložit a odeberte Reliable Actors stavu](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Osvědčené postupy
Tady jsou některé navrhované postupy a tipy k řešení potíží pro správu vašeho stavu objektu actor.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Zkontrolujte jako podrobné nejblíže stavu objektu actor
To je důležité pro výkon a využití prostředků aplikace. Vždy, když je všechny aktualizace nebo zápisu "s názvem stavu" objektu actor, celou hodnotu odpovídající této "s názvem stavu" se serializovat a odesílá přes síť na sekundárních replikách.  Sekundárních zapisují na místní disk a odpověď zpět na primární repliku. Když primární obdrží potvrzení z kvora sekundárních replikách, zapíše stav jeho místní disk. Předpokládejme například, zda že je hodnota třídu, která má 20 členy a jejich velikost je 1 MB. I když mezi členy třídy, které je pouze změnit velikost 1 KB, end až platícího náklady na serializaci a sítě a disku zápisy pro úplné 1 MB. Podobně pokud je hodnota kolekce (například seznam, pole nebo slovník), platíte náklady na úplnou kolekci i v případě, že upravíte jednoho z jeho členů. Rozhraní StateManager třídy objektu actor je jako slovník. Vždy by měl model datovou strukturu představující stavu objektu actor nad tohoto slovníku.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Správně spravovat životní cyklus objektu actor
Měli byste mít jasné zásady o správě velikosti stavu v každém oddílu služby objektu actor. Služby objektu actor by měl mít pevný počet aktéři a znovu použít, je velmi míře. Pokud vytvoříte nepřetržitě nové aktéři, musíte je odstranit, jakmile se provádějí v práci. Rozhraní objektu actor ukládá některé metadata o každém objektu actor, který již existuje. Odstranění všech stavu objektu actor metadata o tomto objektu actor neodebere. Je nutné odstranit objektu actor (viz [odstraňování aktéři a jejich stavu](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) k odebrání všech informací o je uložená v systému. Jako další kontrolu, musí zadat dotaz na službu objektu actor (najdete v části [vytváření výčtu aktéři](service-fabric-reliable-actors-platform.md)) v k zkontrolujte, zda jsou čísla aktéři očekávaný rozsah.
 
Pokud někdy uvidíte, že velikost souboru databáze služby objektu Actor roste překračuje očekávanou velikost, ujistěte se, že jsou následující výše uvedených pokynů. Pokud postupujete podle těchto pokynů a jsou stále databáze problémům s velikostí souboru, měli byste [otevřete lístek podpory](service-fabric-support.md) s produktový tým jak získat nápovědu.

## <a name="next-steps"></a>Další postup

Musí být serializované stavu, který je uložen v Reliable Actors před jeho zapsaný na disk a replikované pro vysokou dostupnost. Další informace o [serializace typu objektu Actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

V dalším kroku Další informace o [objektu Actor Diagnostika a sledování výkonu](service-fabric-reliable-actors-diagnostics.md).

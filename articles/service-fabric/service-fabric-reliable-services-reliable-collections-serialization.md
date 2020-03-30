---
title: Serializace objektu spolehlivé kolekce
description: Další informace o azure service fabric spolehlivé kolekce serializace objektů, včetně výchozí strategie a jak definovat vlastní serializace.
ms.topic: conceptual
ms.date: 5/8/2017
ms.openlocfilehash: 666e1bb45a9c75ee143f15a0d871d6ae1408eca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639543"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serializace objektu spolehlivé kolekce v Azure Service Fabric
Spolehlivé kolekce replikovat a zachovat jejich položky, aby se ujistil, že jsou trvanlivé přes selhání počítače a výpadky napájení.
Replikovat a uchovávat položky, reliable collections 'potřeba serializovat je.

Spolehlivé kolekce 'získat odpovídající serializátor pro daný typ z Reliable State Manager.
Správce spolehlivého stavu obsahuje předdefinované serializátory a umožňuje registraci vlastních serializátorů pro daný typ.

## <a name="built-in-serializers"></a>Vestavěné serializátory

Reliable State Manager obsahuje vestavěný serializátor pro některé běžné typy, takže mohou být serializovány efektivně ve výchozím nastavení. Pro jiné typy Reliable State Manager přejde zpět k použití [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Předdefinované serializátory jsou efektivnější, protože vědí, že jejich typy se nemohou změnit a nemusí obsahovat informace o typu, jako je jeho název typu.

Reliable State Manager má vestavěný serializátor pro následující typy: 
- Identifikátor GUID
- bool
- byte
- sbyte
- bajt[]
- char
- řetězec
- decimal
- double
- float
- int
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Vlastní serializace

Vlastní serializátory se běžně používají ke zvýšení výkonu nebo šifrování dat přes drát a na disku. Mezi jiné důvody vlastní serializátory jsou běžně efektivnější než obecný serializátor, protože není nutné serializovat informace o typu. 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) se používá k registraci vlastní serializátor pro daný typ T. Tato registrace by mělo dojít v konstrukci StatefulServiceBase zajistit, že před obnovením, všechny spolehlivé kolekce mají přístup k příslušné serializátor číst jejich trvalá data.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Vlastní serializátory mají přednost před předdefinovanými serializátory. Například při registraci vlastní serializátor pro int, se používá k serializaci celá čísla namísto vestavěného serializátoru pro int.

### <a name="how-to-implement-a-custom-serializer"></a>Jak implementovat vlastní serializátor

Vlastní serializátor musí implementovat [rozhraní IStateSerializer\<T>.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1)

> [!NOTE]
> IStateSerializer\<T> obsahuje přetížení pro zápis a čtení, který trvá v další T nazývá základní hodnotu. Toto rozhraní API je pro rozdílovou serializaci. V současné době rozdílová serializace funkce není vystavena. Proto tyto dvě přetížení nejsou volány, dokud rozdílserializace je vystavena a povolena.

Následuje příklad vlastního typu s názvem OrderKey, který obsahuje čtyři vlastnosti

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Následuje příklad implementace IStateSerializer\<OrderKey>.
Všimněte si, že čtení a zápis přetížení, které se v baseValue, volání jejich příslušné přetížení pro předávání kompatibility.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Aktualizovatelnost
Při [upgradu postupných aplikací](service-fabric-application-upgrade.md)je upgrade použit na podmnožinu uzlů po jedné inovované doméně. Během tohoto procesu budou některé upgradovací domény v novější verzi aplikace a některé upgradovací domény budou ve starší verzi aplikace. Během zavádění musí být nová verze aplikace schopna číst starou verzi dat a stará verze aplikace musí být schopna číst novou verzi dat. Pokud formát dat není kompatibilní dopředu a zpět, upgrade může selhat nebo horší, data mohou být ztracena nebo poškozena.

Pokud používáte vestavěný serializátor, nemusíte se starat o kompatibilitu.
Však pokud používáte vlastní serializátor nebo DataContractSerializer, data musí být nekonečně dozadu a dopředu kompatibilní.
Jinými slovy, každá verze serializátoru musí být schopen serializovat a de-serializovat libovolnou verzi typu.

Uživatelé kontraktu dat by se měli řídit dobře definovanými pravidly správy verzí pro přidávání, odebírání a změnu polí. Kontrakt dat má také podporu pro práci s neznámými poli, zapisování do procesu serializace a deserializace a zpracování dědičnosti třídy. Další informace naleznete [v tématu Using Data Contract](https://msdn.microsoft.com/library/ms733127.aspx).

Vlastní serializer uživatelé by měli dodržovat pokyny serializátoru, které používají, aby se ujistil, že je kompatibilní dozadu a dopředu.
Běžným způsobem podpory všech verzí je přidání informací o velikosti na začátku a pouze přidání volitelných vlastností.
Tímto způsobem každá verze může číst tolik to může a skákat přes zbývající část datového proudu.

## <a name="next-steps"></a>Další kroky
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Odkaz pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Inovace aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.
  * [Upgrade aplikace pomocí powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.
  * Pomocí funkce [Parametry upgradu](service-fabric-application-upgrade-parameters.md)můžete řídit způsob upgradu aplikace .
  * Naučte se používat pokročilé funkce při inovaci aplikace odkazem na [upřesnit témata](service-fabric-application-upgrade-advanced.md).
  * Běžné problémy při upgradu aplikací opravíte odkazem na kroky při [řešení potíží s inovacemi aplikací](service-fabric-application-upgrade-troubleshooting.md).

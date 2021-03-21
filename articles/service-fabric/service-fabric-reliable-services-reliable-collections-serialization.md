---
title: Serializace objektu spolehlivé kolekce
description: Přečtěte si o Azure Service Fabric serializaci objektů služby Reliable Collections, včetně výchozí strategie a definování vlastní serializace.
ms.topic: conceptual
ms.date: 5/8/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 29bb9a2dfb028d223d63559b35735e78d7e6bcf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784355"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serializace objektu spolehlivé kolekce v Azure Service Fabric
Spolehlivé kolekce replikují a uchovávají jejich položky, aby se zajistilo, že jsou odolné proti selhání počítačů a výpadkům napájení.
Aby bylo možné replikovat a zachovat položky, je nutné je serializovat do spolehlivých kolekcí.

Spolehlivé kolekce získají vhodný serializátor pro daný typ ze Správce spolehlivého stavu.
Správce Reliable State obsahuje předdefinované serializátory a umožňuje registrovat vlastní serializátory pro daný typ.

## <a name="built-in-serializers"></a>Předdefinované serializátory

Reliable State Manager obsahuje vestavěný serializátor pro některé běžné typy, aby je bylo možné ve výchozím nastavení serializovat efektivně. Pro jiné typy se Reliable State Manager vrátí k použití třídy [DataContractSerializer](/dotnet/api/system.runtime.serialization.datacontractserializer).
Předdefinované serializace jsou efektivnější, protože znají jejich typy nemůžou být změněny a nemusejí zahrnovat informace o typu, jako je název jeho typu.

Správce Reliable State má vestavěný serializátor pro následující typy: 
- Identifikátor GUID
- bool
- byte
- sbyte
- Byte []
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

Vlastní koserializátory se běžně používají ke zvýšení výkonu nebo k šifrování dat po drátě a na disku. Mimo jiné důvody jsou vlastní serializace obvykle efektivnější než obecné serializátor, protože nepotřebují serializaci informací o typu. 

[IReliableStateManager. TryAddStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) slouží k registraci vlastního serializátoru pro daný typ T. Tato registrace by se měla proběhnout při vytváření StatefulServiceBase, aby se zajistilo, že před zahájením obnovení mají všechny spolehlivé kolekce přístup k příslušnému serializátoru, aby mohli číst trvalá data.

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
> Vlastní serializátory mají přednost před vestavěnými serializátory. Například při registraci vlastního serializátoru pro int je použit k serializaci celých čísel namísto vestavěného serializátoru pro int.

### <a name="how-to-implement-a-custom-serializer"></a>Implementace vlastního serializátoru

Vlastní serializátor musí implementovat rozhraní [IStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.istateserializer-1) .

> [!NOTE]
> IStateSerializer \<T> zahrnuje přetížení pro zápis a čtení, která přebírají v další hodnotě T nazvané Base. Toto rozhraní API je pro rozdílovou serializaci. Funkce rozdílové serializace není k dispozici. Proto tato dvě přetížení nejsou volána, dokud není k dispozici rozdílové serializace a povolena.

Následuje příklad vlastního typu s názvem OrderKey, který obsahuje čtyři vlastnosti.

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

Následuje příklad implementace IStateSerializer \<OrderKey> .
Všimněte si, že přetížení čtení a zápisu, která přebírají v baseValue, volají jejich příslušné přetížení pro zajištění kompatibility s přesměrováním.

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

## <a name="upgradability"></a>K disgradování
V rámci [upgradu aplikace](service-fabric-application-upgrade.md)se upgrade aplikuje na podmnožinu uzlů, jednu upgradovací doménu v jednom okamžiku. Během tohoto procesu budou některé domény upgradu v novější verzi aplikace a některé domény upgradu budou ve starší verzi vaší aplikace. Při zavedení musí být v nové verzi aplikace možné číst starou verzi vašich dat a stará verze vaší aplikace musí být schopná přečíst si novou verzi vašich dat. Pokud formát dat není předáván dál a zpětně kompatibilní, upgrade může selhat nebo může dojít ke ztrátě nebo poškození dat.

Pokud používáte vestavěný serializátor, nemusíte se obávat kompatibility.
Pokud však používáte vlastní serializátor nebo DataContractSerializer, data musí být nekonečně zpět a předána kompatibilní.
Jinými slovy, každá verze serializátoru musí být schopna serializovat a deserializovat jakékoli verze typu.

Uživatelé kontraktů dat by měli postupovat podle jasně definovaných pravidel správy verzí pro přidání, odebrání a změnu polí. Kontrakt dat také podporuje pro práci s neznámými poli, zapojení do procesu serializace a deserializace a řešit dědičnost tříd. Další informace najdete v tématu [použití kontraktu dat](/dotnet/framework/wcf/feature-details/using-data-contracts).

Uživatelé vlastního serializátoru by měli dodržovat pokyny serializátoru, který používají, aby se zajistilo, že je zpětně kompatibilní a je dál kompatibilní.
Běžným způsobem, jak podpořit všechny verze, je přidání informací o velikosti na začátku a přidávání volitelných vlastností.
Tímto způsobem může každá verze číst, a to vše, co může, a přejít na zbývající část streamu.

## <a name="next-steps"></a>Další kroky
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Referenční informace pro vývojáře pro spolehlivé kolekce](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  * [Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.
  * [Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.
  * Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak se vaše aplikace upgradují.
  * Naučte se používat pokročilé funkce při upgradu vaší aplikace, které odkazují na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).
  * Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.

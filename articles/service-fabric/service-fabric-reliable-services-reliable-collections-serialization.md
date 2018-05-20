---
title: Spolehlivé serializace objektu kolekce v Azure Service Fabric | Microsoft Docs
description: Serializace objektu Azure Service Fabric spolehlivé kolekce
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: mcoskun
ms.openlocfilehash: b02d8924749abb0e2fe815b555d55767bf1e5cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Spolehlivé serializace objektu kolekce v Azure Service Fabric
Spolehlivé kolekce replikovat a zachovat jejich položky a ujistěte se, že jsou odolné napříč selhání počítače a výpadky napájení.
Jak chcete replikovat a zachovat položky, k serializaci je nutné spolehlivé kolekce.

Spolehlivé kolekce získat odpovídající serializátor pro daný typ z spolehlivé správce stavu.
Správce spolehlivé stavu obsahuje integrovanou serializátorů a umožňuje vlastní serializátorů k registraci pro daného typu.

## <a name="built-in-serializers"></a>Předdefinované Serializátorů

Správce spolehlivé stavu zahrnuje integrovanou serializátor pro některé běžné typy tak, aby se Dal serializovat efektivně ve výchozím nastavení. Pro ostatní typy spolehlivé správce stavu se vrátí k použití [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Předdefinované serializátorů jsou efektivnější, protože vědí, nelze změnit jejich typy a není potřeba zahrnovat informace o typu jako její název typu.

Správce spolehlivé stavu má integrovanou serializátor pro následující typy: 
- Guid
- BOOL
- byte
- SByte
- Byte
- Char
- řetězec
- Decimal
- double
- float
- celá čísla
- uint
- zem. šířka
- ulong
- krátký
- ushort

## <a name="custom-serialization"></a>Vlastní serializace

Vlastní serializátorů se běžně používají, pokud chcete zvýšit výkon, nebo k šifrování dat přenášených v síti a na disku. Mezi z jiných důvodů jsou často efektivnější než obecné serializátor vlastní serializátorů, vzhledem k tomu nepotřebují k serializaci informace o typu. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer--1?Microsoft_ServiceFabric_Data_IReliableStateManager_TryAddStateSerializer__1_Microsoft_ServiceFabric_Data_IStateSerializer___0__) slouží k registraci vlastní serializátor pro daný typ T. Tato registrace musí dojít při sestavování StatefulServiceBase, před zahájením obnovení, všechny spolehlivé kolekce získali přístup k příslušné serializátor číst jejich trvalá data.

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
> Vlastní serializátorů dané priority nad předdefinované serializátorů. Například když je zaregistrovaný vlastní serializátor int, použije se k serializaci celých čísel namísto integrované serializátor pro int.

### <a name="how-to-implement-a-custom-serializer"></a>Jak implementovat vlastní serializátor

Vlastní serializátor musí implementovat [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) rozhraní.

> [!NOTE]
> IStateSerializer<T> zahrnuje přetížení pro zápis a čtení, která přebírá další t označuje základní hodnota. Toto rozhraní API je pro rozdílové serializace. Aktuálně nebude vystavena, funkce rozdílové serializace. Proto nejsou tyto dvě přetížení volá dokud rozdílové serializace je vystavený a povolený.

Následuje příklad vlastního typu, v názvem OrderKey, který obsahuje čtyři vlastnosti

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

Následuje příklad implementace IStateSerializer<OrderKey>.
Všimněte si, že čtení a zápis přetížení, které provést ve baseValue, jejich příslušné přetížení volání pro předávání kompatibility.

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

## <a name="upgradability"></a>Zdokonalovány
V [vrácení upgradu aplikace](service-fabric-application-upgrade.md), upgradu se použije pro dílčí sadu uzlů, jednu upgradovací doménu najednou. Během tohoto procesu několik domén upgradu bude v novější verzi aplikace a některé domén upgradu bude na starší verzi aplikace. Při zavedení nová verze aplikace musí být možné číst stará verze vaše data a starší verzi aplikace musí být možné číst novou verzi vaše data. Pokud formát dat není vpřed a zpětně kompatibilní, upgrade může selhat nebo horší, mohou být data ztrátě nebo poškození.

Pokud používáte integrované serializátor, nemáte se starat o kompatibilitě.
Ale pokud používáte vlastní serializátor nebo objektu DataContractSerializer, data mají být nekonečnou zpět a vpřed kompatibilní.
Jinými slovy každou verzi serializátor musí být schopen serializovat a deserializovat všechny verze typu.

Uživatelé kontraktu dat postupujte podle pravidla dobře definovaný Správa verzí pro přidání, odebrání a změna pole. Kontrakt dat má také podpora zabývají neznámé pole, zapojování do procesu serializace a deserializace a plánování práce s dědičnosti tříd. Další informace najdete v tématu [pomocí kontrakt dat](https://msdn.microsoft.com/library/ms733127.aspx).

Vlastní serializátor uživatelé by měl splňovat pokyny serializátoru, který používají zajistěte, aby je zpětné a předává kompatibilní.
Běžný způsob podpory všechny verze je přidání informací o velikosti od začátku a jenom přidávání volitelné vlastnosti.
Tímto způsobem jednotlivých verzí může číst mnohem můžete a přejít přes zbývající část datového proudu.

## <a name="next-steps"></a>Další postup
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Upgrade vaší aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.
  * [Upgrade vaší aplikace pomocí prostředí Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí prostředí PowerShell.
  * Řídí, jak vaše aplikace upgraduje pomocí [Upgrade parametry](service-fabric-application-upgrade-parameters.md).
  * Další informace o použití pokročilých funkcí při upgradu vaší aplikace tím, že odkazuje na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).
  * Řešení běžných potíží v upgradů aplikací podle kroků v části [řešení potíží s aplikací upgrady](service-fabric-application-upgrade-troubleshooting.md).

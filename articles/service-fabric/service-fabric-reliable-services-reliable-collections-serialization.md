---
title: Spolehlivá serializace kolekcí objektů v Azure Service Fabric | Dokumentace Microsoftu
description: Serializace objektu služby Azure Service Fabric Reliable Collections
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
ms.openlocfilehash: 8fb6f1767741e950b300fd297250a6b64656191c
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952422"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Spolehlivá serializace kolekcí objektů v Azure Service Fabric
Reliable Collections replikovat a zachování jejich položky, abyste měli jistotu, že jsou trvalé napříč počítači selhání a výpadky napájení.
Pro replikaci i pro uchování položky, Reliable Collections nutné k serializaci je.

Reliable Collections získat příslušný serializátor pro daný typ z Reliable State Manager.
Reliable State Manager obsahuje integrované serializátory a umožňuje vlastní serializátory k registraci pro daného typu.

## <a name="built-in-serializers"></a>Integrované Serializátorů

Reliable State Manager zahrnuje integrovanou serializátor pro některé běžné typy tak, aby se lze serializovat efektivně ve výchozím nastavení. U jiných typů Reliable State Manager přejde zpět k použití [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Integrované serializátory jsou efektivnější, protože nelze změnit jejich typy a není potřeba zahrnovat informace o typu jako jeho název typu, které znají.

Reliable State Manager má integrované serializátor pro následující typy: 
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
- int
- uint
- zem. šířka
- ulong
- krátké
- ushort

## <a name="custom-serialization"></a>Vlastní serializace

Vlastní serializátory se běžně používají ke zvýšení výkonu nebo k šifrování dat při přenosu i na disku. Mezi z jiných důvodů jsou obvykle efektivnější než obecné serializátor vlastní serializátory, protože není potřeba serializovat informace o typu. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) se použije k registraci uživatelský serializátor pro daný typ T. Tuto registrace se stane v konstrukci StatefulServiceBase před zahájením obnovení všech Reliable Collections měli přístup k příslušné serializátor přečíst trvalá data.

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
> Vlastní serializátory přednost nad integrované serializátory. Například když je zaregistrovaný vlastní serializátor pro int, používá se k serializaci celá čísla namísto integrovaných serializátor pro int.

### <a name="how-to-implement-a-custom-serializer"></a>Jak implementovat vlastní serializátor

Uživatelský serializátor musí implementovat [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) rozhraní.

> [!NOTE]
> IStateSerializer<T> zahrnuje přetížení pro zápis a čtení, která přebírá další t volá základní hodnoty. Toto rozhraní API je pro rozdílovou serializace. Aktuálně není dostupná funkce rozdílové serializace. Tyto dvě přetížení nejsou volány proto dokud rozdílové serializace je vystavený a povolený.

Tady je příklad vlastního typu, v volá OrderKey, který obsahuje čtyři vlastnosti

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

Tady je příklad implementace IStateSerializer<OrderKey>.
Všimněte si, že čtení a zápis v baseValue, přetížení, která přijímají volání jejich odpovídajících přetížení pro předávání kompatibility.

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
V [aplikace upgrade se zajištěním provozu](service-fabric-application-upgrade.md), upgrade se použije pro dílčí sadu uzlů, jednu upgradovací doménu najednou. Během tohoto procesu některé upgradovacích doménách budou na novější verzi aplikace a některé upgradovacích doménách budou na starší verzi aplikace. Během zavádění novou verzi vaší aplikace musí být schopni číst staré verze vašich dat a starou verzi vaší aplikace musí být schopni číst novou verzi vaše data. Pokud formát dat není kompatibilní s vpřed a zpět, může upgrade selhat nebo horší, mohou být data ztrátě nebo poškození.

Pokud používáte integrované serializátor, není nutné se starat o kompatibilitě.
Však-pokud používáte vlastní serializátor nebo objektu DataContractSerializer, data musí být neomezeně zpět a vpřed kompatibilní.
Jinými slovy všechny verze serializátor musí být schopen serializujeme a deserializujeme všechny verze typu.

Uživatelé kontraktu dat postupujte podle pravidla jasně definované správy verzí pro přidání, odebrání a změna polí. Kontrakt dat má také podpora zabývají neznámé pole, zapojení do procesu serializace a deserializace a zabývajících se dědičnost tříd. Další informace najdete v tématu [pomocí kontraktu dat](https://msdn.microsoft.com/library/ms733127.aspx).

Uživatelé uživatelský serializátor by měl splňovat pokyny serializátoru, který používají Ujistěte se, že je zpětně a předává kompatibilní.
Běžný způsob podporující všechny verze je přidání informací o velikosti na začátku a pouze přidáním volitelné vlastnosti.
Tímto způsobem jednotlivé verze můžete přečíst mnohem můžete a přeskočit zbývající část datového proudu.

## <a name="next-steps"></a>Další postup
  * [Serializace a upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Referenční informace pro vývojáře pro Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgrade aplikace pomocí sady Visual Studio.
  * [Upgrade aplikace pomocí Powershellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgrade aplikace pomocí Powershellu.
  * Řídí, jak vaše aplikace upgradovala pomocí [parametry upgradu](service-fabric-application-upgrade-parameters.md).
  * Zjistěte, jak používat pokročilé funkce při upgradu aplikace rekapitulací [Pokročilá témata](service-fabric-application-upgrade-advanced.md).
  * Vyřešit běžné problémy v upgradech aplikací odkazující na kroky v [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md).

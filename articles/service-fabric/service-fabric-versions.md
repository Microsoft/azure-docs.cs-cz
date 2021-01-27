---
title: Upgrade verze clusteru Azure Service Fabric
description: Přečtěte si o verzích clusterů v Azure Service Fabric, včetně odkazu na nejnovější verze z blogu týmu Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: cd9c4be285c68e5482f7276429dd5514c72ae135
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881546"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>Upgrade verze clusteru Azure Service Fabric

Ujistěte se, že cluster vždycky používá podporovanou verzi Azure Service Fabric. Minimálně 60 dní poté, co oznamuje vydání nové verze Service Fabric, podpora pro předchozí verze. Oznámení o nových vydáních najdete na [blogu týmu Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Pro každou verzi modulu Service Fabric runtime můžete použít zadané nebo starší verze balíčků SDK/NuGet. Novější verze balíčků nemusí být schopné cílit na starší clustery. Starší clustery můžou mít změny funkcí nebo protokolů, které nepodporují novější prostředí balíčku.

Podrobnosti o tom, jak udržovat cluster s podporovanou Service Fabric verzí, najdete v následujících článcích:

- [Upgrade clusteru Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade verze Service Fabric, která běží na samostatném clusteru Windows serveru](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Nepodporované verze

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Výstraha upgradu pro verze mezi 5,7 a 6.3.63. *

Pro zlepšení zabezpečení a dostupnosti infrastruktura Azure provedla změnu, která by mohla ovlivnit Service Fabric zákazníky. Tato změna ovlivní všechny Service Fabric clusterů s verzemi 5,7 až 6,3.

Aktualizace modulu runtime Service Fabric je k dispozici pro všechny podporované verze Service Fabric ve všech oblastech. Abyste se vyhnuli přerušení služeb, upgradujte na jednu z nejnovějších podporovaných verzí od 19. ledna 2021.

Pokud máte plán podpory a potřebujete technickou pomoc, obraťte se přes kanály podpory Azure. Otevřete žádost o podporu pro Azure Service Fabric a uveďte tento kontext v lístku podpory.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Pokud neprovádíte upgrade na podporovanou verzi

Clustery Azure Service Fabric spuštěné ve verzích od 5,7 do 6.3.63. * nebudou dostupné, pokud jste upgrade neudělali 19. ledna 2021.

#### <a name="required-action"></a>Požadovaná akce

Upgrade na podporovanou verzi Service Fabric, aby nedocházelo k výpadkům nebo ztrátě funkčnosti souvisejících s touto změnou. Zajistěte, aby byly v clusterech spuštěné alespoň následující verze, aby nedocházelo k problémům ve vašem prostředí.

> [!Note]
> Všechny vydané verze 7,2 obsahují nezbytné změny.
  
  | Operační systém | Aktuální Service Fabric modul runtime v clusteru | Verze CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Výstraha upgradu pro verze novější než 6,3

Pro zlepšení zabezpečení a dostupnosti infrastruktura Azure provedla změnu, která by mohla ovlivnit Service Fabric zákazníky. Tato změna bude mít vliv na všechny Service Fabric clusterů, které používají [režim otevřené sítě pro kontejnery](./service-fabric-networking-modes.md#set-up-open-networking-mode) a verze 6,3 až 7,0 nebo nekompatibilních podporovaných verzí později než 7,0. Aktualizace modulu runtime Service Fabric je k dispozici pro všechny podporované verze Service Fabric ve všech oblastech.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Pokud neprovádíte upgrade na podporovanou verzi

Clustery Azure Service Fabric, které běží na nezměněných verzích později než 6,3, budou mít za následek ztrátu funkčnosti nebo přerušení služeb, pokud nebyly upgradovány na podporovanou verzi 19. ledna 2021.
  
  - V **případě clusterů, ve kterých je spuštěná verze Service Fabric větší než 6,3, se nepoužívá funkce otevřít síť**, zůstane cluster zapnutý.

 - **U clusterů, na kterých běží verze Service Fabric větší než 6,3 a využívají [funkci Open Networking pro kontejnery](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)** , by cluster mohl být nedostupný a přestane fungovat, což by mohlo způsobit přerušení služeb pro vaše úlohy.
 
 -   **Pro clustery s [verzemi Windows mezi 7.0.457 a 7.0.466 (včetně obou verzí)](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-version-names) a operačním systémem Windows je povolená funkce kontejnerů Windows. Poznámka: verze Linux 7.0.457, 7.0.464 a 7.0.465 nejsou ovlivněny**.
    - **Dopad**: cluster přestane fungovat, což by mohlo způsobit přerušení služeb pro vaše úlohy.
    
#### <a name="required-action"></a>Požadovaná akce

Aby nedocházelo k výpadkům nebo ztrátě funkčnosti, zajistěte, aby v clusterech běžela jedna z následujících verzí.

Verze Service Fabric v tabulce obsahují nezbytné změny, aby nedošlo ke ztrátě funkčnosti. Ujistěte se, že používáte jednu z těchto verzí.  

> [!Note]
> Všechny verze 7,2 obsahují nezbytné změny.

  | Operační systém | Aktuální Service Fabric modul runtime v clusteru | Verze CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Linux Ubuntu 16,04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

## <a name="supported-versions"></a>Podporované verze

V následující tabulce jsou uvedeny verze Service Fabric a jejich koncová data podpory.

| Service Fabric modul runtime v clusteru | Může upgradovat přímo z verze clusteru. |Kompatibilní verze sady SDK nebo balíčku NuGet | Konec podpory |
| --- | --- |--- | --- |
| Všechny verze clusteru před 5.3.121 | 5.1.158.* |Menší nebo rovno verzi 2,3 |20. ledna 2017 |
| 5,3. * | 5.1.158.* |Menší nebo rovno verzi 2,3 |24. února 2017 |
| 5,4. * | 5.1.158.* |Menší nebo rovno verzi 2,4 |10. května 2017       |
| 5,5. * | 5.4.164.* |Menší nebo rovno verzi 2,5 |10. srpna 2017    |
| 5,6. * | 5.4.164.* |Menší nebo rovno verzi 2,6 |13. října 2017   |
| 5,7. * | 5.4.164.* |Menší nebo rovno verzi 2,7 |15. prosince 2017  |
| 6,0. * | 5.6.205.* |Menší nebo rovno verzi 2,8 |30. března 2018     |
| 6,1. * | 5.7.221.* |Menší nebo rovno verzi 3,0 |15. července 2018      |
| 6,2. * | 6.0.232.* |Menší nebo rovno verzi 3,1 |26. října 2018   |
| 6,3. * | 6.1.480.* |Menší nebo rovno verzi 3,2 |31. března 2019  |
| 6,4. * | 6.2.301.* |Menší nebo rovno verzi 3,3 |15. září 2019 |
| 6,5. * | 6.4.617.* |Menší nebo rovno verzi 3,4 |Od 1. srpna 2020 |
| 7.0.466.* | 6.4.664.* |Menší nebo rovno verzi 4,0|31. ledna 2021  |
| 7.0.466.* | 6,5. * |Menší nebo rovno verzi 4,0|31. ledna 2021 |
| 7.0.470.* | 7.0.466.* |Menší nebo rovno verzi 4,0 |31. ledna 2021  |
| 7.0.472.* | 7.0.466.* |Menší nebo rovno verzi 4,0 |31. ledna 2021  |
| 7.0.478.* | 7.0.466.* |Menší nebo rovno verzi 4,0 |31. ledna 2021  |
| 7.1.409.* | 7.0.466.* |Menší nebo rovno verzi 4,1 |31. března 2021 |
| 7.1.417.* | 7.0.466.* |Menší nebo rovno verzi 4,1 |31. března 2021 |
| 7.1.428.* | 7.0.466.* |Menší nebo rovno verzi 4,1 |31. března 2021 |
| 7.1.456.* | 7.0.466.* |Menší nebo rovno verzi 4,1 |31. března 2021 |
| 7.1.458.* | 7.0.466.* |Menší nebo rovno verzi 4,1 |31. března 2021 |
| 7.1.459.* | 7.0.466.* |Menší nebo rovno verzi 4,1 |31. března 2021 |
| 7.1.503.* | 7.0.466.* |Menší nebo rovno verzi 4,1 |31. března 2021 |
| 7.2.413.* | 7.0.470.* |Menší nebo rovno verzi 4,2 |Aktuální verze, takže bez koncového data |
| 7.2.432.* | 7.0.470.* |Menší nebo rovno verzi 4,2 |Aktuální verze, takže bez koncového data |
| 7.2.433.* | 7.0.470.* |Menší nebo rovno verzi 4,2 |Aktuální verze, takže bez koncového data |
| 7.2.445.* | 7.0.470.* |Menší nebo rovno verzi 4,2 |Aktuální verze, takže bez koncového data |

## <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí podporované operační systémy pro podporované verze Service Fabric.

| Operační systém | Nejstarší podporovaná verze Service Fabric |
| --- | --- |
| Windows Server 2012 R2 | Všechny verze |
| Windows Server 2016 | Všechny verze |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |
| Linux Ubuntu 18,04 | 7.1 |

## <a name="supported-version-names"></a>Podporované názvy verzí

Následující tabulka uvádí názvy verzí Service Fabric a jejich odpovídající čísla verzí.

| Název verze | Číslo verze Windows | Číslo verze Linux |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | Neuvedeno|
| 5,3 CU1 | 5.3.204.9494 | Neuvedeno|
| 5,3 CU2 | 5.3.301.9590 | Neuvedeno|
| 5,3 CU3 | 5.3.311.9590 | Neuvedeno|
| 5,4 CU2 | 5.4.164.9494 | Neuvedeno|
| 5,5 CU1 | 5.5.216.0    | Neuvedeno|
| 5,5 CU2 | 5.5.219.0 | Neuvedeno|
| 5,5 CU3 | 5.5.227.0 | Neuvedeno|
| 5,5 CU4 | 5.5.232.0 | Neuvedeno|
| 5,6 RTO | 5.6.204.9494 | Neuvedeno|
| 5,6 CU2 | 5.6.210.9494 | Neuvedeno|
| 5,6 CU3 | 5.6.220.9494 | Neuvedeno|
| 5,7 RTO | 5.7.198.9494 | Neuvedeno|
| 5,7 CU4 | 5.7.221.9494 | Neuvedeno|
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Neuvedeno|
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Neuvedeno|
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Neuvedeno|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Neuvedeno|
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7,1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7,2 RTO | 7.2.413.9590 | Neuvedeno|
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | Neuvedeno|
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |

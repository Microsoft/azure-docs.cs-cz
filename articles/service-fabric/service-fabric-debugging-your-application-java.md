---
title: Ladění aplikace Azure Service Fabric v Eclipse | Dokumentace Microsoftu
description: Zlepšení spolehlivosti a výkonu služeb ve vývoji a ladění je v prostředí Eclipse v místním vývojovém clusteru.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 8f0470b10589ecbbc9e2c98e8d3445435e7f8ed4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668819"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Ladění aplikace Service Fabric v Javě pomocí Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Spusťte místní vývojový cluster podle pokynů v [nastavení vývojového prostředí Service Fabric](service-fabric-get-started-linux.md).

2. Aktualizujte entryPoint.sh služby, kterou chcete ladit, tak, aby proces Javy spouštěl s parametry vzdáleného ladění. Tento soubor se nachází v následujícím umístění: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. V tomto příkladu je pro ladění nastavený port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Aktualizujte Manifest aplikace tak, že počet instancí nebo replik pro službu, která je právě laděna nastavíte na hodnotu 1. Toto nastavení zabrání konfliktům na portu, který slouží k ladění. Například pro bezstavové služby nastavte ``InstanceCount="1"`` a pro stavové služby nastavte cílovou a minimální velikost sady replik na 1 následujícím způsobem: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Nasazení aplikace.

5. Integrované vývojové prostředí Eclipse vyberte **Run -> Konfigurace ladění -> Remote Java Application a zadejte vlastnosti připojení** a nastavte vlastnosti následujícím způsobem:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Nastavte zarážky na požadovanou body a ladění aplikace.

Pokud dochází k chybám. aplikace, můžete také povolit coredumps. Spustit ``ulimit -c`` v prostředí a pokud vrátí hodnotu 0, a pak coredumps nejsou povolené. Pokud chcete povolit neomezené coredumps, spusťte následující příkaz: ``ulimit -c unlimited``. Můžete také ověřit stav pomocí příkazu ``ulimit -a``.  Pokud chcete aktualizovat cestu generování coredump, spusťte ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Další postup

* [Shromažďování protokolů pomocí Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorování a Diagnostika služeb místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

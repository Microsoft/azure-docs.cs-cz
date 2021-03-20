---
title: Ladění aplikace v zatmění
description: Vylepšete spolehlivost a výkon svých služeb tím, že je vyvyvíjíte a ladíte v zatmění na místním vývojovém clusteru.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-java
ms.openlocfilehash: d321e0c10b66a15e6cb309cefe711602fa12957c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534104"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Ladění aplikace Service Fabric Java pomocí zatmění
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zatmění/Java](service-fabric-debugging-your-application-java.md)
> 

1. Spusťte místní vývojový cluster podle kroků uvedených v části [Nastavení vývojového prostředí Service Fabric](service-fabric-get-started-linux.md).

2. Aktualizujte entryPoint.sh služby, kterou chcete ladit, aby se spouštěl proces Java s parametry vzdáleného ladění. Tento soubor najdete v následujícím umístění: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh` . V tomto příkladu je pro ladění nastavený port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Aktualizujte manifest aplikace nastavením počtu instancí nebo počtu replik pro službu, která je laděna na 1. Toto nastavení zabrání konfliktům na portu, který slouží k ladění. Například pro bezstavové služby nastavte `InstanceCount="1"` a pro stavové služby nastavte cílovou a minimální velikost sady replik na 1 následujícím způsobem: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Nasaďte aplikaci.

5. V integrovaném vývojovém prostředí (IDE) vyberte **spustit > konfigurace ladění-> vzdálenou aplikaci Java a vlastnosti vstupního připojení** a nastavte vlastnosti následujícím způsobem:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Nastavte zarážky v požadovaných bodech a proveďte ladění aplikace.

Pokud dojde k chybě aplikace, můžete také povolit coredumps. Spustí se `ulimit -c` v prostředí a pokud vrátí hodnotu 0, pak coredumps nejsou povolené. Pokud chcete povolit neomezený coredumps, spusťte následující příkaz: `ulimit -c unlimited` . Stav můžete také ověřit pomocí příkazu `ulimit -a` .  Pokud jste chtěli aktualizovat cestu generování coredump, spusťte `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern` . 

### <a name="next-steps"></a>Další kroky

* [Shromažďování protokolů pomocí Azure Diagnostics Linux](./service-fabric-diagnostics-event-aggregation-lad.md).
* [Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

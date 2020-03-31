---
title: Ladění aplikace v Eclipse
description: Zlepšete spolehlivost a výkon svých služeb jejich vývojem a laděním v eclipse v místním vývojovém clusteru.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614481"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Ladění aplikace Java Service Fabric pomocí Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zatmění/Java](service-fabric-debugging-your-application-java.md)
> 

1. Spusťte cluster místního vývoje podle kroků v [části Nastavení vývojového prostředí Service Fabric](service-fabric-get-started-linux.md).

2. Aktualizace entryPoint.sh služby, kterou chcete ladit, tak, aby se spustí proces java se vzdálenými parametry ladění. Tento soubor lze nalézt v `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`následujícím umístění: . V tomto příkladu je pro ladění nastavený port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Aktualizujte manifest aplikace nastavením počtu instancí nebo počtu replik pro službu, která je odladěna na 1. Toto nastavení zabrání konfliktům na portu, který slouží k ladění. Například pro bezstavové služby nastavte `InstanceCount="1"` a pro stavové služby nastavte cílovou a minimální velikost sady replik na 1 následujícím způsobem: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Nasaďte aplikaci.

5. V rozhraní Eclipse IDE vyberte **spustit -> ladicí konfigurace -> vzdálené aplikace Java a vlastnosti vstupního připojení** a nastavte vlastnosti takto:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Nastavte zarážky v požadovaných bodech a ladit aplikace.

Pokud je aplikace shazovat, můžete také povolit coredumps. Spusťte `ulimit -c` v prostředí a pokud vrátí 0, pak coredumps nejsou povoleny. Chcete-li povolit neomezené coredumps, spusťte následující příkaz: `ulimit -c unlimited`. Stav můžete také ověřit pomocí `ulimit -a`příkazu .  Pokud jste chtěli aktualizovat cestu generování `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`coredump, spusťte . 

### <a name="next-steps"></a>Další kroky

* [Shromažďovat protokoly pomocí Diagnostika Linux Azure](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorujte a diagnostikujte služby místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

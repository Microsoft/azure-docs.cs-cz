---
title: Ladění aplikace Azure Service Fabric v prostředí Eclipse | Microsoft Docs
description: Vylepšit spolehlivost a výkon vašich služeb vývoji a ladění je v prostředí Eclipse v místní vývojový cluster.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 0e9e816fa84816b1b5d12f066dc65aee7b4930f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205449"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Ladění aplikace Java Service Fabric pomocí Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio nebo CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse nebo Java](service-fabric-debugging-your-application-java.md)
> 

1. Spuštění místního vývojového clusteru podle kroků v [nastavení vývojového prostředí Service Fabric](service-fabric-get-started-linux.md).

2. Aktualizujte entryPoint.sh služby, kterou chcete ladit, tak, aby spouštěl procesu java s parametry vzdáleného ladění. Tento soubor se nachází v následujícím umístění: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. V tomto příkladu je pro ladění nastavený port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Nastavení počtu instancí nebo počet replik pro službu, která je laděné 1 Aktualizujte Manifest aplikace. Toto nastavení zabrání konfliktům na portu, který slouží k ladění. Například pro bezstavové služby nastavte ``InstanceCount="1"`` a pro stavové služby nastavte cílovou a minimální velikost sady replik na 1 následujícím způsobem: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Nasazení aplikace.

5. V integrovaném vývojovém prostředí Eclipse, vyberte **spustit -> Konfigurace ladění -> vzdálené aplikace Java a zadejte vlastnosti připojení** a nastavte vlastnosti takto:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Nastavte zarážky v požadované body a ladění aplikace.

Pokud aplikace selhává, můžete také povolit coredumps. Spuštění ``ulimit -c`` v prostředí a pokud vrátí hodnotu 0, pak coredumps nejsou povoleny. Pokud chcete povolit neomezená coredumps, spusťte následující příkaz: ``ulimit -c unlimited``. Můžete si taky ověřit stav pomocí příkazu ``ulimit -a``.  Pokud jste chtěli aktualizovat generování cestu coredump, spouštění ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Další postup

* [Shromažďování protokolů pomocí Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorování a Diagnostika služby místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
